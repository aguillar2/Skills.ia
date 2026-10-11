---
name: "conferir-cnj"
description: "Valida o dígito verificador CNJ (módulo 97) quando o número vai ser gravado em planilha, quando vai dar nome a arquivo ou documento de peça, ou quando o usuário pedir a conferência. Não dispara só por ler intimação."
---

# Conferir número CNJ

Número errado gravado em planilha contamina cadastro, cobrança e controle de prazo, e o
erro só aparece semanas depois. Número errado no nome do arquivo da peça vai parar no
protocolo. Esta skill confere o dígito verificador no momento da gravação e no momento de
nomear a peça.

## Escopo

Dispara em três momentos: na ESCRITA em planilha, ao DAR NOME a arquivo ou documento de
peça (.docx, PDF, título no Google Docs) e sob pedido expresso. Não é um interceptador de
todo número que passa pela conversa — esse comportamento competia com as skills jurídicas
de redação e foi removido de propósito. Conferir o número que vai no nome do arquivo é uma
chamada só, no fim, e não interrompe a redação.

## Script (autocontido — salvar como `cnj.py` ou rodar inline)

```python
import re, sys

PADRAO = re.compile(r'(\d{7})[-.]?(\d{2})\.?(\d{4})\.?(\d)\.?(\d{2})\.?(\d{4})')

def dv_correto(n, ano, j, tr, oooo):
    """Resolucao CNJ 65/2008 — modulo 97 base 10 (ISO 7064), em UMA etapa."""
    return 98 - (int(n + ano + j + tr + oooo) * 100) % 97

def conferir(texto):
    vistos, saida = set(), []
    for m in PADRAO.finditer(texto):
        n, dv, ano, j, tr, oooo = m.groups()
        chave = n + dv + ano + j + tr + oooo
        if chave in vistos:
            continue
        vistos.add(chave)
        dvc = dv_correto(n, ano, j, tr, oooo)
        saida.append({
            "numero": f"{n}-{dv}.{ano}.{j}.{tr}.{oooo}",
            "sem_mascara": chave,
            "valido": int(dv) == dvc,
            "dv_correto": f"{dvc:02d}",
            "ano": ano, "j": j, "tr": tr, "orgao": oooo,
        })
    return saida

if __name__ == "__main__":
    texto = " ".join(sys.argv[1:]) or sys.stdin.read()
    rs = conferir(texto)
    for r in rs:
        print(f'{r["numero"]}  {"OK" if r["valido"] else "X  (DV correto: " + r["dv_correto"] + ")"}')
    print(f'--- {sum(r["valido"] for r in rs)} OK / {sum(not r["valido"] for r in rs)} reprovado(s) de {len(rs)}')
```

```bash
python3 cnj.py "5030516-71.2019.8.13.0079"
cat lote.csv | python3 cnj.py        # extrai todos os numeros do texto, com ou sem mascara
```

O script aceita número com máscara, sem máscara, colado no meio de texto livre e em lote.

## Algoritmo (Resolução CNJ 65/2008)

Formato `NNNNNNN-DD.AAAA.J.TT.OOOO` — 20 dígitos.

O dígito verificador é módulo 97 base 10 (ISO 7064), calculado sobre o número **sem** o DV,
na ordem oficial `NNNNNNN AAAA J TT OOOO`:

```python
DV = 98 - ((int(NNNNNNN + AAAA + J + TT + OOOO) * 100) % 97)
```

ATENÇÃO — o erro comum: implementações que fazem o cálculo em duas etapas encadeadas
(`r1 = seq % 97`, depois `r2 = ...`) reprovam processos válidos. Verificar
qualquer implementação contra autos reais antes de confiar nela.

## Campo J — segmento de justiça

| J | Segmento | J | Segmento |
|---|---|---|---|
| 1 | STF | 6 | Justiça Eleitoral |
| 2 | CNJ | 7 | Justiça Militar da União |
| 3 | STJ | 8 | Justiça Estadual (TJs) |
| 4 | Justiça Federal (TRF 1–6) | 9 | Justiça Militar Estadual |
| 5 | Justiça do Trabalho (TRT 1–24) | | |

Para J=8, TT=13 é TJMG e TT=11 é TJMT — os dois tribunais do escritório.

## Saída

Tabela com: número formatado, válido (OK/X), tribunal, ano, órgão julgador e,
quando inválido, o DV correto para comparação.

Em lote, fechar com a contagem: quantos OK, quantos reprovados, e em que linha da planilha
cada reprovado está.

## Limites

- Confere apenas a consistência matemática do número. **Não confirma que o processo existe**
  nem que está ativo — para isso, consulta ao DataJud, PJe ou eSAJ.
- Não infere comarca a partir do código de órgão julgador: o código OOOO varia por tribunal
  e não deve ser adivinhado. Se a comarca não constar dos autos ou da intimação, perguntar.
- Não altera número de processo por conta própria. DV divergente = avisar o usuário e parar.

## Gravação em planilha

Antes de gravar linha nova com número de autos, rodar a conferência. DV reprovado:
não gravar a linha, reportar o número informado e o DV correto, e pedir confirmação nos
autos. Em importação de lote, gravar as linhas válidas e separar as reprovadas numa lista
à parte — nunca descartar em silêncio.

## Nome de arquivo e documento de peça

Antes de criar o .docx, o PDF ou o documento no Google Docs, rodar a conferência no número
que vai no nome. DV reprovado: não nomear, reportar o número informado e o DV correto.

O nome usa o número SEM a máscara — só os 20 dígitos, sem ponto e sem hífen — seguido de
`_` e o tipo da peça, sem nenhum caractere especial (o eproc rejeita: sem acento, ç, espaço,
travessão, parênteses ou símbolos):
`NNNNNNNDDAAAAJTTOOOO_tipo_da_peca.docx` (ex.: `50019776420258130377_impugnacao.docx`).
A conferência do DV vale do mesmo jeito: o script aceita o número sem máscara.