---
title: Azure 유지 관리 이벤트 계획
description: Azure SQL Database 및 Azure SQL Managed Instance에서 계획 된 유지 관리 이벤트를 준비 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: sstein
ms.date: 3/23/2021
ms.openlocfilehash: eedbc46ee5feb0aa6f6a26c3f5b3c67ac8ca0a5e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044263"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance에서 Azure 유지 관리 이벤트 계획
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에서 데이터베이스에 대 한 계획 된 유지 관리 이벤트를 준비 하는 방법을 알아봅니다.

## <a name="what-is-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트란?

Azure SQL Database 및 Azure SQL Managed Instance 서비스를 안전 하 고 규정을 준수 하 고 안정적으로 유지 하기 위해 서비스 구성 요소를 통해 거의 지속적으로 업데이트를 수행 합니다. 최신의 강력한 서비스 아키텍처와 [핫 패치와](https://aka.ms/azuresqlhotpatching)같은 혁신적인 기술을 사용 하 여 대부분의 업데이트는 완전히 투명 하 고 서비스 가용성 측면에서 매우 완전 하지 않습니다. 그러나 소수의 업데이트는 짧은 서비스 인터럽트를 야기 하 고 특별 한 처리가 필요 합니다. 

각 데이터베이스 Azure SQL Database 및 Azure SQL Managed Instance는 한 복제본이 주 복제본 인 데이터베이스 복제본의 쿼럼을 유지 합니다. 항상 주 복제본이 온라인 서비스 여야 하 고 하나 이상의 보조 복제본이 정상 상태 여야 합니다. 계획된 유지 관리 기간 동안 데이터베이스 쿼럼 멤버는 한 번에 하나씩 오프라인 상태가 되는데, 그 이유는 응답하는 주 복제본이 하나 있고 보조 복제본이 하나 이상 있어서 클라이언트 가동 중단 시간이 없도록 보장하기 위해서 입니다. 주 복제본을 오프 라인 상태로 전환 해야 하는 경우 하나의 보조 복제본이 새로운 주 복제본이 될 수 있는 재구성 프로세스가 수행 됩니다.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>계획된 유지 관리 이벤트 기간 동안 예상되는 상황

유지 관리 이벤트는 유지 관리 이벤트가 시작 될 때 주 복제본과 보조 복제본의 constellation에 따라 단일 또는 여러 재구성을 생성할 수 있습니다. 평균적으로 1.7 재구성는 계획 된 유지 관리 이벤트 당 발생 합니다. 재구성는 일반적으로 30 초 이내에 완료 됩니다. 평균은 8 초입니다. 이미 연결 되어 있는 경우 응용 프로그램은 데이터베이스의 새 주 복제본에 다시 연결 해야 합니다. 새 주 복제본이 온라인 상태가 되기 전에 데이터베이스를 다시 구성 하는 동안 새 연결을 시도 하면 오류 40613 (데이터베이스를 사용할 수 없음): *"{servername} ' 서버에서" 데이터베이스 ' {databasename} '을 (를) 현재 사용할 수 없습니다. 나중에 연결을 다시 시도 하십시오. "* 데이터베이스에 장기 실행 쿼리가 있는 경우이 쿼리는 재구성 중에 중단 되므로 다시 시작 해야 합니다.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>계획 된 유지 관리 이벤트를 시뮬레이트하는 방법

프로덕션에 배포 하기 전에 클라이언트 응용 프로그램을 유지 관리 이벤트에 탄력적으로 적용 하면 응용 프로그램 오류에 대 한 위험을 완화 하 고 최종 사용자에 게 응용 프로그램 가용성을 제공할 수 있습니다. PowerShell, CLI 또는 REST API를 통해 [수동 장애 조치 (failover)를 시작](https://aka.ms/mifailover-techblog) 하 여 계획 된 유지 관리 이벤트 중에 클라이언트 응용 프로그램의 동작을 테스트할 수 있습니다. 주 복제본을 오프 라인 상태로 전환 하는 유지 관리 이벤트와 동일한 동작을 생성 합니다.

## <a name="retry-logic"></a>재시도 논리

클라우드 데이터베이스 서비스에 연결하는 모든 클라이언트 프로덕션 애플리케이션은 강력한 연결 [재시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)를 구현해야 합니다. 이렇게 하면 최종 사용자에 게 투명 하 게 재구성 하거나 최소한의 부정적 효과를 최소화 하는 데 도움이 됩니다.

## <a name="resource-health"></a>리소스 상태

데이터베이스에 로그온 오류가 발생 한 경우 [Azure Portal](https://portal.azure.com) 에서 현재 상태에 대 한 [Resource Health](../../service-health/resource-health-overview.md#get-started) 창을 확인 합니다. 상태 기록 섹션에는 각 이벤트에 대한 가동 중지 시간 이유가 포함됩니다(가능한 경우).

## <a name="maintenance-window-feature"></a>유지 관리 기간 기능

유지 관리 기간 기능을 사용 하면 적합 한 Azure SQL 데이터베이스 및 SQL 관리 되는 인스턴스에 대 한 예측 가능한 유지 관리 기간 일정을 구성할 수 있습니다. 자세한 내용은 [유지 관리 기간](maintenance-window.md) 을 참조 하십시오.

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 및 Azure SQL Managed Instance에 대 한 [Resource Health](resource-health-to-troubleshoot-connectivity.md) 에 대해 자세히 알아보세요.
- 재시도 논리에 대 한 자세한 내용은 [일시적인 오류에 대 한 다시 시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)를 참조 하세요.
- [유지 관리 기간 기능을](maintenance-window.md) 사용 하 여 유지 관리 기간 일정을 구성 합니다.
