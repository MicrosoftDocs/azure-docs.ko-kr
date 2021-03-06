---
title: Azure IoT Hub에 대 한 장치 업데이트에서 장치 그룹 만들기 | Microsoft Docs
description: Azure IoT Hub에 대 한 장치 업데이트에서 장치 그룹 만들기
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679519"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>IoT Hub에 대 한 장치 업데이트에서 장치 그룹 만들기
IoT Hub에 대 한 장치 업데이트를 통해 IoT 장치 그룹에 업데이트를 배포할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [IoT Hub 사용 하도록 설정 된 장치 업데이트를 사용 하는 IoT Hub에 대 한 액세스](create-device-update-account.md). IoT Hub에 대해 S1 (Standard) 계층 이상을 사용 하는 것이 좋습니다. 
* IoT Hub 내에서 장치 업데이트를 위해 프로 비전 된 IoT 장치 (또는 시뮬레이터)입니다.
* [프로 비전 된 장치에 대 한 업데이트를 하나 이상 가져왔습니다.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>장치에 태그 추가  

IoT Hub에 대 한 장치 업데이트를 통해 IoT 장치 그룹에 업데이트를 배포할 수 있습니다. 그룹을 만들려면 첫 번째 단계는 IoT Hub의 대상 장치 집합에 태그를 추가 하는 것입니다. 장치 업데이트에 연결 된 태그는 장치에만 추가할 수 있습니다.

아래 설명서에서는 태그를 추가 하 고 업데이트 하는 방법을 설명 합니다.

### <a name="programmatically-update-device-twin"></a>프로그래밍 방식으로 장치 쌍 업데이트

장치 업데이트를 사용 하 여 장치를 등록 한 후에는 RegistryManager를 사용 하 여 장치 쌍을 적절 한 태그로 업데이트할 수 있습니다. 
[샘플 .NET 앱을 사용 하 여 태그를 추가 하는 방법을 알아봅니다.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[태그 속성에 대해 알아봅니다](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>장치 업데이트 태그 형식

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>작업 사용

[이러한](../iot-hub/iot-hub-devguide-jobs.md) 예제를 수행 하 여 장치 업데이트 태그를 추가 하거나 업데이트 하기 위해 여러 장치에서 작업을 예약할 수 있습니다. [자세한 정보를 알아보세요](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > 이 작업은 현재 IOT Hub 메시지 할당량을 기반으로 하며, 한 번에 최대 5만 개의 장치 쌍 태그만 변경 하는 것이 좋습니다. 그렇지 않으면 일일 IoT Hub 메시지 할당량을 초과 하는 경우 IoT Hub 단위를 추가로 구입 해야 할 수 있습니다. 자세한 내용은 [할당량 및 제한](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling)에서 찾을 수 있습니다.

### <a name="direct-twin-updates"></a>직접 쌍 업데이트

장치 쌍에서 태그를 직접 추가 하거나 업데이트할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 IoT Hub으로 이동 합니다.

2. 왼쪽 탐색 창의 'IoT 디바이스' 또는 'IoT Edge'에서 IoT 디바이스를 찾아 디바이스 쌍으로 이동합니다.

3. 디바이스 쌍에서 기존 디바이스 업데이트 태그 값을 null로 설정하여 삭제합니다.

4. 아래와 같이 새 디바이스 업데이트 태그 값을 추가합니다. [태그가 있는 장치 쌍 JSON 문서 예제입니다.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>제한 사항

* 예약 된 값인 ' 범주화 되지 않음 '을 제외 하 고 모든 값을 태그에 추가할 수 있습니다.
* 태그 값은 255 자를 초과할 수 없습니다.
* 태그 값에는 영숫자 문자와 다음 특수 문자 ".", "-", "_", "~"을 사용할 수 있습니다.
* 태그 및 그룹 이름은 대/소문자를 구분 합니다.
* 장치에는 ADUGroup 이름을 가진 태그를 하나만 포함할 수 있습니다. 그러면 해당 이름의 태그를 추가할 때 태그 이름 ADUGroup에 대 한 기존 값이 재정의 됩니다.
* 한 장치는 하나의 그룹에만 속할 수 있습니다.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>기존 IoT Hub 태그를 선택 하 여 장치 그룹 만들기

1. [Azure 포털](https://portal.azure.com)로 이동합니다.

2. 장치 업데이트 인스턴스에 이전에 연결한 IoT Hub를 선택 합니다.

3. 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.

4. 페이지 맨 위에서 그룹 탭을 선택합니다. 아직 그룹화 되지 않은 장치 업데이트에 연결 된 장치 수를 확인할 수 있습니다.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="그룹화 되지 않은 장치의 스크린샷" lightbox="media/create-update-group/ungrouped-devices.png":::

5. 추가 단추를 선택하여 새 그룹을 만듭니다.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="장치 그룹 추가의 스크린샷" lightbox="media/create-update-group/add-group.png":::

6. 목록에서 IoT Hub 태그를 선택 하 고 업데이트 그룹 만들기를 선택 합니다.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="태그 선택의 스크린샷" lightbox="media/create-update-group/select-tag.png":::

7. 그룹이 만들어지면 업데이트 호환성 차트 및 그룹 목록이 업데이트 된 것을 볼 수 있습니다.  업데이트 호환성 차트는 최신 업데이트, 사용 가능한 새 업데이트, 진행 중인 업데이트 및 아직 그룹화 되지 않은 장치에 대 한 다양 한 준수 상태의 장치 수를 표시 합니다. [업데이트 준수에 대해 알아봅니다.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="업데이트 준수 보기의 스크린샷" lightbox="media/create-update-group/updated-view.png":::

8. 새로 만든 그룹 및 새 그룹의 장치에 대 한 사용 가능한 업데이트가 표시 됩니다. 업데이트 이름을 클릭 하 여이 보기에서 새 그룹에 업데이트를 배포할 수 있습니다. 자세한 내용은 다음 단계: 업데이트 배포를 참조 하세요.

## <a name="view-device-details-for-the-group-you-created"></a>만든 그룹의 장치 세부 정보 보기

1. 새로 만든 그룹으로 이동 하 고 그룹 이름을 클릭 합니다.

2. 그룹의 일부인 장치 목록이 해당 장치 업데이트 속성과 함께 표시 됩니다. 이 보기에는 해당 그룹의 구성원 인 모든 장치에 대 한 업데이트 준수 정보를 볼 수도 있습니다. 업데이트 호환성 차트는 다양 한 규정 준수 상태의 장치 수를 표시 합니다. 최신 업데이트, 사용 가능한 새 업데이트 및 진행 중인 업데이트에 대해 설명 합니다.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="장치 그룹 정보 보기의 스크린샷" lightbox="media/create-update-group/group-details.png":::

3. 그룹 내의 각 개별 장치를 클릭 하 여 IoT Hub의 장치 세부 정보 페이지로 리디렉션할 수도 있습니다.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="장치 세부 정보 보기의 스크린샷" lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>다음 단계 

[업데이트 배포](deploy-update.md)

[장치 그룹에 대 한 자세한 정보](device-update-groups.md)

[업데이트 준수에 대해 알아봅니다.](device-update-compliance.md)
