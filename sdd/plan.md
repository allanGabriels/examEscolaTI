# Plan:
## Decisões

IDs sequenciais simples.
Porta padrão: 8080 {localhost:8080}

### Stack

* Java 25.0.4, Spring Boot 4.1.1, PostgreSQL 18. 


## Arquitetura

Estrutura dos arquivos da API:

```
model      # Modelos que representam as entidades e os dados do domínio.
service    # Camada responsável pelas regras de negócio e operações do sistema.
dto        # Objetos de transferência de dados, com campos específicos para entrada e saída.
controller # Camada que recebe requisições, aciona os serviços e retorna respostas.
repository # Camada responsável pelo acesso e pela persistência dos dados.
```

