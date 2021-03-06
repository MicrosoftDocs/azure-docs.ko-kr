---
title: IoT Edge 할당량 및 제한 사항에 대 한 라이브 비디오 분석-Azure
description: 이 문서에서는 IoT Edge 할당량 및 제한 사항에 대 한 라이브 비디오 분석을 설명 합니다.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97400527"
---
# <a name="quotas-and-limitations"></a>할당량 및 제한 사항

이 문서에서는 IoT Edge 모듈에서 라이브 비디오 분석의 할당량 및 제한 사항을 열거 합니다.

## <a name="maximum-period-of-disconnected-use"></a>연결 되지 않은 최대 사용 기간

Edge 모듈은 인터넷 연결이 일시적으로 손실 될 수 있습니다. 모듈이 36 시간 넘게 연결 해제 상태를 유지 하는 경우 실행 중인 모든 그래프 인스턴스를 비활성화 합니다. 모든 추가 직접 메서드 호출은 차단 됩니다.

Edge 모듈을 작동 상태로 다시 시작 하려면 모듈이 Azure 미디어 서비스 계정과 성공적으로 통신할 수 있도록 인터넷 연결을 복원 해야 합니다.

## <a name="maximum-number-of-graph-instances"></a>최대 그래프 인스턴스 수

모듈 당 최대 1000 개의 그래프 인스턴스 (GraphInstanceSet를 통해 만들어짐)가 지원 됩니다.

## <a name="maximum-number-of-graph-topologies"></a>최대 그래프 토폴로지 수

GraphTopologySet를 통해 생성 된 모듈 당 최대 50 그래프 토폴로지가 지원 됩니다.

## <a name="limitations-on-graph-topologies-at-preview"></a>미리 보기의 그래프 토폴로지에 대 한 제한 사항

미리 보기 릴리스에서는 다른 노드에 대 한 제한 사항이 미디어 그래프 토폴로지에서 함께 연결 될 수 있습니다.

* RTSP 원본
   * 그래프 토폴로지 마다 하나의 RTSP 원본만 허용 됩니다.
* 동작 감지 프로세서
   * RTSP 원본에서 즉시 다운스트림 이어야 합니다.
   * 는 HTTP 또는 gRPC 확장 프로세서의 다운스트림에서 사용할 수 없습니다.
* 신호 게이트 프로세서
   * RTSP 원본에서 즉시 다운스트림 이어야 합니다.
* 자산 싱크 
   * RTSP 원본 또는 신호 게이트 프로세서에서 즉시 다운스트림 이어야 합니다.
* 파일 싱크
   * 신호 게이트 프로세서에서 즉시 다운스트림 이어야 합니다.
   * HTTP 또는 gRPC 확장 프로세서 또는 동작 감지 프로세서의 바로 다운스트림 일 수 없습니다.
* IoT Hub 싱크
   * 은 (는) IoT Hub 소스의 바로 다운스트림으로 수 없습니다.

## <a name="limitations-on-media-service-operations-at-preview"></a>미리 보기에서 미디어 서비스 작업에 대 한 제한 사항

Preview 릴리스 시점에 IoT Edge의 라이브 비디오 분석은 다음을 지원 하지 않습니다.

* 중단 없이 한 구독에서 다른 구독으로 미디어 서비스 계정을 마이그레이션하는 기능입니다.
* 미디어 서비스 계정으로 둘 이상의 저장소 계정을 사용할 수 있습니다.
* 다시 시작 하지 않고 모듈의 desired 속성에서 서비스 주체 정보를 동적으로 변경할 수 있는 기능입니다.

RTSP 프로토콜을 지 원하는 IP 카메라만 사용할 수 있습니다. [ONVIF 규격 제품](https://www.onvif.org/conformant-products) 페이지에서 RTSP를 지원하는 IP 카메라를 찾을 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.

또한 이러한 카메라는 h.264 video 및 AAC audio를 사용 하도록 구성 해야 합니다. 다른 코덱은 현재 지원 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

[개요](overview.md)
