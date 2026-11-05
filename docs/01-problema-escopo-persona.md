Problema, escopo e persona
1. Problema

O CCO e a coordenação de manutenção da Motiva Rodovias recebem, ao longo do dia, um volume de alarmes e ocorrências maior do que conseguem investigar um a um,
e não há um filtro que separe o que exige ação de campo do que não exige.

Na prática, o mesmo conjunto de alarmes mistura causas que pedem respostas opostas e ainda precisa ser ordenado:

Falha de ativo: o equipamento realmente quebrou ou degradou e precisa de manutenção.
Condição ambiental: o alarme veio de chuva, neblina ou glare solar (reflexo do sol), e o equipamento está bem; não exige equipe de campo.
Ordem de prioridade: entre as falhas reais, é preciso decidir quem é atendido primeiro e redigir a descrição da ordem de serviço de cada uma.

Sem essa triagem, o risco é de dois tipos: mandar equipe a campo sem necessidade, ou demorar para atender o que é crítico. Decidir isso depende de consultar o cadastro do ativo, o histórico de falhas e o procedimento aplicável, o que hoje exige procurar em fontes separadas sob pressão de tempo.

Tema do Recorte B, conforme o enunciado: dado um conjunto de alarmes e ocorrências do dia, decidir o que é de fato falha de ativo, o que é condição ambiental (chuva, neblina, glare solar) e qual a ordem de prioridade dos atendimentos, propondo a descrição da ordem de serviço.

2. Objetivos

Objetivo geral: projetar um assistente de IA generativa que apoie o operador do CCO e o coordenador de manutenção a triar alarmes e ocorrências do dia, sugerindo a classificação de cada evento (falha de ativo ou condição ambiental), a ordem de prioridade dos atendimentos e a descrição da ordem de serviço, sempre com base na base de conhecimento do projeto e sem inventar dados.

Objetivos específicos:

Classificar cada alarme ou ocorrência em falha de ativo ou condição ambiental (chuva, neblina, glare solar), justificando a classificação com a fonte consultada.
Priorizar a fila do dia usando a matriz de criticidade dos ativos e o histórico de falhas.
Propor a descrição da ordem de serviço de cada falha de ativo e indicar o procedimento aplicável.
Declarar quando não há dado suficiente, em vez de responder por suposição.
Resistir a pedidos fora de escopo e a tentativas de contornar as regras.

Critérios de sucesso desta sprint (documentação):

Os entregáveis do enunciado existem e contam a mesma história.
Prompt, tools, Eval Set, base de conhecimento, fluxograma e riscos usam o mesmo recorte, a mesma persona e os mesmos ativos.
Todo dado fictício está identificado como simulado.
3. Escopo
Dentro do escopo	Fora do escopo
Triagem de alarmes e ocorrências do dia (Recorte B)	Diagnóstico técnico profundo de causa raiz (Recorte A)
Consulta de ativo, histórico de falhas e procedimento	Qualquer ação automática no campo ou nos sistemas da Motiva
Sugestão de prioridade e descrição da ordem de serviço	Decisão final de acionar equipe, que continua sendo humana
Respostas em português, curtas e com fonte citada	Previsão de falhas, otimização de rotas e escalas de equipe
Documentação, planejamento e especificação (Sprint 01)	Código, notebooks, API keys e protótipo funcional (não exigidos na Sprint 01)

Premissas

A base de conhecimento será montada pelo grupo, com dados reais quando houver e simulados quando não houver, sempre rotulados.
O assistente sugere; o operador ou o coordenador decide.
A solução precisa respeitar a LGPD; dados pessoais de usuários e de equipes não entram na base.

Restrições

Entrega em arquivo .txt com nomes completos, RMs, turma e link público do GitHub.
GitHub público e organizado, com README.
4. Persona principal

A persona principal é o operador do CCO; o coordenador de manutenção é usuário secundário. O nome e os detalhes abaixo são fictícios, criados para orientar o prompt e os testes.

Campo	Descrição
Nome (fictício)	Rafael, operador do CCO
Papel	Monitora alarmes e ocorrências em tempo real e decide o que escalar
Contexto	Turno com muitos eventos simultâneos, pouco tempo por evento, comunicação com equipes de campo
Dores	Alarmes repetidos; dificuldade de separar falha real de condição ambiental (chuva, neblina, glare); consulta a várias fontes; medo de deixar passar o crítico
O que precisa	Classificação rápida, prioridade clara, procedimento aplicável e rascunho de ordem de serviço
O que não quer	Resposta longa, dado inventado, decisão tomada por ele
Sucesso	Fila do dia ordenada em poucos minutos, com motivo e fonte para cada item

Usuário secundário: o coordenador de manutenção usa a mesma triagem para decidir quais ocorrências viram atividade de campo e em que ordem.

5. Subconjunto de ativos

Quatro famílias de ativos típicas de rodovia, para que a base, as tools e os testes fiquem consistentes e factíveis. Os identificadores e dados de cada ativo são simulados.

Família de ativo	Por que entra	Exemplo de alarme/ocorrência	Origem dos dados
Câmeras de CFTV	Imagem afetada por neblina, chuva e glare solar sem defeito do equipamento	Imagem ilegível ao nascer do sol (glare) ou na neblina	Simulado
Sensores de tráfego (radar/laço)	Detectam lentidão e parada; chuva e neblina mudam o tráfego e geram alarme sem falha	Velocidade zero em trecho com neblina (condição ambiental ou sensor travado?)	Simulado
Telefones de emergência (SOS)	Impacto direto na segurança	Poste SOS sem comunicação	Simulado
Estações meteorológicas/sensores	Fonte de dados para confirmar chuva e neblina e descartar falha	Visibilidade reduzida por neblina	Simulado

Regras para os dados

Todo ativo, histórico e procedimento fictício recebe a marca "SIMULADO" no inventário e nos arquivos.
Se algum dado real da Motiva for fornecido pelo desafio, ele entra como "REAL" e os demais como "PREVISTO" ou "SIMULADO".
Nenhum dado pessoal entra na base.
