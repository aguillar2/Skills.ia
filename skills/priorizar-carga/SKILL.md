---
name: priorizar-carga
description: "Biblioteca de 20 frameworks de gestão aplicados ao escritório — ordenar fila, achar causa raiz, decidir entre opções, tirar iniciativa do papel e definir quem faz. Dispare em \"prioriza\", \"por onde começo\", \"o que faço primeiro\", \"minha semana\", \"por que isso sempre dá errado\", \"vale a pena\", \"tira do papel\", \"quem faz o quê\", ou ao colar lista de prazos, processos, problemas ou opções sem ordem."
---

# Frameworks de gestão para o escritório

Vinte frameworks de domínio público em formato operacional. A skill não conversa sobre método: escolhe um, aplica e devolve decisão.

## Roteador — escolher o framework pela entrada

| O que o usuário traz | Framework | Família |
|---|---|---|
| Tarefas e prazos da semana | Eisenhower | Ordenar |
| Problemas em aberto sem prazo fatal | GUT | Ordenar |
| Muita reclamação, muito atraso, muita perda | Pareto | Ordenar |
| Várias iniciativas, tempo para uma | ICE | Ordenar |
| Carteira de clientes ou processos por retorno | Curva ABC | Ordenar |
| Erro que se repete | 5 Porquês | Entender |
| Falha com muitas causas possíveis | Ishikawa | Entender |
| Processo que ninguém sabe descrever | SIPOC | Entender |
| "Não sei nem que tipo de problema é esse" | Cynefin | Entender |
| Escolher entre opções com critérios | Matriz de decisão | Decidir |
| Antes de aceitar caso ou projeto grande | Pré-mortem | Decidir |
| Risco processual ou operacional a mapear | Matriz de risco | Decidir |
| "Faço depois" — mas depois custa | Custo de atraso | Decidir |
| Iniciativa parada há semanas | 5W2H | Executar |
| Trimestre sem foco | OKR | Executar |
| Rotina que precisa melhorar em ciclo | PDCA | Executar |
| Escopo estourando | MoSCoW | Executar |
| Muita coisa começada, nada terminado | Limite de WIP | Executar |
| Mais de uma pessoa envolvida | RACI | Delegar |
| "Ele pode decidir isso sozinho?" | Níveis de delegação | Delegar |

Entrada ambígua: escolher e dizer em uma linha por quê. Não perguntar. Entrada que pede dois (diagnóstico + plano): aplicar em sequência, na ordem Entender → Decidir → Executar.

## Regra dura de prazo processual

Prazo processual **não entra em nenhum framework de priorização**. Prazo fatal com data marcada sai da fila e vai para o topo, em bloco separado "PRAZO — não negociável", ordenado por data. Os frameworks só ordenam o que sobra.

Prazo informado sem data: perguntar a data de intimação ou publicação antes de ordenar. Nunca estimar prazo processual, nunca contar prazo de cabeça sem confirmar o marco inicial.

## Ordenar a fila

**Eisenhower** — semana com tudo misturado.
Saída: quatro quadrantes. FAZER (urgente + importante, com tempo estimado); AGENDAR (importante, não urgente, com data sugerida e lembrete no Google Calendar, conforme a rotina de pendências); DELEGAR (urgente, não importante, com perfil de quem recebe — correspondente, estagiário, secretaria, automação); ELIMINAR (com o motivo em uma frase). Sem ninguém a quem delegar, dizer isso e mover o item para AGENDAR.
Prompt: `Tarefas da minha semana: [lista com prazo e impacto]. Aplique Eisenhower. Para delegar, diga o perfil de quem recebe. Para eliminar, diga por quê.`

**GUT** — fila de problemas sem data fatal.
Nota 1–5 em Gravidade, Urgência e Tendência. Score = G × U × T (máx. 125). Saída: tabela ordenada por score decrescente, com as colunas `#`, Problema, G, U, T, Score e "Por que essa nota"; depois, plano de ataque dos 3 primeiros com primeiro passo executável de cada um.
Prompt: `Problemas em aberto: [lista]. Aplique GUT com nota 1 a 5 por eixo, ranqueie e me dê o plano dos 3 primeiros.`

