---
name: "treino-audiencia"
description: "Treino ativo com quiz progressivo em 5 níveis e técnica de Feynman, só com base nos autos: preparar audiência ou estudar área nova. Dispare em TREINO, me sabatina, me testa no caso, simula a audiência, quero aprender [área]."
---

# Treino — audiência e área nova

Treino ativo para o advogado. Não redige peça. Não ensina por exposição longa: pergunta, espera a resposta, corrige com âncora.

Dois modos:

- **A — AUDIÊNCIA**: há CNJ ou digest/PDF dos autos. Objetivo: chegar à audiência sabendo fatos, prova, tese, ataque adverso e limites de acordo.
- **B — ÁREA NOVA**: há um tema de direito a dominar (em geral por causa de um caso concreto), ou um curso completo de uma matéria.

Se a mensagem não deixar claro o modo, perguntar uma vez: "audiência ou área nova?".

Atalho: **TREINO RÁPIDO** (audiência em menos de 1 h) = Feynman de 60 s + 5 perguntas (níveis 1, 2, 4, 5, 5) + ficha de bolso.

## Regras que valem nos dois modos

1. **Fonte única.** Modo A: só autos, digest do NotebookLM e o que o usuário disser. Modo B: lei seca aberta na sessão ou fornecida por ele. Fato, data, valor, ID ou artigo que não puder ser ancorado vira "SEM ÂNCORA" e não entra em pergunta nem em gabarito.
2. **Sem jurisprudência**, com duas exceções no modo B:
   - **Trabalhista: súmulas, OJs, instruções normativas e atos do TST entram sempre**, conferidos no texto oficial (ver "Conferência das fontes").
   - Outras áreas: só se ele pedir, e aí pela skill `pesquisa-jurisprudencia` (nada sem URL aberta na sessão).
3. **Uma pergunta por vez.** Nunca mostrar gabarito antes da resposta. Aceitar resposta curta, por áudio transcrito ou com erro de digitação.
4. **Nota honesta, 0 a 10.** Sem inflar. Abaixo de 7: nova pergunta do mesmo nível antes de subir. Dois erros seguidos no mesmo ponto: explicar em até 5 linhas e voltar a perguntar.
5. **Gabarito sempre com âncora**: ID/página dos autos (modo A) ou artigo + diploma / súmula + resolução (modo B).
6. **Sigilo.** Conteúdo dos autos não sai desta sessão para serviço de terceiro.
7. Sem introdução, sem elogio, sem explicar conceito básico.

## Conferência das fontes (modo B)

- **Lei seca no Planalto**: a página mantém redações antigas ao lado da vigente, e nem toda redação antiga vem tachada. Para cada parágrafo ou inciso usado, verificar se há duas versões; vale a que traz a alteração mais recente ("Redação dada pela Lei nº …"). Caso já ocorrido: CPC art. 1.003 §6º, alterado pela Lei 14.939/2024.
- **TST**: www.tst.jus.br bloqueia leitura automatizada (403/robots) — não insistir nem contornar. Conferir súmulas e OJs nas resoluções do Tribunal Pleno em juslaboris.tst.jus.br (Biblioteca Digital do TST): achar a resolução que deu a redação atual e checar se resolução posterior alterou ou cancelou. Valores de depósito recursal: Ato SEGJUD.GP do ano (vigência em 1º de agosto).
- Item que não fechar a conferência literal entra marcado (ex.: "texto original oficial; redação consolidada pendente") ou fica de fora.

## Modo A — AUDIÊNCIA

### 0. Entrada

- CNJ → validar o dígito verificador (módulo 97) antes de usar. Ler o arquivo de memória do caso em /areas/ se existir (aliases trazem o CNJ).
- Pedir o que faltar, de uma vez: tipo de audiência (conciliação, AIJ, UNA, custódia, júri, outra), data, polo que ele defende, digest ou PDF dos autos.
- Sem autos nem digest: parar e pedir. Não montar treino com fatos da memória ou de suposição.
- PDF dos autos disponível → usar a skill `ancorar-citacoes-autos` para gerar a matriz.

### 1. Matriz do caso (base de todas as perguntas)

Tabela interna, mostrada a ele só no fim ou se pedir:

| Fato | Fonte (ID/pág.) | Quem prova | Ponto fraco |

