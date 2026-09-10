# Serviço de Cardápio — Turma A03

## 1. Descrição

O serviço de Cardápio é responsável por disponibilizar as pizzas cadastradas no sistema, juntamente com suas informações, como nome, descrição, ingredientes, preço e disponibilidade.

O serviço faz parte da arquitetura distribuída do projeto **Pzaas (Pizza as a Service)** realizado por meio do n8n.

### URL Base

`https://pzaas.online/webhook`

---

## 2. Menu

### 2.1 Consulta do Cardápio

### GET `/turmaa03/v1/menu`

Retorna as pizzas disponíveis para consumo no Cardápio.

Uma pizza será retornada somente quando estiver disponível no cadastro do Cardápio e todos os seus ingredientes estiverem cadastrados no Estoque em quantidade suficiente.

### Headers

| **Header**     | **Obrigatório** | **Valor**          |
| -------------- | --------------- | ------------------ |
| `Content-Type` | Sim             | `application/json` |
| `X-API-Key`    | Sim             | `turma2026`        |

### Requisição

Não possui corpo.

Exemplo:

```text
GET https://pzaas.online/webhook/turmaa03/v1/menu
Content-Type: application/json
X-API-Key: turma2026
```

### Resposta — 200

Quando houver pizzas disponíveis, o serviço retorna a lista de pizzas e suas respectivas informações.

Exemplo de resposta real:

```json
[
    {
        "code": 200,
        "pizzas": [
            {
                "id": 1,
                "nome": "Pizza de Mussarela",
                "descricao": "Pizza com molho de tomate, mussarela, tomate, orégano e azeitona.",
                "preco": "60.00",
                "emPromocao": false,
                "precoPromocional": null,
                "disponivel": true,
                "ingredientes": [
                    {
                        "nome": "molho de tomate",
                        "quantidade": 0.1,
                        "unidade": "L"
                    },
                    {
                        "nome": "mussarela",
                        "quantidade": 0.3,
                        "unidade": "kg"
                    },
                    {
                        "nome": "tomate",
                        "quantidade": 0.1,
                        "unidade": "kg"
                    },
                    {
                        "nome": "orégano",
                        "quantidade": 0.005,
                        "unidade": "kg"
                    },
                    {
                        "nome": "azeitona",
                        "quantidade": 0.05,
                        "unidade": "kg"
                    }
                ]
            },
            {
                "id": 2,
                "nome": "Pizza de Calabresa Acebolada",
                "descricao": "Pizza com molho de tomate, calabresa, cebola roxa, orégano e azeitona.",
                "preco": "70.00",
                "emPromocao": false,
                "precoPromocional": null,
                "disponivel": true,
                "ingredientes": [
                    {
                        "nome": "molho de tomate",
                        "quantidade": 0.1,
                        "unidade": "L"
                    },
                    {
                        "nome": "orégano",
                        "quantidade": 0.005,
                        "unidade": "kg"
                    },
                    {
                        "nome": "azeitona",
                        "quantidade": 0.05,
                        "unidade": "kg"
                    },
                    {
                        "nome": "calabresa",
                        "quantidade": 0.3,
                        "unidade": "kg"
                    },
                    {
                        "nome": "cebola roxa",
                        "quantidade": 0.1,
                        "unidade": "kg"
                    }
                ]
            }
        ]
    }
]
```

### Resposta — 200 sem pizzas disponíveis

Caso o serviço esteja disponível, mas nenhuma pizza possua ingredientes suficientes para ser preparada, será retornada a seguinte resposta:

```json
{
  "code": 200,
  "message": "Não há pizzas disponíveis no momento.",
  "pizzas": []
}
```

Essa situação não representa uma falha do serviço. Por isso, o código retornado é `200`.

### Possíveis erros

| **Código** | **Situação**                                              |
| ---------- | --------------------------------------------------------- |
| `400`      | `Content-Type` ausente ou diferente de `application/json` |
| `401`      | `X-API-Key` ausente                                       |
| `403`      | `X-API-Key` inválida                                      |
| `500`      | Erro interno durante o processamento                      |
| `503`      | Serviço ou recurso necessário para consulta indisponível  |

---

## 3. Health

O serviço possui um mecanismo próprio de Health para informar se o Cardápio está disponível para consumo.

---

### 3.1 Consultar Health

### GET `/turmaa03/v1/health`

Consulta o estado atual do serviço.

### Headers

| Header         | Obrigatório | Valor              |
| -------------- | ----------- | ------------------ |
| `Content-Type` | Sim         | `application/json` |
| `X-API-Key`    | Sim         | `turma2026`        |

Exemplo:

```text
GET https://pzaas.online/webhook/turmaa03/v1/health
Content-Type: application/json
X-API-Key: turma2026
```

### Resposta — Serviço disponível

Quando o Health estiver `true`:

```json
{
  "code": 200,
  "message": "Serviço disponível"
}
```

### Resposta — Serviço indisponível

Quando o Health estiver `false`:

```json
{
  "code": 503,
  "message": "Serviço indisponível"
}
```

### Possíveis erros

| Código | Situação                            |
| ------ | ----------------------------------- |
| `400`  | Content-Type inválido               |
| `401`  | X-API-Key não informado             |
| `403`  | X-API-Key inválido                  |
| `500`  | Erro interno do Serviço             |

---

### 3.2 Atualização do Health

### POST `/turmaa03/v1/health/atualizar`

Permite alterar o estado de Health do serviço.

### Headers

| Header         | Obrigatório | Valor              |
| -------------- | ----------- | ------------------ |
| `Content-Type` | Sim         | `application/json` |
| `X-API-Key`    | Sim         | `turma2026`        |

### Corpo da requisição

O campo `health` deve ser booleano.

Exemplo para ativar:

```json
{
  "health": true
}
```

Exemplo para desativar:

```json
{
  "health": false
}

```

### Resposta — 200

Ao ativar:

```json
{
  "code": 200,
  "message": "Health ativado com sucesso"
}
```

Ao desativar:

```json
{
  "code": 200,
  "message": "Health desativado com sucesso"
}
```

### Possíveis erros

| Código | Situação                            |
| ------ | ----------------------------------- |
| `400`  | Content-Type inválido               |
| `400`  | Campo `health` não informado        |
| `400`  | Campo `health` não é booleano       |
| `401`  | X-API-Key não informado             |
| `403`  | X-API-Key inválido                  |
| `500`  | Erro interno                        |

---

## 4. Resumo dos endpoints

| Método | Endpoint                        | Função                       |
| ------ | ------------------------------- | ---------------------------- |
| `GET`  | `/turmaa03/v1/menu`             | Consulta o cardápio          |
| `GET`  | `/turmaa03/v1/health`           | Consulta o estado do serviço |
| `POST` | `/turmaa03/v1/health/atualizar` | Atualiza o estado do Health  |

