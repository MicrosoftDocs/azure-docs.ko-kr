---
title: 무선을 통해 Azure Percept 진한를 업데이트 합니다.
description: Azure Percept에 대 한 air 업데이트를 통해 수신 하는 방법을 알아봅니다.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 2e627e582b47c5174e70f5d21d758148cde8dbdd
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022857"
---
# <a name="update-your-azure-percept-dk-over-the-air"></a>무선을 통해 Azure Percept 진한를 업데이트 합니다.

이 가이드를 참조 하 여 IoT Hub에 대 한 장치 업데이트를 통해 Azure Percept 진한 공중파의 캐리어 보드를 업데이트 하는 방법을 알아봅니다.

## <a name="import-your-update-file-and-manifest-file"></a>업데이트 파일 및 매니페스트 파일을 가져옵니다.

> [!NOTE]
> 업데이트를 이미 가져온 경우 직접 건너뛰고 **장치 업데이트 된 그룹을 만들** 수 있습니다.

1. Azure Percept 장치에 사용 하는 Azure IoT Hub으로 이동 합니다. 왼쪽 메뉴 패널의 **자동 장치 관리** 에서 **장치 업데이트** 를 선택 합니다.
 
1. 화면 위쪽에 여러 탭이 표시 됩니다. **업데이트** 탭을 선택 합니다.
 
1. **배포 준비 완료** 헤더 아래에서 **+ 새 업데이트 가져오기** 를 선택 합니다.
 
