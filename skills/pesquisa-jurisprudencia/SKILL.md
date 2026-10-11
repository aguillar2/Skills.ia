---
name: pesquisa-jurisprudencia
description: "Pesquisa de jurisprudência com verificação obrigatória (nenhum acórdão entra na resposta sem URL aberta na sessão) e rota de monitoramento periódico de tema. Dispare em \"pesquisa jurisprudência\", \"como o STJ decide\", \"tem acórdão sobre\", \"precedente\", \"tese firmada\", \"tema repetitivo\", \"súmula\", \"entendimento do TJMG/TJDFT\", \"monitorar tema\", \"boletim de jurisprudência\", \"me avisa quando sair acórdão sobre\"."
---

# Pesquisa de jurisprudência verificada

Duas rotas, mesma regra dura de verificação:

- **Rota A — consulta pontual.** Pergunta agora, dossiê agora. Passos 1 a 5.
- **Rota B — monitoramento periódico.** Acompanhar um tema ao longo do tempo, sem intervenção diária.

## Quando dispara

**Rota A**: "pesquisa jurisprudência sobre X", "como o STJ vem decidindo", "tem acórdão sobre", "qual a tese do tema Y", "tem súmula disso", "entendimento do TJMG/TJDFT/TJMT sobre".

**Rota B**: "monitora esse tema", "me avisa quando sair acórdão sobre", "quero acompanhar", "boletim de jurisprudência", "informativo do STJ", ou pedido de rotina/tarefa agendada de jurisprudência.

## Quando NÃO dispara

- Redação de petição, recurso ou qualquer peça processual. Regra fixa do escritório: **jurisprudência não entra em peça**. Se a peça precisar de fundamento, é lei e fato dos autos.
- Extração de trecho dos próprios autos → `ancorar-citacoes-autos`.
- Estratégia processual sem pedido de precedente → `juridica-adpatada`.

O produto desta skill é **dossiê de leitura para decisão de estratégia**, não texto para colar em peça.

## Regra dura — não negociável (vale nas duas rotas)

Um acórdão só pode ser afirmado se **a URL dele foi aberta nesta sessão e a ementa foi lida aqui**.

Proibido, sem exceção:

- Escrever número de processo, relator, órgão julgador ou data de memória.
- Escrever "REsp 1.xxx.xxx/SP" ou similar sem ter aberto a página.
- Afirmar "entendimento pacificado" com menos de 3 acórdãos verificados do mesmo órgão.
- Citar súmula ou tese de repetitivo/repercussão geral sem conferir o texto no site do tribunal.
- Usar Jusbrasil, blog, escritório ou agregador como fonte do dado. Servem para **achar** o número; a confirmação é sempre no portal do tribunal.

O que não foi confirmado vai para a seção **NÃO LOCALIZADO** com o termo buscado. Lacuna declarada é resultado válido; número plausível inventado é o único erro que não se corrige depois.

---

# Rota A — consulta pontual

## Passo 1 — Fixar o pedido antes de buscar

Registre em uma linha, e confirme com o usuário se algum campo estiver vazio:

- **Tribunal(is)** e por quê (STJ para uniformização de lei federal; tribunal local para o juízo que vai julgar).
- **Tese** na forma afirmativa que se quer testar (não o tema genérico). Ruim: "guarda compartilhada". Bom: "guarda compartilhada é obrigatória mesmo com litígio entre os genitores".
- **Recorte temporal** — padrão: últimos 5 anos, salvo pedido diverso.
- **Posição** — precedente a favor, contra, ou os dois. Padrão: os dois.

## Passo 2 — Portais oficiais

| Tribunal | Portal |
|---|---|
| STJ | https://processo.stj.jus.br/SCON/ |
| STF | https://jurisprudencia.stf.jus.br/ |
| TST | https://jurisprudencia.tst.jus.br/ |
| TJMG | https://www5.tjmg.jus.br/jurisprudencia/ |
| TJDFT | https://jurisdf.tjdft.jus.br/ |
| TJMT | https://jurisprudencia.tjmt.jus.br/ |

Se o portal estiver fora do ar, bloquear acesso automatizado ou mudar de endereço, **diga isso** e não substitua por agregador.

Busca: termos da ementa entre aspas, conectados por E/OU conforme o buscador do tribunal. Comece restrito; só amplie se vier vazio.

## Passo 3 — Extrair de cada acórdão aberto

Seis campos, todos lidos da página:

1. Tribunal e órgão julgador (turma/seção/câmara)
2. Classe e número do processo, como grafado no portal
3. Relator
4. Data de julgamento e data de publicação
5. URL aberta
6. Trecho literal da ementa que sustenta (ou derruba) a tese — entre aspas, sem reescrever

Marque também, quando houver: repetitivo/repercussão geral (nº do tema), súmula aplicada, e se o julgado foi superado ou está afetado.

## Passo 4 — Saída

**Resposta direta em 2 linhas** — a tese se sustenta, se sustenta em parte, ou não se sustenta, e por quê. Antes da tabela.

**Tabela A — A favor da tese**

| Tribunal/órgão | Processo | Relator | Julgamento | Trecho da ementa | URL |

**Tabela B — Contra a tese** (mesmas colunas). Se estiver vazia, escreva "nenhum contrário localizado nos termos buscados" — não deixe implícito que não existe.

**Tema/súmula aplicável** — só se conferido no site do tribunal, com o número e o texto da tese.

**NÃO LOCALIZADO** — termos buscados que não retornaram nada, e portais que falharam.

