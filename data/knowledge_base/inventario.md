# Padronização de dados: EngMan Copilot (Recorte B)

## 1. Formato da TAG

Formato: `TIPO-RODOVIA-KMXXX-NN`

| Parte | Regra | Exemplo |
|---|---|---|
| TIPO | `CFTV`, `SAT`, `CBX` ou `SCA` | `CFTV` |
| RODOVIA | Código fictício `R01` (nunca usar nome de rodovia real) | `R01` |
| KMXXX | `K` + km com 3 dígitos | `K412` |
| NN | Sequencial de 2 dígitos (sempre `01` neste projeto) | `01` |

Exemplo completo: `CFTV-R01-K412-01`

Expressão regular (para o parâmetro `tag` do tools_spec.json):

```
^(CFTV|SAT|CBX|SCA)-R01-K[0-9]{3}-[0-9]{2}$
```

## 2. Trechos da rodovia fictícia R01 e estação SCA de cada trecho

| Trecho | Faixa de km | Estação SCA |
|---|---|---|
| A | 380 a 394 | SCA-R01-K388-01 |
| B | 395 a 409 | SCA-R01-K402-01 |
| C | 410 a 424 | SCA-R01-K416-01 |
| D | 425 a 440 | SCA-R01-K432-01 |

## 3. As 30 TAGs do catálogo de ativos

### CFTV: 12 câmeras

| # | TAG | Trecho |
|---|---|---|
| 1 | CFTV-R01-K382-01 | A |
| 2 | CFTV-R01-K390-01 | A |
| 3 | CFTV-R01-K398-01 | B |
| 4 | CFTV-R01-K405-01 | B |
| 5 | CFTV-R01-K408-01 | B |
| 6 | CFTV-R01-K412-01 | C |
| 7 | CFTV-R01-K418-01 | C |
| 8 | CFTV-R01-K422-01 | C |
| 9 | CFTV-R01-K426-01 | D |
| 10 | CFTV-R01-K430-01 | D |
| 11 | CFTV-R01-K435-01 | D |
| 12 | CFTV-R01-K438-01 | D |

### SAT: 8 sensores de tráfego

| # | TAG | Trecho |
|---|---|---|
| 13 | SAT-R01-K385-01 | A |
| 14 | SAT-R01-K393-01 | A |
| 15 | SAT-R01-K400-01 | B |
| 16 | SAT-R01-K407-01 | B |
| 17 | SAT-R01-K413-01 | C |
| 18 | SAT-R01-K421-01 | C |
| 19 | SAT-R01-K428-01 | D |
| 20 | SAT-R01-K436-01 | D |

### CBX: 6 telefones SOS (Callbox)

| # | TAG | Trecho |
|---|---|---|
| 21 | CBX-R01-K384-01 | A |
| 22 | CBX-R01-K401-01 | B |
| 23 | CBX-R01-K406-01 | B |
| 24 | CBX-R01-K414-01 | C |
| 25 | CBX-R01-K427-01 | D |
| 26 | CBX-R01-K437-01 | D |

### SCA: 4 estações meteorológicas

| # | TAG | Trecho |
|---|---|---|
| 27 | SCA-R01-K388-01 | A |
| 28 | SCA-R01-K402-01 | B |
| 29 | SCA-R01-K416-01 | C |
| 30 | SCA-R01-K432-01 | D |

## 4. TAG inexistente (reservada para o caso de teste `dado_inexistente`)

`CFTV-R01-K999-01`

Regra: esta TAG NUNCA pode aparecer no catálogo, no histórico, nos boletins nem nos alarmes. Entregar ao QA.

## 5. Vocabulários fechados (enums)

Sempre sem acento e em minúsculas, para evitar problema de encoding no JSON.

| Campo | Valores permitidos |
|---|---|
| tipo_ativo | `cftv`, `sat`, `cbx`, `sca` |
| tipo_intervencao | `corretiva`, `preventiva`, `inspecao`, `cbm` |
| prioridade | `baixa`, `media`, `alta`, `critica` |
| criticidade (do ativo) | `baixa`, `media`, `alta` |
| classificacao_alarme | `real`, `ambiental`, `indeterminado` |

