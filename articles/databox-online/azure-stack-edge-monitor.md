---
title: Azure Stack Edge Pro 장치 모니터링 | Microsoft Docs
description: Azure Portal 및 로컬 웹 UI를 사용 하 여 Azure Stack Edge Pro를 모니터링 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: aae64cad3603725a4062d5afb42df974bbf8ac40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438792"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro 모니터링

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

이 문서에서는 Azure Stack Edge Pro를 모니터링 하는 방법을 설명 합니다. 디바이스를 모니터링하기 위해 Azure Portal 또는 로컬 웹 UI를 사용할 수 있습니다. Azure Portal을 사용하여 디바이스 이벤트를 보고 경고를 구성 및 관리하고 메트릭을 봅니다. 실제 디바이스에서 로컬 웹 UI를 사용하여 다양한 디바이스 구성 요소의 하드웨어 상태를 봅니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 디바이스 이벤트 및 해당 경고 보기
> * 디바이스 구성 요소의 하드웨어 상태 보기
> * 디바이스의 용량 및 트랜잭션 메트릭 보기

## <a name="view-device-events"></a>디바이스 이벤트 보기

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>하드웨어 상태 보기

디바이스 구성 요소의 하드웨어 상태를 보려면 로컬 웹 UI에서 다음 단계를 수행합니다.

1. 디바이스의 로컬 웹 UI에 연결합니다.
2. **유지 관리 > 하드웨어 상태** 로 이동합니다. 다양한 디바이스 구성 요소의 상태를 볼 수 있습니다.

    ![하드웨어 상태 보기](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>메트릭 보기

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>디바이스의 메트릭

이 섹션에서는 디바이스의 모니터링 메트릭에 대해 설명합니다. 메트릭은 다음과 같습니다.

* 용량 메트릭. 용량 메트릭은 디바이스의 용량과 관련이 있습니다.

* 트랜잭션 메트릭. 트랜잭션 메트릭은 Azure Storage에 대한 읽기 및 쓰기 작업과 관련이 있습니다.

* Edge 컴퓨팅 메트릭. Edge 컴퓨팅 메트릭은 디바이스의 Edge 컴퓨팅 사용량과 관련이 있습니다.

메트릭의 전체 목록은 다음 표에 나와 있습니다.

|용량 메트릭                     |Description  |
|-------------------------------------|-------------|
|**사용 가능한 용량**               | 디바이스에 쓸 수 있는 데이터의 크기를 나타냅니다. 즉,이 메트릭은 장치에서 사용 가능 하 게 설정할 수 있는 용량입니다. <br></br>장치와 클라우드 모두에 복사본이 있는 파일의 로컬 복사본을 삭제 하 여 장치 용량을 확보할 수 있습니다.        |
|**총 용량**                   | 는 데이터를 쓸 장치의 총 바이트 수를 나타냅니다 .이는 로컬 캐시의 전체 크기가 라고도 합니다. <br></br> 이제 데이터 디스크를 추가하여 기존 가상 디바이스의 용량을 늘릴 수 있습니다. VM에 대한 하이퍼바이저 관리를 통해 데이터 디스크를 추가한 다음, VM을 다시 시작합니다. 게이트웨이 디바이스의 로컬 스토리지 풀이 새로 추가된 데이터 디스크를 수용할 수 있도록 확장됩니다. <br></br>자세한 내용은 [Hyper-V 가상 머신용 하드 드라이브 추가](https://www.youtube.com/watch?v=EWdqUw9tTe4)로 이동하세요. |

|트랜잭션 메트릭              | Description         |
|-------------------------------------|---------|
|**클라우드 업로드 바이트 수(디바이스)**    | 디바이스의 모든 공유에 업로드된 모든 바이트의 합계입니다.        |
|**클라우드 업로드 바이트 수(공유)**     | 공유당 업로드된 바이트 수입니다. 이 메트릭은 다음과 같을 수 있습니다. <br></br> 평균 - 공유당 업로드된 모든 바이트의 합계/공유 수  <br></br>최대 - 공유에서 업로드된 최대 바이트 수 <br></br>최소 - 공유에서 업로드된 최소 바이트 수      |
|**클라우드 다운로드 처리량(공유)**| 공유당 다운로드된 바이트 수입니다. 이 메트릭은 다음과 같을 수 있습니다. <br></br> 평균 - 공유에서 읽거나 다운로드된 모든 바이트의 합계/공유 수 <br></br> 최대 - 공유에서 다운로드된 최대 바이트 수<br></br> 최소 - 공유에서 다운로드된 최소 바이트 수  |
|**클라우드 읽기 처리량**            | 디바이스의 모든 공유에 걸쳐 클라우드에서 읽은 모든 바이트의 합계입니다.     |
|**클라우드 업로드 처리량**          | 디바이스의 모든 공유에 걸쳐 클라우드에 쓴 모든 바이트의 합계입니다.     |
|**클라우드 업로드 처리량(공유)**  | 공유에서 클라우드에 쓴 모든 바이트 수의 합계/공유 수는 공유당 평균, 최대 및 최소입니다.      |
|**읽기 처리량 (네트워크)**           | 클라우드에서 읽은 모든 바이트 수에 대한 시스템 네트워크 처리량이 포함됩니다. 이 보기에는 공유로 제한되지 않는 데이터가 포함될 수 있습니다. <br></br>분할 하면 연결 되지 않았거나 사용 하도록 설정 되지 않은 어댑터를 포함 하 여 장치의 모든 네트워크 어댑터에 대 한 트래픽이 표시 됩니다.      |
|**쓰기 처리량 (네트워크)**       | 클라우드에 쓴 모든 바이트 수에 대한 시스템 네트워크 처리량이 포함됩니다. 이 보기에는 공유로 제한되지 않는 데이터가 포함될 수 있습니다. <br></br>분할 하면 연결 되지 않았거나 사용 하도록 설정 되지 않은 어댑터를 포함 하 여 장치의 모든 네트워크 어댑터에 대 한 트래픽이 표시 됩니다.          |

| Edge 컴퓨팅 메트릭              | Description         |
|-------------------------------------|---------|
|**Edge 컴퓨팅 - 메모리 사용량**      |           |
|**Edge 컴퓨팅 - CPU 사용률**    |         |

## <a name="next-steps"></a>다음 단계

[대역폭을 관리](azure-stack-edge-manage-bandwidth-schedules.md)하는 방법에 대해 알아봅니다.
[장치 이벤트 경고 알림을 관리](azure-stack-edge-gpu-manage-device-event-alert-notifications.md)하는 방법을 알아봅니다.
