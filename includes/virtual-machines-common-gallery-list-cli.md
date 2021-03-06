---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 83d70a8d4c5806120ddb4ea776a8f4a6f7e63857
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799984"
---
## <a name="list-information"></a>정보 나열

[az sig list](/cli/azure/sig#az_sig_list)를 사용하여 사용 가능한 이미지 갤러리에 대한 위치, 상태 및 기타 정보를 가져올 수 있습니다.

```azurecli-interactive 
az sig list -o table
```

[az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list)를 사용하여 OS 유형 및 상태에 대한 정보를 포함한 갤러리의 이미지 정의를 나열합니다.

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

[az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list)를 사용하여 갤러리의 공유 이미지 버전을 나열합니다.

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

[az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show)를 사용하여 이미지 버전의 ID를 가져옵니다.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
