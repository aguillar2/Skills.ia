---
name: "triagem-negativacao"
description: "Triagem de relatório de crédito e cadastro restritivo (Serasa, SPC, Boa Vista/Quod, SCR/Registrato, protesto): separa o que se contesta do que não se contesta, aponta o vício e a prova, aplica o teste da Súmula 385 e define a rota. Dispare em: negativação, nome sujo, limpa nome, relatório de crédito, score, SPC, Serasa, Boa Vista, Registrato, SCR, protesto indevido, inscrição indevida, superendividamento, Lei 14.181."
---

# Triagem de negativação

Esta skill **tria**. Não redige petição inicial nem recurso. Quando a rota for R3 ou R4,
fecha um memorando de viabilidade e passa o caso para a skill `juridica-adpatada`.

O valor está na separação — decidir *o que* se contesta. Contestar tudo é o erro que
faz o requerimento ser indeferido em bloco e a inicial morrer na Súmula 385.

---

## ETAPA 0 — Anonimização (BLOQUEANTE)

Nada entra na análise sem mascaramento. Relatório de crédito é CPF, endereço, valores e
histórico de inadimplência de cliente: colar íntegra em ferramenta de IA é exposição de
dado sob sigilo (art. 34, VII, EOAB) e tratamento sem base legal adequada.

Antes de qualquer leitura, rode o mascaramento por regex:

```python
import re

SUBS = [
    (r'\b\d{3}\.?\d{3}\.?\d{3}-?\d{2}\b', '[CPF]'),
    (r'\b\d{2}\.?\d{3}\.?\d{3}/?\d{4}-?\d{2}\b', '[CNPJ]'),
    (r'\b\d{5}-?\d{3}\b', '[CEP]'),
    (r'\(?\b\d{2}\)?\s?9?\d{4}-?\d{4}\b', '[TEL]'),
    (r'[\w\.\-]+@[\w\.\-]+\.\w+', '[EMAIL]'),
]

def mascarar(txt: str) -> str:
    for pad, rep in SUBS:
        txt = re.sub(pad, rep, txt)
    return txt
```

Nome do titular vira `[TITULAR]`. **Nome da mãe e endereços históricos também saem** — o
relatório traz ambos e nenhum serve à triagem. Número de contrato mantém só os 4 últimos
dígitos (`CONTRATO ****4471`). A triagem funciona igual assim. O dado real reentra apenas
no documento de saída, na hora de protocolar — nunca no raciocínio.

Se o material chegou sem mascarar, mascare primeiro e diga que fez isso. Não pule.

---

## ETAPA 1 — Gate de tipo de documento (BLOQUEANTE), extração e datas

### 1.1 Identificar o tipo antes de extrair

Nem todo "relatório Serasa" serve para triagem. Identifique o tipo pelo cabeçalho e pelas
colunas presentes, e **declare qual é na primeira linha da saída**:

| Tipo | Como reconhecer | Traz vencimento? | Serve para |
|---|---|---|---|
| **Relatório do titular** (Serasa Consumidor, SPC Consumidor, Boa Vista, gov.br ou app do próprio CPF) | consulta em nome próprio; colunas separadas de **vencimento** e **inclusão**; menciona notificação | Sim | triagem definitiva |
| **Registrato / SCR (BCB)** | cabeçalho do Banco Central; operações por instituição, com vencido e prejuízo | Parcial | cruzamento obrigatório |
| **Certidão de protesto** | tabelionato | Sim | cruzamento |
| **Serasa Recomenda / Serasa Empresas** | "Você consultou a solução Serasa Recomenda"; blocos REFIN/PEFIN/CONVEM; score e renda estimada; rodapé sobre decisão de crédito | **Não** — só data de registro | triagem **preliminar** apenas |
| **Print de app ou tela** | imagem, sem colunas completas | Não | indiciar apenas |

### 1.2 Se o insumo for Recomenda/Empresas ou print

**Pare a triagem definitiva.** Marque a saída como `TRIAGEM PRELIMINAR` e produza, em vez
do quadro final:

