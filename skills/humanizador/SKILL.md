---
name: humanizador
description: "Remove marcas de escrita gerada por LLM (\"cara de IA\") de texto em prosa — posts, artigos, e-mail a cliente, copy de chatbot, material de curso, README. NUNCA usar em petição, recurso ou qualquer peça processual: nessas vale a juridica-adpatada. Dispare quando o usuário disser \"humaniza\", \"tira a cara de IA\", \"esse texto tá com jeito de ChatGPT\", \"revisa esse texto\", \"deixa mais natural\", \"despadroniza\", ou colar rascunho gerado por IA pedindo passada de revisão."
---

# Humanizador (PT-BR)

Camada de revisão sobre rascunho gerado por IA. Identifica e remove padrões que denunciam texto de modelo de linguagem, sem alterar o que o texto afirma.

Adaptado de `blader/humanizer` (MIT), que por sua vez deriva do guia *Signs of AI Writing* do WikiProject AI Cleanup da Wikipédia. Os 35 padrões originais são do inglês; aqui só entram os que transferem para o português, mais os vícios próprios de PT-BR.

## QUANDO NÃO USAR

**Peça processual está fora.** Petição, contestação, recurso, memoriais, impugnação, HC — nada disso passa por aqui. O registro jurídico brasileiro é legitimamente formal; esta skill corta formalidade e voz passiva por design e degradaria a peça. Para peça: `juridica-adpatada`.

Também fora: texto de terceiro que o usuário só quer analisar, transcrição literal, citação, dispositivo legal, ementa, contrato. Nesses, sugerir e não reescrever.

Se o texto for misto (post sobre um caso, artigo com trecho de peça), humanizar só a prosa autoral e deixar o trecho técnico intacto, avisando o que ficou de fora.

## REGRAS INVIOLÁVEIS

1. Não mudar o que o texto afirma.
2. Não inventar fato, nome, número, data, citação, fonte, ranking ou exemplo. Se algo não está no original nem foi dado pelo usuário, não entra.
3. Não acrescentar opinião nem primeira pessoa onde não havia.
4. Não inventar detalhe pessoal para "dar humanidade".
5. Se houver amostra da escrita do usuário, ela manda: não trocar palavra coloquial por formal nem apagar cacoete deliberado.
6. Preservar dado, número, nome próprio, link, código, YAML e bloco de dados exatamente como estão.

## MODOS

- **Colado (padrão):** devolve o texto reescrito, depois uma lista curta dos padrões que ainda restam e por quê.
- **Arquivo:** grava só o texto final no arquivo; mexe só na prosa; devolve resumo do que mudou.
- **Embutido:** devolve só o texto final, sem comentário.

## PADRÕES QUE TRANSFEREM PARA O PORTUGUÊS

### Conteúdo

1. **Importância inflada.** Detalhe comum apresentado como marco ou legado. Corte "marcou uma virada", "consolidou-se como referência", "deixou um legado duradouro".
2. **Nome jogado sem função.** Citar autoridade sem dizer o que ela disse de útil. Ou traz o conteúdo, ou tira o nome.
3. **Análise rasa em gerúndio.** "..., demonstrando a importância de...", "..., evidenciando como...". Vire afirmação direta ou corte.
4. **Linguagem de vendedor.** "solução robusta", "experiência única", "potencializa resultados". Diga o que a coisa faz.
5. **Fonte vaga.** "especialistas apontam", "estudos indicam", "muitos afirmam". Nomeie a fonte real ou remova a afirmação.
6. **Seção formulaica de "desafios e perspectivas".** Parágrafo final genérico sobre desafios, futuro, importância. Corte inteiro.
7. **Fingir revelar verdade oculta.** "o que ninguém te conta", "a verdade é que", "na prática, o que acontece é". Afirme direto.
8. **Responder objeção que ninguém fez.** "não se trata de dizer que X é ruim". Corte.
9. **Rejeitar alternativa falsa.** Descartar opção que o texto nunca usaria, só para parecer ponderado. Corte.

### Construção de frase

10. **"Não é X, é Y".** O vício mais denunciador em PT-BR. Também nas variantes "mais do que X, é Y", "não apenas X, mas Y". Afirme Y direto.
11. **Trinca forçada.** Três adjetivos ou três substantivos em série onde dois bastam: "claro, objetivo e direto". Quebre.
12. **Final cortado, em fragmento.** "E isso muda tudo." / "Simples assim." / "É esse o ponto." Vire oração completa ou corte.
13. **Falso intervalo "de X a Y".** "de startups a multinacionais", quando não há continuidade real. Liste os itens de fato.
14. **Repetir a mesma abertura de frase** em parágrafos seguidos, e trocar o nome do sujeito a cada menção por sinônimo ("o advogado", "o profissional", "o causídico"). Use um nome só e varie a estrutura.
15. **Anunciar o próximo ponto.** "Vamos falar agora sobre...", "Antes de tudo, é preciso entender...". Vá direto.
16. **Título repetido na primeira frase** do parágrafo abaixo dele. Corte a repetição.
17. **Punchline dramática em sequência.** Fileira de frases de uma linha para criar tensão. Varie o comprimento.