**Pareto** — dor concentrada.
Saída: ranking de causas por frequência ou valor, com o corte onde a soma acumulada passa de 80%. Nomear as poucas causas que respondem pela maioria e o que muda se só elas forem atacadas.
Prompt: `Estes são os [atrasos/erros/perdas] dos últimos [período]: [lista]. Faça o Pareto e me diga onde está o corte de 80%.`

**ICE** — escolher entre iniciativas.
Nota 1–10 em Impacto, Confiança e Esforço. Score = Impacto × Confiança ÷ Esforço. Saída: tabela ordenada, com a premissa mais frágil de cada linha exposta.
Prompt: `Iniciativas na mesa: [lista]. Aplique ICE, ranqueie e aponte a premissa mais frágil de cada uma.`

**Curva ABC** — carteira.
Classes A (poucos, maior retorno), B (intermediários), C (muitos, pouco retorno). Saída: as três faixas com o que muda de tratamento em cada uma — atenção, cadência de contato, aceitação de novos casos semelhantes.
Prompt: `Carteira: [cliente ou processo + retorno]. Monte a curva ABC e diga o que muda no tratamento de cada faixa.`

## Entender o problema

**5 Porquês** — erro que se repete.
Saída: cadeia de cinco perguntas encadeadas, cada resposta ancorada em fato verificável. Fechar na causa que, corrigida, impede a recorrência — não na pessoa. Se a cadeia quebrar por falta de informação, dizer em qual "porquê" quebrou.
Prompt: `Isto aconteceu de novo: [fato]. Aplique 5 Porquês até a causa que impede a recorrência.`

**Ishikawa** — falha com muitas causas candidatas.
Espinhas: Método, Máquina (sistema/PJe), Mão de obra, Material (documento/prova), Meio (prazo, vara, comarca), Medida (controle). Saída: causas por espinha, marcando as que têm evidência e as que são hipótese.
Prompt: `Falha: [descrição]. Monte o Ishikawa pelas 6 espinhas e separe causa com evidência de hipótese.`

**SIPOC** — processo mal descrito.
Saída: Fornecedor, Entrada, Processo (5 a 7 passos), Saída, Cliente. Marcar o passo onde o processo trava hoje e o que falta para automatizar.
Prompt: `Processo: [nome, do gatilho ao resultado]. Monte o SIPOC e aponte onde ele trava.`

**Cynefin** — antes de escolher método.
Classificar em Claro (aplicar a prática conhecida), Complicado (analisar ou consultar quem sabe), Complexo (testar pequeno e observar), Caótico (agir para estabilizar). Saída: o domínio, por quê, e o próximo passo compatível com ele.
Prompt: `Situação: [descrição]. Classifique no Cynefin e diga o próximo passo compatível com o domínio.`

## Decidir

**Matriz de decisão ponderada** — opções e critérios.
Saída: tabela opções × critérios, cada critério com peso declarado antes das notas, nota 1–5, total ponderado. Fechar dizendo qual critério, sozinho, viraria o resultado.
Prompt: `Opções: [lista]. Critérios e pesos: [lista]. Monte a matriz ponderada e diga qual critério inverteria o resultado.`

**Pré-mortem** — antes de assumir.
Premissa: já deu errado, seis meses à frente. Saída: as causas mais prováveis do fracasso, em ordem, e o que dá para blindar hoje em cada uma. Fechar com a condição objetiva que faria recusar o caso.
Prompt: `Vou assumir: [caso ou projeto]. Faça o pré-mortem e diga a condição objetiva que me faria recusar.`

**Matriz de risco** — probabilidade × impacto.
Saída: tabela com nota 1–5 nos dois eixos, produto, e resposta por risco (evitar, reduzir, transferir, aceitar). Risco de prazo ou de preclusão nunca fica em "aceitar".
Prompt: `Riscos de [caso ou operação]: [lista]. Aplique probabilidade × impacto e defina a resposta de cada um.`

