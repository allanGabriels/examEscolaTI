# Spec

## Use cases

UC1 — Abrir bilhete
- Endpoint: POST /bilhetes.
- Entrada: placa (string, obrigatória, exatamente 7 caracteres alfanuméricos, com letras maiúsculas); entrada (opcional, ISO-8601 com fuso).
- Critérios de aceite:
  - Quando válido, deve retornar 201 com id gerado, placa, entrada em ISO-8601 com fuso -03:00 e status: "aberto".
  - Quando entrada for informada, deve abrir o bilhete naquele instante.
  - Quando entrada não for informada, deve utilizar o instante atual.
  - Quando a placa estiver ausente ou inválida, deve retornar 422 com {"erro": "placa_invalida"}.
  - Quando a entrada estiver em formato inválido ou sem fuso, deve retornar 422 com {"erro": "entrada_invalida"}.
- Observação: o campo entrada permite testar a cobrança sem esperar o tempo passar.


UC2 — Encerrar bilhete
- Endpoint: POST /bilhetes/{id}/encerramento.
- Entrada: id do bilhete, informado no caminho.
- Critérios de aceite:
  - Quando válido, deve retornar 200 com id, placa, entrada, saida, minutos e valor_centavos.
  - A cobrança deve ocorrer em frações de 15 minutos, conforme FRACAO_MINUTOS, arredondando a quantidade de frações para cima.
  - Quando completar exatamente uma fração, deve cobrar aquela fração. Quando ultrapassar por 1 minuto, deve cobrar a seguinte.
  - O valor da fração deve ser calculado por TARIFA_HORA_CENTAVOS ÷ (60 ÷ FRACAO_MINUTOS), resultando em 150 centavos.
  - O valor cobrado não deve ultrapassar 6000 centavos, conforme TETO_DIARIO_CENTAVOS.
  - O campo valor_centavos deve ser inteiro, sem ponto flutuante.
  - Quando o bilhete não existir, deve retornar 404 com {"erro": "bilhete_nao_encontrado"}.
  - Quando o bilhete já estiver encerrado, deve retornar 409 com {"erro": "bilhete_ja_encerrado"}.


UC3 — Listar bilhetes ativos
- Endpoint: GET /bilhetes/ativos.
- Entrada: nenhuma.
- Critérios de aceite:
  - Deve retornar 200 com uma lista dos bilhetes com status: "aberto".
  - A lista deve apresentar os bilhetes mais recentes primeiro.


UC4 — Consultar relatório diário
- Endpoint: GET /relatorios/diario?data=AAAA-MM-DD.
- Entrada: data da consulta, no formato AAAA-MM-DD.
- Critérios de aceite:
  - Quando válido, deve retornar 200 com data, total_bilhetes, faturamento_centavos e tempo_medio_minutos.
  - O tempo médio deve considerar apenas os bilhetes encerrados naquele dia.
  - O tempo médio deve ser arredondado para um número inteiro, com 0,5 arredondado para cima.
  - Quando a data estiver fora do formato exigido, deve retornar 422 com {"erro": "data_invalida"}.


UC5 — Cancelar bilhete
- Endpoint: POST /bilhetes/{id}/cancelamento.
- Entrada: id do bilhete, informado no caminho.
- Critérios de aceite:
  - Quando válido, deve retornar 200 com status: "cancelado".
  - Somente bilhetes abertos podem ser cancelados.
  - O cancelamento não deve gerar cobrança, saída ou valor_centavos.
  - Quando o bilhete não existir, deve retornar 404 com {"erro": "bilhete_nao_encontrado"}.
  - Quando o bilhete não estiver aberto, deve retornar 409 com {"erro": "bilhete_nao_aberto"}.


UC6 — Consultar histórico por placa
- Endpoint: GET /bilhetes?placa=ABC1D23.
- Entrada: placa, informada no parâmetro de consulta.
- Critérios de aceite:
  - Quando válido, deve retornar 200 com todos os bilhetes daquela placa, incluindo abertos, encerrados e cancelados.
  - A lista deve apresentar os bilhetes mais recentes primeiro.
  - Quando a placa nunca tiver estacionado, deve retornar uma lista vazia.
  - Quando a placa for inválida, deve retornar 422 com {"erro": "placa_invalida"}.


UC7 — Aplicar tolerância gratuita
- Aplicação: cálculo da cobrança no encerramento do bilhete.
- Entrada: duração do bilhete.
- Critérios de aceite:
  - Quando a duração for menor ou igual a 15 minutos, conforme TOLERANCIA_MINUTOS, deve retornar valor_centavos: 0.
  - Quando ultrapassar a tolerância, mesmo por 1 minuto, deve cobrar o período completo desde o primeiro minuto, seguindo as regras de fração e teto diário.
  - A tolerância não deve ser descontada do tempo cobrado.


UC8 — Permitir somente um bilhete aberto por placa
- Aplicação: abertura de bilhete em POST /bilhetes.
- Entrada: placa informada na abertura.
- Critérios de aceite:
  - Antes de abrir um bilhete, deve verificar se a placa já possui um bilhete aberto.
  - Quando existir um bilhete aberto para a placa, deve retornar 409 com {"erro": "bilhete_em_aberto"}.
  - Depois de encerrar ou cancelar o bilhete, deve permitir a abertura de outro para a mesma placa.

  ### Variáveis
  TARIFA_HORA_CENTAVOS = 600
  FRACAO_MINUTOS = 15
  TETO_DIARIO_CENTAVOS = 6000
  PORTA_SERVICO = 8005
  TOLERANCIA_MINUTOS = 15
