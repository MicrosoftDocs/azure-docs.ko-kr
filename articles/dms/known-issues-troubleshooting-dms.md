---
title: 일반적인 문제-Azure Database Migration Service
description: Azure Database Migration Service 사용과 관련 된 일반적인 알려진 문제/오류를 해결 하는 방법에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: ce53e8a77186f96801879e5c9d8f8c65809470d0
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639806"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>일반적인 Azure Database Migration Service 문제 및 오류 해결

이 문서에서는 사용자가 수행할 수 Azure Database Migration Service 있는 몇 가지 일반적인 문제 및 오류에 대해 설명 합니다. 이 문서에는 이러한 문제 및 오류를 해결 하는 방법에 대 한 정보도 포함 되어 있습니다.

> [!NOTE]
> 바이어스 없는 통신
>
> Microsoft는 다양하고 포용적인 환경을 지원합니다. 이 문서에는 _slave(슬레이브)_ 라는 단어에 대한 참조가 포함되어 있습니다. [바이어스 없는 통신을 위한 Microsoft 스타일 가이드](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)에서는 이 단어를 '배제(exclusionary)'라는 단어로 인식합니다. 이 단어는 현재 소프트웨어에 표시되는 단어이므로 일관성을 위해 이 문서에서 사용됩니다. 이 단어를 제거하도록 소프트웨어가 업데이트되면 이 문서도 이에 맞춰 업데이트됩니다.
>

## <a name="migration-activity-in-queued-state"></a>대기 상태의 마이그레이션 작업

Azure Database Migration Service 프로젝트에서 새 활동을 만들 때 활동은 대기 중 상태로 유지 됩니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 문제는 Azure Database Migration Service 인스턴스가 동시에 실행 되는 진행 중인 작업의 최대 용량에 도달 했을 때 발생 합니다. 용량을 사용할 수 있게 될 때까지 새 활동은 모두 큐에 대기 됩니다. | 데이터 마이그레이션 서비스 인스턴스가 프로젝트 간에 작업을 실행 하 고 있는지 확인 합니다. 실행을 위해 큐에 자동으로 추가 되는 새 활동을 계속 해 서 만들 수 있습니다. 실행 중인 기존 작업이 완료 되 면 바로 다음에 대기 중인 작업이 실행 되기 시작 하 고 상태가 자동으로 실행 중 상태로 변경 됩니다. 대기 중인 작업의 마이그레이션을 시작 하기 위해 추가 작업을 수행할 필요는 없습니다.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>마이그레이션을 위해 선택한 최대 데이터베이스 수

Azure SQL Database 또는 Azure SQL Managed Instance로 이동 하기 위해 데이터베이스 마이그레이션 프로젝트에 대 한 작업을 만들 때 다음과 같은 오류가 발생 합니다.

* **오류**: 마이그레이션 설정 유효성 검사 오류 "," errordetail ":" ' 데이터베이스 '의 최대 개수 ' 4 ' 개 이상의 개체를 마이그레이션하도록 선택 했습니다. "

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 단일 마이그레이션 작업을 위해 데이터베이스를 4 개 이상 선택한 경우에 표시 됩니다. 현재 각 마이그레이션 작업은 4 개의 데이터베이스로 제한 됩니다. | 마이그레이션 작업당 4 개 이하의 데이터베이스를 선택 합니다. 네 개 이상의 데이터베이스를 병렬로 마이그레이션해야 하는 경우 Azure Database Migration Service의 다른 인스턴스를 프로 비전 해야 합니다. 현재 각 구독은 최대 2 개의 Azure Database Migration Service 인스턴스를 지원 합니다.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>복구 오류가 발생 하 여 Azure MySQL에 대 한 MySQL 마이그레이션 오류

Azure Database Migration Service를 사용 하 여 MySQL에서 Azure Database for MySQL로 마이그레이션하면 마이그레이션 작업이 실패 하 고 다음 오류가 발생 합니다.

