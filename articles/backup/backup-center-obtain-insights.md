---
title: Backup center를 사용 하 여 정보 얻기
description: 백업 센터에서 기록 추세를 분석 하 고 백업에 대 한 심층적인 통찰력을 얻는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c48173749a9b47be7eeb906e9f8eec716e0cb200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506017"
---
# <a name="obtain-insights-using-backup-center"></a>Backup center를 사용 하 여 정보 얻기

기록 추세를 분석 하 고 백업에 대 한 심층적인 통찰력을 얻기 위해 Backup 센터는 [Azure Monitor 로그](../azure-monitor/logs/data-platform-logs.md) 및 [Azure 통합 문서](../azure-monitor/visualize/workbooks-overview.md)를 사용 하는 [백업 보고서](configure-reports.md)에 대 한 인터페이스를 제공 합니다. 백업 보고서는 다음과 같은 기능을 제공 합니다.

- 사용된 클라우드 스토리지를 할당하고 예측합니다.

- 백업 및 복원을 감사합니다.

- 여러 세분성 수준에서 주요 추세를 확인합니다.

- 백업에 대 한 비용 최적화 기회에 대 한 가시성과 통찰력을 확보 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

- 백업 보고서는 현재 Azure Database for PostgreSQL 서버 백업에 사용할 수 없습니다.

- 지원 되는 시나리오 및 지원 되지 않는 시나리오에 대 한 자세한 목록은 [지원 매트릭스](backup-center-support-matrix.md) 를 참조 하세요.

## <a name="get-started"></a>시작

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Log Analytics 작업 영역에 데이터를 보내도록 자격 증명 모음 구성

[자격 증명 모음에 대 한 대규모로 진단 설정을 구성 하는 방법 알아보기](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>백업 센터 포털에서 백업 보고서 보기

백업 센터에서 **Backup reports** 메뉴 항목을 선택 하면 보고서가 열립니다. 하나 이상의 Log Analytics 작업 영역을 선택 하 여 백업에 대 한 주요 정보를 보고 분석 합니다.

![백업 센터에서 보고서 백업](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

사용할 수 있는 보기는 다음과 같습니다.

1. **요약** -이 탭을 사용 하 여 백업 공간에 대 한 개략적인 개요를 볼 수 있습니다. [자세히 알아보기](./configure-reports.md#summary)

2. **백업 항목** -이 탭을 사용 하 여 백업 항목 수준에서 사용 되는 클라우드 저장소에 대 한 정보 및 추세를 볼 수 있습니다. [자세히 알아보기](./configure-reports.md#backup-items)

3. **사용** -이 탭을 사용 하 여 백업에 대 한 주요 청구 매개 변수를 볼 수 있습니다. [자세히 알아보기](./configure-reports.md#usage)

4. **작업** -이 탭을 사용 하 여 하루에 실패 한 작업 수 및 작업 실패의 상위 원인 등의 장기 실행 추세를 볼 수 있습니다. [자세히 알아보기](./configure-reports.md#jobs)

5. **정책** -이 탭을 사용 하 여 연결 된 항목 수 및 지정 된 정책에 따라 백업 된 항목에서 사용 되는 총 클라우드 저장소와 같은 모든 활성 정책에 대 한 정보를 볼 수 있습니다. [자세히 알아보기](./configure-reports.md#policies)

6. **최적화** -이 탭을 사용 하 여 백업에 대 한 잠재적 비용 최적화 기회를 파악할 수 있습니다. [자세히 알아보기](./configure-reports.md#optimize)

7. **정책 준수** -이 탭을 사용 하 여 모든 백업 인스턴스에 하루에 하나 이상의 성공한 백업이 있는지 여부를 파악할 수 있습니다. [자세히 알아보기](./configure-reports.md#policy-adherence)

[전자 메일 보고서](backup-reports-email.md) 기능을 사용 하 여 이러한 보고서에 대 한 전자 메일을 구성할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [백업 모니터링 및 작동](backup-center-monitor-operate.md)
- [백업 공간 관리](backup-center-govern-environment.md)
- [백업 센터를 사용 하 여 작업 수행](backup-center-actions.md)