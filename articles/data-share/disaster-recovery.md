---
title: Azure 데이터 공유에 대 한 재해 복구
description: Azure 데이터 공유에 대 한 재해 복구
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218700"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure 데이터 공유에 대 한 재해 복구

이 문서에서는 Azure 데이터 공유에 대 한 재해 복구 환경을 구성 하는 방법을 설명 합니다. Azure 데이터 센터 중단이 드물게 발생 하지만 몇 분에서 몇 시간까지 지속 될 수 있습니다. 데이터 센터 중단으로 인해 데이터 공급자가 공유 하는 데이터에 의존 하는 환경이 중단 될 수 있습니다. 이 문서에서 설명 하는 단계에 따라 데이터 공급자는 데이터 공유를 호스트 하는 주 지역에 대 한 데이터 센터 가동 중단 시 데이터 소비자와 데이터를 계속 공유할 수 있습니다. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Azure 데이터 공유에 대 한 비즈니스 연속성 달성

데이터 센터 가동 중단을 준비 하기 위해 데이터 공급자는 보조 지역에 프로 비전 된 데이터 공유 환경을 가질 수 있습니다. 데이터 센터가 중단 되는 경우 원활한 장애 조치 (failover)를 위해 측정값을 사용할 수 있습니다. 

데이터 공급자는 보조 Azure 데이터 공유 리소스를 추가 지역에 프로 비전 할 수 있습니다. 이러한 데이터 공유 리소스는 기본 Azure 데이터 공유 리소스에 있는 공유 및 데이터 집합을 포함 하도록 구성할 수 있습니다. DR 환경을 구성할 때 또는 나중에 데이터 소비자를 보조 공유에 초대할 수 있습니다. 즉, 수동 장애 조치 (failover) 단계의 일부로).

데이터 소비자가 DR 용도로 프로 비전 된 보조 환경에서 활성 공유 구독을 보유 하는 경우 장애 조치 (failover)의 일부로 스냅숏 일정을 사용 하도록 설정할 수 있습니다. 데이터 소비자가 DR 용도로 보조 지역을 구독 하지 않으려는 경우 나중에 보조 공유에 초대 될 수 있습니다. 

데이터 소비자는 DR 용도로 유휴 상태인 활성 공유 구독을 보유 하거나, 데이터 공급자가 수동 장애 조치 (failover) 절차의 일부로 나중에 이러한 구독을 초대할 수 있습니다. 

## <a name="related-information"></a>관련 정보

- [비즈니스 연속성 및 재해 복구](../best-practices-availability-paired-regions.md)
- [BCDR 전략에 고가용성 구축](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.