1. **매니페스트 파일 가져오기 선택** 아래의 상자를 클릭 하 고 **파일 업데이트를 선택** 하 여 적절 한 매니페스트 파일 (json) 및 업데이트 파일 하나 (. swu)를 선택 합니다. Azure Percept 장치에 대 한 이러한 업데이트 파일은 [여기](https://go.microsoft.com/fwlink/?linkid=2155625)에서 찾을 수 있습니다.
 
1. **저장소 컨테이너 선택** 에서 폴더 아이콘 또는 텍스트 상자를 선택 하 고 적절 한 저장소 계정을 선택 합니다.
 
1. 저장소 컨테이너를 이미 만든 경우에는 다시 사용할 수 있습니다. 그렇지 않으면 **+ 컨테이너** 를 선택 하 여 OTA 업데이트에 대 한 새 저장소 컨테이너를 만듭니다. 사용할 컨테이너를 선택 하 고 **선택** 을 클릭 합니다.
 
    >[!Note]
    >컨테이너가 없으면 컨테이너를 만들라는 메시지가 표시 됩니다.
 
1. **제출** 을 선택 하 여 가져오기 프로세스를 시작 합니다. 제출 프로세스는 약 4 분 정도 소요 됩니다.
 
    >[!Note]
    >선택한 저장소 컨테이너에 액세스 하는 CORS (원본 간 요청) 규칙을 추가 하 라는 메시지가 표시 될 수 있습니다. **규칙 추가** 를 선택 하 고 계속 진행 합니다.
 
    >[!Note]
    >이미지 크기로 인해 페이지를 **전송** 하는 중 ... 다음 단계를 표시 하기 전에 최대 5 분
    
1. 가져오기 프로세스가 시작 되 고 **장치 업데이트** 페이지의 **가져오기 기록** 탭으로 리디렉션됩니다. 가져오기 프로세스가 완료 되는 동안 진행률을 모니터링 하려면 **새로 고침** 을 클릭 합니다. 업데이트 크기에 따라 몇 분 이상 걸릴 수 있습니다 (사용량이 가장 많은 시간 동안 가져오기 서비스를 최대 1 시간까지 걸릴 수 있음).

1. 상태 열에 가져오기가 성공 했음을 표시 되 면 **배포 준비 완료** 탭을 선택 하 고 **새로 고침** 을 클릭 합니다. 이제 목록에 가져온 업데이트가 표시 됩니다.
 
## <a name="create-a-device-update-group"></a>장치 업데이트 그룹 만들기
IoT Hub에 대 한 장치 업데이트를 사용 하면 Azure Percept의 특정 그룹에 대 한 업데이트를 대상으로 지정할 수 있습니다. 그룹을 만들려면 Azure IoT Hub의 대상 장치 집합에 태그를 추가 해야 합니다.

> [!NOTE]
> 그룹을 이미 만든 경우에는 다음 단계로 바로 건너뛸 수 있습니다.

그룹 태그 요구 사항:
- 예약 된 값인 **범주화** 되지 않은 경우를 제외 하 고 모든 값을 태그에 추가할 수 있습니다.
- 태그 값은 255 자를 초과할 수 없습니다.
- 태그 값에는 ".", "-", "_", "~"와 같은 특수 문자만 사용할 수 있습니다.
- 태그 및 그룹 이름은 대/소문자를 구분 합니다.
- 장치에는 태그가 하나만 있을 수 있습니다. 장치에 추가 된 모든 후속 태그는 이전 태그를 재정의 합니다.
- 장치는 하나의 그룹에만 속할 수 있습니다.

1. 장치에 태그를 추가 합니다.
    1. 왼쪽 탐색 창의 **IoT Edge** 에서 AZURE Percept 진한 사용자를 찾아 해당 **장치** 쌍으로 이동 합니다.
    1. 아래와 같이 **IoT Hub 태그 값에 대 한 새 장치 업데이트** 를 추가 ```<CustomTagValue>``` 합니다 (값 (예: AzurePerceptGroup1). 장치 [쌍 JSON 문서 태그](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)에 대해 자세히 알아보세요.

    ```
    "tags": {
    "ADUGroup": "<CustomTagValue>"
    },
    ```

 
1. **저장** 을 클릭 하 여 서식 지정 문제를 해결 합니다.
 
1. 기존 Azure IoT Hub 태그를 선택 하 여 그룹을 만듭니다.
    1. Azure IoT Hub 페이지로 다시 이동 합니다.
    1. 왼쪽 메뉴 패널의 **장치 자동 관리** 에서 **장치 업데이트** 를 선택 합니다.
    1. **그룹** 탭을 선택 합니다. 이 페이지에는 장치 업데이트에 연결 된 그룹화 되지 않은 장치 수가 표시 됩니다.
    1. **+ 추가** 를 선택 하 여 새 그룹을 만듭니다.
    1. 목록에서 IoT Hub 태그를 선택 하 고 **제출** 을 클릭 합니다.
    1. 그룹이 만들어지면 업데이트 호환성 차트 및 그룹 목록이 업데이트 됩니다. 이 차트에서는 **최신 업데이트**, **사용 가능한 새 업데이트**, **진행 중인 업데이트**, **아직 그룹화 되지 않음** 의 다양 한 준수 상태에 있는 장치의 수를 보여 줍니다.
 

## <a name="deploy-an-update"></a>업데이트 배포
1. 새로 만든 그룹은 **사용 가능한 업데이트** (한 번 새로 고침 해야 할 수 있음)에 나열 된 새 업데이트로 표시 됩니다. 업데이트를 선택 합니다.
 
1. 올바른 장치 그룹이 대상 장치 그룹으로 선택 되어 있는지 확인 합니다. 배포 **시작 날짜** 와 **시작 시간** 을 선택 하 고 **배포 만들기** 를 클릭 합니다. 

    >[!CAUTION]
    >과거의 시작 시간을 설정 하면 배포가 즉시 트리거됩니다.
 
1. 준수 차트를 확인 합니다. 이제 업데이트가 진행 중인 것으로 표시됩니다.
 
1. 업데이트가 완료 되 면 준수 차트에 새 업데이트 상태가 반영 됩니다.
 
1. **장치 업데이트** 페이지의 맨 위에서 **배포** 탭을 선택 합니다.
 
1. 배포 세부 정보를 보려면 배포를 선택 합니다. **상태가** **성공** 으로 변경 될 때까지 **새로 고침** 을 클릭 해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이제 dev kit가 성공적으로 업데이트 되었습니다. Devkit를 사용 하 여 개발 및 작업을 계속할 수 있습니다.