Correspondência TIPO da TAG e tipo_ativo: CFTV = `cftv`, SAT = `sat`, CBX = `cbx`, SCA = `sca`.

## 6. Fabricantes e modelos fictícios

Todos inventados. Não corresponde a produto real.

| Tipo | Fabricante (fictício) | Modelo (fictício) |
|---|---|---|
| CFTV | VisionTec | PTZ-200 |
| CFTV | OptiRoad | OR-PTZ-300 |
| SAT | TrafficSense | RD-10 (radar) |
| SAT | FluxoMax | FM-20 (radar) |
| CBX | SOS-Via | CB-100 |
| SCA | ClimaVia | WS-50 |

# Inventário da Base de Conhecimento: EngMan Copilot

Recorte B (Operação e Priorização de Campo) · Persona principal: operador do CCO · Persona secundária: coordenador de manutenção.
Ativos cobertos: câmeras CFTV, sensores de tráfego (SAT), telefones SOS (Callbox) e estações meteorológicas (SCA).

> Todos os dados desta base são **SIMULADOS ou REAIS DE FONTE PÚBLICA/BRIEFING**. A Motiva não libera base real para trabalho acadêmico. Nenhum dado simulado deve ser apresentado como real.

---

## 1. Legenda de status

| Status | Significado |
|---|---|
| **REAL** | Veio do briefing da Motiva, do enunciado do Challenge ou de norma pública. |
| **PREVISTO** | Não existe neste repositório. A Motiva forneceria em produção (por exemplo, histórico real do FSM, alarmes reais do CCO, leituras reais do SCA). Está documentado para que a solução possa ser implantada depois. |
| **SIMULADO** | Criado pelo grupo com dados fictícios plausíveis. Pode ser gerado com apoio de IA. |

---

## 2. Tabela-resumo

| ID | Documento | Status | Volume previsto | Entrega via |
|---|---|---|---|---|
| D01 | Catálogo de ativos | SIMULADO (versão real: PREVISTO) | 30 ativos | tool `consultar_ativo` |
| D02 | Plano de manutenção preventiva | SIMULADO | 1 documento | RAG |
| D03 | Histórico de falhas do FSM | SIMULADO (versão real: PREVISTO) | 60 registros | tool `consultar_historico_falhas` |
| D04 | POPs (procedimentos operacionais) | SIMULADO | 4 POPs | tool `consultar_procedimento` |
| D05 | Fichas técnicas dos 4 tipos de ativo | REAL + SIMULADO | 4 fichas | RAG |
| D06 | Matriz de criticidade | SIMULADO | 1 matriz | RAG |
| D07 | Regras de triagem de alarmes | SIMULADO | 1 documento | RAG |
| D08 | Registro de alarmes do CCO (lotes de exemplo) | SIMULADO (versão real: PREVISTO) | 3 lotes | entrada do usuário (não indexado) |
| D09 | Glossário de siglas | REAL | 1 documento | RAG |
| D10 | Normas e requisitos de segurança | REAL (resumo) | 1 documento | RAG |
| D11 | Documentos do briefing Motiva | REAL | 1 documento | RAG |
| D12 | Boletins de ocorrência e laudos de campo | SIMULADO | 12 boletins | RAG |
| D13 | Leituras das estações meteorológicas (SCA) | SIMULADO (versão real: PREVISTO). | 4 estações x 7 dias | RAG |

Total: 13 documentos confirmados (mínimo exigido: 8)

---

## 3. Padronização (vale para todos os documentos e tools)

### 3.1 Formato da TAG

`TIPO-RODOVIA-KMXXX-NN`, por exemplo `CFTV-R01-K412-01`. A rodovia `R01` é fictícia.

Regex de validação:

```
^(CFTV|SAT|CBX|SCA)-R01-K[0-9]{3}-[0-9]{2}$
```

### 3.2 Trechos e estações SCA

