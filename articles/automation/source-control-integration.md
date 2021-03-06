---
title: Azure Automation에서 소스 제어 통합 사용
description: 이 문서에서는 Azure Automation 소스 제어를 다른 리포지토리와 동기화하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 281da27ce95649e85dae5d0795bb743f21fdb578
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631747"
---
# <a name="use-source-control-integration"></a>원본 제어 통합 사용

 Azure Automation의 소스 제어 통합은 소스 제어 리포지토리에서 단방향 동기화를 지원합니다. 소스 제어를 사용하면 GitHub 또는 Azure Repos 소스 제어 리포지토리의 스크립트로 Automation 계정의 Runbook을 최신 상태로 유지할 수 있습니다. 이 기능을 통해 개발 환경에서 테스트한 코드를 프로덕션 Automation 계정으로 쉽게 승격할 수 있습니다.

 소스 제어 통합을 사용하면 팀과 쉽게 공동 작업하고 변경 내용을 추적하며 이전 버전의 Runbook으로 롤백할 수 있습니다. 예를 들어, 소스 제어를 사용하면 소스 제어의 여러 다른 분기를 개발, 테스트 또는 프로덕션 Automation 계정과 동기화할 수 있습니다.

## <a name="source-control-types"></a>소스 제어 형식

Azure Automation은 3가지 유형의 소스 제어를 지원합니다.

* GitHub
* Azure Repos(Git)
* Azure Repos(TFVC)

## <a name="prerequisites"></a>사전 요구 사항