**Leitura de risco** — divergência entre turmas, julgado antigo sem confirmação recente, tese minoritária, precedente de outro tribunal sem força no juízo do caso.

## Passo 5 — Fecho obrigatório

Uma linha ao final, sempre:

> Verificado nesta sessão: N acórdãos abertos em portal oficial. Nada nesta resposta foi escrito de memória.

---

# Rota B — monitoramento periódico de tema

Acompanhar um tema sem abrir portal todo dia. O ciclo entrega **candidatos**; nada vira afirmação sem passar pela Rota A.

## B0 — Regra de fonte (decide se o monitoramento é possível)

Monitoramento só se apoia em fonte **sem captcha e sem login**: boletim, informativo, feed ou página de listagem.

**Formulário de busca protegido por captcha não se automatiza — e não se contorna.** Não existe rota autorizada que resolva captcha para gerar coleta periódica. Se a única fonte do tema for formulário com captcha, a resposta correta é: monitoramento automático inviável nessa fonte — ou o usuário roda a Rota A quando quiser, ou se acha fonte-boletim equivalente.

### Estado de acesso verificado em 10/09/2026

Revalidar antes de montar o ciclo — portal muda sem aviso.

| Fonte | Verificado | Serve para monitorar? |
|---|---|---|
| TJMG — formulário de jurisprudência (`www5.tjmg.jus.br/jurisprudencia/formEspelhoAcordao.do`) | HTTP 200, carrega `ValidacaoCaptchaAction.js` e `captcha.svl` — captcha de imagem | Não |
| TJSP — e-SAJ CJSG (`esaj.tjsp.jus.br/cjsg/consultaCompleta.do`) | HTTP 200, reCAPTCHA v3 na consulta (`action: 'consulta'`) | Não |
| TJMG — Boletim de Jurisprudência (`tjmg.jus.br/portal-tjmg/jurisprudencia/boletim-de-jurisprudencia/`) | HTTP 200, sem captcha; lista de edições carregada por JavaScript | Sim, com navegador |
| TJDFT (`jurisdf.tjdft.jus.br`), TJMT (`jurisprudencia.tjmt.jus.br`), TST (`jurisprudencia.tst.jus.br`) | HTTP 200, sem captcha na página inicial | Provável — confirmar a página de listagem antes |
| STJ (`scon.stj.jus.br/SCON/`, `processo.stj.jus.br/SCON/`) | 403/520 em fetch server-side | Só por navegador; fetch direto não serve |
| STF (`jurisprudencia.stf.jus.br`) | falha de TLS/robots em fetch server-side | Só por navegador; fetch direto não serve |
| DataJud CNJ | não verificado | Metadado processual, não ementa — não substitui |

Fonte que exige JavaScript: usar navegador, não `curl`. Fonte que responde 403 a fetch: idem, e nunca disfarçar requisição para contornar o bloqueio.

## B1 — Fixar o monitoramento

Antes de montar, registre e confirme:

- **Tema**, na forma afirmativa (mesma exigência da Rota A) — e as variações de termo que o boletim pode usar.
- **Fontes**, escolhidas por B0, com o link exato de listagem.
- **Periodicidade** — padrão: semanal. Diário só se a fonte publicar diariamente; boletim semanal monitorado todo dia é ruído.
- **Onde salva** — pasta e formato. Padrão: um `.md` por ciclo, mais um índice acumulado.
- **O que aciona aviso** — item novo, ou só item que muda a tese.

## B2 — Montar o ciclo

1. Agendar como tarefa agendada (Routine), não como script solto. Registrar horário e periodicidade escolhidos.
2. Cada execução abre as fontes de B0, coleta os itens do período e extrai: fonte, data da edição, número do processo como grafado, órgão, ementa/verbete literal e URL.
3. **Dedup por hash** do par (número do processo + fonte). Item já visto não reaparece.
4. Filtrar pelo tema e variações de B1. Item fora do filtro é descartado com contagem, não silenciosamente.
5. Gravar o `.md` do ciclo e atualizar o índice acumulado.

## B3 — Saída de cada ciclo

**Cabeçalho em 1 linha**: período coberto, fontes consultadas, quantos itens novos.

**Novidades** — tabela:

| Fonte/edição | Processo | Órgão | Verbete literal | URL | Muda a tese? |

A coluna "Muda a tese?" só admite: `confirma` / `contraria` / `neutro`. Sem adjetivo.

**Nada novo** é resultado válido e se escreve assim: "ciclo X: nenhum item novo nas fontes Y e Z".

**FALHA DE FONTE** — fonte que não abriu, mudou de endereço ou passou a exigir captcha. Registrar e parar de usá-la. **Nunca substituir fonte oficial que falhou por agregador** — nem por um ciclo.

## B4 — Ponte obrigatória com a Rota A

Item coletado no monitoramento é **candidato**, não precedente. Antes de entrar em dossiê, em decisão de estratégia ou em qualquer resposta afirmativa:

- abrir a URL do acórdão nesta sessão;
- conferir os seis campos do Passo 3;
- conferir se foi superado ou está afetado.

Verbete de boletim é resumo do próprio tribunal sobre o julgado — bom indício e fonte oficial, mas não substitui a ementa aberta.

## B5 — Fecho do ciclo

> Ciclo de monitoramento: N fontes abertas, M itens novos, K já verificados na Rota A. Nada aqui foi escrito de memória.

## Tom

Veredito primeiro, prova depois. Sem introdução, sem adjetivo, sem "é importante destacar". O usuário programa desde 1997 e é advogado — não explique o que é ementa, repetitivo ou súmula.