---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 552a40be0c069d1002ebc7ea4dafe0d6f93a5755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85128190"
---
[메시지 라우팅](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)을 사용하면 원격 분석 데이터를 IoT 디바이스에서 기본 제공된 Event Hub 호환 엔드포인트 또는 Blob Storage, Service Bus 큐, Service Bus 항목 및 Event Hubs와 같은 사용자 지정 엔드포인트에 전송할 수 있습니다. 사용자 지정 메시지 라우팅을 구성하려면 특정 조건에 일치하는 경로를 사용자 지정하는 [라우팅 쿼리](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md)를 만듭니다. 한 번 설정하면 들어오는 데이터가 자동으로 IoT Hub에 의해 엔드포인트로 라우팅됩니다. 메시지는 정의된 모든 라우팅 쿼리와 일치하지 않으면 기본 엔드포인트로 라우팅됩니다.

2부분으로 구성된 자습서에서는 IoT Hub로 사용자 지정 라우팅 쿼리를 설정하고 사용하는 방법을 알아봅니다. IoT 디바이스에서 Blob Storage 및 Service Bus 큐를 포함한 여러 엔드포인트 중 하나로 메시지를 라우팅합니다. Service Bus 큐로 보내는 메시지는 Logic App에서 선택되며 이메일을 통해 전송됩니다. 사용자 지정 메시지 라우팅이 정의되지 않은 메시지는 기본 엔드포인트로 전송된 다음, Azure Stream Analytics에서 선택되어 Power BI 시각화에서 표시됩니다.

이 자습서의 1부와 2부를 완료하려면 다음 작업을 수행합니다.

**1부: 리소스 만들기, 메시지 라우팅 설정**
> [!div class="checklist"]
> * IoT Hub, 스토리지 계정, Service Bus 큐 및 시뮬레이션된 디바이스와 같은 리소스를 설정합니다. 이 작업은 Azure Portal, Azure Resource Manager 템플릿, Azure CLI 또는 Azure PowerShell을 사용하여 수행할 수 있습니다.
> * 스토리지 계정 및 Service Bus 큐에 대한 IoT Hub에서 엔드포인트 및 메시지 경로를 구성합니다.

**2부: 허브에 메시지 보내기, 라우팅된 결과 보기**
> [!div class="checklist"]
> * 메시지가 Service Bus 큐에 추가될 때 트리거되어 이메일을 전송하는 Logic App을 만듭니다.
> * 다양한 라우팅 옵션에 대한 허브로 메시지를 전송하는 IoT 디바이스를 시뮬레이션하는 앱을 다운로드하여 실행합니다.
> * 기본 엔드포인트에 전송된 데이터에 대한 Power BI 시각화를 만듭니다.
> * 다음에서 결과를 확인합니다.
> * ...Service Bus 큐 및 이메일에서
> * ...스토리지 계정에서
> * ...Power BI 시각화에서

## <a name="prerequisites"></a>필수 구성 요소

* 이 자습서의 1부:
  - Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* 이 자습서의 2부:
  - 이 자습서의 1부를 완료해야 하고 리소스를 계속 사용할 수 있어야 합니다.
  - [Visual Studio](https://www.visualstudio.com/)를 설치합니다.
  - Power BI 계정에 액세스하여 기본 엔드포인트의 스트림 분석을 분석합니다. ([Power BI를 무료로 사용해 보세요](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - 알림 이메일을 보낼 수 있는 회사 또는 학교 계정이 있어야 합니다.
  - 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 자습서의 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
