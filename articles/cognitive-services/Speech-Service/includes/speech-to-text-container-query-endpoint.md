---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 167e33ff4a3af463e2537e2714e9e9bf5e125b61
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98947766"
---
컨테이너는 [Speech SDK](../index.yml)를 통해 액세스되는 Websocket 기반 쿼리 끝점 API를 제공합니다. 기본적으로 Speech SDK는 온라인 음성 서비스를 사용합니다. 컨테이너를 사용하려면 초기화 메서드를 변경해야 합니다.

> [!TIP]
> 컨테이너와 함께 Speech SDK를 사용 하는 경우 Azure 음성 리소스 [구독 키 또는 인증 전달자 토큰](../rest-speech-to-text.md#authentication)을 제공 하지 않아도 됩니다.

아래 예제를 참조하세요.

# <a name="c"></a>[C#](#tab/csharp)

다음 Azure 클라우드 초기화 호출을

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

컨테이너 [호스트](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost)에서이 호출을 사용 하려면 다음을 수행 합니다.

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

다음 Azure 클라우드 초기화 호출을

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

컨테이너 [끝점과](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)함께이 호출을 사용 하려면 다음을 수행 합니다.

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
