---
title: Azure Cosmos DB 쿼리 언어의 ARRAY_CONCAT
description: 배열이 두 개 이상의 배열 값을 연결한 결과인 배열을 반환 Azure Cosmos DB에서 SQL 시스템 함수를 연결 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b6424a161b5fc4d0c075ade6f852b33dff20a09
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332766"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 둘 이상의 배열 값을 연결한 결과인 배열을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>인수
  
*arr_expr*  
   다른 값에 연결할 배열 식입니다. 함수에는 `ARRAY_CONCAT` 두 개 이상의 *arr_expr* 인수가 필요 합니다.  
  
## <a name="return-types"></a>반환 형식
  
  배열 식을 반환합니다.  
  
## <a name="examples"></a>예제
  
  다음 예제에서는 두 배열을 연결하는 방법을 보여 줍니다.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [배열 함수 Azure Cosmos DB](sql-query-array-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
