---
title: ROUND in Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre as arredondamentos de função do sistema SQL em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302110"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
 Retorna um valor numérico, arredondado para o valor inteiro mais próximo.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="remarks"></a>Comentários
  
  A operação de arredondamento executada segue o arredondamento do ponto médio para longe de zero. Se a entrada for uma expressão numérica que está exatamente entre dois inteiros, o resultado será o valor inteiro mais próximo, distante do zero.  
  
  |< numeric_expr >|Arredondado|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0.5|1|
  |6,5000|7||
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir arredonda os números positivos e negativos para o inteiro mais próximo.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Este é o conjunto de resultados.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>Comentários

Essa função de sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