- o que dá para ver (credor, modalidade, valor, data de registro, avalista);
- a lista do que pedir ao cliente para fechar a triagem: relatório do próprio titular nos
  três birôs, extrato do Registrato/SCR, certidão de protesto do domicílio e das comarcas
  dos credores, e comprovantes de pagamento do que ele diz ter quitado;
- rota R0. Nenhuma rota definitiva e nenhuma promessa de dano moral.

**Aviso de finalidade, obrigatório quando o insumo for Recomenda/Empresas:** esse relatório
é emitido para subsidiar decisão de crédito e restringe reprodução a terceiros. Consulta que
o escritório fez *sobre* alguém não é instrumento legítimo para instruir a defesa *dessa*
pessoa — é desvio de finalidade (LGPD art. 6º, I). O documento correto é o que o próprio
titular obtém em nome dele. Registre isso na saída; não siga adiante só porque o arquivo
estava na pasta do caso.

### 1.3 Extração e cálculo

PDF/print → texto → quadro de registros. Colunas mínimas por apontamento:

| # | Credor informante | Órgão | Contrato | Modalidade | Valor | Vencimento | Inclusão | Avalista | Sai em |

O quinquênio é **calculado por função**, nunca estimado pelo modelo:

```python
from datetime import date

def sai_em(vencimento: date) -> date:
    """CDC 43 §1º + Súm. 323 STJ: 5 anos contados do VENCIMENTO."""
    try:
        return vencimento.replace(year=vencimento.year + 5)
    except ValueError:          # 29/02
        return vencimento.replace(month=2, day=28, year=vencimento.year + 5)
```

**Data de vencimento ausente sai como `NÃO CONSTA` e vira pedido de exibição.**
Nunca inferir vencimento a partir da data de inclusão nem da data de registro — ambas são
posteriores, e contar delas prorroga indevidamente a permanência no cadastro.

**Coluna avalista = S:** o titular garantiu dívida de terceiro. A negativação é legítima em
tese, mas abre duas perguntas ao cliente: o afiançado quitou? houve exigência prévia ao
devedor principal? Quitado e ainda negativado → Pilha B, Súm. 548.

---

## ETAPA 2 — Três pilhas

| Pilha | O que é | Destino |
|---|---|---|
| **A — devido e regular** | é do cliente, valor e data certos, dentro dos 5 anos do vencimento | não se contesta → ordenação de pagamento |
| **B — devido, mas viciado** | é do cliente, mas há erro de valor, data, prazo, notificação prévia ou baixa após quitação | contesta o campo exato |
| **C — inexistente** | fraude, homônimo, contrato nunca firmado, dívida quitada, duplicidade de cessão | declaratória + indenização |

Sem vencimento no insumo, nenhum item vai para a Pilha A em definitivo — vai para
**A provisória**, com a exibição pendente.

Quando o cliente diz "não reconheço" sobre item que reconhece sob pergunta específica,
refaça a pergunta em três partes — **credor, data, valor** — em vez de perguntar pelo
conjunto. Metade dos "não reconheço" some aí.

---

## ETAPA 3 — Matriz vício → tese (um vício principal por item, o mais forte)

| Vício | Prova necessária | Base | Efeito | Dano moral |
|---|---|---|---|---|
| Registro além de 5 anos do **vencimento** | contrato/boleto com a data de vencimento | CDC 43 §1º; Súm. 323 | exclusão imediata | forte |
| Ausência de notificação prévia | silêncio/negativa do birô sobre comprovante de envio | Súm. 359; Súm. 404 | exclusão — réu é o **birô** | forte |
| Não exclusão em 5 dias úteis após quitação | comprovante de pagamento + print datado | Súm. 548 | exclusão — réu é o **credor** | forte |
| Débito de terceiro, homônimo ou fraude | ausência de contrato assinado; divergência documental | Súm. 479 | inexistência do débito | forte |
| Dívida da PJ lançada no CPF, sem avalista nem sócio-garantidor | contrato social + contrato da dívida | CDC 43 caput | exclusão | forte |
| Dívida prescrita ainda informada | termo inicial + prazo prescricional do CC | CDC 43 §5º | exclusão | média |
| Mesmo débito duplicado / cessões sucessivas | dois registros apontando o mesmo contrato | CDC 43 caput | exclusão dos excedentes | média |
| Dívida sub judice com tutela deferida | decisão + print posterior do cadastro | descumprimento | exclusão + multa | média |
| Valor divergente do contrato | contrato ou planilha de evolução | CDC 43 caput | retificação | fraca |
| Data de vencimento divergente | contrato | CDC 43 §1º | retificação (pode virar a 1ª linha) | fraca |
| Score sem esclarecimento das fontes | pedido formal de esclarecimento negado | Súm. 550 | só esclarecimento | fraca |

