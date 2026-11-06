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