| Trecho | Faixa de km | Estação SCA |
|---|---|---|
| A | 380 a 394 | SCA-R01-K388-01 |
| B | 395 a 409 | SCA-R01-K402-01 |
| C | 410 a 424 | SCA-R01-K416-01 |
| D | 425 a 440 | SCA-R01-K432-01 |

### 3.3 TAGs do catálogo (30 ativos)

| Tipo | TAGs |
|---|---|
| CFTV (12) | CFTV-R01-K382-01, K390-01, K398-01, K405-01, K408-01, K412-01, K418-01, K422-01, K426-01, K430-01, K435-01, K438-01 |
| SAT (8) | SAT-R01-K385-01, K393-01, K400-01, K407-01, K413-01, K421-01, K428-01, K436-01 |
| CBX (6) | CBX-R01-K384-01, K401-01, K406-01, K414-01, K427-01, K437-01 |
| SCA (4) | SCA-R01-K388-01, K402-01, K416-01, K432-01 |

TAG reservada como **inexistente** (caso de teste `dado_inexistente`): `CFTV-R01-K999-01`. Nunca pode aparecer em nenhum documento.

### 3.4 Vocabulários fechados (enums)

| Campo | Valores |
|---|---|
| tipo_ativo | `cftv`, `sat`, `cbx`, `sca` |
| tipo_intervencao | `corretiva`, `preventiva`, `inspecao`, `cbm` |
| prioridade | `baixa`, `media`, `alta`, `critica` |
| criticidade | `baixa`, `media`, `alta` |
| classificacao_alarme | `real`, `ambiental`, `indeterminado` |

### 3.5 Fabricantes e modelos (fictícios)

| Tipo | Fabricante | Modelo |
|---|---|---|
| CFTV | VisionTec | PTZ-200 |
| CFTV | OptiRoad | OR-PTZ-300 |
| SAT | TrafficSense | RD-10 |
| SAT | FluxoMax | FM-20 |
| CBX | SOS-Via | CB-100 |
| SCA | ClimaVia | WS-50 |

---

## 4. Descrição de cada documento

### D01. Catálogo de ativos
- **Status:** SIMULADO. Versão real: PREVISTO (cadastro de ativos da Motiva).
- **Descrição:** cadastro de todos os ativos cobertos, um por TAG.
- **Campos:** tag, tipo_ativo, fabricante, modelo, km, trecho, instalacao (data), vida_util_anos, criticidade (enum), estacao_sca_do_trecho.
- **Volume previsto:** 30 linhas (12 CFTV, 8 SAT, 6 CBX, 4 SCA).
- **Formato:** CSV.
- **Fonte única de verdade para:** dados cadastrais e criticidade de cada ativo.
- **Entrega ao assistente:** exclusivamente pela tool `consultar_ativo`. Não é indexado no RAG.
- **Metadados de filtro:** tag, tipo_ativo, trecho.

### D02. Plano de manutenção preventiva
- **Status:** SIMULADO.
- **Descrição:** tarefas preventivas por tipo de ativo, com periodicidade e condição de execução.
- **Campos:** tipo_ativo, tarefa, periodicidade_dias, responsavel, tempo_estimado_h, recursos, condicao_execucao.
- **Volume previsto:** 1 documento, cerca de 12 tarefas (3 por tipo de ativo).
- **Formato:** Markdown.
- **Entrega ao assistente:** RAG.
- **Metadados de filtro:** tipo_ativo, tipo_documento = `plano_preventivo`.

### D03. Histórico de falhas do FSM
- **Status:** SIMULADO. Versão real: PREVISTO (exportação do FSM da Motiva).
- **Descrição:** falhas passadas por TAG, usadas para identificar reincidência e padrão de falso positivo.
- **Campos:** id_falha, tag, data, sintoma, causa_identificada, tempo_reparo_h, peca_trocada, reincidente (sim/não), condicao_ambiental_no_momento (chuva, neblina, glare, seca, outra), classificacao (`real` ou `ambiental`).
- **Volume previsto:** 60 registros, com 3 a 5 ativos de histórico mais rico (candidatos ao bônus de personas sintéticas).
- **Formato:** CSV.
- **Fonte única de verdade para:** falhas passadas.
- **Entrega ao assistente:** exclusivamente pela tool `consultar_historico_falhas`. Não é indexado no RAG.
- **Metadados de filtro:** tag, tipo_ativo, data.

