---
title: 지원 되는 버전-Azure Database for MySQL
description: Azure Database for MySQL 서비스에서 지원 되는 MySQL server 버전을 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 314462517ba4e63694266b5e49231cb8536f3635
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604732"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>지원되는 MySQL용 Azure 데이터베이스 서버 버전

Azure Database for MySQL는 InnoDB 저장소 엔진을 사용 하 여 [MySQL Community Edition](https://www.mysql.com/products/community/)에서 개발 되었습니다. 이 서비스는 커뮤니티에서 지원 되는 현재 주 버전 (MySQL 5.6, 5.7 및 8.0)을 모두 지원 합니다. MySQL은 X-y 명명 스키마를 사용 합니다. 여기서 X는 주 버전이 고 Y는 부 버전이 며 Z는 버그 수정 릴리스입니다. 스키마에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)를 참조하세요.



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>특정 MySQL 버전을 실행 하는 게이트웨이 노드에 연결

단일 서버 배포 옵션에서 게이트웨이는 서버 인스턴스에 대 한 연결을 리디렉션하는 데 사용 됩니다. 연결이 설정되면 MySQL 클라이언트는 MySQL Server 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MySQL 버전을 표시합니다. MySQL Server 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다. [연결 아키텍처](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) 를 검토 하 여 Azure Database for MySQL 서비스 아키텍처의 게이트웨이에 대해 자세히 알아보세요.

는 주 버전 v 5.6, v 5.7 및 v 8.0을 지원 Azure Database for MySQL, Azure Database for MySQL에 연결 하는 기본 포트 3306는 MySQL 클라이언트 버전 5.6 (최소 공통 분모)을 실행 하 여 지원 되는 세 가지 주 버전의 서버에 대 한 연결을 지원 합니다. 그러나 응용 프로그램이 특정 주 버전에 연결 해야 하는 경우 (예를 들어, v2.0 또는 v 8.0) 서버 연결 문자열의 포트를 변경 하 여 수행할 수 있습니다.

Azure Database for MySQL 서비스에서 게이트웨이 노드는 v 5.7 클라이언트의 경우 포트 3308에서, v 8.0 클라이언트의 경우 포트 3309에서 수신 합니다. 즉, v2.0 게이트웨이 클라이언트에 연결 하려는 경우 정규화 된 서버 이름 및 포트 3308을 사용 하 여 클라이언트 응용 프로그램에서 서버에 연결 해야 합니다. 마찬가지로, v 8.0 게이트웨이 클라이언트에 연결 하려는 경우 정규화 된 서버 이름 및 포트 3309을 사용 하 여 서버에 연결할 수 있습니다. 다음 예제를 확인 하 여 더 명확 하 게 합니다.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="다른 게이트웨이 mysql 버전을 통해 연결 하는 예제":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>현재 Azure Database for MySQL는 다음과 같은 주 버전 및 부 버전의 MySQL을 지원 합니다.


| 버전 | 단일 서버 <br/> 현재 부 버전 |유연한 서버(미리 보기) <br/> 현재 부 버전  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 버전 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (사용 되지 않음) | 지원 안 함|
|MySQL 버전 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 버전 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

버전 [지원 정책 설명서](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql) 에서 사용 중지 된 버전에 대 한 버전 지원 정책을 참조 하세요.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
서비스는 버그 수정 버전 업데이트에 대한 패치를 자동으로 관리합니다. 예: 5.7.20~5.7.21  

주 버전 업그레이드는 현재 service에서 MySQL v 5.6에서 v 5.7로의 업그레이드를 지원 합니다. 자세한 내용은 [주 버전 업그레이드를 수행 하는 방법](how-to-major-version-upgrade.md)을 참조 하세요. 5.7에서 8.0로 업그레이드 하려는 경우 새 엔진 버전으로 만든 서버에 대해 [덤프를 수행 하 고 복원](./concepts-migrate-dump-restore.md) 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL 버전 관리 정책에 대 한 자세한 내용은 [이 문서](concepts-version-policy.md)를 참조 하세요.
- **서비스 계층** 에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.