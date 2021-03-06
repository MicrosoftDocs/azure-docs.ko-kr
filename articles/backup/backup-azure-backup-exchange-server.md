---
title: System Center DPM을 통해 Exchange server 백업
description: System Center 2012 R2 DPM을 사용하여 Azure Backup에 Exchange 서버를 백업하는 방법을 알아봅니다.
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: ee89af311619922fa6ca585381d70ca66955f36a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91271650"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>System Center 2012 R2 DPM을 사용하여 Azure Backup에 Exchange 서버 백업

이 문서에서는 System Center 2012 R2 Data Protection Manager(DPM) 서버를 구성하여 Azure Backup에 Microsoft Exchange server를 백업하는 방법을 설명합니다.  

## <a name="updates"></a>업데이트

Azure Backup을 사용하여 DPM 서버를 성공적으로 등록하려면 System Center 2012 R2 DPM 및 Azure Backup 에이전트의 최신 버전에 대한 최신 업데이트 롤업을 설치해야 합니다. [Microsoft 카탈로그](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)에서 최신 업데이트 롤업을 가져옵니다.

> [!NOTE]
> 이 문서의 예의 경우 Azure Backup 에이전트의 2.0.8719.0 버전을 설치하고 업데이트 롤업 6을 System Center 2012 R2 DPM에 설치합니다.
>
>

## <a name="prerequisites"></a>필수 구성 요소