### D04. POPs (procedimentos operacionais padrão)
- **Status:** SIMULADO.
- **Descrição:** passo a passo de inspeção e atendimento, sempre com pré-requisitos de segurança.
- **Lista prevista (4):**
  1. POP-01 Limpeza e inspeção de lente de câmera CFTV
  2. POP-02 Verificação de comunicação de ativo (link, CCO)
  3. POP-03 Teste de funcionamento de Callbox (botão, áudio, bateria)
  4. POP-04 Checagem de sensor SAT e de estação SCA
- **Campos por POP:** id_pop, tipo_ativo, tipo_intervencao, pre_requisitos_seguranca, passos_numerados, criterio_de_conclusao.
- **Formato:** Markdown (um arquivo por POP).
- **Fonte única de verdade para:** procedimentos.
- **Entrega ao assistente:** exclusivamente pela tool `consultar_procedimento`. Não é indexado no RAG.
- **Metadados de filtro:** tipo_ativo, tipo_intervencao.

### D05. Fichas técnicas dos equipamentos
- **Status:** REAL + SIMULADO. A estrutura e a função de cada ativo vêm do briefing (real). Fabricante, modelo e especificações numéricas são fictícios (simulado).
- **Descrição:** uma ficha por tipo de ativo: função, componentes, falhas típicas, sensibilidade a condições ambientais.
- **Campos:** tipo_ativo, funcao, componentes, falhas_tipicas, sensibilidade_ambiental, especificacoes (simuladas).
- **Volume previsto:** 4 fichas (CFTV, SAT, CBX, SCA).
- **Formato:** Markdown.
- **Entrega ao assistente:** RAG.
- **Metadados de filtro:** tipo_ativo, tipo_documento = `ficha_tecnica`.

### D06. Matriz de criticidade
- **Status:** SIMULADO.
- **Descrição:** regra que liga cada tipo de ativo a seus impactos, base da priorização auditável.
- **Campos:** tipo_ativo, impacto_seguranca_viaria, impacto_arrecadacao, impacto_disponibilidade (cada um de 1 a 3), criticidade_resultante.
- **Volume previsto:** 1 matriz (4 linhas, uma por tipo de ativo, mais regras de desempate).
- **Formato:** Markdown.
- **Entrega ao assistente:** RAG.
- **Metadados de filtro:** tipo_documento = `matriz_criticidade`.
- **Observação:** a criticidade por TAG (D01) deve ser coerente com esta matriz.

### D07. Regras de triagem de alarmes
- **Status:** SIMULADO.
- **Descrição:** regras que dizem como classificar um alarme como `real`, `ambiental` ou `indeterminado`, e como atribuir prioridade. É o documento que diferencia o Recorte B.
- **Campos:** id_regra, tipo_ativo, condicao (sintoma + condição ambiental), classificacao, prioridade, acao_recomendada.
- **Exemplos de regra:** (a) alarme de perda de imagem em CFTV + chuva forte no trecho + link ativo = `ambiental`, prioridade `baixa`, monitorar; (b) alarme sem link + sem chuva = `real`, prioridade `alta`; (c) dados insuficientes = `indeterminado`.
- **Volume previsto:** 1 documento com 15 a 20 regras.
- **Formato:** Markdown.
- **Entrega ao assistente:** RAG.
- **Metadados de filtro:** tipo_ativo, tipo_documento = `regras_triagem`.

### D08. Registro de alarmes do CCO (lotes de exemplo)
- **Status:** SIMULADO. Versão real: PREVISTO (fila de alarmes real do CCO).
- **Descrição:** lotes de alarmes de um dia, como o operador os colaria no chat.
- **Campos:** id_alarme, tag, horario, tipo_alarme, severidade_original, status.
- **Volume previsto:** 3 lotes (por exemplo, 30, 15 e 10 alarmes), com mistura de alarmes reais, ambientais e ambíguos.
- **Formato:** CSV ou texto colado.
- **Entrega ao assistente:** entrada do usuário a cada conversa. **Não é indexado no RAG**, porque é dado do dia, não conhecimento.
- **Uso no projeto:** serve de base para os casos do eval set.

