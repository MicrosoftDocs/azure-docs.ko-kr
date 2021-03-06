---
title: 다중 모델 기능
description: SQL Microsoft Azure를 사용 하면 동일한 데이터베이스에서 여러 데이터 모델을 사용할 수 있습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: b16a2fc9f107a8420fb7d05667807a869fa3e00a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172760"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Azure SQL Database & SQL Managed Instance의 다중 모델 기능
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

다중 모델 데이터베이스를 사용 하면 관계형 데이터, 그래프, JSON/XML 문서, 키-값 쌍 등 여러 데이터 형식으로 표현 된 데이터를 저장 하 고 작업할 수 있습니다.

## <a name="when-to-use-multi-model-capabilities"></a>다중 모델 기능을 사용 하는 경우

[AZURE SQL 제품군](azure-sql-iaas-vs-paas-what-is-overview.md) 은 다양 한 범용 응용 프로그램에 대 한 대부분의 경우 최상의 성능을 제공 하는 관계형 모델에서 사용할 수 있도록 설계 되었습니다. 그러나 Azure SQL 제품군은 관계형 데이터만으로 제한 되지 않습니다. Azure SQL 제품 제품군을 사용 하면 관계형 모델에 긴밀 하 게 통합 되는 다양 한 비관계형 형식을 사용할 수 있습니다.
다음과 같은 경우 Azure SQL 제품군의 다중 모델 기능을 사용 하는 것을 고려해 야 합니다.

- NoSQL 모델에 적합 하 고 별도의 NoSQL 데이터베이스를 사용 하지 않으려는 일부 정보나 구조가 있습니다.
- 대부분의 데이터는 관계형 모델에 적합 하며 NoSQL 스타일로 데이터의 일부를 모델링 해야 합니다.
- 풍부한 Transact-sql 언어를 활용 하 여 관계형 및 NoSQL 데이터를 쿼리하고 분석 하 고 SQL 언어를 사용할 수 있는 다양 한 도구 및 응용 프로그램과 통합 하려고 합니다.
- [메모리 내 기술과](in-memory-oltp-overview.md) 같은 데이터베이스 기능을 적용 하 여 nosql 데이터 구조의 분석 또는 처리 성능을 향상 시킬 수 있습니다. [트랜잭션 복제](managed-instance/replication-transactional-overview.md) 나 [읽을 수 있는 복제본](database/read-scale-out.md) 을 사용 하 여 다른 장소에 데이터 복사본을 만들고 주 데이터베이스에서 일부 분석 작업을 오프 로드 합니다.

## <a name="overview"></a>개요

Azure SQL 제품 제품군은 다음과 같은 다중 모델 기능을 제공 합니다.

