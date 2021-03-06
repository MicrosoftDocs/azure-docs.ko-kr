---
title: 빠른 시작 - Azure Communication Services의 Teams interop
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication Calling SDK를 사용하여 Teams 미팅에 참가하는 방법을 알아봅니다.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 0e75d2b480a9cbfd2977d9d449c1ea12bdfe4920
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095614"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>빠른 시작: 통화 앱으로 Teams 미팅 참가

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> [Teams 테넌트 상호 운용성](../../concepts/teams-interop.md)을 사용/사용하지 않도록 설정하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)을 작성합니다.

JavaScript SDK를 사용하여 호출 솔루션을 Microsoft Teams에 연결하여 Azure Communication Services를 시작하세요.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

이 문서에 설명된 기능은 Communication Services SDK의 일반 가용성 버전을 사용합니다. 팀 상호 운용성을 위해서는 Communication Services SDK의 베타 버전이 필요합니다. 베타 SDK는 [릴리스 정보 페이지](https://github.com/Azure/Communication/tree/master/releasenotes)에서 탐색할 수 있습니다.

베타 SDK를 사용하여 "패키지 설치" 단계를 실행하는 경우 `communication-calling` 패키지 이름에 버전 `@1.0.0-beta.10`(이 문서를 작성하는 시점의 버전)을 지정하여 패키지 버전을 최신 베타 릴리스로 수정합니다. `communication-common` 패키지 명령을 수정할 필요가 없습니다. 예를 들면 다음과 같습니다.

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [영웅 샘플 호출](../../samples/calling-hero-sample.md) 체크 아웃
- [통화 SDK 기능](./calling-client-samples.md)에 대해 알아보기
- [호출 작동 방식](../../concepts/voice-video-calling/about-call-types.md)에 대해 자세히 알아보기
