---
title: Video Indexer 웹 사이트를 사용하여 브랜드 모델 사용자 지정
titleSuffix: Azure Media Services
description: Video Indexer 웹 사이트를 사용 하 여 브랜드 모델을 사용자 지정 하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: kumud
ms.openlocfilehash: a2de9dbb479f43d6b646cd9f6cf604d6a08c8b6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586124"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Video Indexer 웹 사이트를 사용하여 브랜드 모델 사용자 지정

Video Indexer는 비디오 및 오디오 콘텐츠의 인덱싱 및 재인덱싱 동안 연설 및 시각적 텍스트에서 브랜드를 검색하도록 지원합니다. 브랜드 검색 기능은 Bing의 브랜드 데이터베이스에서 제안하는 제품, 서비스 및 회사의 멘션을 식별합니다. 예를 들어 Microsoft가 비디오 또는 오디오 콘텐츠를 통해 표시 되거나 비디오의 시각적 텍스트에 표시 되는 경우 Video Indexer 콘텐츠에서 브랜드로 검색 합니다.

사용자 지정 브랜드 모델을 사용 하면 다음을 수행할 수 있습니다.

- Video Indexer Bing 브랜드 데이터베이스에서 브랜드를 검색 하도록 하려면 선택 합니다.
- 특정 브랜드를 검색 하지 않도록 Video Indexer 하려면 선택 합니다 (기본적으로 브랜드의 거부 목록을 생성 함).
- Bing의 브랜드 데이터베이스에 없을 수 있는 모델에 포함 되어야 하는 브랜드를 포함 하도록 Video Indexer 하려면 선택 합니다 (기본적으로 브랜드의 수락 목록 만들기).

자세한 개요는이 [개요](customize-brands-model-overview.md)를 참조 하세요.

Video Indexer 웹 사이트를 사용하여 이 항목에 설명된 것처럼 비디오에서 검색되는 사용자 지정 브랜드 모델을 생성, 사용 및 편집할 수 있습니다. [API를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)에 설명된 대로 API를 사용할 수도 있습니다.

> [!NOTE]
> 브랜드를 추가 하기 전에 비디오가 인덱싱되는 경우 인덱스를 다시 만들어야 합니다. 비디오와 연결 된 드롭다운 메뉴에서 **다시 인덱스** 항목을 찾을 수 있습니다. **고급 옵션**  ->  **브랜드 범주** 를 선택 하 고 **모든 브랜드** 를 확인 합니다.

## <a name="edit-brands-model-settings"></a>브랜드 모델 설정 편집

Bing 브랜드 데이터베이스의 브랜드를 검색할지를 여부를 설정하는 옵션이 제공됩니다. 이 옵션을 설정 하려면 브랜드 모델의 설정을 편집 해야 합니다. 다음 단계를 수행합니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동 하 여 로그인 합니다.
1. 계정에서 모델을 사용자 지정 하려면 페이지 왼쪽에 있는 **콘텐츠 모델 사용자 지정** 단추를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Video Indexer에서 콘텐츠 모델 사용자 지정":::
1. 브랜드를 편집하려면 **브랜드** 탭을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-brand-model/customize-brand-model.png" alt-text="콘텐츠 모델 사용자 지정 대화 상자의 브랜드 탭을 보여 주는 스크린샷":::
1. Bing에서 제안 하는 브랜드를 검색 Video Indexer 하려면 **bing에서 제안 하는 브랜드 표시** 옵션을 선택 합니다. 그렇지 않은 경우에는이 옵션을 선택 하지 않은 상태로 둡니다.

## <a name="include-brands-in-the-model"></a>모델에 브랜드 포함

**브랜드 포함** 섹션은 Bing에서 제안 하지 않는 경우에도 Video Indexer 검색을 원하는 사용자 지정 브랜드를 나타냅니다.  

### <a name="add-a-brand-to-include-list"></a>포함 목록에 브랜드 추가

1. **+ 새 브랜드 만들기** 를 선택 합니다.

    이름(필수), 범주(선택), 설명(선택) 및 참조 URL(선택)을 제공합니다.
    범주 필드는 브랜드에 태그를 지정하는 데 도움을 주기 위한 것입니다. 이 필드는 Video Indexer API를 사용할 때 브랜드의 *태그* 로 표시됩니다. 예를 들어 “Azure” 브랜드는 “클라우드”로 태그를 지정하거나 분류할 수 있습니다.

    참조 URL 필드는 해당 제품에 대 한 링크와 같은 브랜드의 모든 참조 웹 사이트 일 수 있습니다.

2. **저장** 을 선택 하면 브랜드가 **포함** 된 브랜드 목록에 추가 된 것을 확인할 수 있습니다.

### <a name="edit-a-brand-on-the-include-list"></a>포함 목록에서 브랜드를 편집 합니다.

1. 편집 하려는 브랜드 옆의 연필 아이콘을 선택 합니다.

    브랜드의 범주, 설명 또는 참조 URL을 업데이트할 수 있습니다. 브랜드 이름은 고유하므로 변경할 수 없습니다. 브랜드 이름을 변경해야 할 경우 전체 브랜드를 삭제하고(다음 섹션 참조) 새 이름으로 새 브랜드를 만듭니다.

2. **업데이트** 단추를 선택 하 여 새 정보로 브랜드를 업데이트 합니다.

### <a name="delete-a-brand-on-the-include-list"></a>포함 목록에서 브랜드를 삭제 합니다.

1. 삭제 하려는 브랜드 옆에 있는 휴지통 아이콘을 선택 합니다.
2. **삭제** 를 선택 하면 브랜드가 *포함 된 제품* 목록에 더 이상 표시 되지 않습니다.

## <a name="exclude-brands-from-the-model"></a>모델에서 브랜드 제외

**브랜드 제외** 섹션은 Video Indexer 감지 하지 않으려는 브랜드를 나타냅니다.

### <a name="add-a-brand-to-exclude-list"></a>제외할 브랜드 추가 목록

1. **+ 새 브랜드 만들기를 선택 합니다.**

    이름(필수), 범주(선택)를 제공합니다.

2. **저장** 을 선택 하면 브랜드가 *제외* 된 목록에 추가 된 것을 볼 수 있습니다.

### <a name="edit-a-brand-on-the-exclude-list"></a>제외 목록에서 브랜드를 편집 합니다.

1. 편집 하려는 브랜드 옆의 연필 아이콘을 선택 합니다.

    브랜드의 범주만 업데이트할 수 있습니다. 브랜드 이름은 고유하므로 변경할 수 없습니다. 브랜드 이름을 변경해야 할 경우 전체 브랜드를 삭제하고(다음 섹션 참조) 새 이름으로 새 브랜드를 만듭니다.

2. **업데이트** 단추를 선택 하 여 새 정보로 브랜드를 업데이트 합니다.

### <a name="delete-a-brand-on-the-exclude-list"></a>제외 목록에서 브랜드를 삭제 합니다.

1. 삭제 하려는 브랜드 옆에 있는 휴지통 아이콘을 선택 합니다.
2. **삭제** 를 선택 하면 브랜드가 *제외* 된 목록에 더 이상 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

[API를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)