**Custo de atraso** — o preço de deixar para depois.
Saída: por item, o que se perde por semana de atraso (dinheiro, prazo, prova que se deteriora, cliente que sai) e se o custo é linear, degrau ou irreversível. Item com custo irreversível sobe na fila independentemente do resto.
Prompt: `Itens que estou empurrando: [lista]. Calcule o custo de atraso semanal de cada um e marque os irreversíveis.`

## Tirar do papel

**5W2H** — iniciativa parada.
Saída em blocos: What, Why, Where, When, Who, How, How much. Cada bloco com dono nomeável, prazo concreto e marco mensurável. Fechar com as dependências entre blocos ("B só começa depois de A"). Sem valor informado, How much fica "não informado" — nunca estimar honorário, custa processual ou preço de terceiro.
Prompt: `Iniciativa: [projeto, objetivo, prazo]. Aplique 5W2H com dono, prazo e marco por bloco, e aponte as dependências.`

**OKR** — trimestre.
Saída: 1 objetivo qualitativo, 3 a 5 resultados-chave numéricos com linha de base e meta. Resultado-chave que mede esforço em vez de efeito é rejeitado e reescrito.
Prompt: `Trimestre: [foco]. Monte 1 objetivo e 3 a 5 KRs com linha de base e meta, sem KR de esforço.`

**PDCA** — melhorar rotina existente.
Saída: Plan (hipótese e métrica), Do (o teste mínimo), Check (o número que confirma ou nega), Act (padronizar ou descartar). Um ciclo por vez, com data de fechamento.
Prompt: `Rotina a melhorar: [qual, e o número atual]. Monte um ciclo PDCA com data de fechamento.`

**MoSCoW** — escopo estourando.
Must, Should, Could, Won't. Saída: os quatro grupos, com o Won't explícito — é ele que segura o escopo. Nada entra em Must sem consequência concreta se faltar.
Prompt: `Escopo de [entrega]: [itens]. Aplique MoSCoW e seja explícito no Won't desta rodada.`

**Limite de WIP** — muita coisa começada.
Saída: o que está em curso, o limite proposto por etapa, e o que precisa ser congelado ou terminado antes de começar qualquer coisa nova. Nada novo começa enquanto o limite estiver estourado.
Prompt: `Em curso agora: [lista]. Defina limite de WIP por etapa e diga o que congelo até terminar.`

## Quem faz

**RACI** — mais de uma pessoa.
Responsável (executa), Aprovador (responde, um só), Consultado, Informado. Saída: tabela tarefa × pessoa. Toda tarefa tem exatamente um Aprovador; tarefa sem responsável nomeado é apontada como buraco.
Prompt: `Tarefas e pessoas: [listas]. Monte o RACI com um único Aprovador por tarefa e aponte os buracos.`

**Níveis de delegação** — quanto o outro decide.
Seis níveis: 1 executa como mandado; 2 executa e relata; 3 propõe e espera aval; 4 decide e avisa antes; 5 decide e avisa depois; 6 decide sozinho. Saída: nível por tarefa, com o gatilho que faz subir de nível.
Prompt: `Tarefas que quero tirar da minha mesa: [lista]. Defina o nível de delegação de cada uma e o gatilho para subir de nível.`

## Regras gerais

- Sem invenção: só entra na análise o que o usuário listou ou o que está nos autos e nas pendências já registrados.
- Nota, peso ou valor sem base no que o usuário informou é alucinação: escrever "—" e dizer o que falta para pontuar.
- Item que não tem primeiro passo executável em menos de 1 minuto não é item: quebrar até ter.
- Veredito primeiro, justificativa depois. Sem introdução.
- Processo citado: usar número CNJ, não nome de cliente por extenso, quando a análise for para fora do stack contratado.
- Análise fechada → oferecer registrar as pendências e criar os lembretes de calendário, conforme a rotina padrão.