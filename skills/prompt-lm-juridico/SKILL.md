---
name: prompt-lm-juridico
description: "Gera conjuntos de prompts jurídicos personalizados e otimizados para uso no NotebookLM (LM), por meio de entrevista sequencial com o usuário sobre o caso. Use esta skill SEMPRE que o usuário mencionar 'LM', 'NotebookLM', 'digest', 'prompt para o LM', 'mandar pro LM', ou qualquer variação dessas expressões no contexto jurídico. Também disparar quando o usuário disser que vai analisar um processo no NotebookLM ou perguntar o que pedir ao LM sobre um caso."
---

# Skill: Gerador de Prompts Jurídicos para NotebookLM

## Objetivo

Gerar um **prompt principal otimizado** para o NotebookLM, partindo do resumo automático que o LM produz no upload e da identificação de quem o advogado defende. Em seguida, complementar com prompts de apoio personalizados à área e fase.

---

## REGRAS FUNDAMENTAIS DE FLUXO

1. **Execute UMA etapa por vez.** Após cada etapa, PARE e aguarde a resposta do usuário antes de continuar.
2. **Etapa 0** é solicitação de texto livre — peça em prosa e aguarde a resposta.
3. **Etapas 1, 2 e 3** usam **obrigatoriamente** a pergunta de múltipla escolha da interface (opções clicáveis). Nunca substitua por texto puro.
4. **Só gere os prompts após concluir todas as etapas.**

---

## Etapa 0 — Resumo Automático do LM

Solicite em texto corrido (sem opções clicáveis):

> "Cole aqui o resumo automático que o NotebookLM gerou quando você fez o upload do processo. É aquele pequeno texto que aparece logo abaixo do título do notebook. Se não tiver, escreva 'sem resumo' e continuamos."

⚠️ **PARE aqui. Aguarde a resposta antes de continuar.**

Se o usuário não tiver o resumo, registre internamente "resumo ausente" e prossiga para a Etapa 1.

---

## Etapa 1 — Quem você defende?

**Após receber o resumo**, apresente a pergunta de múltipla escolha (seleção única):

**Pergunta:** "Quem você defende neste processo?"
**Opções:**
- Réu / Acusado (criminal)
- Autor / Requerente (cível ou família)
- Réu / Requerido (cível ou família)
- Executado
- Exequente
- Outro — vou digitar

⚠️ **PARE aqui. Aguarde a seleção antes de continuar.**

---

## Etapa 2 — Área do Direito

**Após receber a resposta da Etapa 1**, apresente a pergunta de múltipla escolha (seleção única):

**Pergunta:** "Qual é a área do direito deste caso?"
**Opções:**
- Criminal
- Família
- Cível
- Consumidor
- Execução Civil
- Outro — vou digitar

⚠️ **PARE aqui. Aguarde a seleção antes de continuar.**

---

## Etapa 3 — Fase e Pontos Estratégicos

**Após receber a resposta da Etapa 2**, apresente as perguntas conforme a área selecionada. São **duas perguntas** nesta etapa — faça-as juntas numa única rodada de perguntas de múltipla escolha, com dois blocos.

### Se CRIMINAL:

**P3a** — `single_select`
> "Qual é a fase atual?"
- Inquérito / Pré-processual
- Instrução processual
- Alegações finais
- Recurso (apelação, RESE, agravo)
- Execução penal

**P3b** — `multi_select`
> "Quais são os principais pontos de defesa? (pode marcar mais de um)"
- Negativa de autoria
- Fragilidade ou ausência de provas
- Nulidade processual
- Excludente de ilicitude
- Excludente de culpabilidade
- Bis in idem / absorção
- Outro — vou digitar

### Se FAMÍLIA:

**P3a** — `single_select`
> "Qual é o objeto da ação?"
- Alimentos (fixação ou revisão)
- Execução de alimentos
- Guarda / Visitas
- Divórcio
- Paternidade
- Outro — vou digitar

**P3b** — `multi_select`
> "Qual é a maior controvérsia? (pode marcar mais de um)"
- Valor dos alimentos
- Capacidade financeira do alimentante
- Necessidade do alimentando
- Alienação parental
- Disputa de guarda
- Paradeiro do executado
- Outro — vou digitar

### Se CÍVEL / CONSUMIDOR / EXECUÇÃO:

