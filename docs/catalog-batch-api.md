---
id: catalog-batch-api
title: Catalog Batch API
sidebar_label: Catalog Batch API
---

A API de catálogo é utilizada para envio de produtos quando há milhões de produtos a serem enviados para o DynAd AdServer.
É possível criar, remover e atualizar mais de um produto por requisição.

## Base URL

```
https://catalog.api.dynad.net/v1
```

## Endpoints

A API consiste no seguinte endpoint

| **Endpoint**               | **Descrição**                    | **Versões** |
| -------------------------- | -------------------------------- | ----------- |
| POST /{customerCode}/batch | Inserção ou alteração de produto | 1.0         |

## Requisições

As requisições para a API devem obedecer o seguinte formato:

```
POST https://catalog.api.dynad.net/v1/{customerCode}/batch
Content-type: application/json
{
    "requests": [
        {
            "method": "[ UPSERT | DELETE ]",
            "data": <Produto>
        }
    ]
}
```

## Definição de Tipos

Esta seção define os atributos permitidos nos dados enviados nas requisições

### Método - atributo `method`

* UPSERT - Utilizado para inserir ou atualizar produtos no catálogo
* DELETE - Utilizado para remover/inativar produto do catálogo

### Produto - atributo `data`

Item do catálogo. A tabela abaixo define cada atributo e suas restrições

| **Field**       | **Type** | **Mandatory** | **Description**                                                      |
| --------------- | -------- | ------------- | -------------------------------------------------------------------- |
| sku             | String   | true          | Itendificador único do produto                                       |
| active          | Boolean  | true          | Disponibilidade do produto                                           |
| brand           | String   | true          | Marca                                                                |
| category        | [String] | true          | Array de categorias do produto. Tamanho limitado a 3                 |
| description     | String   | false         | Descrição do produto                                                 |
| imagem          | String   | true          | URL da imagem                                                        |
| name            | String   | true          | Nome o produto                                                       |
| price           | Float    | false         | Preço original do produto. Padrão ##.##                              |
| sellPrice       | Float    | true          | Proço promocional. Padrão ##.##                                      |
| parcels         | Int      | false         | Quantidade de parcelas                                               |
| parcelsValue    | Float    | false         | Valor de cada parcela                                                |
| url             | String   | true          | URL do produto                                                       |
| recommendations | [String] | false         | Lista de skus de produtos a serem recomendados. Tamanho mínimo de 10 |

## Remoção de Produtos - /{customerCode}/batch

No caso de remoção, o único campo obrigatório do produto será o sku.

```
POST https://catalog.api.dynad.net/v1/magalu/batch
```

Conteúdo da requisição

```
{
    "requests": [
        {
            "method": "DELETE",
            "data": { "sku": "123" }
        },
        {
            "method": "DELETE",
            "data": { "sku": "456" }
        }
    ]
}
```

Reposta:
200 OK

```
{
   "status":"OK",
   "response":[
      {
        "accepted":true, "sku":"456"
      },
      {
        "accepted":true, "sku":"123"
      }
   ]
}
```

## Atualização/Inserção de Produtos - /{customerCode}/batch

```
POST https://catalog.api.dynad.net/v1/magalu/batch
```

Conteúdo da requisição
```
{
    "requests": [
        {
            "method": "UPSERT",
            "data": {
                "sku": "665",
                "active": true,
                "brand": "Mike",
                "category": ["Masculino", "Calçados", "Nike"],
                "description": "Product description",
                "image": "https://image.com/image.jpg",
                "name": "Tênis Nike",
                "price": 10.00,
                "sellPrice": 9.99,
                "parcels": 10,
                "parcelsValue": 0.99,
                "url": "https://brand.com/nike",
                "recommendations": ["sku1", "sku2"]
            }
        },
        {
            "method": "UPSERT",
            "data": {
                "active": true,
                "brand": "Mike",
                "category": ["Masculino", "Calçados", "Nike"],
                "description": "Product description",
                "image": "https://image.com/image.jpg",
                "name": "Tênis Nike",
                "price": 10.00,
                "sellPrice": 9.99,
                "parcels": 10,
                "parcelsValue": 0.99,
                "url": "https://brand.com/nike",
                "recommendations": ["sku1", "sku2"]
            }
        },
        {
            "method": "DELETE",
            "data": {
                "sku": "123"
            }
        }
    ]
}
```

Reposta:
200 OK

```
{
   "status":"OK",
   "response":[
      {
        "accepted":true, "sku":"665"
      },
      {
        "accepted":false, "errors":[{ "sku":"Invalid value" }]
      },
      {
        "accepted":true, "sku":"123"
      }
   ]
}
```
