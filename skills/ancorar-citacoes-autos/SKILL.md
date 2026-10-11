---
name: "ancorar-citacoes-autos"
description: "Extrai afirmações ancoradas em página dos PDFs dos autos usando a Citations API da Anthropic, para que nenhuma alegação entre em peça sem o trecho literal e o número da página de origem. Dispare ao pedir \"ancora nos autos\", \"de que página saiu isso\", \"confere se está nos autos\", \"cita a página\", \"monta a matriz fato/página\", ou ao subir PDF de autos pedindo extração verificável."
---

# Ancorar afirmações nos autos

Produz uma tabela fato → trecho literal → página, gerada pela Citations API da
Anthropic, para alimentar a redação da peça. A API só devolve citação apontando
para documento efetivamente fornecido: não há como inventar uma página.

## Quando NÃO usar

- Redação da peça em si → `juridica-adpatada` (ou `juridica-generica`).
- Bateria crítica sobre a tese → `auditoria-adversarial`.
- Esta skill é o passo anterior a ambas: gera o material ancorado que elas consomem.
- **Não há equivalente no Claude Code.** Ler o PDF pela ferramenta de leitura de
  arquivo não é Citations API: não devolve `page_location`, e a página volta a
  ser texto gerado — exatamente o erro que esta skill existe para eliminar. O
  ancoramento só vale pela API, com `citations` ligado.

## Como funciona

Cada documento vai na mensagem com `citations: {enabled: true}`. A resposta traz
blocos de texto com um array `citations`. Para PDF, cada citação tem
`cited_text`, `start_page_number` e `end_page_number` (1-indexados).

Fatos verificados na documentação oficial:

- Suportado em **todos os modelos ativos**, inclusive `claude-opus-5`.
- PDF → `page_location`, página 1-indexada. Texto puro → `char_location`.
  Conteúdo customizado (listas, transcrições) → `content_block_location`.
- `source` aceita `base64`, `text`, `url`, `content` e `file` (Files API, por
  `file_id`). Citations funciona em todos.
- O chunking é por **sentença**: a citação nunca é menor que uma frase.
- `cited_text` **não conta como token de saída**, e também não conta como token
  de entrada quando devolvido em turnos seguintes. Citar sai barato.
- **Incompatível com structured outputs** (`output_config.format`). Se precisar
  de JSON estruturado, monte a tabela a partir do array `citations` no cliente,
  não peça JSON ao modelo.
- **Compatível com prompt caching**: `cache_control: {"type": "ephemeral"}` no
  bloco do documento. Vale quando o mesmo PDF responde várias perguntas.
- PDF precisa ter texto extraível. Autos digitalizados sem OCR não geram
  citação — passe por OCR antes.
- Compatibilidade com Batch API **não está documentada** na página de citations.
  Não assumir metade de preço; se for usar, validar em um documento antes.

## Limites que quebram o lote

O erro típico é mandar a pilha inteira de acórdãos numa requisição só:

| Limite | Valor |
|---|---|
| Tamanho da **requisição** (base64 inline) | 32 MB, somando tudo na mensagem |
| Páginas por requisição | 600 (ou 100 se a janela de contexto for < 1M) |
| Tamanho por arquivo via **Files API** | 500 MB |
| Storage total da organização | 1 TB |

Regra prática: **acima de ~3 PDFs ou ~20 MB, nada de base64 inline.** Subir pela
Files API, referenciar por `file_id`, **uma chamada por documento**, e
consolidar a matriz no cliente. Isso também evita reenviar o PDF a cada pergunta.

## Procedimento

1. Confirmar o número dos autos (acione `conferir-cnj` se ainda não validado).
2. **Pré-check de cada PDF** (páginas, tamanho, camada de texto) — comando
   abaixo. PDF sem texto extraível sai da fila e vai para OCR.
3. Escolher o caminho: poucos documentos → base64 inline; lote → Files API.
4. Rodar as perguntas do caso, uma chamada por documento.
5. Devolver a tabela: **Afirmação | Trecho literal | Página | Documento**.
6. Marcar explicitamente toda pergunta que voltou **sem** citação — ausência de
   citação significa que a afirmação não está nos autos fornecidos. Isso é
   resultado, não falha: é exatamente o que não pode ir para a peça.
7. Se subiu pela Files API, **deletar os arquivos ao final** (ver Sigilo).

## Pré-check (antes de qualquer chamada paga)

```bash
for f in *.pdf; do
  p=$(pdfinfo "$f" | awk '/^Pages:/{print $2}')
  t=$(pdftotext -l 3 "$f" - 2>/dev/null | tr -d '[:space:]' | wc -c)
  mb=$(du -m "$f" | cut -f1)
  st=$([ "$t" -lt 200 ] && echo "SEM TEXTO -> OCR" || echo ok)
  printf '%-45s %5s pag %5s MB  %s\n' "$f" "$p" "$mb" "$st"
done
```

## Script

