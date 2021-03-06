---
title: Azure Monitor Application Insights 종속성 데이터 모델
description: 종속성 원격 분석을 위한 Azure Application Insights 데이터 모델
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0f9fc96479569c3411024068ed614d422035ab17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87315974"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>종속성 원격 분석: Application Insights 데이터 모델

[Application Insights](./app-insights-overview.md)에서 종속성 원격 분석은 모니터링되는 구성 요소와 원격 구성 요소(예: SQL 또는 HTTP 엔드포인트)의 상호 작용을 나타냅니다.

## <a name="name"></a>Name

이 종속성 호출을 사용하여 시작된 명령의 이름입니다. 낮은 카디널리티 값입니다. 예로는 저장 프로시저 이름 및 URL 경로 템플릿이 있습니다.

## <a name="id"></a>ID

종속성 호출 인스턴스의 식별자입니다. 이 종속성 호출에 해당하는 요청 원격 분석 항목과의 상관 관계에 사용됩니다. 자세한 내용은 [상관 관계](./correlation.md) 페이지를 참조하세요.

## <a name="data"></a>데이터

이 종속성 호출로 시작되는 명령입니다. 예로 모든 쿼리 매개 변수를 포함하는 SQL 문 및 HTTP URL을 들 수 있습니다.

## <a name="type"></a>Type

종속성 형식 이름입니다. 다른 필드(예: commandName 및 resultCode)의 종속성 논리적 그룹에 대한 낮은 카디널리티 값 및 해석입니다. 예로 SQL, Azure 테이블 및 HTTP를 들 수 있습니다.

## <a name="target"></a>대상

종속성 호출의 대상 사이트입니다. 예로 서버 이름, 호스트 주소를 들 수 있습니다. 자세한 내용은 [상관 관계](./correlation.md) 페이지를 참조하세요.

## <a name="duration"></a>Duration

요청 기간은 `DD.HH:MM:SS.MMMMMM` 형식으로 나타냅니다. `1000`일보다 작아야 합니다.

## <a name="result-code"></a>결과 코드

종속성 호출의 결과 코드입니다. 예로 SQL 오류 코드 및 HTTP 상태 코드를 들 수 있습니다.

## <a name="success"></a>Success

성공 또는 실패한 호출을 나타냅니다.

## <a name="custom-properties"></a>사용자 지정 속성

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>사용자 지정 측정

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>다음 단계

- [.NET](./asp-net-dependencies.md)에 대한 종속성 추적을 설정합니다.
- [Java](./java-agent.md)에 대한 종속성 추적을 설정합니다.
- [사용자 지정 종속성 원격 분석을 작성합니다](./api-custom-events-metrics.md#trackdependency).
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](data-model.md)을 참조하세요.
- Application Insights에서 지원되는 [플랫폼](./platforms.md)을 확인합니다.

