---
title: 변수 할당
description: 이 문서에서는 Azure Synapse Analytics에서 전용 SQL 풀에 대 한 T-sql 변수를 할당 하기 위한 필수 팁을 찾을 수 있습니다.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459226"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀에 대 한 변수 할당

이 문서에서는 전용 SQL 풀에서 T-sql 변수를 할당 하는 데 필요한 팁을 찾을 수 있습니다.

## <a name="set-variables-with-declare"></a>DECLARE을 사용 하 여 변수 설정

전용 SQL 풀의 변수는 문 또는 문을 사용 하 여 설정 됩니다 `DECLARE` `SET` . DECLARE을 사용 하 여 변수를 초기화 하는 것은 SQL 풀에서 변수 값을 설정 하는 가장 유연한 방법 중 하나입니다.

```sql
DECLARE @v  int = 0
;
```

한 번에 둘 이상의 변수를 설정하려면 DECLARE를 사용할 수도 있습니다. SELECT 또는 UPDATE를 사용 하 여 다음 작업을 수행할 수 없습니다.

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

동일한 DECLARE 문에서 변수를 초기화 하 고 사용할 수 없습니다. 지점을 설명하기 위해 다음 예에서는 @p1이 동일한 DECLARE 문에서 시작되고 사용되기 때문에 허용되지 **않습니다**. 따라서 다음 예제에서는 오류를 제공 합니다.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>집합을 사용 하 여 값 설정

SET은 단일 변수를 설정하기 위한 일반적인 방법입니다.

다음 명령문은 SET을 사용하여 변수를 설정하는 모든 유효한 방법입니다.

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

SET을 사용하여 한 번에 하나의 변수만 설정할 수 있습니다. 그러나 복합 연산자는 허용됩니다.

## <a name="limitations"></a>제한 사항

변수 할당에는 UPDATE를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