A coluna de dano moral é **expectativa antes** do teste da Súmula 385, não depois.

---

## ETAPA 4 — Teste da Súmula 385 (antes de prometer indenização)

> Havendo negativação legítima preexistente, não cabe dano moral — resta o direito ao
> cancelamento.

Três perguntas, nesta ordem:

1. **Restou item na pilha A vigente na data do registro impugnado?**
   Se sim, presuma a Súmula 385 e diga isso ao cliente **agora**, não depois de protocolar.
2. **Os registros preexistentes também estão sendo impugnados?**
   Há flexibilização quando demonstrada a verossimilhança da impugnação dos anteriores,
   mesmo sem trânsito em julgado. É exceção e exige **juntar prova** dessas impugnações —
   não se alega, se documenta.
3. **O vício é do birô ou do credor?**
   Falta de notificação prévia → órgão mantenedor. Baixa fora do prazo após quitação →
   credor. Réu errado é carência com honorários contra o cliente.

Dois registros legítimos e simultâneos se sustentam mutuamente: cada um é o preexistente do
outro. Nesse cenário não há dano moral a prometer — só exclusão, se houver vício.

---

## ETAPA 5 — Rotas de saída (uma por item, com o porquê registrado ao lado)

- **R0 — Insumo insuficiente.** Saída única quando o gate da Etapa 1 barrou: lista do que
  pedir ao cliente, sem rota e sem promessa.
- **R1 — Requerimento administrativo.** Ao credor e ao órgão mantenedor. Deferida a
  correção, o arquivista comunica os destinatários em 5 dias úteis (CDC 43 §3º). Primeira
  tentativa em pilha B por erro de valor ou data. Protocola e guarda o número.
- **R2 — consumidor.gov.br e Procon.** Custo zero e produz o que a inicial precisa: prova
  documentada de resistência do fornecedor. Fazer **antes** de ajuizar, sempre.
- **R3 — Declaratória de inexistência c/c indenização, com tutela de urgência.** Pilha C e
  pilha B com vício forte, condicionada à aprovação no teste da Súmula 385.
- **R4 — Repactuação por superendividamento (Lei 14.181/2021).** Quando o problema não é
  vício e sim volume: dívidas de consumo, boa-fé, incapacidade de pagar sem comprometer o
  mínimo existencial. Plano em até 5 anos, audiência global com todos os credores.
  O julgamento do STF de 23/04/2026 (ADPFs 1005, 1006 e 1097) trouxe o consignado para
  dentro da proteção do mínimo existencial e obrigou o CMN a estudo técnico anual de
  atualização do valor — o piso fixo do Dec. 11.150/22 deixou de encerrar a discussão.
- **R5 — Nada a fazer.** Item legítimo e vigente. Informe o **mês** em que sai sozinho.
  Registro que sai em menos de 6 meses não se paga para "limpar" — espera-se.

**Ordenação de pagamento da pilha A:** não é a maior nem a menor dívida — é a que sai do
cadastro mais rápido com o que o cliente tem por mês (retorno em score por real gasto).

---

## Fontes a cruzar (mínimo cinco, todas gratuitas)

Serasa · SPC · Boa Vista/Quod · **Registrato do Banco Central** (SCR, via gov.br — pega
dívida bancária que não aparece em birô, incluindo contrato marcado como prejuízo) ·
certidão de protesto do tabelionato do domicílio e das comarcas dos credores.

