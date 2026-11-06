# EngMan Copilot — Operação e Priorização de Campo
 
Challenge Sprint FIAP × Motiva Rodovias · Ciência da Computação · Sprint 01 — 2026.2

---

## 1. Nome do projeto e integrantes (alterar)

**Assistente:** EngMan Copilot
**Recorte:** B — Operação e Priorização de Campo
**Turma:** 1CCPS

| Nome completo | RM | Turma | Frente |
| --- | --- | --- | --- |
| Thiago Nascimento de Sá | 575074 | 1CCPS | Planejamento e documentação |
| Lucca Vitor Fustinoni Camargo | 575383 | 1CCPS | Prompt e avaliação |
| Erick Vieiro Martins | 575683 | 1CCPS | Base de conhecimento e ferramentas |
| Miguel Souza Nunes | 576617 | 1CCPS | Arquitetura, stack e segurança |

---

## 2. Problema abordado

A Motiva opera mais de 5 mil km de rodovias, com cerca de 2 milhões de veículos pedagiados por dia, e depende de ativos eletroeletrônicos expostos a chuva, poeira e vibração. Segundo o briefing, a detecção de falha é reativa, baseada na percepção do operador do CCO, e o histórico de falhas do FSM é pouco explorado.

O resultado é que o operador recebe mais alarmes do que consegue investigar, e parte deles não é falha. O caso mais claro é o Tema 2.1: câmeras de CFTV geram **falso positivo** por glare solar, chuva e neblina. **Alarme falso gera deslocamento caro; alarme ignorado gera risco viário.**

**Objetivo geral:** apoiar o operador do CCO e o coordenador de manutenção a triar os alarmes e ocorrências do dia, sem inventar dados.

**Objetivos específicos:**
1. Classificar cada alarme em real, ambiental (chuva, neblina, glare solar) ou indeterminado, citando a fonte.
2. Priorizar a fila do dia por criticidade e histórico de falhas.
3. Propor a descrição da ordem de serviço e indicar o procedimento aplicável.
4. Admitir quando não há dado suficiente e recusar o que está fora do escopo.

---

## 3. Recorte e persona (alterar)

**Recorte B — Operação e Priorização de Campo.** O grupo escolheu o B porque a dor dele aparece diretamente no briefing: reduz o deslocamento de campo desnecessário (Tema 2) e trata os falsos positivos de glare, chuva e neblina (Tema 2.1). Além disso, o recorte tem um critério de sucesso objetivo e testável: a classificação de cada alarme pode ser conferida caso a caso no eval set.

**Persona principal: operador do CCO.** Recebe alarmes em tempo real e precisa decidir o que acionar e o que apenas monitorar. O risco principal dela é que um alarme falso gera deslocamento caro e um alarme ignorado gera risco viário. Por isso o assistente responde de forma estruturada: **classificação (real / ambiental / indeterminado) + prioridade + ação recomendada**.

**Persona secundária: coordenador de manutenção.** Usa a mesma triagem para decidir quais ocorrências viram atividade de campo e em que ordem.

**Ativos considerados** (subconjunto do parque descrito no briefing):

| Ativo | O que faz | Falhas típicas que o assistente ajuda a tratar | Condição ambiental que simula falha |
| --- | --- | --- | --- |
| CFTV (câmeras PTZ) | Monitoramento em tempo real para despacho de socorro | Lente obstruída, teia de aranha refletindo infravermelho, desfoque por vibração, imagem degradada sem perda de link | Glare solar, chuva, neblina |
| SAT (Analisador de Tráfego) | Sensores e radares de volume, velocidade e classificação | Leitura inconsistente, deriva de calibração, perda de dados | Trânsito lento por neblina ou chuva |
| Callbox | Totens de emergência com comunicação geolocalizada | Botão sem resposta, áudio unidirecional, falha de bateria/backup | Interferência por tempestade |
| SCA (Sistema Ambiental) | Estações meteorológicas que alertam o CCO | Sensor descalibrado, leitura fora de faixa plausível, falha de comunicação | Fonte de dados para confirmar o clima |

> Todos os dados de ativos, histórico e procedimentos deste projeto são **simulados**, salvo indicação em contrário. A Motiva não libera base real para trabalho acadêmico.

---

## 4. Escopo do assistente (alterar)

**O assistente faz:**

- Classificar cada alarme ou ocorrência em **real**, **ambiental** ou **indeterminado**, justificando com a fonte consultada.
- Ordenar a fila do dia por prioridade, usando a matriz de criticidade e o histórico de falhas do ativo.
- Consultar a ficha de um ativo e o histórico de falhas por TAG.
- Propor a descrição da ordem de serviço e indicar o procedimento (POP) aplicável.
- Explicar conceitos de manutenção e operação dos ativos cobertos.
- Informar o que fazer quando o dado não existe: dizer que não sabe e encaminhar ao responsável.

**O assistente recusa** (com a frase padrão de recusa do system prompt): (alterar)

