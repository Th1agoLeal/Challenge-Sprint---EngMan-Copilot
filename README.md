# EngMan Copilot — Operação e Priorização de Campo

Challenge Sprint FIAP × Motiva Rodovias · Ciência da Computação · Sprint 01 — 2026.2

## Equipe
| Nome completo | RM | Frente |
| --- | --- | --- |
| Thiago Nascimento de Sá | 575074 | Planejamento e documentação |
| Lucca Vitor Fustinoni Camargo| 575383 | Prompt e avaliação |
| Erick Vieiro Martins | 575683 | Base de conhecimento e ferramentas |
| Miguel Souza Nunes | 576617 | Arquitetura, stack e segurança |

Turma: 1CCPS

## O que é
Assistente de IA generativa que apoia o operador do CCO e o coordenador de manutenção na triagem de alarmes e ocorrências do dia: separa falha de ativo de condição ambiental (chuva, neblina, glare solar), define a ordem de prioridade dos atendimentos e propõe a descrição da ordem de serviço. Responde apenas com base na base de conhecimento do projeto e não inventa dados.

## Recorte escolhido
Recorte B — Operação e Priorização de Campo.

## Persona e ativos
- Persona principal: operador do CCO (secundário: coordenador de manutenção).
- Ativos considerados: câmeras CFTV, sensores de tráfego, telefones SOS, estações meteorológicas.
- Todos os dados de ativos, histórico e procedimentos são simulados, salvo indicação em contrário.

## Escopo da Sprint 01
Documentação e planejamento. Não há código nem protótipo funcional nesta sprint.

## Entregáveis
| Entregável | Arquivo |
| --- | --- |
| Problema, escopo e persona | docs/01-problema-escopo-persona.md |
| System Prompt (7 blocos) | prompts/system_prompt.md |
| Eval Set (10+ testes) | evals/eval_set.md |
| Inventário da base (8+ fontes) | knowledge_base/inventario.md |
| Comparação da stack | docs/02-comparacao-stack.md |
| Tools em JSON Schema | tools/tools_schema.json |
| Fluxograma (8 blocos) | docs/fluxograma |
| Matriz de riscos (8+) | docs/03-matriz-de-riscos.md |

## Limitações e LGPD
O assistente apenas sugere; a decisão é humana. Nenhum dado pessoal é usado na base.