Incluir: datas e valores, pedidos, testemunhas arroladas, contradições entre peças/depoimentos, documentos que faltam.

### 2. Feynman de abertura

Pedir: "Explique o caso em até 90 segundos como se o juiz não tivesse lido os autos."

Corrigir apontando só quatro coisas: fato relevante omitido; fato afirmado sem âncora; data/valor/nome errado; termo vago onde cabia dado concreto. Ele refaz até passar (nota ≥ 7). No TREINO RÁPIDO, 60 s e uma única rodada de correção.

### 3. Escada de 5 níveis (2 perguntas por nível)

- **N1 — Fatos**: quem, quando, quanto, onde.
- **N2 — Prova**: qual documento/ID prova cada fato; o que não está provado e como suprir na audiência.
- **N3 — Tese**: fundamento legal (lei seca) e pedido exato; o que precisa sair da audiência para a tese fechar.
- **N4 — Ataque**: perguntas que o juiz, o MP ou o advogado adverso farão; contradições dos autos que serão exploradas contra ele.
- **N5 — Improviso**: cenário surpresa com resposta em 30 s (testemunha muda a versão, juiz propõe acordo com valor X, documento novo juntado na hora, parte não comparece).

Ajuste por tipo de audiência:

- **Conciliação**: N3 e N5 focam em piso, teto e alternativa ao acordo; valores só os que estiverem nos autos ou que ele informar.
- **AIJ / UNA**: N2 e N4 viram roteiro de perguntas às próprias testemunhas e de contradita às da parte adversa.
- **Criminal / júri**: N4 confronta versão do inquérito x versão em juízo; N5 inclui pedido de palavra pela ordem e consignação em ata.
- **Custódia**: N1–N3 em requisitos da prisão e condições pessoais documentadas.

### 4. Ficha de bolso (1 página, sem timbrado)

- Tese em 3 linhas.
- 5 fatos-chave com ID/página.
- Perguntas por testemunha (próprias e adversas).
- 3 pontos fracos + resposta pronta.
- Limites de acordo (se houver, informados por ele).
- Lista "REVISAR": tudo em que tirou nota < 7.

Se a audiência não estiver no Google Calendar, oferecer cadastro pela skill `agendar-audiencia`.

Fechar o loop do CNJ ao final: perguntar o que aconteceu com ele — resolvi / protocolei / agendei / desisti / ainda não.

## Modo B — ÁREA NOVA

### 0. Entrada

Tema + o caso concreto que motivou (se houver) + prazo até precisar dominar. Pedido de matéria "completa" vira currículo em módulos (um por sessão), com mapa de módulos e dispositivos conferidos.

### 1. Mapa 80/20

8 a 12 conceitos que resolvem a maior parte dos casos práticos daquele tema no escritório. Cada um com o dispositivo legal (só se conferido na sessão; senão "conferir"). No trabalhista, com as súmulas e OJs do tema.

### 2. Feynman por conceito

Claude explica em até 5 linhas → o usuário explica de volta com as próprias palavras → Claude aponta lacuna ou erro → repete até nota ≥ 7. Um conceito por vez.

### 3. Escada de 5 níveis

2 perguntas por nível; 3 por nível quando o módulo tiver muitas súmulas/OJs.

- **N1** — conceito e requisitos.
- **N2** — prazo, competência e procedimento.
- **N3** — aplicação ao caso concreto dele (fatos do caso só se ancorados).
- **N4** — o que a parte contrária alegaria e como rebater.
- **N5** — pegadinha: exceção, conflito de normas ou ponto de divergência.

### 4. Ficha de 1 página

Mapa dos conceitos + dispositivos + checklist de requisitos + lista REVISAR. No trabalhista, tabela de súmulas/OJs com a resolução que deu a redação vigente e o link do PDF oficial.

## Saída

- Treino para ele mesmo: acontece no chat; a ficha vai no chat; arquivo só se ele pedir.
- Treino para outra pessoa (ex.: assistente): publicar como página (Artifact) com mapa de módulos, Feynman com autoconferência, quiz com gabarito travado até a resposta, lista REVISAR e ficha. Sem nenhum dado de cliente. Lembrar que a página só abre para quem ele compartilhar pelo menu Share.