- Emitir laudo técnico, ART ou parecer de engenharia com validade legal.
- Liberar, bloquear ou intervir fisicamente em equipamento.
- Dar orientação de segurança elétrica que substitua o procedimento oficial (NR-10 e NR-35).
- Responder sobre assuntos alheios à manutenção (receitas, política, esportes, código de outros projetos).
- Inventar TAG, número de série, modelo, norma, métrica ou histórico que não esteja na base de conhecimento.
- Tratar dados pessoais de terceiros (placas, imagens de ocupantes, dados de empregados) fora do previsto na LGPD.

**Regras de decisão:** o assistente sugere e a decisão é sempre humana. Toda conclusão é rotulada como **fato** (está na base) ou **hipótese** (inferência a validar em campo), com nível de confiança.

---

## 5. Arquitetura da solução

Fluxograma: [`docs/arquitetura.png`](docs/arquitetura.png) *(em breve, entrega da Pessoa 4)*

Fluxo em 8 blocos, do alarme à resposta:

1. **Entrada:** o operador envia um alarme ou um lote de ocorrências, em texto livre ou lista estruturada.
2. **Guardrail de entrada:** valida se o pedido está no escopo e detecta pedido perigoso ou hostil antes de chamar o modelo.
3. **Montagem do contexto:** junta o system prompt, o histórico da conversa, os trechos recuperados da base (RAG) e os dados das tools.
4. **Chamada ao LLM:** o modelo escolhido, com os parâmetros definidos (temperature e max_tokens).
5. **Decisão de tool:** o modelo decide se já tem informação suficiente ou se precisa consultar ativo, histórico ou procedimento.
6. **Execução da tool:** a função devolve o resultado simulado, que volta ao contexto para uma nova chamada.
7. **Guardrail de saída:** verifica se a resposta está no escopo, cita a fonte, não inventou TAG ou métrica e marca hipótese como hipótese.
8. **Resposta e histórico:** entrega no formato definido (classificação, prioridade, ação e rascunho de OS) e grava o turno no histórico, com política de limite de contexto.

*A explicação detalhada de cada bloco entra aqui quando o fluxograma for finalizado.*

---

## 6. Stack e justificativa técnica

Comparação completa em [`docs/decisoes_tecnicas.md`](docs/decisoes_tecnicas.md).

**Caminho gratuito (custo zero):** Qwen 2.5 7B Instruct via Ollama local + ChromaDB local + Sentence Transformers local. Nenhuma etapa depende de API paga ou de chave.

**Modelos (LLM)**

| Critério | Llama 3.1 8B | Qwen 2.5 7B Instruct |
| --- | --- | --- |
| Custo de uso | Gratuito (Ollama local) | Gratuito (Ollama local) |
| Licença | Llama Community License | Apache 2.0 |
| Qualidade em pt-BR | Português oficialmente suportado | Suportado, ainda não testado |
| Janela de contexto | Até 128K tokens (declarado) | Até 128K tokens (declarado) |
| Privacidade | Roda localmente | Roda localmente |
| Integração com Python | Biblioteca `ollama` | Biblioteca `ollama` |
| Maturidade | Ecossistema consolidado | Boa documentação |

**Vector stores**

| Critério | ChromaDB | FAISS |
| --- | --- | --- |
| Custo | Gratuito | Gratuito |
| Licença | Apache 2.0 | MIT |
| Privacidade | Roda localmente | Roda localmente |
| Integração com Python | Sim | Wrappers Python/NumPy |
| Maturidade | Boa documentação | Consolidado (Meta) |
| Metadados | Filtros integrados | Exigem implementação extra |

**Escolha:** Qwen 2.5 7B Instruct + ChromaDB.

**Trade-off aceito:** priorizamos licença permissiva, privacidade local e filtros por metadados integrados. Aceitamos que a qualidade em pt-BR do Qwen ainda não foi validada (plano B: Llama 3.1 8B) e abrimos mão do controle fino do FAISS, que não pesa numa base pequena e simulada.

---

## 7. Base de conhecimento

Inventário completo: [`data/knowledge_base/inventario.md`](data/knowledge_base/inventario.md) *(em breve, entrega da Pessoa 3)*.

Documentos previstos (mínimo de 8):

| Documento | Origem |
| --- | --- |
| Catálogo de ativos | simulado |
| Plano de manutenção preventiva | simulado |
| Histórico de falhas do FSM | simulado |
| Procedimentos operacionais (POPs) | simulado |
| Fichas técnicas dos equipamentos | real + simulado |
| Matriz de criticidade | simulado |
| Glossário de siglas | real |
| Normas e requisitos de segurança (NR-10, NR-35) | real (resumo) |
| Documentos do briefing Motiva | real |
| Boletins de ocorrência / laudos de campo (incluindo condição ambiental) | simulado |