Todas obtidas **pelo cliente, em nome dele**. Divergência entre fontes não é ruído, é vício
em potencial: débito que consta no SCR e não no birô, ou valor diferente entre dois birôs
para o mesmo contrato, entra direto na pilha B.

---

## ETAPA 6 — Saídas

1. **Quadro de apontamentos** — abre declarando o **tipo de documento** e se a triagem é
   definitiva ou preliminar. Uma linha por item: pilha, vício, prova que falta, rota, mês
   de saída.
2. **Requerimento administrativo por item** — aponta o campo exato que está errado e lista
   o documento que prova. Requerimento genérico pedindo "tirar tudo" não funciona.
3. **Memorando de viabilidade** — só se houver rota R3 ou R4; é o que passa para `juridica-adpatada`.
4. **Carta ao cliente** — em português comum, com o alerta antifraude abaixo e, quando for
   o caso, a lista do que ele precisa puxar em nome próprio.

Nome de arquivo (vale também para o título no Google Docs):
- Havendo autos: número CNJ na frente SEM ponto e SEM hífen, só os 20 dígitos, com o
  dígito verificador conferido (skill conferir-cnj): `NNNNNNNDDAAAAJTTOOOO_requerimento.docx`
  (ex.: `50019776420258130377_requerimento.docx`). No corpo da peça o CNJ continua com
  pontos e hífen.
- Não havendo autos: tipo da peça + nome do cliente, como `requerimento_joao_da_silva.docx`.
- Em qualquer caso, nenhum caractere especial, porque o eproc rejeita: sem ponto (fora o da
  extensão), sem hífen, sem acento, sem ç, sem espaço, sem travessão, sem parênteses, sem
  símbolos. Só letras a-z sem acento, números e `_` como separador.

---

## ETAPA 7 — Alerta antifraude (vai na carta ao cliente)

1. Promete **apagar dívida verdadeira** mediante pagamento.
2. Cobra taxa antecipada por "remoção" ou "acordo exclusivo" com o birô.
3. Pede senha do gov.br, do internet banking ou selfie com documento.
4. Promete prazo fixo — "limpo em 48 h". Nenhuma baixa regular tem prazo garantido.

Dívida verdadeira sai sozinha em 5 anos contados do vencimento, sem pedido e sem pagar
ninguém. Consulta a birô e ao Registrato é gratuita.

---

## Base normativa

- **CDC art. 43** — caput e §§1º, 2º, 3º e 5º: exatidão, cinco anos, notificação, correção em 5 dias úteis, prescrição
- **Lei 12.414/11** — cadastro positivo
- **Lei 14.181/21** — superendividamento e repactuação
- **Dec. 11.150/22** — mínimo existencial (ler junto com STF, 23/04/2026, ADPFs 1005/1006/1097)
- **LGPD arts. 6º, I, 18 e 20** — finalidade, direitos do titular e revisão de decisão automatizada
- Súmulas STJ **323** (5 anos), **359** (notificação é do mantenedor), **385** (preexistência afasta dano moral), **404** (AR dispensável), **479** (fortuito interno), **548** (baixa em 5 dias úteis), **550** (score exige esclarecimento), **572** (CCF — BB não notifica nem responde)

---

## Regras fixas

- Nunca inferir data de vencimento a partir de inclusão ou registro.
- Nunca entregar triagem definitiva sobre Serasa Recomenda/Empresas ou print de tela.
- Nunca usar consulta de terceiro feita pelo escritório como base da defesa do consultado.
- Nunca prometer dano moral antes da ETAPA 4.
- Nunca contestar item da pilha A.
- Um vício principal por item, o mais forte — não empilhar teses.
- Nada de jurisprudência nas peças de saída, salvo pedido expresso: só o dispositivo e o fato.
  Pedida, entra sempre e referenciada (tribunal, classe e número, relator, órgão julgador,
  datas de julgamento e publicação, link da fonte oficial aberto na sessão), pela skill
  `pesquisa-jurisprudencia`. As súmulas da base normativa servem à triagem, não à peça.