**P3a** — `single_select`
> "Qual é a natureza da ação?"
- Cobrança / Cumprimento de sentença
- Impugnação ao cumprimento
- Exceção de pré-executividade
- Indenizatória (danos morais / materiais)
- Revisional de contrato
- Mandado de segurança
- Outro — vou digitar

**P3b** — `multi_select`
> "O que precisa ser extraído com prioridade? (pode marcar mais de um)"
- Histórico processual completo
- Documentos juntados pelas partes
- Decisões e despachos relevantes
- Cálculos e valores cobrados
- Provas produzidas
- Prazos e intimações pendentes
- Outro — vou digitar

⚠️ **PARE aqui. Aguarde as seleções antes de continuar.**

---

## Etapa 4 — Geração dos Prompts

**Somente após receber todas as respostas das Etapas 0–3**, gere os prompts abaixo. O **Prompt Principal** é o mais importante — deve ser denso, direcional e já incorporar o resumo do LM e o lado processual defendido.

---

### 📌 PROMPT PRINCIPAL — ANÁLISE DIRECIONADA À DEFESA

Personalize integralmente com:
- O conteúdo do resumo automático do LM (se fornecido)
- O lado processual que o advogado defende
- A área, fase e pontos estratégicos coletados

**Estrutura:**

```
Você está analisando os autos do processo descrito a seguir.

[SE O RESUMO FOI FORNECIDO]
Contexto inicial (resumo automático do notebook): "[INSERIR RESUMO DO LM AQUI]"

Minha posição neste processo: Represento [PARTE DEFENDIDA].

Com base em todos os documentos carregados neste notebook, responda:

1. Quais são os fatos centrais do processo, narrados de forma cronológica?
2. Quais provas existem nos autos e o que cada uma demonstra objetivamente?
3. Quais elementos dos autos favorecem [PARTE DEFENDIDA]? Seja específico — cite páginas ou documentos.
4. Quais são os pontos fracos ou lacunas probatórias exploráveis em favor de [PARTE DEFENDIDA]?
5. [LINHA ADICIONAL CONFORME ÁREA/FASE]

Seja factual. Não invente informações. Se algo não estiver nos documentos, diga que não está nos autos.
```

Exemplos da linha 5 por fase:
- **Alegações finais criminal:** "Há vício processual, nulidade ou cerceamento de defesa identificável nos autos?"
- **Execução de alimentos:** "Há prova de pagamento parcial ou total? O cálculo do débito está documentado e correto?"
- **Impugnação cível:** "Há excesso de execução, prescrição ou nulidade formal identificável nos documentos?"

---

### 🔍 PROMPT 2 — PROVAS E CONTRADIÇÕES

> Liste todas as provas produzidas neste processo (documentais, testemunhais, periciais). Para cada prova, indique: o que ela demonstra, quem a produziu e se há contradição com outras provas ou depoimentos. Cite a página ou documento de origem de cada item.

---

### ⚖️ PROMPT 3 — ANÁLISE JURÍDICA

*(Personalizado conforme área, fase e pontos estratégicos coletados)*

---

### 📅 PROMPT 4 — PRAZOS E PROVIDÊNCIAS PENDENTES

*(Incluir apenas quando houver fase processual crítica)*

> Liste todos os prazos identificados nos autos, intimações pendentes e providências que ainda precisam ser adotadas. Para cada item, indique a data, o prazo em dias e a página ou documento onde aparece.

---

## Regras de Geração

1. **O Prompt Principal vem primeiro e é o mais extenso** — é ele que orienta o LM de ponta a ponta.
2. **Incorpore o resumo do LM no Prompt Principal** — se o usuário colou o resumo, ele entra literalmente entre aspas no campo "Contexto inicial".
3. **Nunca use jurisprudência nos prompts** — foco exclusivo em extração factual dos autos.
4. **Cada prompt deve ser autoexplicativo** — o LM não tem contexto da conversa; cada um funciona de forma isolada.
5. **Separe os prompts visualmente** com linhas horizontais para facilitar o copy-paste.
6. respeite o limite de caracteres do LM Perguntas e Prompts: O campo de chat/consulta no painel principal limita-se a cerca de 2.000 caracteres ou 300 palavras por vez.
7. **Ao final**, pergunte com opções clicáveis: *"Deseja ajustar algum dos prompts?"* com opções: "Sim — vou indicar qual" / "Não, está ótimo".