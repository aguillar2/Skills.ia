---
name: "auditoria-adversarial"
description: "Bateria fixa de 5 blocos (fato-prova, divergências, red team, premortem, varredura) para auditar os autos antes de redigir a peça. Dispare com \"auditar\", \"AUDITORIA\", \"roda a bateria\", \"red team do processo\", \"fura minha tese\", \"premortem\", \"e se a gente perder\"."
---

# Auditoria Adversarial dos Autos

## Quando usar
Depois do digest do NotebookLM e ANTES de redigir a peça. A função é achar o que derruba a tese enquanto ainda dá tempo de mudar de estratégia.
Não usar para: redigir peça, resumir processo, buscar jurisprudência.

## Pré-requisitos
1. Todas as fontes do processo carregadas no notebook do caso.
2. Digest do processo já gerado.
3. Tese central e pedidos definidos — os Blocos 3 e 4 não rodam sem isso.
Se faltar qualquer um, pare e peça ao usuário.

## Procedimento
Rodar os 5 blocos NA ORDEM, um por chamada, sempre com REGRAS DE SAÍDA colado no início. Colar as saídas brutas no relatório e salvar como `{CNJ só com os 20 dígitos}_auditoria.md` — ex.: `50019776420258130377_auditoria.md` (sem ponto, hífen, acento ou espaço).

### REGRAS DE SAÍDA (colar em todo bloco)
1. Trabalhe exclusivamente com as fontes carregadas. Sem conhecimento externo, jurisprudência, doutrina ou presunção.
2. Toda afirmação precisa de citação [documento — página/id]. Linha sem citação não entra.
3. Sem base nas fontes: escreva "NÃO CONSTA NOS AUTOS" e siga.
4. Fontes divergentes: apresente as duas e marque "DIVERGÊNCIA".
5. Não redija peça, não sugira tese, não avalie mérito.
6. Não repita conteúdo de blocos anteriores.
7. Tabela quando o bloco pedir tabela. Sem introdução e sem conclusão.

### BLOCO 1 — MATRIZ FATO -> PROVA
Relacione cada fato relevante alegado à respectiva prova. Tabela: `| # | Fato alegado | Quem alega | Prova indicada | Localização | Status | Efeito se cair |`. Status: PROVADO / INSUFICIENTE / SEM PROVA. Ao final, liste os SEM PROVA por impacto decrescente.

### BLOCO 2 — DIVERGÊNCIAS, OMISSÕES E ELO MAIS FRACO
2.1 Contradições e mudança de versão: o que foi dito antes [doc — pág.], o que foi dito depois [doc — pág.], em que ponto são incompatíveis.
2.2 Argumentos e documentos não enfrentados: onde foi levantado, onde deveria ter sido respondido, o que ficou sem resposta.
2.3 Elo mais fraco: um único fato essencial que depende da prova mais frágil, e o que rui junto.

### BLOCO 3 — RED TEAM DA TESE
Informar polo, tese central e pedidos. Assuma a perspectiva do advogado contrário e dê os três melhores ataques. Para cada: ATAQUE, MUNIÇÃO [doc — pág.], FORÇA (ALTA/MÉDIA/BAIXA), RESPOSTA POSSÍVEL nos autos — ou "SEM RESPOSTA NOS AUTOS". Ordenar por força.

### BLOCO 4 — PREMORTEM
Informar polo, tese central e pedidos (os mesmos do Bloco 3). Premissa fixa: o processo terminou e o cliente perdeu — pedidos indeferidos ou tese contrária acolhida. Não discuta se isso vai acontecer; explique por que aconteceu, sempre a partir das fontes.

4.1 AUTÓPSIA — até 7 causas da derrota, em ordem de probabilidade. Para cada uma:
- CAUSA: o que levou à derrota, em uma linha.
- ONDE NASCE: o fato, a prova ou a omissão dos autos de onde a causa parte [doc — pág.].
- PREMISSA DO NOSSO LADO: o que a tese assume como certo e os autos não garantem.
- PRIMEIRO SINAL: ato ou documento já existente nos autos que antecipava essa causa [doc — pág.], ou "NÃO CONSTA NOS AUTOS".
Proibida causa genérica ("o juiz não se convenceu", "prova insuficiente") sem dizer qual fato e qual página. Causa que coincida com ataque do Bloco 3: escrever "ver Bloco 3, ataque n" e passar à próxima.

4.2 VEREDITO — entre as causas da 4.1, indicar a MAIS PROVÁVEL e a MAIS PERIGOSA (a que derruba mais pedidos de uma vez) e dizer por que são diferentes, ou que coincidem. Em seguida, a MAIOR PREMISSA ESCONDIDA: o que a tese assume sem prova nos autos [doc — pág. onde a premissa aparece]. Se alguma causa não tiver resposta possível nos autos, escrever "FALHA FATAL:" e descrevê-la em uma linha.

4.3 ALARME — tabela `| Causa | Sinal observável no processo | Onde vai aparecer | Quando checar |`.
- Sinal: ato ou documento verificável (manifestação da parte contrária, decisão, laudo, depoimento, certidão, resposta de ofício).
- Quando checar: ato ou fase já designado ou previsto nos autos [doc — pág.] — audiência marcada, prazo aberto, perícia deferida. Sem data nos autos, indicar a fase ("após a contestação", "na decisão saneadora") sem inventar data.

### BLOCO 5 — VARREDURA FINAL
Pontos relevantes ausentes dos blocos 1-4. Trava: só entra com documento e página citados. Proibido ponto "potencialmente relevante" sem localização e especulação sobre o que não está nos autos. Se nada satisfizer a trava, responder exatamente "NADA NOVO A APONTAR."

## Verificação (obrigatória)
- Sortear 3 citações do relatório e abrir os documentos. Se alguma não conferir, descartar o bloco inteiro e rodar de novo.
- Conferir se todo SEM PROVA realmente não tem prova, e não é prova mal indexada no notebook.
- Bloco 4: conferir se cada causa aponta para página real e se a FALHA FATAL, quando houver, não tem mesmo resposta nos autos. Causa genérica ou sem página é descartada.
- Achado sem citação verificável não entra em peça. Sem exceção.

## Depois da verificação
Cada linha do ALARME (4.3) com ato datado nos autos vira pendência: propor lembrete no Google Calendar com o CNJ no título, na data do ato.

## Regra de ouro
A bateria produz hipóteses, não fatos. Nada sai daqui direto para a peça sem passar pela verificação.