계속하기 전에 워크로드를 보호하기 위하여 Microsoft Azure Backup 사용을 위한 [필수 구성 요소](backup-azure-dpm-introduction.md#prerequisites-and-limitations) 를 모두 충족하는지 확인합니다. 이러한 필수 구성 요소는 다음과 같습니다.

* Azure 사이트에서 백업 자격 증명 모음을 만들었습니다.
* DPM 서버에 에이전트 및 자격 증명 모음 자격 증명을 다운로드했습니다.
* 에이전트는 DPM 서버에 설치됩니다.
* DPM 서버를 등록하는 데 자격 증명 모음 자격 증명이 사용되었습니다.
* Exchange 2016을 보호 하는 경우 DPM 2012 R2 UR9 이상으로 업그레이드 합니다.

## <a name="dpm-protection-agent"></a>DPM 보호 에이전트

Exchange 서버에서 DPM 보호 에이전트를 설치하려면 다음 이 단계를 수행합니다.

1. 방화벽이 올바르게 구성되어 있는지 확인합니다. [에이전트에 대한 방화벽 예외 구성](/system-center/dpm/configure-firewall-settings-for-dpm)을 참조하세요.
2. DPM 관리자 콘솔에서 **관리 > 에이전트 > 설치** 를 선택 하 여 Exchange 서버에 에이전트를 설치 합니다. 자세한 단계는 [DPM 보호 에이전트 설치](/system-center/dpm/deploy-dpm-protection-agent) 를 참조하세요.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Exchange 서버에 보호 그룹 만들기

1. DPM 관리자 콘솔에서 **보호** 를 선택 하 고 도구 리본에서 **새로** 만들기를 선택 하 여 **새 보호 그룹 만들기** 마법사를 엽니다.
2. 마법사의 **시작** 화면에서 **다음** 을 선택 합니다.
3. **보호 그룹 종류 선택** 화면에서 **서버** 를 선택 하 고 **다음** 을 선택 합니다.
4. 보호할 Exchange server 데이터베이스를 선택 하 고 **다음** 을 선택 합니다.

   > [!NOTE]
   > Exchange 2013을 보호 하는 경우 [exchange 2013 필수 구성 요소](/system-center/dpm/back-up-exchange)를 확인 합니다.
   >
   >

    다음 예제에서는 Exchange 2010 데이터베이스를 선택합니다.

    ![그룹 구성원 선택](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. 데이터 보호 방법을 선택합니다.

    보호 그룹의 이름을 지정하고 다음 옵션 모두를 선택합니다.

   * 디스크를 사용하여 단기 보호를 하려고 합니다.
   * 온라인 보호를 사용하려고 합니다.
6. **다음** 을 선택합니다.
7. Exchange Server 데이터베이스의 무결성을 확인하려는 경우 **Eseutil 실행하여 데이터 무결성 확인** 옵션을 선택합니다.

    이 옵션을 선택 하면 Exchange 서버에서 **eseutil** 명령을 실행 하 여 생성 되는 i/o 트래픽을 방지 하기 위해 백업 일관성 확인이 DPM 서버에서 실행 됩니다.

   > [!NOTE]
   > 이 옵션을 사용하려면 DPM 서버에서 C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin 디렉터리로 Ese.dll 및 Eseutil.exe 파일을 복사해야 합니다. 그렇지 않으면 다음 오류가 트리거됩니다.  
   > ![eseutil 오류](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. **다음** 을 선택합니다.
9. **복사 백업** 에 사용할 데이터베이스를 선택 하 고 **다음** 을 선택 합니다.

   > [!NOTE]
   > 데이터베이스의 DAG 복사본 하나 이상에 대해 "전체 백업"을 선택 하지 않은 경우 로그는 잘리지 않습니다.
   >
   >
10. **단기 백업** 에 대 한 목표를 구성 하 고 **다음** 을 선택 합니다.
11. 사용 가능한 디스크 공간을 검토 하 고 **다음** 을 선택 합니다.
12. DPM 서버에서 초기 복제를 만들 시간을 선택 하 고 **다음** 을 선택 합니다.
13. 일관성 확인 옵션을 선택한 후 **다음** 을 선택 합니다.
14. Azure에 백업 하려는 데이터베이스를 선택 하 고 **다음** 을 선택 합니다. 예를 들면 다음과 같습니다.

    ![온라인 보호 데이터 지정](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. **Azure Backup** 일정을 정의한 후 **다음** 을 선택 합니다. 예를 들면 다음과 같습니다.

    ![온라인 백업 일정 지정](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > 온라인 복구 지점은 Express 전체 복구 지점을 기반으로 합니다. 따라서 빠른 전체 복구 지점에 지정 된 시간 이후 온라인 복구 지점을 예약 해야 합니다.
    >
    >
16. **Azure Backup** 에 대 한 보존 정책을 구성 하 고 **다음** 을 선택 합니다.
17. 온라인 복제 옵션을 선택 하 고 **다음** 을 선택 합니다.

    큰 데이터베이스를 가지고 있다면 네트워크를 통해 만들 초기 백업에 시간이 오래 걸릴 수 있습니다. 이 문제를 방지하려면 오프라인 백업을 만들 수 있습니다.  

    ![온라인 보존 정책 지정](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. 설정을 확인 하 고 **그룹 만들기** 를 선택 합니다.
19. **닫기** 를 선택합니다.

## <a name="recover-the-exchange-database"></a>Exchange 데이터베이스 복구

1. Exchange 데이터베이스를 복구 하려면 DPM 관리자 콘솔에서 **복구** 를 선택 합니다.
2. 복구하려는 Exchange 데이터베이스를 찾습니다.
3. *복구 시간* 드롭 다운 목록에서 온라인 복구 지점을 선택합니다.
4. 복구 **를 선택 하** 여 **복구 마법사** 를 시작 합니다.

온라인 복구 지점의 경우 다섯 가지 형식의 복구가 있습니다.

* **원래 Exchange Server 위치에 복구:** 원래 Exchange 서버에 데이터가 복구됩니다.
* **Exchange 서버에서 다른 데이터베이스 복구:** 데이터는 다른 Exchange 서버에 있는 다른 데이터베이스에 복구됩니다.
* **복구 데이터베이스에 복구:** 데이터는 Exchange 복구 데이터베이스(RDB)에 복구됩니다.
* **네트워크 폴더에 복사:** 데이터는 네트워크 폴더에 복구됩니다.
* **테이프에 복사:** DPM 서버에 연결되고 구성된 테이프 라이브러리 또는 독립 실행형 테이프 드라이브가 있는 경우 복구 지점은 사용 가능한 테이프에 복사됩니다.

    ![온라인 복제 선택](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>다음 단계

* [Azure Backup - FAQ](backup-azure-backup-faq.md)