```python
import base64, os, anthropic

client = anthropic.Anthropic(api_key=os.environ["ANTHROPIC_API_KEY"])
MODEL = "claude-opus-5"

SYSTEM = (
    "Você responde exclusivamente com base nos documentos fornecidos. "
    "Se a resposta não estiver neles, diga 'NÃO CONSTA DOS AUTOS' e pare. "
    "Nunca complete lacuna com conhecimento geral ou inferência."
)


def doc_base64(caminho, titulo, cache=False):
    """Caminho curto: poucos PDFs, inline. Teto de 32 MB por requisição."""
    with open(caminho, "rb") as f:
        bloco = {
            "type": "document",
            "source": {
                "type": "base64",
                "media_type": "application/pdf",
                "data": base64.standard_b64encode(f.read()).decode(),
            },
            "title": titulo,
            "citations": {"enabled": True},
        }
    if cache:
        bloco["cache_control"] = {"type": "ephemeral"}
    return bloco


def subir(caminho):
    """Caminho de lote: sobe uma vez, reusa o file_id em todas as perguntas."""
    with open(caminho, "rb") as f:
        up = client.files.upload(
            file=(os.path.basename(caminho), f, "application/pdf")
        )
    return up.id


def doc_file(file_id, titulo, cache=False):
    bloco = {
        "type": "document",
        "source": {"type": "file", "file_id": file_id},
        "title": titulo,
        "citations": {"enabled": True},
    }
    if cache:
        bloco["cache_control"] = {"type": "ephemeral"}
    return bloco


def ancorar(documentos, pergunta, model=MODEL):
    r = client.messages.create(
        model=model,
        max_tokens=2048,
        temperature=0,
        system=SYSTEM,
        messages=[
            {"role": "user", "content": documentos},
            {"role": "user", "content": [{"type": "text", "text": pergunta}]},
        ],
    )
    linhas = []
    for bloco in r.content:
        if bloco.type != "text":
            continue
        for c in getattr(bloco, "citations", None) or []:
            linhas.append({
                "afirmacao": bloco.text.strip(),
                "trecho": c.cited_text.strip(),
                "pagina": getattr(c, "start_page_number", None),
                "documento": c.document_title,
            })
    texto = "".join(b.text for b in r.content if b.type == "text")
    return texto, linhas


def matriz(caminhos, perguntas):
    """Lote: um documento por chamada. Nunca junta N acórdãos na mesma
    requisição — é assim que se estoura 32 MB / 600 páginas."""
    ids, out, sem_citacao = {}, [], []
    try:
        for caminho in caminhos:
            ids[caminho] = subir(caminho)
        for caminho, fid in ids.items():
            titulo = os.path.basename(caminho)
            doc = [doc_file(fid, titulo, cache=len(perguntas) > 1)]
            for pergunta in perguntas:
                texto, linhas = ancorar(doc, pergunta)
                if linhas:
                    out.extend({**l, "pergunta": pergunta} for l in linhas)
                else:
                    sem_citacao.append((titulo, pergunta, texto.strip()[:160]))
    finally:
        for fid in ids.values():   # sigilo: não deixa autos parados na Files API
            try:
                client.files.delete(fid)
            except Exception as e:
                print(f"FALHA AO DELETAR {fid}: {e}")
    return out, sem_citacao


# uso — documento único
docs = [doc_base64("autos.pdf", "Autos 0000000-00.0000.0.00.0000")]
texto, citacoes = ancorar(docs, "Em que data foi feita a citação do réu?")
print(texto)
for c in citacoes:
    print(f"p. {c['pagina']} — {c['trecho'][:120]}")

# uso — lote
linhas, faltou = matriz(
    ["ac_01.pdf", "ac_02.pdf", "ac_03.pdf"],
    ["Qual a tese firmada e em que parágrafo?"],
)
for l in linhas:
    print(f"{l['documento']} p. {l['pagina']} — {l['trecho'][:120]}")
for t, q, r in faltou:
    print(f"SEM CITAÇÃO | {t} | {q} | {r}")
```

## Perguntas padrão para abertura de caso

Rodar em lote e montar a matriz antes de qualquer redação:

- Qual a data da citação/intimação e por qual meio?
- Qual o pedido exato da inicial, na literalidade?
- Que valor foi atribuído à causa?
- Que documentos a parte contrária juntou e em que páginas?
- Que prazos e datas de audiência constam?
- Houve decisão interlocutória? Qual o dispositivo, literal?
- Que alegação de fato a parte contrária faz sem prova documental correspondente?

## Sigilo

Conteúdo de autos é Classe A: só vai para provedor com contrato conhecido e sem
treinamento. A chave fica em variável de ambiente, nunca no arquivo. Repositório
privado. Não subir PDF de autos para notebook público (Colab compartilhado).

Específico da Files API: o arquivo fica **visível para todo o workspace** e
persiste até ser deletado ou expirar. Autos em segredo de justiça não ficam
parados lá — deletar no `finally`, como no script, e conferir o log de falha de
deleção antes de encerrar.

## Custo

Citações aumentam levemente os tokens de entrada (chunking e adição ao system
prompt) e reduzem os de saída, porque o modelo não precisa transcrever o trecho
citado. Para várias perguntas sobre o mesmo documento, ligar prompt caching no
bloco do documento — é o ganho verificado. Batch API não consta como compatível
com citations na documentação: testar em um documento antes de contar com ela.