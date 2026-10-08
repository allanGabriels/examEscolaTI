# Testes (Cenário TDD)

cada item abaixo deve virar um teste organizado em pastas em test / java.

| # | Cenário | Tipo |
| --- | --- | --- |
| T1 | Abrir bilhete com placa válida retorna 201, com `id` gerado, `placa`, `entrada` em ISO-8601 com fuso `-03:00` e `status: "aberto"`. | Feliz |
| T2 | Abrir bilhete sem placa retorna 422, com `{"erro": "placa_invalida"}`. | Validação |
| T3 | Abrir bilhete com placa inválida retorna 422, com `{"erro": "placa_invalida"}`. | Validação |
| T4 | Abrir bilhete com `entrada` informada retorna 201 e utiliza o instante informado. | Feliz |
| T5 | Abrir bilhete sem `entrada` retorna 201 e utiliza o instante atual. | Feliz |
| T6 | Abrir bilhete com `entrada` sem fuso retorna 422, com `{"erro": "entrada_invalida"}`. | Validação |
| T7 | Abrir bilhete com `entrada` em formato inválido retorna 422, com `{"erro": "entrada_invalida"}`. | Validação |
| T9 | Encerrar bilhete aberto retorna 200, com `id`, `placa`, `entrada`, `saida`, `minutos` e `valor_centavos`. | Feliz |
| T10 | Encerrar bilhete com exatamente 30 minutos cobra 300 centavos. | Borda |
| T11 | Encerrar bilhete com 31 minutos cobra 450 centavos. | Borda |
| T12 | Encerrar bilhete com 601 minutos limita a cobrança a 6000 centavos. | Borda |
| T13 | Encerrar bilhete retorna `valor_centavos` como número inteiro. | Feliz |
| T14 | Encerrar bilhete inexistente retorna 404, com `{"erro": "bilhete_nao_encontrado"}`. | Erro |
| T15 | Encerrar bilhete já encerrado retorna 409, com `{"erro": "bilhete_ja_encerrado"}`. | Conflito |
| T17 | Listar bilhetes ativos retorna 200 e somente bilhetes com `status: "aberto"`. | Feliz |
| T18 | Listar bilhetes ativos apresenta os mais recentes primeiro. | Feliz |
| T19 | Consultar relatório com data válida retorna 200, com `data`, `total_bilhetes`, `faturamento_centavos` e `tempo_medio_minutos`. | Feliz |
| T20 | Consultar relatório diário calcula o tempo médio somente dos bilhetes encerrados naquele dia. | Feliz |
| T21 | Consultar relatório com tempos de 20 e 21 minutos retorna `tempo_medio_minutos: 21`. | Borda |
| T22 | Consultar relatório com data fora do formato `AAAA-MM-DD` retorna 422, com `{"erro": "data_invalida"}`. | Validação |
| T24 | Cancelar bilhete aberto retorna 200, com `status: "cancelado"`. | Feliz |
| T25 | Cancelar bilhete não gera cobrança, saída ou `valor_centavos`. | Feliz |
| T26 | Cancelar bilhete inexistente retorna 404, com `{"erro": "bilhete_nao_encontrado"}`. | Erro |
| T27 | Cancelar bilhete encerrado retorna 409, com `{"erro": "bilhete_nao_aberto"}`. | Conflito |
| T28 | Cancelar bilhete já cancelado retorna 409, com `{"erro": "bilhete_nao_aberto"}`. | Conflito |
| T29 | Consultar histórico por placa retorna 200 e inclui bilhetes abertos, encerrados e cancelados. | Feliz |
| T30 | Consultar histórico por placa apresenta os bilhetes mais recentes primeiro. | Feliz |
| T31 | Consultar histórico de placa que nunca estacionou retorna 200, com lista vazia. | Borda |
| T32 | Consultar histórico com placa inválida retorna 422, com `{"erro": "placa_invalida"}`. | Validação |
| T34 | Encerrar bilhete com 14 minutos retorna `valor_centavos: 0`. | Borda |
| T35 | Encerrar bilhete com exatamente 15 minutos retorna `valor_centavos: 0`. | Borda |
| T36 | Encerrar bilhete com 16 minutos cobra 300 centavos, sem descontar a tolerância. | Borda |
| T37 | Abrir bilhete para placa que já possui bilhete aberto retorna 409, com `{"erro": "bilhete_em_aberto"}`. | Conflito |
| T38 | Abrir novo bilhete para a mesma placa após encerrar o anterior retorna 201. | Feliz |
| T39 | Abrir novo bilhete para a mesma placa após cancelar o anterior retorna 201. | Feliz |