* **오류**: 데이터베이스 마이그레이션 오류-[n] 연속 복구 오류로 인해 ' TaskID ' 태스크가 일시 중단 되었습니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 마이그레이션을 수행 하는 사용자에 게 게 replicationadmin 역할이 역할 및/또는 복제 클라이언트, 복제 복제본 및 슈퍼 (MySQL 5.6.6 이전 버전)의 권한이 없는 경우에 발생할 수 있습니다.<br><br><br><br><br><br><br><br><br><br><br><br><br> | 사용자 계정에 대 한 필수 구성 요소 [권한이](./tutorial-mysql-azure-mysql-online.md#prerequisites) Azure Database for MySQL 인스턴스에서 정확 하 게 구성 되었는지 확인 합니다. 예를 들어 다음 단계를 수행 하 여 필요한 권한이 있는 ' migrateuser ' 라는 사용자를 만들 수 있습니다.<br>1. ' secret '로 식별 된 사용자 migrateuser@ '% '을 (를) 만듭니다. <br>2. ' migrateuser ' @ '% '에 대 db_name 한 모든 권한을 ' secret '로 식별 된 모든 권한을 부여 합니다. 이 단계를 반복 하 여 더 많은 데이터베이스에 대 한 액세스 권한 부여 <br>3. 복제 종속 *을에 부여 합니다.* ' secret '로 식별 되는 ' migrateuser ' @ '% '<br>4 .에 복제 클라이언트를 *부여 합니다.* ' secret '로 식별 되는 ' migrateuser ' @ '% '<br>5. Flush 권한; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Azure Database Migration Service를 중지 하는 동안 오류 발생

Azure Database Migration Service 인스턴스를 중지 하면 다음과 같은 오류가 표시 됩니다.

* **오류**: 서비스를 중지 하지 못했습니다. 오류: {'error':{'code':'InvalidRequest','message':'하나 이상의 작업이 현재 실행 중입니다. 서비스를 중지 하려면 작업이 완료 될 때까지 기다리거나 해당 작업을 수동으로 중지 한 후 다시 시도 하십시오. '}}

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 중지 하려는 서비스 인스턴스에 마이그레이션 프로젝트에 아직 실행 중이거나 있는 작업이 포함 되어 있을 때 표시 됩니다. <br><br><br><br><br><br> | 중지 하려는 Azure Database Migration Service 인스턴스에서 실행 중인 작업이 없는지 확인 합니다. 서비스를 중지 하기 전에 활동이 나 프로젝트를 삭제할 수도 있습니다. 다음 단계에서는 실행 중인 모든 태스크를 삭제 하 여 마이그레이션 서비스 인스턴스를 정리 하는 프로젝트를 제거 하는 방법을 보여 줍니다.<br>1. Install-Module-Name AzureRM. Microsoft.datamigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription-SubscriptionName " \<subName> " <br> 4. Remove-AzureRmDataMigrationProject- \<projectName> ResourceGroupName- \<rgName> \<serviceName> DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Azure Database Migration Service를 시작 하는 동안 오류 발생

Azure Database Migration Service 인스턴스를 시작할 때 다음과 같은 오류가 표시 됩니다.

* **오류**: 서비스를 시작 하지 못했습니다. 오류: {' errorDetail ': ' 서비스를 시작 하지 못했습니다. Microsoft 지원에 문의 하세요. '}

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 이전 인스턴스가 내부적으로 실패 한 경우 표시 됩니다. 이 오류는 거의 발생 하지 않으며 엔지니어링 팀이이를 인식 합니다. <br> | 시작할 수 없는 서비스 인스턴스를 삭제 한 다음 새 서비스를 프로 비전 하 여 바꿉니다. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>SQL을 Azure SQL DB 관리 되는 인스턴스로 마이그레이션하는 동안 데이터베이스를 복원 하는 동안 오류 발생

SQL Server에서 Azure SQL Managed Instance로 온라인 마이그레이션을 수행 하는 경우 다음과 같은 오류가 발생 하 여 가공선이 실패 합니다.

* **오류**: 작업 Id ' operationId '의 복원 작업에 실패 했습니다. ' AuthorizationFailed ' 코드, 개체 id가 ' objectId ' 인 클라이언트 ' clientId '에 '/subscriptions/subscriptionId ' 범위에 대해 ' managedDatabaseRestoreAzureAsyncOperation/위치//읽기 ' 작업을 수행할 권한이 없습니다.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 오류는 SQL Server에서 SQL Managed Instance로의 온라인 마이그레이션에 사용 되는 응용 프로그램 보안 주체가 구독에 대 한 참가 권한이 없음을 나타냅니다. 현재 Managed Instance 있는 특정 API 호출에는 복원 작업에 대 한 구독에 대 한이 권한이 필요 합니다. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | `Get-AzureADServicePrincipal` `-ObjectId` 오류 메시지에서 사용할 수 있는 PowerShell cmdlet을 사용 하 여 사용 중인 응용 프로그램 ID의 표시 이름을 나열 합니다.<br><br> 이 응용 프로그램에 대 한 사용 권한의 유효성을 검사 하 고 구독 수준에서 [참가자 역할이](../role-based-access-control/built-in-roles.md#contributor) 있는지 확인 합니다. <br><br> Azure Database Migration Service 엔지니어링 팀에서 구독에 대 한 현재 참가 역할의 필수 액세스를 제한 하기 위해 작업 중입니다. 참가 역할의 사용을 허용 하지 않는 비즈니스 요구 사항이 있는 경우 Azure 지원에 문의 하 여 추가 도움을 요청 합니다. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Azure Database Migration Service와 연결 된 NIC를 삭제 하는 동안 오류 발생

Azure Database Migration Service와 연결 된 네트워크 인터페이스 카드를 삭제 하려고 하면 다음 오류와 함께 삭제 시도가 실패 합니다.

* **오류**: nic를 활용 하는 DMS 서비스로 인해 Azure Database Migration Service 연결 된 nic를 삭제할 수 없습니다.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 문제는 Azure Database Migration Service 인스턴스가 존재 하 고 NIC를 사용할 수 있을 때 발생 합니다. <br><br><br><br><br><br><br><br> | 이 NIC를 삭제 하려면 서비스에서 사용 하는 NIC를 자동으로 삭제 하는 DMS 서비스 인스턴스를 삭제 합니다.<br><br> **중요**: 삭제 중인 Azure Database Migration Service 인스턴스에 실행 중인 작업이 없는지 확인 합니다.<br><br> Azure Database Migration Service 인스턴스에 연결 된 모든 프로젝트와 활동을 삭제 한 후에는 서비스 인스턴스를 삭제할 수 있습니다. 서비스 인스턴스에서 사용 하는 NIC는 서비스 삭제 과정에서 자동으로 정리 됩니다. |

## <a name="connection-error-when-using-expressroute"></a>ExpressRoute를 사용할 때 발생하는 연결 오류

Azure Database Migration 서비스 프로젝트 마법사에서 원본에 연결하려고 할 때 원본이 ExpressRoute를 연결에 사용하는 경우 긴 제한 시간이 지난 후 연결이 실패합니다.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| [Express](https://azure.microsoft.com/services/expressroute/)경로를 사용 하는 경우 서비스와 연결 된 Virtual Network 서브넷에서 3 개의 서비스 끝점을 프로 비전 [해야](./tutorial-sql-server-to-azure-sql.md) Azure Database Migration Service.<br> --Service Bus 끝점<br> --저장소 끝점<br> --대상 데이터베이스 끝점 (예: SQL 끝점, Cosmos DB 끝점)<br><br><br><br><br> | 원본 및 Azure Database Migration Service 간 Express 경로 연결에 필요한 서비스 끝점을 [사용 하도록 설정](./tutorial-sql-server-to-azure-sql.md) 합니다. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Mysql 용 Azure DB로 MySQL 데이터베이스를 마이그레이션할 때 잠금 대기 시간 초과 오류가 발생 했습니다.

Azure Database Migration Service를 통해 MySQL 데이터베이스를 Azure Database for MySQL 인스턴스로 마이그레이션하면 다음과 같은 잠금 대기 시간 초과 오류가 발생 하 여 마이그레이션이 실패 합니다.

* **오류**: 데이터베이스 마이그레이션 오류-파일을 로드 하지 못했습니다. 파일 ' n ' RetCode에 대 한 로드 프로세스를 시작 하지 못했습니다.: SQL_ERROR SQLSTATE: HY000 NativeError: 1205 메시지: [MySQL] [ODBC 드라이버] [mysqld] 잠금 대기 시간 초과 트랜잭션을 다시 시작 해 보세요.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 오류는 마이그레이션하는 동안 잠금 대기 시간 제한으로 인해 마이그레이션이 실패할 때 발생 합니다. | 서버 매개 변수 **' innodb_lock_wait_timeout '** 의 값을 늘립니다. 허용 되는 최대값은 1073741824입니다. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>동적 포트 또는 명명 된 인스턴스를 사용 하는 경우 원본 SQL Server에 연결 하는 동안 오류 발생

명명 된 인스턴스나 동적 포트 중 하나에서 실행 되는 SQL Server 원본에 Azure Database Migration Service 연결 하려고 하면 다음 오류가 발생 하 여 연결이 실패 합니다.

* **오류**:-1-SQL 연결에 실패 했습니다. SQL Server에 연결하는 중에 네트워크 관련 오류 또는 인스턴스별 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인합니다. (공급자: SQL 네트워크 인터페이스, 오류: 26 - 지정된 서버/인스턴스 찾기 오류)

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 문제는 Azure Database Migration Service 연결 하려고 하는 원본 SQL Server 인스턴스가 동적 포트를가지고 있거나 명명 된 인스턴스를 사용 하는 경우에 발생 합니다. SQL Server Browser 서비스는 명명 된 인스턴스에 대 한 들어오는 연결 또는 동적 포트를 사용할 때 UDP 포트 1434을 수신 합니다. SQL Server 서비스가 다시 시작 될 때마다 동적 포트가 변경 될 수 있습니다. SQL Server 구성 관리자의 네트워크 구성을 통해 인스턴스에 할당 된 동적 포트를 확인할 수 있습니다.<br><br><br> |Azure Database Migration Service이 해당 하는 경우 동적으로 할당 된 TCP 포트를 통해 UDP 포트 1434 및 SQL Server 인스턴스에서 원본 SQL Server Browser 서비스에 연결할 수 있는지 확인 합니다. |

## <a name="additional-known-issues"></a>추가 알려진 문제

* [Azure SQL Database에 대 한 온라인 마이그레이션과 관련 하 여 알려진 문제/마이그레이션 제한 사항](./index.yml)
* [Azure Database for MySQL에 대 한 온라인 마이그레이션과 관련 하 여 알려진 문제/마이그레이션 제한 사항](./known-issues-azure-mysql-online.md)
* [Azure Database for PostgreSQL에 대 한 온라인 마이그레이션과 관련 하 여 알려진 문제/마이그레이션 제한 사항](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>다음 단계

* [PowerShell Azure Database Migration Service](/powershell/module/azurerm.datamigration#data_migration)문서를 참조 하세요.
* [Azure Portal를 사용 하 여 Azure Database for MySQL에서 서버 매개 변수를 구성 하는 방법](../mysql/howto-server-parameters.md)문서를 참조 하세요.
* [Azure Database Migration Service 사용을 위한 필수 구성 요소 개요](./pre-reqs.md)문서를 참조 하세요.
* [Azure Database Migration Service 사용에 대 한 FAQ](./faq.md)를 참조 하세요.