### D09. Glossário de siglas
- **Status:** REAL (enunciado e briefing).
- **Conteúdo:** CFTV, PMV, SAT, SCA, CCO, CBM, RCM, WCM, FSM, RGA, LDA, lambda, MTBF, MTTR, WIM, OCR, EPI, POP, OS, TAG.
- **Campos:** sigla, significado, observação.
- **Volume previsto:** 1 documento, cerca de 25 entradas.
- **Formato:** Markdown.
- **Entrega ao assistente:** RAG.
- **Metadados de filtro:** tipo_documento = `glossario`.

### D10. Normas e requisitos de segurança
- **Status:** REAL (resumo de norma pública).
- **Conteúdo:** pontos aplicáveis da NR-10 (eletricidade), NR-35 (trabalho em altura) e regras gerais de trabalho em via. É um **resumo**, não o texto integral da norma.
- **Campos:** norma, tema, requisito_resumido, relevancia_para_ativo.
- **Volume previsto:** 1 documento.
- **Formato:** Markdown.
- **Entrega ao assistente:** RAG.
- **Metadados de filtro:** tipo_documento = `norma_seguranca`.
- **Observação:** o assistente cita como referência e encaminha ao procedimento oficial. Nunca substitui a norma.

### D11. Documentos do briefing Motiva
- **Status:** REAL.
- **Conteúdo:** contexto da empresa, ondas de inovação, descrição dos Temas 1, 2 e 2.1 (em especial falso positivo por glare, chuva e neblina).
- **Volume previsto:** 1 documento resumido.
- **Formato:** Markdown.
- **Entrega ao assistente:** RAG.
- **Metadados de filtro:** tipo_documento = `briefing`.

### D12. Boletins de ocorrência e laudos de campo
- **Status:** SIMULADO.
- **Descrição:** texto livre do que o técnico relatou ao atender, incluindo a condição ambiental.
- **Campos:** id_boletim, id_falha (referência ao D03), tag, data, relato_tecnico, condicao_ambiental, acao_executada.
- **Volume previsto:** 12 boletins.
- **Formato:** Markdown ou texto.
- **Entrega ao assistente:** RAG.
- **Metadados de filtro:** tag, tipo_ativo, data, tipo_documento = `boletim`.
- **Regra de consistência:** todo `id_falha` citado precisa existir no D03 com os mesmos dados (data, tag, causa). Os boletins só acrescentam narrativa.

### D13. Leituras das estações meteorológicas (SCA) (condicional)
- **Status:** SIMULADO. Versão real: PREVISTO (telemetria real do SCA).
- **Descrição:** leituras para cruzar alarme com clima no momento.
- **Campos:** estacao_tag, horario, chuva_mm_h, visibilidade_m, vento_km_h, temperatura_c.
- **Volume previsto:** 4 estações x 7 dias, a cada hora.
- **Formato:** CSV.
- **Fonte única de verdade para:** condição ambiental.
- **Entrega ao assistente:** pelo RAG, em trechos por estação e por dia. Metadados de filtro: estacao_tag e data.
---

## 5. Regra de fonte única de verdade

| Dado | Única fonte | Nunca duplicar em |
|---|---|---|
| Cadastro e criticidade de cada TAG | D01 via `consultar_ativo` | RAG, boletins, fichas |
| Falhas passadas | D03 via `consultar_historico_falhas` | RAG (boletins só narram, sem divergir) |
| Procedimentos | D04 via `consultar_procedimento` | RAG |
| Condição ambiental | D13 via RAG | tools |
| Regras, normas, glossário, briefing, fichas, plano, matriz, boletins | RAG | tools |

Se dois documentos divergirem, o assistente usa a fonte da tabela acima e informa a divergência ao operador.

---

## 6. Decisões técnicas da base

