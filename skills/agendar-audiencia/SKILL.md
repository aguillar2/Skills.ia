---
name: agendar-audiencia
description: Cadastra audiências e eventos jurídicos no Google Calendar do advogado seguindo padrão fixo de título, descrição, convidados, cor, fuso e lembretes. ACIONE SEMPRE que a mensagem do usuário COMEÇAR com "AGENDAR" (em qualquer capitalização — "agendar", "Agendar", "AGENDAR"), ou quando ele encaminhar/colar intimação ou convite de audiência (PJe, DJEN, correspondente, WhatsApp, e-mail do juízo) e pedir para registrar, ou quando disser "agenda essa audiência", "registra essa audiência", "cadastra audiência", "marca audiência", "põe no calendar/agenda" ou variações similares. Dispare também ao detectar convite de Conciliação, Instrução, AIJ, Custódia, Pronúncia, Júri, IDPJ ou UNA que precise virar evento de calendário. Não pular esta skill — o cadastro fora deste padrão gera evento genérico inútil (sem número de processo, sem link, sem convidados), exatamente o problema que esta skill existe para resolver.
---

# AGENDAR — Cadastro padronizado de audiências no Google Calendar

Esta skill define o protocolo único para registrar audiências (e correlatos) no Google Calendar do advogado. Aplique sempre que disparada — não improvise variações do padrão.

## 1. Quando esta skill aciona

- Mensagem do usuário **começa com "AGENDAR"** (case-insensitive) — gatilho explícito.
- Usuário encaminha/cola **intimação, convite, ata ou mensagem** mencionando audiência designada e pede registro.
- Usuário escreve: "agenda", "registra", "cadastra", "marca", "põe no calendar", "agendar audiência", etc., referindo-se a evento processual.

## 2. Fluxo obrigatório

### Passo 1 — Extrair o que já veio na fonte

Antes de perguntar qualquer coisa, **extraia** da mensagem/anexo:

- Número do processo no padrão CNJ (`NNNNNNN-NN.NNNN.J.TR.OOOO`)
- Tipo da audiência
- Data e horário (atenção ao fuso — converter para horário de Brasília)
- Cliente (nome + CPF se houver)
- Adverso/parte contrária
- Juízo, vara, comarca, tribunal
- Link da reunião (Webex, Meet, Teams) — incluindo sala/ID
- Modalidade, se já estiver clara (presencial/virtual)
- Preposto ou correspondente, se mencionado

Liste mentalmente o que falta. Só pergunte o que **realmente** não está disponível — isso vale para os dados do Passo 3; as duas perguntas do Passo 2 são feitas sempre.

### Passo 2 — Perguntar SEMPRE (com a pergunta de múltipla escolha com botões da interface)

Mesmo que pareça óbvio pelo contexto, pergunte:

**Pergunta A — Modalidade** (sempre):
- Presencial
- Virtual
- Precisa pedir (audiência virtual ainda não deferida nos autos)

**Pergunta B — Link adicional?** (sempre, mesmo que já haja o link principal):
- Sim — tem outro link (ex.: link da defesa, do MP, da parte adversa)
- Não — só o link principal já capturado

### Passo 3 — Perguntar SE FALTAR

- **E-mail do cliente**: pergunte por texto livre se não veio na intimação. Se o usuário disser "não tenho" / "depois eu adiciono", siga sem o cliente.
- **Número do processo CNJ completo**: se a fonte só trouxe parcial ou abreviado, pergunte.
- **Cliente x Adverso**: se ambíguo, pergunte.
- **Link principal**: se a fonte não trouxer e a modalidade for "virtual", pergunte.

Regra geral: **não invente dados**. Se faltar algo essencial e o usuário não souber, escreva `[a confirmar]` no campo e siga.

### Passo 4 — Criar o evento

Use `Google Calendar:create_event` com os parâmetros abaixo.

## 3. Padrão do evento

### 3.1. Título

```
[Nº CNJ] – AUD [TIPO] – [CLIENTE] x [ADVERSO]
```

- **Nº CNJ**: completo, com pontuação padrão (`5001977-64.2025.8.13.0377`).
- **Separador**: travessão " – " (en-dash com espaços).
- **TIPO**: usar abreviação fixa:
  - `CONC` — Conciliação
  - `INST` — Instrução
  - `AIJ` — Audiência de Instrução e Julgamento
  - `UNA` — Una (conciliação + instrução em ato único)
  - `CUS` — Custódia
  - `PRON` — Pronúncia
  - `JÚRI` — Tribunal do Júri
  - `IDPJ` — Incidente de Desconsideração
  - `ADM` — Administrativa/despacho
- **CLIENTE** e **ADVERSO**: identificação curta (primeiro nome, sobrenome marcante, ou apelido reconhecível). Empresa: nome de fantasia (ex.: `Bradesco`, `AMIL`, `Nubank`).

**Exemplo**: `1010687-73.2024.8.11.0041 – AUD CONC – Edson x Bradesco`

### 3.2. Descrição (campos fixos, nesta ordem)

```
Processo: [número CNJ completo]
Tipo: [conciliação / instrução / AIJ / una / custódia / pronúncia / júri / IDPJ / administrativa]
Modalidade: [presencial / virtual / a pedir]
Juízo: [vara + comarca + tribunal]
Cliente: [nome completo + CPF se houver]
Adverso: [nome]
Link principal: [URL completa]
Link adicional: [URL ou "não há"]
Sala/ID: [se aplicável]
Preposto/correspondente: [nome + telefone, se aplicável]
Documentos: [link Drive da pasta do processo, se aplicável]
Observações: [orientações específicas, vestimenta, documentos a levar, etc.]
```

