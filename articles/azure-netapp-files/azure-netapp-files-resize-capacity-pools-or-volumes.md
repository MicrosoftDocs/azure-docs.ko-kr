---
title: Azure NetApp Files에 대한 용량 풀 또는 볼륨의 크기 조정 | Microsoft Docs
description: 용량 풀 또는 볼륨의 크기를 변경 하는 방법에 대해 알아봅니다. 용량 풀 크기를 조정하면 구매한 Azure NetApp Files 용량을 변경합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594481"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>용량 풀 또는 볼륨 크기 조정
필요에 따라 용량 풀 또는 볼륨의 크기를 변경할 수 있습니다. 

## <a name="resize-the-capacity-pool"></a>용량 풀 크기 조정 

용량 풀 크기는 1-TiB 증가 또는 감소에서 변경할 수 있습니다. 그러나 용량 풀 크기는 4 TiB 보다 작을 수 없습니다. 용량 풀 크기를 조정하면 구매한 Azure NetApp Files 용량을 변경합니다.

1. NetApp 계정 관리 블레이드에서 크기를 조정하려는 용량 풀을 클릭합니다. 
2. 상황에 맞는 메뉴를 표시하려면 용량 풀 이름을 마우스 오른쪽 단추로 클릭하거나, 용량 풀의 행의 끝에 있는 "..." 아이콘을 클릭합니다. 
3. 상황에 맞는 메뉴 옵션을 사용하여 용량 풀을 크기 조정하거나 삭제합니다.

## <a name="resize-a-volume"></a>볼륨 크기 조정

필요에 따라 볼륨의 크기를 변경할 수 있습니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.

1. NetApp 계정 관리 블레이드에서 **볼륨** 을 클릭합니다. 
2. 상황에 맞는 메뉴를 표시하려면 크기를 조정하려는 볼륨 이름을 마우스 오른쪽 단추로 클릭하거나 볼륨의 행의 끝에 있는 "..." 아이콘을 클릭합니다.
3. 상황에 맞는 메뉴 옵션을 사용하여 볼륨을 크기 조정하거나 삭제합니다.

## <a name="resize-a-cross-region-replication-destination-volume"></a>지역 간 복제 대상 볼륨 크기 조정 

[지역 간 복제](cross-region-replication-introduction.md) 관계에서 대상 볼륨의 크기는 원본 볼륨의 크기에 따라 자동으로 조정 됩니다. 따라서 대상 볼륨의 크기를 별도로 조정할 필요가 없습니다. 이러한 자동 크기 조정 동작은 볼륨이 활성 복제 관계에 있거나 복제 피어 링이 다시 [동기화 작업과](cross-region-replication-manage-disaster-recovery.md#resync-replication)충돌 하는 경우에 적용할 수 있습니다. 

다음 표에서는 [미러 상태](cross-region-replication-display-health-status.md)를 기준으로 하는 대상 볼륨 크기 조정 동작에 대해 설명 합니다.

|  미러 상태  | 대상 볼륨 크기 조정 동작 |
|-|-|
| *미러링됨* | 대상 볼륨이 초기화 되어 미러링 업데이트를 받을 준비가 되 면 원본 볼륨의 크기를 조정 하면 대상 볼륨의 크기가 자동으로 조정 됩니다. |
| *올리기* | 원본 볼륨의 크기를 조정 하 고 미러 상태가 *손상* 된 경우 다시 [동기화 작업](cross-region-replication-manage-disaster-recovery.md#resync-replication)을 사용 하 여 대상 볼륨의 크기가 자동으로 조정 됩니다.  |
| *초기화되지 않음* | 원본 볼륨의 크기를 조정할 때 미러 상태가 아직 *초기화* 되지 않은 경우에는 대상 볼륨의 크기를 수동으로 조정 해야 합니다. 따라서 초기화가 완료 될 때까지 기다리는 것이 좋습니다. 즉, 미러 상태가 *미러링된* 경우 원본 볼륨의 크기를 조정 하는 것이 좋습니다. | 

> [!IMPORTANT]
> 지역 간 복제의 원본 볼륨과 대상 볼륨 모두의 용량 풀에 충분 한 여유가 있는지 확인 합니다. 원본 볼륨의 크기를 조정 하면 대상 볼륨의 크기가 자동으로 조정 됩니다. 그러나 대상 볼륨을 호스트 하는 용량 풀에 충분 한 공간이 없는 경우 원본 볼륨과 대상 볼륨의 크기를 모두 조정 하지 못합니다.

## <a name="next-steps"></a>다음 단계

- [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
- [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
- [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md) 