### 6.1 Tamanho do chunk
- **Faixa adotada:** 300 a 500 tokens por trecho, com sobreposição (overlap) de 10 a 15%.
- **Por que essa faixa (Aula 02, janela de contexto):** documentos técnicos como regras de triagem e normas precisam de trechos coesos, e uma regra cortada ao meio perde o sentido. Trechos maiores ocupam muito a janela e deixam menos espaço para histórico e alarmes.
- **Por que overlap:** evita perder a condição de uma regra que fica na fronteira entre dois trechos.
- **Regra especial:** cada regra do D07 e cada ficha do D05 deve caber em um único chunk. Prefira dividir por título de seção, não por contagem de tokens.
- **Alarmes (D08) não sofrem chunking:** o lote entra inteiro pela entrada do usuário.

### 6.2 Estimativa de orçamento de tokens (valores aproximados)

Janela de trabalho assumida: 8.000 tokens, que é conservadora para modelos locais.

| Componente | Tokens estimados |
|---|---|
| System prompt | 1.500 |
| Histórico (últimas mensagens ou resumo) | 2.000 |
| Trechos recuperados no RAG (4 x 400) | 1.600 |
| Lote de alarmes (30 alarmes x ~40) | 1.200 |
| Resultados de tools | 800 |
| Reserva para a resposta | 900 |
| **Total** | **8.000** |

Estratégia de recorte: manter as últimas 6 mensagens e resumir as anteriores; reafirmar a TAG ativa a cada turno. Estas são estimativas a validar nos testes das próximas Sprints.

### 6.3 Metadados guardados em cada trecho
`tag` (quando houver), `tipo_ativo`, `data`, `tipo_documento`, `trecho_km` e `fonte` (id do documento, por exemplo D07). Permitem busca filtrada (por exemplo, só regras de triagem de CFTV) e citação da origem na resposta.

### 6.4 O que NÃO entra na base (LGPD)

| Item excluído | Motivo | Tratamento |
|---|---|---|
| Imagens de CFTV com ocupantes de veículos ou pessoas | Dado pessoal | Nunca incluir. Só descrições textuais do estado do equipamento |
| Placas de veículos | Dado pessoal de terceiro (OCR) | Fora da base. Se aparecer em um laudo, substituir por `[PLACA REMOVIDA]` |
| Gravações de áudio e geolocalização de quem acionou o Callbox | Dado pessoal sensível | Fora da base. Só o estado do equipamento (botão, áudio, bateria) |
| Dados de empregados (nome, matrícula, telefone) | Dado pessoal | Boletins usam apenas o cargo ("técnico de campo") |
| Dados contratuais sensíveis (valores, SLAs, penalidades) | Confidencialidade | Fora da base |
| Qualquer dado real de histórico, arrecadação ou alarmes da Motiva | Confidencialidade operacional | Usar apenas dado simulado, identificado como tal |

Nenhum dado desta base é enviado a modelo externo sem justificativa. A preferência é por modelo local, como documentado na comparação de stack do README.

---

## 7. Mapa de uso no RAG e nas tools

| Pergunta típica do operador | Fonte |
|---|---|
| "Qual a criticidade da câmera do km 412?" | `consultar_ativo` (D01) |
| "Essa câmera já teve esse problema?" | `consultar_historico_falhas` (D03) |
| "Isso é falha real ou chuva?" | Regras (D07 no RAG) + leituras das estações (D13 no RAG) ou clima informado pelo operador |
| "Que POP aplicar?" | `consultar_procedimento` (D04) |
| "Abra a OS" | `abrir_ordem_servico` (simulada) |
| "O que significa CBM?" | Glossário (D09) |
| "Posso abrir o painel energizado?" | Normas (D10) + recusa e encaminhamento |

---

## 8. Limitações conhecidas
- Todo o histórico e os alarmes são simulados, então os padrões de falso positivo são plausíveis, mas não estatisticamente reais.
- Com dados reais da Motiva (histórico do FSM, alarmes do CCO e telemetria do SCA), as regras de triagem (D07) precisariam ser recalibradas.
- O catálogo cobre uma rodovia fictícia de 4 trechos, e a escala real é muito maior.