**Uso no RAG:** os documentos são divididos em trechos (chunks) e guardados com metadados para busca filtrada (TAG, tipo de ativo, data, tipo de documento). O tamanho do chunk e a justificativa pela janela de contexto serão definidos pela Pessoa 3.

**O que não entra na base:** dado pessoal de empregado, imagem de ocupante de veículo, placa não anonimizada e dado contratual sensível.

---

## 8. System prompt

Arquivo: [`prompts/system_prompt.md`](prompts/system_prompt.md) *(em breve, entrega da Pessoa 2)*.

Estruturado em 7 blocos (PAPEL, OBJETIVO, ESCOPO, REGRAS, FORMATO, TOM, BORDA), com comentário em cada bloco. A lista de recusas do escopo (seção 4) aparece como regras numeradas no prompt, e o formato de saída segue a classificação real / ambiental / indeterminado.

---

## 9. Eval set

Arquivo: [`evals/sprint1_eval_set.json`](evals/sprint1_eval_set.json) *(em breve, entrega da Pessoa 2)*.

Mínimo de 10 casos, cobrindo as 8 categorias:

| Categoria | Mínimo | O que testa |
| --- | --- | --- |
| factual | 2 | Recupera e cita corretamente um dado da base |
| instrucional | 2 | Procedimento ou geração de texto (ex.: descrição de OS) |
| ambíguo | 1 | Pede clarificação em vez de inventar |
| dado_inexistente | 1 | Admite que não sabe |
| out_of_scope | 1 | Recusa educada e padronizada |
| jailbreak | 1 | Resiste a tentativa de subverter as regras |
| risco_operacional | 1 | Escala e recusa pedido perigoso |
| memoria_contexto | 1 | Mantém coerência com o que foi dito antes |

---

## 10. Tools especificadas

Contrato em JSON Schema: [`tools/tools_spec.json`](tools/tools_spec.json) *(em breve, entrega da Pessoa 3)*.

| Tool | O que faz | Parâmetros |
| --- | --- | --- |
| `consultar_ativo` | Devolve a ficha do ativo: tipo, fabricante, modelo, km, praça, instalação, vida útil e criticidade | `tag` (obrigatório) |
| `consultar_historico_falhas` | Lista falhas em uma janela de tempo: data, sintoma, causa, tempo de reparo e reincidência | `tag`, `janela_meses` (padrão 12), `tipo_falha` (opcional) |
| `abrir_ordem_servico` | Confirma a abertura e devolve o número da OS (simulado) | `tag`, `tipo_intervencao`, `prioridade`, `descricao`, `janela_inicio`, `janela_fim` |
| `consultar_procedimento` | Devolve o passo a passo do POP aplicável, com pré-requisitos de segurança | `tipo_ativo`, `tipo_intervencao` |

---

## 11. Riscos e mitigações

Matriz completa: [`docs/riscos_lgpd.md`](docs/riscos_lgpd.md) *(em breve, entrega da Pessoa 4)*.

| Risco | Impacto | Mitigação | Onde aparece |
| --- | --- | --- | --- |
| Alucinação (inventar TAG, OS ou histórico) | Decisão errada em campo | Regra de não inventar, citar fonte, tools com dado real, admitir "não sei" | Prompt, tools |
| Prompt injection | Quebra das regras | Guardrail de entrada, regra de imutabilidade das instruções | Fluxo, eval (jailbreak) |
| Risco operacional (intervenção energizada ou em via) | Segurança | Recusa obrigatória, encaminhamento ao POP e a profissional habilitado | Prompt, eval |
| Viés na priorização | Atendimento desigual | Critérios explícitos (criticidade, segurança, arrecadação) | Prompt, base |
| LGPD (placas, imagens, dados de empregados) | Legal | Declarar o que não entra na base, anonimizar placas | Base |
| Confidencialidade operacional | Vazamento de dados | Modelo local ou dado simulado | Stack, base |
| Excesso de confiança no modelo | Hipótese tratada como fato | Rotular fato e hipótese, nível de confiança, validação humana | Prompt |
| Contexto estourado | Perda do TAG ou mistura de ativos | Política de histórico e reafirmação do TAG a cada turno | Fluxo |

---

## 12. Limitações conhecidas

- Todos os dados de ativos, histórico e procedimentos são simulados; o assistente real dependeria de a Motiva fornecer a base.
- A classificação é uma sugestão: a decisão de acionar equipe continua sendo humana.
- O escopo cobre quatro famílias de ativos (CFTV, SAT, Callbox e SCA), e não todos os possíveis problemas.

---

## 13. Referências (alterar)

- Briefing Inovação Aberta FIAP — EngMan Motiva (16/09/2026).
- Documento oficial do Challenge Sprint — EngMan Copilot, Prof. Jorge Luiz Gomes, Prompt Engineering & Artificial Intelligence, FIAP.
- Materiais da disciplina, Módulos 1 e 2.
- NR-10 (segurança em instalações elétricas) e NR-35 (trabalho em altura).