### Vocabulário e enchimento

18. **Palavras-marca de LLM em PT-BR:** "crucial", "fundamental", "essencial", "robusto", "vasto", "intrincado", "aprofundar-se", "navegar por", "desbloquear", "impulsionar", "holístico", "panorama", "cenário", "jornada", "nesse sentido", "vale ressaltar", "é importante destacar", "em suma", "por fim, mas não menos importante". Troque por termo comum ou corte.
19. **Fuga do verbo simples.** "constitui", "configura-se como", "apresenta-se enquanto", "trata-se de" onde cabe "é", "tem", "faz".
20. **Excesso de modalizador.** "pode potencialmente vir a contribuir de certa forma". Um por frase, no máximo, e só se o original sustentar.
21. **Enchimento.** "no que diz respeito a" → "sobre"; "a fim de" → "para"; "devido ao fato de que" → "porque"; "em virtude de" → "por".
22. **Tom concordante demais.** "Ótima pergunta", "Você tem razão em se preocupar", pergunta retórica encenada antes de ponto banal. Corte.
23. **Fecho positivo genérico.** "No fim das contas, o importante é...", "O futuro é promissor". Termine no último fato concreto.
24. **Provérbio de encomenda.** "Como diz o ditado...", "Roma não foi construída em um dia". Troque por afirmação concreta.
25. **Abertura de falsa franqueza.** "Olha", "Sinceramente", "Vou ser honesto" isolados no começo. Corte.

### Formatação

26. **Negrito demais**, sem função. Remova o que não marca termo definido.
27. **Lista com mini-título em negrito** onde a prosa corrida serviria. Converta.
28. **Emoji decorativo.** Remova, salvo se a amostra do usuário usar.
29. **Resto de chatbot:** saudação, "Claro!", "Espero ter ajudado", "Se precisar de mais alguma coisa", oferta de continuar. Corte.
30. **Ressalva de data de corte** ("até minha última atualização"). Corte, ou diga o que a fonte não mostra.

## PADRÕES DO INGLÊS — NÃO APLICAR CEGAMENTE

- **Travessão (—).** Em inglês o em dash é marca de IA. Em português é pontuação legítima de aposto e de diálogo. Só corte se houver mais de um por parágrafo ou se substituir vírgula banal.
- **Voz passiva.** Em inglês, suspeita. Em português técnico e jurídico, muitas vezes é a construção correta. Só troque quando esconder quem age e isso importar.
- **Title Case em título.** Não existe em PT-BR. Ignore.
- **Aspas curvas, hífen em par de adjetivos.** Regras de inglês. Ignore.

## VÍCIOS ADICIONAIS DE PT-BR E DE JURIDIQUÊS

Só em texto que **não** é peça — em post, artigo e e-mail eles pesam:

- "Ademais", "outrossim", "cumpre salientar", "resta claro", "no caso em tela", "data venia" fora de peça.
- Mesóclise ("far-se-á") em texto informal.
- "O mesmo" como pronome ("o cliente ligou e o mesmo informou").
- Parágrafo-resumo repetindo o que o tópico acabou de dizer.
- Numeração romana e "DOS FATOS"/"DO DIREITO" vazando para fora de peça.

## FALSOS POSITIVOS — NÃO MARQUE

- Gramática correta, por si só, não é sinal de IA.
- Formalidade legítima do gênero (parecer, contrato, e-mail a juízo).
- Conectivo comum ("portanto", "além disso") usado uma vez.
- Termo técnico jurídico com sentido próprio: não troque "litisconsórcio" por sinônimo "mais natural".
- Repetição intencional para ênfase retórica, quando a amostra do usuário mostra esse hábito.

## DETALHE HUMANO A PRESERVAR

Número específico, data, nome de caso, valor, erro admitido, opinião assumida, anedota, ironia, gíria do autor. É o que sobra de humano no texto — não uniformize.

## COMO DEVOLVER

1. Texto reescrito.
2. Lista curta: padrão nº, trecho original, o que virou. Só dos que você mexeu.
3. O que você deixou intacto de propósito e por quê (termo técnico, citação, trecho de peça).
4. Se sobrou padrão que você não conseguiu resolver sem inventar ou sem mudar o sentido, diga qual e por quê — não force.

Depois de devolver, lembre em uma linha: a passada final é do autor, com repertório e exemplo próprios. Esta skill despadroniza; ela não escreve no lugar dele.

## FONTE

- `blader/humanizer` — https://github.com/blader/humanizer (MIT)
- *Wikipedia:Signs of AI writing* / WikiProject AI Cleanup