- [그래프 기능](#graph-features)을 사용하면 데이터를 노드 및 에지 세트로 표시하고 그래프 `MATCH` 연산자로 향상된 표준 Transact-SQL 쿼리를 사용하여 그래프 데이터를 쿼리할 수 있습니다.
- [JSON 기능](#json-features)을 사용하면 JSON 문서를 테이블에 넣고, 관계형 데이터를 JSON 문서로 변환하거나 그 반대로 변환할 수 있습니다. JSON 함수로 향상된 표준 Transact-SQL 언어를 문서 구문 분석에 사용하고, 비클러스터형 인덱스, columnstore 인덱스 또는 메모리 최적화 테이블을 사용하여 쿼리를 최적화할 수 있습니다.
- [공간 기능](#spatial-features)을 사용하면 지리적 및 기하학적 데이터를 저장하고, 공간 인덱스를 사용하여 해당 데이터를 인덱싱하고, 공간 쿼리를 사용하여 데이터를 검색할 수 있습니다.
- [XML 기능](#xml-features)을 사용하면 데이터베이스에서 XML 데이터를 저장 및 인덱싱하고 네이티브 XQuery/XPath 작업을 통해 XML 데이터를 사용할 수 있습니다. Azure SQL 제품군에는 XML 데이터를 처리 하는 특수 한 기본 제공 XML 쿼리 엔진이 있습니다.
- 키-값 쌍은 기본적으로 두 열 테이블로 모델링할 수 있으므로 [키-값 쌍](#key-value-pairs) 은 특별 한 기능으로 명시적으로 지원 되지 않습니다.

  > [!Note]
  > 동일한 Transact-SQL 쿼리에서 JSON 경로 식, XQuery/XPath 식, 공간 함수 및 그래프-쿼리 식을 사용하여 데이터베이스에 저장한 데이터에 액세스할 수 있습니다. Transact-SQL 쿼리를 실행할 수 있는 도구나 프로그래밍 언어도 해당 쿼리 인터페이스를 사용하여 다중 모델 데이터에 액세스할 수 있습니다. 이는 다양한 데이터 모델에 대한 특수화된 API를 제공하는 [Azure Cosmos DB](../cosmos-db/index.yml)와 같은 다중 모델 데이터베이스와 비교되는 주요 차이점입니다.

다음 섹션에서는 Azure SQL 제품 제품군의 가장 중요 한 다중 모델 기능에 대해 알아볼 수 있습니다.

## <a name="graph-features"></a>그래프 기능

Azure SQL 제품군은 데이터베이스에서 다 대 다 관계를 모델링 하는 그래프 데이터베이스 기능을 제공 합니다. 그래프는 노드(또는 정점) 및 에지(또는 관계)의 컬렉션입니다. 노드는 엔터티(예: 개인 또는 조직)를 나타내고 에지는 에지가 연결하는 두 노드 간의 관계(예: 좋아요 또는 친구)를 나타냅니다. 그래프 데이터베이스를 고유하게 만드는 몇 가지 기능은 다음과 같습니다.

- 에지 또는 관계는 그래프 데이터베이스의 첫 번째 클래스 엔터티이며 엔터티와 연결된 특성 또는 속성을 포함할 수 있습니다.
- 단일 에지는 유연하게 그래프 데이터베이스의 여러 노드를 연결할 수 있습니다.
- 패턴 일치 및 멀티 홉 탐색 쿼리를 쉽게 표현할 수 있습니다.
- 이행적 폐쇄 및 다형적 쿼리를 쉽게 표현할 수 있습니다.

[그래프 관계와 그래프 쿼리 기능은](/sql/relational-databases/graphs/sql-graph-overview) transact-sql에 통합 되어 있으므로 SQL Server 데이터베이스 엔진을 기본 데이터베이스 관리 시스템으로 사용 하는 이점을 얻을 수 있습니다.

### <a name="when-to-use-a-graph-capability"></a>그래프 기능을 사용하는 경우

그래프 데이터베이스로 수행할 수 있는 작업은 없고, 이러한 작업은 관계형 데이터베이스로도 수행할 수 없습니다. 그러나 그래프 데이터베이스를 사용하면 특정 쿼리를 더 쉽게 표현할 수 있습니다. 둘 중 하나를 선택하는 결정은 다음 요소를 기반으로 할 수 있습니다.

- 모델 계층 구조 데이터의 한 노드에 여러 부모가 포함될 수 있습니다(HieararchyId를 사용할 수 없음).
- 모델에는 복잡한 다 대 다 관계를 가진 애플리케이션이 포함됩니다. 애플리케이션이 발전함에 따라 새로운 관계가 추가됩니다.
- 상호 연결된 데이터 및 관계를 분석해야 합니다.

## <a name="json-features"></a>JSON 기능

Azure SQL 제품 제품군을 사용 하 여 JavaScript Object Notation [(json)](https://www.json.org/) 형식으로 표현 된 데이터를 구문 분석 및 쿼리하고 관계형 데이터를 json 텍스트로 내보낼 수 있습니다.

JSON은 최신 웹 및 모바일 애플리케이션에서 데이터를 교환하는 데 사용되는 일반적인 데이터 형식입니다. 또한 JSON은 로그 파일 또는 NoSQL 데이터베이스(예: [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/))에 반구조화된 데이터를 저장하는 데도 사용됩니다. 많은 REST 웹 서비스에서 JSON 텍스트로 형식이 지정된 결과를 반환하거나 JSON으로 형식이 지정된 데이터를 허용합니다. [Azure Cognitive Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)및 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 와 같은 대부분의 azure 서비스에는 JSON을 반환 하거나 사용 하는 REST 끝점이 있습니다.


Azure SQL 제품군을 사용 하면 JSON 데이터를 쉽게 사용 하 고 데이터베이스를 최신 서비스와 통합할 수 있으며, 다음과 같은 기능을 통해 JSON 데이터를 사용할 수 있습니다.

![JSON 함수](./media/multi-model-features/image_1.png)

JSON 텍스트가 있는 경우 JSON에서 데이터를 추출하거나 기본 제공 함수 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql) 및 [ISJSON](/sql/t-sql/functions/isjson-transact-sql)을 사용하여 JSON 형식이 제대로 지정되었는지 확인할 수 있습니다. [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) 함수를 사용하면 JSON 텍스트 내의 값을 업데이트할 수 있습니다. 고급 쿼리 및 분석을 위해 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql) 함수는 JSON 개체의 배열을 행 집합을 변환할 수 있습니다. 모든 SQL 쿼리는 반환된 결과 집합에서 실행할 수 있습니다. 마지막으로 관계형 테이블에 저장된 데이터의 형식을 JSON 텍스트로 지정할 수 있는 [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) 절이 있습니다.

자세한 내용은 [JSON 데이터로 작업 하는 방법](database/json-features.md)을 참조 하세요.
[JSON](/sql/relational-databases/json/json-data-sql-server) 은 핵심 SQL Server 데이터베이스 엔진 기능입니다.

### <a name="when-to-use-a-json-capability"></a>JSON 기능을 사용하는 경우

일부 특정 시나리오에서는 관계형 모델 대신 문서 모델을 사용할 수 있습니다.

- 스키마의 높은 정규화는 개체의 모든 필드를 한 번에 액세스 하거나 개체의 정규화 된 부분을 업데이트 하지 않기 때문에 상당한 이점을 제공 하지 않습니다. 그러나 정규화된 모델의 경우 데이터를 가져오기 위해 많은 테이블을 조인해야 하므로 쿼리의 복잡성이 커집니다.
- 통신 및 데이터 모델인 JSON 문서를 기본적으로 사용하는 애플리케이션 관련 작업을 수행 중이고, 관계형 데이터를 JSON으로 변환하거나 그 반대로 변환하는 추가 계층을 도입하지 않으려고 합니다.
- 자식 테이블 또는 엔터티-개체-값 패턴을 비정규화하여 데이터 모델을 간소화해야 합니다.
- 데이터를 구문 분석하는 추가 도구 없이 JSON 형식으로 저장된 데이터를 로드하거나 내보내야 합니다.

## <a name="spatial-features"></a>공간 기능

공간 데이터는 기하학적 개체의 물리적 위치 및 모양 정보를 나타냅니다. 이러한 개체는 지점 위치 또는 국가/지역,도로 또는 레이크와 같은 복잡 한 개체 일 수 있습니다.

 지원 되는 두 가지 공간 데이터 형식은 다음과 같습니다. 

- 기하 도형 형식은 유클리드(평면) 좌표계의 데이터를 나타냅니다.
- 지리 형식은 둥근 표면 좌표계의 데이터를 나타냅니다.

Azure SQL 제품군에서 사용할 수 있는 여러 공간 개체를 사용 하 여 JavaScript Object Notation [(json)](https://www.json.org/) 형식으로 표현 된 데이터를 구문 분석 및 쿼리하고 관계형 데이터를 json 텍스트로 내보낼 수 있습니다.
[Point](/sql/relational-databases/spatial/point), [LineString](/sql/relational-databases/spatial/linestring), [Polygon](/sql/relational-databases/spatial/polygon)등이 있습니다.

또한 Azure SQL 제품군은 공간 쿼리의 성능을 개선 하는 데 사용할 수 있는 특수 [공간 인덱스](/sql/relational-databases/spatial/spatial-indexes-overview) 를 제공 합니다.

[공간 지원은](/sql/relational-databases/spatial/spatial-data-sql-server) 핵심 SQL Server 데이터베이스 엔진 기능입니다.

## <a name="xml-features"></a>XML 기능

SQL Server 데이터베이스 엔진은 반 구조화 된 데이터 관리를 위한 다양 한 응용 프로그램을 개발 하기 위한 강력한 플랫폼을 제공 합니다. XML에 대 한 지원은 데이터베이스 엔진의 모든 구성 요소에 통합 되며 다음을 포함 합니다.

- xml 데이터 형식. 기본적으로 XML 값은 XML 스키마의 컬렉션에 따라 형식화되거나 형식화되지 않을 수 있는 xml 데이터 형식 열에 저장할 수 있습니다. XML 열을 인덱싱할 수 있습니다.
- xml 형식의 열 및 변수에 저장된 XML 데이터에 대한 XQuery 쿼리를 지정하는 기능. XQuery 기능은 데이터베이스에서 사용하는 데이터 모델에 액세스하는 모든 Transact-SQL 쿼리에서 사용할 수 있습니다.
- [기본 XML 인덱스](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index)를 사용하여 XML 문서의 모든 요소를 자동으로 인덱싱하거나 [보조 XML 인덱스](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)를 사용하여 인덱싱해야 하는 정확한 경로를 지정합니다.
- XML 데이터를 대량으로 로드할 수 있는 OPENROWSET.
- 관계형 데이터를 XML 형식으로 변환합니다.

[XML](/sql/relational-databases/xml/xml-data-sql-server) 은 핵심 SQL Server 데이터베이스 엔진 기능입니다.

### <a name="when-to-use-an-xml-capability"></a>XML 기능을 사용하는 경우

일부 특정 시나리오에서는 관계형 모델 대신 문서 모델을 사용할 수 있습니다.

- 스키마의 높은 정규화는 개체의 모든 필드를 한 번에 액세스 하거나 개체의 정규화 된 부분을 업데이트 하지 않기 때문에 상당한 이점을 제공 하지 않습니다. 그러나 정규화된 모델의 경우 데이터를 가져오기 위해 많은 테이블을 조인해야 하므로 쿼리의 복잡성이 커집니다.
- 통신 및 데이터 모델인 XML 문서를 기본적으로 사용하는 애플리케이션 관련 작업을 수행 중이고, 관계형 데이터를 XML로 변환하거나 그 반대로 변환하는 추가 계층을 도입하지 않으려고 합니다.
- 자식 테이블 또는 엔터티-개체-값 패턴을 비정규화하여 데이터 모델을 간소화해야 합니다.
- 데이터를 구문 분석하는 추가 도구 없이 XML 형식으로 저장된 데이터를 로드하거나 내보내야 합니다.

## <a name="key-value-pairs"></a>키-값 쌍

키-값 구조는 기본적으로 표준 관계형 테이블로 표현할 수 있으므로 Azure SQL 제품군에는 키-값 쌍을 지 원하는 특수 형식이 나 구조가 없습니다.

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

제약 조건 없이 필요에 맞게 이 키-값 구조를 사용자 지정할 수 있습니다. 예를 들어 값은 `nvarchar(max)` 형식 대신 XML 문서일 수 있습니다. 값이 JSON 문서인 경우 JSON 콘텐츠의 유효성을 확인하는 `CHECK` 제약 조건을 넣을 수 있습니다. 하나의 키에 관련된 여러 값을 추가 열에 넣고, 계산 열 및 인덱스를 추가하여 데이터 액세스를 간소화 및 최적화하고, 테이블을 메모리/최적화 스키마 전용 테이블로 정의하여 성능을 개선할 수 있습니다.

실제로 관계형 모델을 키-값 쌍 솔루션으로 효과적으로 사용할 수 있는 방법의 예로, 초당 1.200.000개 배치를 수행한 ASP.NET 캐싱 솔루션에 대한 [how BWin is using In-Memory OLTP to achieve unprecedented performance and scale](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale)(BWin이 메모리 내 OLTP를 사용하여 전례 없는 성능 및 규모를 사용하는 방법)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure SQL 제품 제품군의 다중 모델 기능은 Azure SQL 제품군 간에 공유 되는 핵심 SQL Server 데이터베이스 엔진 기능 이기도 합니다. 이러한 기능에 대한 자세한 내용을 보려면 SQL 관계형 데이터베이스 설명서 페이지를 방문하세요.

- [그래프 처리 중](/sql/relational-databases/graphs/sql-graph-overview)
- [JSON 데이터](/sql/relational-databases/json/json-data-sql-server)
- [공간 지원](/sql/relational-databases/spatial/spatial-data-sql-server)
- [XML 데이터](/sql/relational-databases/xml/xml-data-sql-server)
