---
title: Bing Image Search API란?
titleSuffix: Azure Cognitive Services
description: Bing Image Search API를 사용하면 애플리케이션에서 Bing의 인지 이미지 검색 기능을 사용할 수 있습니다. API를 사용하여 사용자 검색 쿼리를 보내면 Bing 이미지와 비슷한 관련 고품질 이미지를 가져와서 표시할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c52098d86efe60ee524735e6158add5260a0757f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338234"
---
# <a name="what-is-the-bing-image-search-api"></a>Bing Image Search API란?

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Image Search API를 사용하면 애플리케이션에서 Bing의 이미지 검색 기능을 사용할 수 있습니다. 검색 쿼리를 API로 보내면 [bing.com/images](https://www.bing.com/images)와 비슷한 고품질 이미지를 얻을 수 있습니다.

Bing Image Search API에서 이미지 전용 검색 결과를 제공하지만, 다른 사용 가능한 [Bing Search API](../bing-web-search/bing-api-comparison.md)를 결합하거나 사용하여 웹에서 다양한 형식의 콘텐츠를 찾을 수 있습니다.

## <a name="bing-image-search-features"></a>Bing Image Search 기능

| 기능                                                                                                                                                                                 | Description                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [실시간 검색 용어 제안](./concepts/bing-image-search-sending-queries.md) | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 통해 입력하는 대로 제안되는 검색 용어를 표시하여 응용 프로그램 환경을 향상시킵니다. |
| [이미지 결과 필터링 및 제한](./concepts/bing-image-search-get-images.md)                       | 쿼리 매개 변수를 편집하여 Bing에서 반환하는 이미지를 필터링합니다.                                                                                                       |
| [썸네일 자르기, 크기 조정 및 표시](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Bing Image Search에서 반환하는 이미지에 대한 썸네일 미리 보기를 편집하고 표시합니다.                                                                                      |
| [사용자 검색 쿼리 피벗 및 확장](./concepts/bing-image-search-sending-queries.md)               | 쿼리에 Bing 제안 검색 용어를 포함시키고 표시하여 검색 기능을 확장합니다.                                                                    |
| [최신 이미지 가져오기](trending-images.md)                                                                     | 전 세계로부터의 최신 이미지 검색을 사용자 지정합니다.                                                                                                          |

## <a name="workflow"></a>워크플로

Bing Image Search API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다. [REST API](./quickstarts/csharp.md) 또는 [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)를 사용하여 서비스를 사용할 수 있습니다.

1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](../cognitive-services-apis-create-account.md)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/cognitive-services/)을 만들 수 있습니다.
2. 유효한 [검색 쿼리](./concepts/bing-image-search-sending-queries.md)를 사용하여 API에 요청을 보냅니다.
3. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="next-steps"></a>다음 단계

먼저 Bing Image Search API [대화형 데모](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)를 사용해 보세요.
이 데모에서는 검색 쿼리를 빠르게 사용자 지정하고 웹에서 이미지를 샅샅이 검색하는 방법을 보여 줍니다.

첫 번째 API 요청을 빠르게 시작하려면 다음을 알아볼 수 있습니다.

* REST API를 사용하여 [Bing에 검색 쿼리를 보냅니다](./quickstarts/csharp.md). 또는
* SDK를 사용하여 Bing에서 반환하는 이미지를 [요청하고 필터링합니다](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

## <a name="see-also"></a>참고 항목

* Bing Search API에 대한 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 

* [Bing Image Search API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 참조 섹션에는 API의 엔드포인트, 헤더, API 응답 및 쿼리 매개 변수에 대한 정보가 나와 있습니다.

* [Bing 사용 및 표시 요구 사항](../bing-web-search/use-display-requirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.

* [Bing Image Search API를 사용하여 웹에서 이미지 가져오기](./concepts/bing-image-search-get-images.md) 문서에서는 웹에서 이미지를 검색하고 가져오는 방법에 대해 설명합니다.

* [검색 쿼리 보내기 및 사용](./concepts/bing-image-search-sending-queries.md) 문서에서는 검색 쿼리를 만들고, 사용자 지정하고, 피벗하는 방법에 대해 설명합니다.

* [Bing Search API 허브 페이지](../bing-web-search/overview.md)를 방문하여 사용 가능한 다른 API를 살펴보세요.