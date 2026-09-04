# Pzaas — Serviço de Catálogo/Cardápio

Serviço responsável pelo gerenciamento e disponibilização do cardápio da pizzaria no projeto **Pzaas (Pizza as a Service)**.

## Tecnologias

* n8n
* Redis
* Redis Cloud
* HTTP/JSON

---

# API

## GET `240081/197375/v1/menu`

Retorna as pizzas disponíveis no cardápio.

### Headers

| Header         | Obrigatório | Descrição                                 |
| -------------- | ----------- | ----------------------------------------- |
| `Content-Type` | Sim         | Deve ser `application/json`               |
| `x-api-key`    | Sim         | turma2026                                 |

### Resposta de sucesso

**200 OK**

```json
{
  "pizzas": [
    {
      "id": 1,
      "nome": "Pizza de Mussarela",
      "descricao": "Pizza com molho de tomate, mussarela, tomate, orégano e azeitona.",
      "ingredientes": [
        "molho de tomate",
        "mussarela",
        "tomate",
        "orégano",
        "azeitona"
      ],
      "preco": 0,
      "emPromocao": false,
      "precoPromocional": null,
      "disponivel": true
    },
    {
      "id": 2,
      "nome": "Pizza de Calabresa Acebolada",
      "descricao": "Pizza com molho de tomate, calabresa, cebola roxa, orégano e azeitona.",
      "ingredientes": [
        "molho de tomate",
        "calabresa",
        "cebola roxa",
        "orégano",
        "azeitona"
      ],
      "preco": 0,
      "emPromocao": false,
      "precoPromocional": null,
      "disponivel": true
    }
  ]
}
```

---

## GET `240081/197375/v1/health`

Consulta o estado de saúde do serviço.

### Headers

| Header         | Obrigatório | Descrição                    |
| -------------- | ----------- | ---------------------------- |
| `Content-Type` | Sim         | Deve ser `application/json`  |
| `x-api-key`    | Sim         | turma2026                    |


### Resposta de sucesso

Quando o serviço estiver disponível:

**200 OK**

```json
{
  "code": 200,
  "message": "Serviço disponível",
  "health": true
}
```

### Serviço indisponível

Quando o estado de Health estiver como `false`:

**503 Service Unavailable**

```json
{
  "code": 503,
  "message": "Serviço indisponível",
  "health": false
}
```

---

## POST `240081/197375/v1/health/atualizar`

Atualiza o estado de saúde do serviço.

### Headers

| Header         | Obrigatório | Descrição                    |
| -------------- | ----------- | ---------------------------- |
| `Content-Type` | Sim         | Deve ser `application/json`  |
| `x-api-key`    | Sim         | turma2026                    |

### Body

Para deixar o serviço disponível:

```json
{
  "health": true
}
```

Para deixar o serviço indisponível:

```json
{
  "health": false
}
```

O campo `health` deve ser obrigatoriamente um valor booleano (`true` ou `false`).

### Resposta de sucesso

**200 OK**

```json
{
  "code": 200,
  "message": "Health atualizado com sucesso",
  "health": true
}
```

---

# Códigos de resposta

| Código | Descrição                        |
| ------ | -------------------------------- |
| `200`  | Requisição realizada com sucesso |
| `400`  | Requisição inválida              |
| `401`  | `x-api-key` não informado        |
| `403`  | `x-api-key` inválido             |
| `500`  | Erro interno do servidor         |
| `503`  | Serviço indisponível             |

---

# Validações

As requisições realizadas à API possuem validações dos headers e dos dados enviados.

Entre as validações realizadas estão:

* Verificação do `Content-Type`;
* Verificação da existência do `x-api-key`;
* Validação do `x-api-key`;
* Verificação dos campos obrigatórios;
* Validação do tipo dos dados enviados.

---

# Armazenamento

Os dados do cardápio são armazenados utilizando **Redis Cloud**.

As pizzas são armazenadas utilizando chaves específicas para cada item do cardápio, como:

```text
menu:pizza:1
menu:pizza:2
```

O estado de saúde do serviço é armazenado na chave:

```text
health
```

O valor armazenado nessa chave representa o estado atual do serviço (`true` ou `false`).

---

# Resiliência

O serviço utiliza mecanismos de resiliência para lidar com possíveis falhas em suas operações internas.

## Retry

Em caso de falha na comunicação com o Redis, são realizadas até **3 tentativas**, considerando a tentativa inicial e mais 2 retries.

O intervalo configurado entre as tentativas é de **500 ms**.

## Fallback

O serviço poderá utilizar os dados armazenados anteriormente no Redis como fallback em situações de indisponibilidade de serviços externos.

> 

---

# Integração com outros serviços

O Cardápio faz parte de uma arquitetura de microsserviços do projeto **Pzaas**.

Durante a integração, o serviço poderá consultar o serviço de **Estoque/Disponibilidade** para verificar a disponibilidade dos ingredientes utilizados nas pizzas.

> 

---

# Testes

Os endpoints da API foram testados utilizando o **Postman**.

Os testes contemplam:

* Requisições com headers válidos;
* Ausência do `x-api-key`;
* `x-api-key` inválido;
* `Content-Type` inválido;
* Dados inválidos;
* Consulta do cardápio;
* Atualização do estado de Health;
* Resposta de serviço indisponível (`503`).

## Exemplo de chamada

### Requisição

```http
GET 240081/197375/v1/menu
```

Headers:

```text
Content-Type: application/json
x-api-key: turma2026
```

### Resposta

```json
{
  "pizzas": [
    {
      "id": 1,
      "nome": "Pizza de Mussarela",
      "disponivel": true
    }
  ]
}
```

---
