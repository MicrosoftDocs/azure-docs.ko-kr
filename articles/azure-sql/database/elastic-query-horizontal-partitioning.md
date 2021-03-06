---
title: 확장된 클라우드 데이터베이스에서 보고
description: 행 분할에서 탄력적 쿼리를 설정하는 방법
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 148c4828309738a18dbda5fd35ea634e8384bfde
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792109"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>확장된 클라우드 데이터베이스에서 보고(미리 보기)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![분할된 데이터베이스에 대한 쿼리][1]

분할된 데이터베이스는 확장된 데이터 계층에 행을 분산합니다. 스키마는 모든 참여하는 데이터베이스에서 동일하며 이를 수평 분할이라고도 합니다. 탄력적 쿼리를 사용하여 분할된 데이터베이스의 모든 데이터베이스를 망라하는 보고서를 만들 수 있습니다.

빠른 시작은 확장 된 [클라우드 데이터베이스에서 보고](elastic-query-getting-started.md)를 참조 하세요.

비분할 데이터베이스의 경우 [여러 스키마를 사용하여 클라우드 데이터베이스에서 쿼리](elastic-query-vertical-partitioning.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 탄력적 데이터베이스 클라이언트 라이브러리를 사용하여 분할된 데이터베이스 맵을 만듭니다. [분할된 데이터베이스 맵 관리](elastic-scale-shard-map-management.md)를 참조하세요. 또는 [탄력적 데이터베이스 도구 시작하기](elastic-scale-get-started.md)의 샘플 앱을 사용하세요.
* 또는 [기존 데이터베이스를 확장된 데이터베이스로 마이그레이션](elastic-convert-to-use-elastic-tools.md)을 참조합니다.
* 사용자는 모든 외부 데이터 원본 ALTER 권한이 있어야 합니다. 이 사용 권한은 ALTER DATABASE 권한에 포함됩니다.
* 기본 데이터 원본을 참조하기 위해 ALTER ANY EXTERNAL DATA SOURCE 권한이 필요합니다.

## <a name="overview"></a>개요

이 문은 탄력적 쿼리 데이터베이스에서 분할된 데이터 계층의 메타데이터 표현을 만듭니다.

1. [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql)
2. [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 데이터베이스 범위 마스터 키 및 자격 증명 만들기

자격 증명은 탄력적 쿼리에서 원격 데이터베이스 연결에 사용됩니다.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> " *\<username\> "* 에 " *\@ servername"* 접미사가 포함 되지 않아야 합니다.

## <a name="12-create-external-data-sources"></a>1.2 외부 데이터 원본 만들기

구문

```sql
<External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
        (TYPE = SHARD_MAP_MANAGER,
                   LOCATION = '<fully_qualified_server_name>',
        DATABASE_NAME = ‘<shardmap_database_name>',
        CREDENTIAL = <credential_name>,
        SHARD_MAP_NAME = ‘<shardmapname>’
               ) [;]
```

### <a name="example"></a>예제

```sql
CREATE EXTERNAL DATA SOURCE MyExtSrc
WITH
(
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
);
```

현재 외부 데이터 원본의 목록을 검색합니다.

```sql
select * from sys.external_data_sources;
```

외부 데이터 원본에는 분할 맵을 참조합니다. 그러면 탄력적 쿼리는 외부 데이터 원본과 기본 분할 맵을 사용하여 데이터 계층에 참여하는 데이터베이스를 열거합니다.
탄력적 쿼리를 처리하는 동안 분할 데이터베이스 맵을 읽고 분할 데이터베이스의 데이터에 액세스할 때는 동일한 자격 증명을 사용합니다.

## <a name="13-create-external-tables"></a>1.3 외부 테이블 만들기

구문  

```sql
CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
    ( { <column_definition> } [ ,...n ])
    { WITH ( <sharded_external_table_options> ) }
) [;]  

<sharded_external_table_options> ::=
  DATA_SOURCE = <External_Data_Source>,
  [ SCHEMA_NAME = N'nonescaped_schema_name',]
  [ OBJECT_NAME = N'nonescaped_object_name',]
  DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN
```

**예제**

```sql
CREATE EXTERNAL TABLE [dbo].[order_line](
     [ol_o_id] int NOT NULL,
     [ol_d_id] tinyint NOT NULL,
     [ol_w_id] int NOT NULL,
     [ol_number] tinyint NOT NULL,
     [ol_i_id] int NOT NULL,
     [ol_delivery_d] datetime NOT NULL,
     [ol_amount] smallmoney NOT NULL,
     [ol_supply_w_id] int NOT NULL,
     [ol_quantity] smallint NOT NULL,
      [ol_dist_info] char(24) NOT NULL
)

WITH
(
    DATA_SOURCE = MyExtSrc,
     SCHEMA_NAME = 'orders',
     OBJECT_NAME = 'order_details',
    DISTRIBUTION=SHARDED(ol_w_id)
);
```

현재 데이터베이스에서 외부 테이블 목록을 검색합니다.

```sql
SELECT * from sys.external_tables;
```

외부 테이블을 삭제하려면:

```sql
DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]
```

### <a name="remarks"></a>설명

DATA\_SOURCE 절은 외부 테이블에 사용하는 외부 데이터 원본(분할 맵)을 정의합니다.  

SCHEMA\_NAME 및 OBJECT\_NAME 절은 외부 테이블 정의를 다른 스키마의 테이블에 매핑합니다. 생략한 경우 원격 개체의 스키마를 “dbo”라 가정하고 그 이름이 정의하는 외부 테이블 이름과 동일하다고 간주합니다. 외부 테이블을 만들려는 데이터베이스에서 이미 원격 테이블의 이름을 가져온 경우에 유용합니다. 수평 확장된 데이터 계층에서 DMV의 카탈로그 뷰 집계를 가져오기 위해 외부 테이블을 정의하는 경우를 예로 들 수 있습니다. 카탈로그 뷰와 DMV는 이미 로컬로 존재하므로 외부 테이블 정의에 그 이름을 사용할 수 없습니다. 대신 다른 이름을 사용하고 SCHEMA\_NAME 및/또는 OBJECT\_NAME 절에서 카탈로그 뷰나 DMV의 이름을 사용합니다. 아래 예제를 참조하세요.

DISTRIBUTION 절은 이 테이블에 사용되는 데이터 배포를 지정합니다. 쿼리 프로세서는 가장 효율적인 쿼리 계획을 구성하기 위해 DISTRIBUTION에서 제공하는 정보를 활용합니다.

1. **분할 된** 는 데이터가 데이터베이스 전체에 걸쳐 수평 분할 됨을 의미 합니다. 데이터 배포의 분할 키는 **<sharding_column_name>** 매개 변수입니다.
2. **복제** 됨은 각 데이터베이스에 동일한 테이블 복사본이 있음을 의미 합니다. 사용자가 데이터베이스 전체에서 복제본이 동일한지 확인해야 합니다.
3. **ROUND \_ 로빈** 은 테이블이 응용 프로그램 종속 배포 방법을 사용 하 여 수평 분할 됨을 의미 합니다.

**데이터 계층 참조**: 외부 테이블 DDL은 외부 데이터 원본을 가리킵니다. 외부 데이터 원본은 데이터 계층에서 모든 데이터베이스를 찾는 데 필요한 정보를 사용 하 여 외부 테이블을 제공 하는 분할 된 데이터베이스 맵을 지정 합니다.

### <a name="security-considerations"></a>보안 고려 사항

외부 테이블에 대한 액세스 권한이 있는 사용자는 외부 데이터 원본 정의에서 제공한 자격 증명에 따라 자동으로 기본 원격 테이블에 액세스할 수 있습니다. 외부 데이터 원본의 자격 증명을 통해 원치 않는 권한 상승을 방지합니다. 일반 테이블인 것 처럼 외부 테이블에 GRANT 또는 REVOKE를 사용 합니다.  

외부 데이터 원본 및 외부 테이블을 정의한 후 외부 테이블을 통해 전체 T-SQL을 사용할 수 있습니다.

## <a name="example-querying-horizontal-partitioned-databases"></a>예: 수평 분할된 데이터베이스 쿼리

다음 쿼리는 웨어하우스, 주문 및 주문 라인 간에 3 방향 조인을 수행 하 고 여러 개의 집계 및 선택적 필터를 사용 합니다. (1) 행 분할 (분할) 및 (2) 웨어하우스, 주문 및 주문 줄이 웨어하우스 ID 열에 의해 분할 된 되 고 탄력적 쿼리가 분할에서 조인을 공동 배치 하 고 분할에서 쿼리의 비용이 많이 드는 부분을 처리할 수 있다고 가정 합니다.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>원격 T-SQL 실행을 위한 저장 프로시저: sp\_execute_remote

또한 탄력적 쿼리는 분할된 데이터베이스에 대한 직접 액세스를 제공하기 위해 저장 프로시저를 사용합니다. 저장 프로시저는 [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database)라고 하며, 원격 데이터베이스에서 원격 저장 프로시저 또는 T-SQL 코드를 실행하는 데 사용될 수 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* 데이터 원본 이름(nvarchar): RDBMS 형식의 외부 데이터 원본 이름입니다.
* 쿼리(nvarchar): T-SQL 쿼리를 각 분할된 데이터베이스에서 실행할 수 있습니다.
* 매개 변수 선언(nvarchar), 선택 사항: 쿼리 매개 변수(예: sp_executesql)에 사용된 매개 변수에 대한 데이터 형식 정의가 있는 문자열입니다.
* 매개 변수 값 목록, 선택 사항: 쉼표로 구분한 매개 변수 값(예: sp_executesql) 목록.

sp\_execute\_remote는 호출 매개 변수에 제공된 외부 데이터 원본을 사용하여 원격 데이터베이스에서 지정된 T-SQL 문을 실행합니다. 또한 외부 데이터 원본의 자격 증명을 사용하여 분할 맵 관리자 데이터베이스 및 원격 데이터베이스에 연결합니다.  

예제:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>도구에 대한 연결

일반 SQL Server 연결 문자열을 사용 하 여 응용 프로그램, BI 및 데이터 통합 도구를 외부 테이블 정의를 사용 하 여 데이터베이스에 연결 합니다. SQL Server 도구에 대한 데이터 소스로 지원 되는지 확인 합니다. 그런 다음, 도구에 연결된 다른 SQL Server 데이터베이스와 같이 탄력적 쿼리 데이터베이스를 참조하고 도구 또는 애플리케이션의 외부 테이블을 로컬 테이블처럼 사용합니다.

## <a name="best-practices"></a>모범 사례

* 탄력적 쿼리 끝점 데이터베이스에 shardmap 데이터베이스에 대 한 액세스 권한을 부여 하 고 SQL Database 방화벽을 통해 모든 분할를 확인 합니다.  
* 외부 테이블에서 정의한 데이터 배포의 유효성을 검사하거나 해당 배포를 적용합니다. 실제 데이터 배포는 테이블 정의에 지정된 배포와 다른 경우 쿼리는 예기치 않은 결과를 얻을 수도 있습니다.
* 탄력적 쿼리는 처리되는 과정에서 특정 분할된 데이터베이스가 안전하게 제외될 수 있도록 조건자 분할키가 허락할 때 분할된 데이터베이스 제거를 수행하지 않습니다.
* 탄력적 쿼리는 분할된 데이터베이스에서 대부분의 계산을 수행할 수 있는 상황에서 가장 잘 실행됩니다. 일반적으로 분할된 데이터베이스 에서 평가될 수 있는 선택적인 필터 조건자를 가진 최상의 쿼리성능을 얻게 되거나 모든 분할 데이터베이스에서 파티션 정렬 방식으로 수행할 수 있는 분할 키를 조인하게 됩니다. 다른 쿼리 패턴은 분할된 데이터베이스에서 많은 양의 데이터를 로드해야 하기 때문에 성능이 좋지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 탄력적 쿼리의 개요는 [탄력적 쿼리 개요](elastic-query-overview.md)를 참조하세요.
* 수직 분할 자습서는 [데이터베이스 간 쿼리 시작(수직 분할)](elastic-query-getting-started-vertical.md)을 참조하세요.
* 수직 분할된 데이터에 대한 구문 및 예제 쿼리는 [수직 분할된 데이터 쿼리하기](elastic-query-vertical-partitioning.md)를 참조하세요.
* 행 분할(분할) 자습서는 [행 분할(분할)을 위한 탄력적 데이터베이스 쿼리 시작하기](elastic-query-getting-started.md)를 참조하세요.
* 단일 원격 Azure SQL Database 또는 수평 분할 구성표의 분할을 제공하는 데이터베이스 세트에서 Transact-SQL 문을 실행하는 저장된 프로시저는 [sp\_실행 \_원격](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database)을 참조하세요.

<!--Image references-->
[1]: ./media/elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->