Campos sem informação ficam com `[a confirmar]` — nunca em branco.

### 3.3. Convidados (`attendeeEmails`)

Sempre incluir, nesta ordem:

1. E-mail do cliente (se disponível)
2. `{{EMAIL_PRINCIPAL}}`
3. `{{EMAIL_AUDIENCIAS}}`

Se cliente não tiver e-mail, incluir apenas os dois últimos.

### 3.4. Outros parâmetros

- **`timeZone`**: `"America/Sao_Paulo"` — sempre. Nunca `"America/Bogota"` (erro recorrente).
- **`startTime` / `endTime`**: ISO 8601 com offset `-03:00`. Converter horário de MT (UTC-4) ou outros para horário de Brasília quando necessário.
- **Duração default por tipo** (quando a fonte só trouxer o horário inicial):
  - CONC: 1h
  - INST / AIJ / UNA: 2h
  - CUS: 1h
  - PRON: 2h
  - JÚRI: dia inteiro (evento all-day — ver Casos especiais; não usar bloco de 8h)
  - IDPJ / ADM: 1h
- **`colorId`** (paleta Calendar):
  - `"11"` (Tomato/vermelho) — INST, AIJ, UNA, CUS, PRON, JÚRI (audiências críticas)
  - `"6"` (Tangerine/laranja) — CONC
  - `"5"` (Banana/amarelo) — ADM, despachos virtuais
  - `"8"` (Graphite/cinza) — quando modalidade for "a pedir" (ainda incerto)
- **`overrideReminders`** (sempre os três):
  ```json
  [
    {"method": "popup", "minutes": 1440},
    {"method": "popup", "minutes": 60},
    {"method": "email", "minutes": 30}
  ]
  ```
  (1 dia antes, 1 hora antes, 30 min antes por e-mail)
- **`location`**: se presencial → endereço do fórum/vara; se virtual → deixar em branco (link vai na descrição).

## 4. Após criar

Confirme ao usuário em mensagem curta:

```
✅ Audiência cadastrada
[título do evento]
[data e hora BRT]
Convidados: [cliente, se houver] + {{EMAIL_PRINCIPAL}} + {{EMAIL_AUDIENCIAS}}
Lembretes: 1 dia / 1 hora / 30 min (e-mail)
Link Calendar: [htmlLink retornado pelo create_event]
```

Se o usuário tiver pedido "AGENDAR" no início mas o evento ficou com campos `[a confirmar]`, lembre brevemente o que ele precisa completar depois.

## 5. Casos especiais

- **Audiência presencial fora de {{CIDADE_BASE}}**: incluir no campo `location` o endereço completo e no campo "Observações" da descrição uma estimativa de deslocamento (ex.: "≈ 3h de viagem — sair 8h").
- **Audiência de custódia**: lembrete adicional de 2h antes (popup) — pessoa autuada em flagrante, prazo de 24h da prisão.
- **Júri**: criar evento de dia inteiro (`allDay: true`, start e end à meia-noite UTC do dia). Não usar duração de 8h em horário específico.
- **Audiência "a pedir"**: criar o evento na data presumida (ou um placeholder no final do dia), modalidade "a pedir", cor cinza (`"8"`), e na descrição em "Observações" anotar "Pedir audiência virtual em petição — verificar deferimento antes da data".
- **Múltiplas audiências no mesmo dia**: criar um evento por audiência, nunca consolidar.

## 6. O que NUNCA fazer

- Criar evento sem número CNJ no título — se não tiver, peça.
- Usar fuso `America/Bogota` (erro recorrente já detectado em eventos anteriores).
- Pular as perguntas de modalidade e link adicional, mesmo que pareçam óbvias.
- Inventar dados que não vieram da fonte.
- Misturar instrução e conciliação no mesmo evento.
- Esquecer de incluir `{{EMAIL_PRINCIPAL}}` e `{{EMAIL_AUDIENCIAS}}` como convidados.

## 7. Exemplo completo

**Entrada do usuário:**
> AGENDAR essa audiência: "Convite: Audiência conciliação - 1010687-73.2024.8.11.0041 - sex. 25 out. 2024 11:30 - 12:30 (BRT). Link Teams: https://teams.microsoft.com/l/meetup-join/xxx. Cliente: Edson Silva (CPF 123.456.789-00). Vara: 7º JEC Cuiabá."

**Passo 1 — Extração**: tudo presente exceto e-mail do cliente, modalidade explícita e link adicional.

**Passo 2 — Perguntas**:
- Modalidade (botões: Presencial / Virtual / Precisa pedir)
- Link adicional? (botões: Sim / Não)

**Passo 3 — Pergunta de texto**: "Qual o e-mail do Edson?"

**Passo 4 — Criar evento**:
- Título: `1010687-73.2024.8.11.0041 – AUD CONC – Edson x [adverso da intimação]`
- Início: `2024-10-25T11:30:00-03:00`, Fim: `2024-10-25T12:30:00-03:00`
- Fuso: `America/Sao_Paulo`
- Cor: `"6"`
- Convidados: e-mail do Edson + `{{EMAIL_PRINCIPAL}}` + `{{EMAIL_AUDIENCIAS}}`
- Descrição preenchida com todos os campos
- Lembretes padrão

## 8. Ferramentas necessárias

- `Google Calendar:create_event` — para criar
- `Google Calendar:list_events` — para verificar conflito de horário (opcional, mas recomendado se houver dúvida)
- `Google Calendar:update_event` — se o usuário pedir para corrigir evento já criado
- Pergunta de múltipla escolha da interface — para as perguntas com botões (modalidade, link adicional)

Se as ferramentas do Google Calendar não estiverem carregadas, carregá-las primeiro pela busca de ferramentas da sessão.
