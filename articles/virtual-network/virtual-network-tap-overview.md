---
title: Azure 가상 네트워크 TAP 개요 | Microsoft Docs
description: 가상 네트워크 TAP에 대해 알아봅니다. 가상 네트워크 TAP은 패킷 수집기에 스트리밍될 수 있는 가상 머신 네트워크 트래픽의 전체 복사본을 제공합니다.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 6160dd09edc57f2f52306d4dad0dde413fff0616
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617185"
---
# <a name="virtual-network-tap"></a>가상 네트워크 TAP
> [!IMPORTANT]
> 가상 네트워크 탭 미리 보기는 현재 모든 Azure 지역에서 보류 중입니다. 구독 ID를 사용 하 여에 전자 메일을 보낼 수 <azurevnettap@microsoft.com> 있으며,이 미리 보기에 대 한 향후 업데이트를 알려 드리겠습니다. 그 동안에는 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners)에서 제공 하는 [패킷 브로커 파트너 솔루션](#virtual-network-tap-partner-solutions) 을 통해 탭/네트워크 표시 기능을 제공 하는 에이전트 기반 또는 nva 솔루션을 사용할 수 있습니다.

Azure 가상 네트워크 TAP(터미널 액세스 지점)을 사용하면 네트워크 패킷 수집기 또는 분석 도구로 가상 머신 네트워크 트래픽을 지속적으로 스트리밍할 수 있습니다. 수집기 또는 분석 도구는 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 파트너에 의해 제공 됩니다. 가상 네트워크 TAP 사용의 유효성이 검사된 파트너 솔루션 목록은 [파트너 솔루션](#virtual-network-tap-partner-solutions)을 참조하세요.
다음 그림은 가상 네트워크 TAP이 작동하는 방법을 보여 줍니다. 가상 네트워크에 배포된 가상 머신에 연결된 [네트워크 인터페이스](virtual-network-network-interface.md)에 TAP 구성을 추가할 수 있습니다. 대상은 모니터링된 네트워크 인터페이스 또는 [피어링된 가상](virtual-network-peering-overview.md) 네트워크와 동일한 가상 네트워크의 가상 네트워크 IP 주소입니다. 가상 네트워크 TAP에 대한 수집기 솔루션은 고가용성을 위해 Azure 내부 부하 분산 장치 배후에 배포될 수 있습니다.
![가상 네트워크 TAP이 작동하는 방법](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>필수 조건

가상 네트워크 탭을 만들기 전에 미리 보기에 등록 된 확인 메일을 수신 하 고, [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 배포 모델을 사용 하 여 만든 하나 이상의 가상 컴퓨터와 동일한 Azure 지역에서 탭 트래픽을 집계 하기 위한 파트너 솔루션을 받아야 합니다. 가상 네트워크에 파트너 솔루션이 없다면 [파트너 솔루션](#virtual-network-tap-partner-solutions)을 참조하여 하나의 솔루션을 배포합니다. 동일한 가상 네트워크 TAP 리소스를 사용하여 동일하거나 다른 구독의 여러 네트워크 인터페이스에서 트래픽을 집계할 수 있습니다. 모니터링된 네트워크 인터페이스가 다른 구독에 있는 경우 구독은 동일한 Azure Active Directory 테넌트에 연결되어야 합니다. 또한 모니터링된 네트워크 인터페이스 및 TAP 트래픽 집계를 위한 대상 엔드포인트는 동일한 지역의 피어링된 가상 네트워크에 있을 수 있습니다. 이 배포 모델을 사용하는 경우 [가상 네트워크 피어링](virtual-network-peering-overview.md)이 가상 네트워크 TAP을 구성하기 전에 사용하도록 설정되어 있어야 합니다.

## <a name="permissions"></a>권한

네트워크 인터페이스에 TAP 구성을 적용하는 데 사용하는 계정은 다음 표에서 필요한 작업이 할당된 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 할당되어야 합니다.

| 작업 | Name |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | 가상 네트워크 TAP 리소스를 만들고,업데이트하고, 읽고, 삭제해야 함 |
| Microsoft.Network/networkInterfaces/read | TAP이 구성될 네트워크 인터페이스 리소스를 읽어야 함 |
| Microsoft.Network/tapConfigurations/* | 네트워크 인터페이스에서 TAP 구성을 만들고, 업데이트하고, 읽고, 삭제해야 함 |


## <a name="virtual-network-tap-partner-solutions"></a>가상 네트워크 TAP 파트너 솔루션

### <a name="network-packet-brokers"></a>네트워크 패킷 브로커

- [Azure 용 GigaVUE Cloud Suite](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>보안 분석, 네트워크/애플리케이션 성능 관리

- [활성 보안](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch 클라우드](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Noname 보안](https://nonamesecurity.com/)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® Platform](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>다음 단계

- [가상 네트워크 TAP을 만드는](tutorial-tap-virtual-network-cli.md) 방법에 대해 알아봅니다.
