---
title: Azure로의 마이그레이션에 대 한 Azure Migrate 및 Site Recovery 비교
description: Site Recovery 대신 마이그레이션에 Azure Migrate를 사용 하는 경우의 이점을 요약 합니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: raynew
ms.openlocfilehash: 358efaa1493aa08fb76c9bb83e0e4289950e0969
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87844323"
---
# <a name="migrating-to-azure"></a>Azure로 마이그레이션

마이그레이션의 경우 Azure Migrate 서비스를 사용 하 여 Azure Site Recovery 서비스가 아닌 Vm 및 서버를 Azure로 마이그레이션하는 것이 좋습니다. Azure Migrate에 [대해 자세히 알아보세요](../migrate/migrate-services-overview.md) .


## <a name="why-use-azure-migrate"></a>Azure Migrate를 사용하는 이유

마이그레이션을 위해 Azure Migrate를 사용 하면 다음과 같은 여러 가지 이점이 있습니다.
 
 
- Azure Migrate는 Azure로의 검색, 평가 및 마이그레이션에 대 한 중앙 집중식 허브를 제공 합니다.
- Azure Migrate를 사용 하 여 Azure Migrate 도구, 다른 Azure 서비스 및 타사 도구와의 상호 운용성 및 향후 확장성을 제공 합니다.
- Azure Migrate: 서버 마이그레이션 도구는 Azure로의 서버 마이그레이션을 위해 작성 되었습니다. 마이그레이션에 최적화 되어 있습니다. 마이그레이션과 직접적인 관련이 없는 개념 및 시나리오에 대해 알 필요가 없습니다. 
- VM에 대 한 복제가 시작 된 시간부터 180 일간의 마이그레이션에 대 한 도구 사용 요금은 없습니다. 마이그레이션을 완료 하는 데 걸리는 시간을 제공 합니다. 복제에 사용 되는 저장소 및 네트워크 리소스와 테스트 마이그레이션 중 사용 된 계산 요금에 대해서만 비용을 지불 합니다.
- Azure Migrate은 Site Recovery에서 지원 되는 모든 마이그레이션 시나리오를 지원 합니다. 또한 VMware Vm의 경우 Azure Migrate는 에이전트 없는 마이그레이션 옵션을 제공 합니다.
- Azure Migrate에 대 한 새로운 마이그레이션 기능에 우선 순위를 정 합니다. 서버 마이그레이션 도구에만 해당 됩니다. 이러한 기능은 Site Recovery 대상이 아닙니다.

## <a name="when-to-use-site-recovery"></a>Site Recovery를 사용 하는 경우

Site Recovery를 사용 해야 합니다.

- Azure에 대 한 온-프레미스 컴퓨터의 재해 복구
- Azure 지역 간에 Azure Vm의 재해 복구를 위한 것입니다.

Azure Migrate를 사용 하 여 온-프레미스 서버를 Azure로 마이그레이션하는 것이 좋지만, 이미 Site Recovery를 사용 하 여 마이그레이션 경험을 시작한 경우에는 계속 사용 하 여 마이그레이션을 완료할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

> Azure Migrate에 대한 [일반적인 질문을 검토](../migrate/resources-faq.md)합니다.