* 소스 제어 리포지토리(GitHub 또는 Azure Repos)
* [실행 계정](automation-security-overview.md#run-as-accounts)
* 이 [ `AzureRM.Profile` 모듈](/powershell/module/azurerm.profile/) 은 Automation 계정으로 가져와야 합니다. 해당 Az module ( `Az.Accounts` )은 Automation 소스 제어에서 작동 하지 않습니다.

> [!NOTE]
> 소스 제어 동기화 작업은 사용자 Automation 계정에서 실행되며 다른 Automation 작업과 동일한 요금으로 청구됩니다.

## <a name="configure-source-control"></a>소스 제어 구성

이 섹션에서는 Automation 계정에 대한 소스 제어를 구성하는 방법을 설명합니다. Azure Portal 또는 PowerShell을 사용할 수 있습니다.

### <a name="configure-source-control-in-azure-portal"></a>Azure Portal에서 소스 제어 구성

Azure Portal을 사용하여 소스 제어를 구성하려면 다음 절차를 수행합니다.

1. Automation 계정에서 **소스 제어** 를 선택하고 **추가** 를 클릭합니다.

    ![소스 제어 선택](./media/source-control-integration/select-source-control.png)

2. **소스 제어 형식** 을 선택한 후 **인증** 을 클릭합니다.

3. 브라우저 창이 열리고 로그인하라는 메시지가 표시됩니다. 지시에 따라 인증을 완료합니다.

4. 소스 제어 요약 페이지에서 필드를 사용하여 아래에 정의된 소스 제어 속성을 입력합니다. 완료되면 **저장** 을 클릭합니다.

    |속성  |Description  |
    |---------|---------|
    |소스 제어 이름     | 소스 제어의 식별 이름 이름에는 문자 및 숫자만 포함해야 합니다.        |
    |소스 제어 형식     | 소스 제어 메커니즘의 형식입니다. 사용 가능한 옵션은 다음과 같습니다.</br> * GitHub</br>* Azure Repos(Git)</br> * Azure Repos(TFVC)        |
    |리포지토리     | 리포지토리 또는 프로젝트의 이름입니다. 처음 200개의 리포지토리가 검색됩니다. 리포지토리를 검색하려면 필드에 이름을 입력하고 **GitHub에서 검색** 을 클릭합니다.|
    |Branch     | 소스 파일을 끌어올 분기입니다. 분기 대상 지정은 TFVC 소스 제어 형식에 사용할 수 없습니다.          |
    |폴더 경로     | 동기화할 Runbook을 포함하는 폴더(예: **/Runbooks**)입니다. 지정된 폴더의 Runbook만 동기화됩니다. 재귀는 지원되지 않습니다.        |
    |자동 동기화<sup>1</sup>     | 소스 제어 리포지토리에서 커밋이 이루어질 때 자동 동기화를 켜거나 끄는 설정입니다.        |
    |Runbook 게시     | 소스 제어에서 동기화한 후 Runbook이 자동으로 게시되는 경우 켜짐 설정이고, 그렇지 않으면 꺼짐 설정입니다.           |
    |Description     | 소스 제어에 대한 추가 세부 정보를 지정하는 텍스트입니다.        |

    <sup>1</sup> Azure Repos와 소스 제어 통합을 구성할 때 자동 동기화를 사용하려면 프로젝트 관리자여야 합니다.

   ![소스 제어 요약](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 소스 제어 리포지토리의 로그인은 Azure Portal 로그인과 다를 수도 있습니다. 소스 제어를 구성할 때는 소스 제어 리포지토리의 올바른 계정을 사용하여 로그인해야 합니다. 계정이 올바른지 확실치 않으면 브라우저에서 새 탭을 열고 **dev.azure.com**, **visualstudio.com** 또는 **github.com** 에서 로그아웃한 후 소스 제어를 다시 연결합니다.

### <a name="configure-source-control-in-powershell"></a>PowerShell에서 소스 제어 구성

PowerShell을 사용하여 Azure Automation에 소스 제어를 구성할 수도 있습니다. 이 작업에 PowerShell cmdlet을 사용하려면 PAT(개인용 액세스 토큰)가 필요합니다. [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol) cmdlet을 사용하여 소스 제어 연결을 만듭니다. 이 cmdlet에는 PAT에 대한 보안 문자열이 필요합니다. 보안 문자열을 만드는 방법을 알아보려면 [Convertto-html-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)을 참조하세요.

다음 하위 섹션에서는 GitHub, Azure Repos(Git) 및 Azure Repos(TFVC)에 대한 소스 제어 연결의 PowerShell 만들기를 보여 줍니다.

#### <a name="create-source-control-connection-for-github"></a>GitHub에 대한 소스 제어 연결 만들기

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Azure Repos(Git)에 대한 소스 제어 연결 만들기

> [!NOTE]
> Azure Repos(Git)는 이전 형식에서 사용된 **visualstudio.com** 대신 **dev.azure.com** 에 액세스하는 URL을 사용합니다. 이전 URL 형식인 `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>`은 더 이상 사용되지 않지만 계속 지원됩니다. 새 형식을 사용하는 것이 좋습니다.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Azure Repos(TFVC)에 대한 소스 제어 연결 만들기

> [!NOTE]
> Azure Repos(TFVC)는 이전 형식에서 사용된 **visualstudio.com** 대신 **dev.azure.com** 에 액세스하는 URL을 사용합니다. 이전 URL 형식인 `https://<accountname>.visualstudio.com/<projectname>/_versionControl`은 더 이상 사용되지 않지만 계속 지원됩니다. 새 형식을 사용하는 것이 좋습니다.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>PAT(개인용 액세스 토큰) 권한

소스 제어에는 PAT에 대한 최소 권한이 필요합니다. 다음 표에는 GitHub 및 Azure Repos에 필요한 최소 권한이 나와 있습니다.

##### <a name="minimum-pat-permissions-for-github"></a>GitHub에 대한 최소 PAT 권한

다음 표에는 GitHub에 필요한 최소 PAT 권한이 정의되어 있습니다. GitHub에서 PAT를 만드는 방법에 대한 자세한 내용은 [명령줄에 대한 개인용 액세스 토큰 만들기](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)를 참조하세요.

|범위  |Description  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | 커밋 상태 액세스         |
|`repo_deployment`      | 배포 상태 액세스         |
|`public_repo`     | 공용 리포지토리 액세스         |
|`repo:invite` | 액세스 저장소 초대 |
|`security_events` | 읽기 및 쓰기 보안 이벤트 |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | 리포지토리 후크 쓰기         |
|`read:repo_hook`|리포지토리 후크 읽기|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure Repos에 대한 최소 PAT 권한

다음 목록에는 Azure Repos에 필요한 최소 PAT 권한이 정의되어 있습니다. Azure Repos에서 PAT를 만드는 방법에 대한 자세한 내용은 [개인용 액세스 토큰을 사용하여 액세스 인증](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)을 참조하세요.

| 범위  |  액세스 형식  |
|---------| ----------|
| `Code`      | 읽기  |
| `Project and team` | 읽기 |
| `Identity` | 읽기     |
| `User profile` | 읽기     |
| `Work items` | 읽기    |
| `Service connections` | 읽기, 쿼리, 관리<sup>1</sup>    |

<sup>1</sup> `Service connections` 권한은 자동 동기화를 사용하도록 설정한 경우에만 필요합니다.

## <a name="synchronize-with-source-control"></a>소스 제어와 동기화

소스 제어와 동기화하려면 다음 단계를 수행합니다.

1. 소스 제어 페이지의 표에서 소스를 선택합니다.

2. **동기화 시작** 을 클릭하여 동기화 프로세스를 시작합니다.

3. **동기화 작업** 탭을 클릭하여 현재 동기화 작업 또는 이전 작업의 상태를 볼 수 있습니다.

4. **소스 제어** 드롭다운 메뉴에서 소스 제어 메커니즘을 선택합니다.

    ![동기화 상태](./media/source-control-integration/sync-status.png)

5. 작업을 클릭하면 작업 출력을 볼 수 있습니다. 다음은 소스 제어 동기화 작업의 출력 예입니다.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. 소스 제어 동기화 작업 요약 페이지에서 **모든 로그** 를 선택하여 추가 로깅을 사용할 수 있습니다. 이러한 추가 로그 항목은 소스 제어를 사용할 때 발생할 수 있는 문제를 해결하는 데 도움이 될 수 있습니다.

## <a name="disconnect-source-control"></a>소스 제어 연결 끊기

소스 제어 리포지토리에서 연결을 끊으려면 다음을 수행합니다.

1. Automation 계정의 **계정 설정** 에서 **소스 제어** 를 엽니다.

2. 제거할 소스 제어 메커니즘을 선택합니다.

3. 소스 제어 요약 페이지에서 **삭제** 를 클릭합니다.

## <a name="handle-encoding-issues"></a>인코딩 문제 처리

여러 사용자가 여러 편집기를 사용하여 소스 제어 리포지토리에서 Runbook을 편집하는 경우 인코딩 문제가 발생할 수 있습니다. 이 상황을 자세히 알아보려면 [인코딩 문제의 일반적인 원인](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)을 참조하세요.

## <a name="update-the-pat"></a>PAT 업데이트

현재는 Azure Portal을 사용하여 소스 제어에서 PAT를 업데이트할 수 없습니다. PAT가 만료되거나 철회되면 다음 방법 중 하나로 새 액세스 토큰을 사용하여 소스 제어를 업데이트할 수 있습니다.

* [REST API](/rest/api/automation/sourcecontrol/update)를 사용합니다.
* [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) cmdlet을 사용합니다.

## <a name="next-steps"></a>다음 단계

* Azure Automation에서 소스 제어를 통합하려면 [Azure Automation: Azure Automation에서 소스 제어 통합](https://azure.microsoft.com/blog/azure-automation-source-control-13/)을 참조하세요.  
* Visual Studio Codespaces runbook 소스 제어를 통합 하는 방법에 대 한 자세한 내용은 [Azure Automation: Visual Studio Codespaces를 사용 하 여 Runbook 원본 제어 통합](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)을 참조 하세요.
