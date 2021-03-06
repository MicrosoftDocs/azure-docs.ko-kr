---
title: VM insights 종속성 에이전트를 업그레이드 하는 방법
description: 이 문서에서는 명령줄, 설치 마법사 및 기타 방법을 사용 하 여 VM insights 종속성 에이전트를 업그레이드 하는 방법을 설명 합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: acd1b6c8e5c1be76b29c93fddc57c799aef0a526
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046706"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>VM insights 종속성 에이전트를 업그레이드 하는 방법

VM insights 종속성 에이전트의 초기 배포 후에는 버그 수정 이나 새로운 기능 지원을 포함 하는 업데이트가 릴리스됩니다.  이 문서는 사용 가능한 방법과 자동화를 통해 업그레이드를 수행 하는 방법을 이해 하는 데 도움이 됩니다.

## <a name="upgrade-options"></a>업그레이드 옵션 

Windows 및 Linux 용 종속성 에이전트는 컴퓨터가 실행 되는 배포 시나리오 및 환경에 따라 수동으로 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다. 다음 메서드를 사용 하 여 에이전트를 업그레이드할 수 있습니다.

|환경 |설치 방법 |업그레이드 방법 |
|------------|--------------------|---------------|
|Azure VM | [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 용 종속성 에이전트 VM 확장 | *AutoUpgradeMinorVersion* 속성을 **false** 로 설정 하 여 옵트아웃 (opt out) 하도록 Azure Resource Manager 템플릿을 구성 하지 않은 경우 에이전트는 기본적으로 자동으로 업그레이드 됩니다. 자동 업그레이드를 사용 하지 않도록 설정 된 부 버전에 대 한 업그레이드 및 주 버전 업그레이드는 동일한 방법을 따릅니다. 제거 하 고 확장을 다시 설치 합니다. |
| 사용자 지정 Azure VM 이미지 | Windows/Linux 용 종속성 에이전트 수동 설치 | Vm을 최신 버전으로 업데이트 하려면 Windows installer 패키지를 실행 하는 명령줄 이나 Linux 자동 압축 풀기 및 설치 가능 셸 스크립트 번들에서 수행 해야 합니다.|
| 비 Azure Vm | Windows/Linux 용 종속성 에이전트 수동 설치 | Vm을 최신 버전으로 업데이트 하려면 Windows installer 패키지를 실행 하는 명령줄 이나 Linux 자동 압축 풀기 및 설치 가능 셸 스크립트 번들에서 수행 해야 합니다. |

## <a name="upgrade-windows-agent"></a>Windows 에이전트 업그레이드 

Windows VM의 에이전트를 종속성 에이전트 VM 확장을 사용 하 여 설치 되지 않은 최신 버전으로 업데이트 하려면 명령 프롬프트, 스크립트나 기타 자동화 솔루션 또는 InstallDependencyAgent-Windows.exe 설치 마법사를 사용 하 여를 실행 합니다.  

최신 버전의 Windows 에이전트는 [여기](https://aka.ms/dependencyagentwindows)에서 다운로드할 수 있습니다.

### <a name="using-the-setup-wizard"></a>설치 마법사 사용

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **InstallDependencyAgent-Windows.exe** 를 실행 하 여 설치 마법사를 시작 합니다.
   
3. **Dependency Agent 설치** 마법사의 지침에 따라 이전 버전의 종속성 에이전트를 제거한 다음 최신 버전을 설치 합니다.


### <a name="from-the-command-line"></a>명령줄에서

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 다음 명령을 실행합니다.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    `/RebootMode=manual`매개 변수를 사용 하는 경우 일부 프로세스에서 이전 버전의 파일을 사용 하 고 있고 잠금을 보유 하 고 있는 경우 업그레이드에서 자동으로 컴퓨터를 다시 부팅 하지 않습니다. 

3. 업그레이드에 성공 했는지 확인 하려면 `install.log` 자세한 설치 정보를 확인 하십시오. 로그 디렉터리는 *%Programfiles%\Microsoft Dependency Agent\logs* 입니다.

## <a name="upgrade-linux-agent"></a>Linux 에이전트 업그레이드 

Linux에서 종속성 에이전트의 이전 버전에서의 업그레이드는 지원 되며 새로 설치 하는 것과 동일한 명령에 따라 수행 됩니다.

최신 버전의 Linux 에이전트는 [여기](https://aka.ms/dependencyagentlinux)에서 다운로드할 수 있습니다.

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 루트로 다음 명령을 실행 합니다 `sh InstallDependencyAgent-Linux64.bin -s` . 

Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리는 */var/opt/microsoft/dependency-agent/log* 입니다. 

## <a name="next-steps"></a>다음 단계

일정 기간 동안 vm 모니터링을 중지 하거나 VM 정보를 완전히 제거 하려면 [vm insights에서 vm 모니터링 사용 안 함](../vm/vminsights-optout.md)을 참조 하세요.
