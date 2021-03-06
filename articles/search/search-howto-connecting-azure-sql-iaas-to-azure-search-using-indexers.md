---
title: 검색 인덱싱을 위한 Azure SQL VM 연결
titleSuffix: Azure Cognitive Search
description: 암호화 된 연결을 사용 하도록 설정 하 고 Azure Cognitive Search의 인덱서에서 Azure VM (가상 머신)에서 SQL Server에 대 한 연결을 허용 하도록 방화벽을 구성 합니다.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: 23c5d138463a52f4ff4c52b4a919b71a87b7fd6d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802882"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Azure VM에서 Azure Cognitive Search 인덱서에 SQL Server에 대 한 연결 구성

Azure 가상 컴퓨터의 데이터베이스에서 콘텐츠를 추출 하도록 [AZURE SQL 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq) 를 구성 하는 경우 보안 연결에 대 한 추가 단계가 필요 합니다. 

Azure Cognitive Search에서 가상 컴퓨터의 SQL Server에 연결 하는 것은 공용 인터넷 연결입니다. 보안 연결을 성공적으로 수행 하려면 다음 단계를 완료 합니다.

+ 가상 컴퓨터에서 SQL Server 인스턴스의 정규화 된 도메인 이름에 대 한 인증서를 [인증 기관 공급자](https://en.wikipedia.org/wiki/Certificate_authority#Providers) 에서 가져옵니다.

+ 가상 컴퓨터에 인증서를 설치한 후이 문서의 지침을 사용 하 여 VM에서 암호화 된 연결을 사용 하도록 설정 하 고 구성 합니다.

## <a name="enable-encrypted-connections"></a>암호화 연결 사용

Azure Cognitive Search에는 공용 인터넷 연결을 통해 모든 인덱서 요청에 대 한 암호화 된 채널이 필요 합니다. 이 섹션에는 작동하도록 하는 단계가 나열되어 있습니다.

1. 인증서의 속성을 확인하여 주체 이름이 Azure VM의 정규화된 도메인 이름(FQDN)인지 확인합니다. 속성을 보기 위해 CertUtils 또는 인증서 스냅인과 같은 도구를 사용할 수 있습니다. VM 서비스 블레이드의 필수 패키지 섹션, **공용 IP 주소/DNS 이름 레이블** 필드, [Azure 포털](https://portal.azure.com/)에서 FQDN을 가져올 수 있습니다.
  
   + 최신 **Resource Manager** 템플릿을 사용하여 만든 VM의 경우 FQDN 형식은 `<your-VM-name>.<region>.cloudapp.azure.com`과 같습니다.

   + **클래식** VM으로 만든 이전 VM의 경우 FQDN 형식은 `<your-cloud-service-name.cloudapp.net>`과 같습니다.

1. 레지스트리 편집기(regedit)를 사용하여 인증서를 사용하도록 SQL Server를 구성합니다. 

   이 작업에는 보통 SQL Server 구성 관리자가 사용되지만 이 시나리오에 대해서는 사용할 수 없습니다. Azure에서 VM의 FQDN이 VM에서 결정한 것과 일치하지 않으므로 가져온 인증서를 찾지 못합니다(로컬 컴퓨터 또는 가입된 네트워크 도메인으로 도메인 식별). 이름이 일치하지 않는 경우 regedit를 사용하여 인증서를 지정합니다.

   + regedit에서 다음 레지스트리 키를 찾습니다. `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.

     `[MSSQL13.MSSQLSERVER]` 부분은 버전 및 인스턴스 이름에 따라 다릅니다. 

   + **인증서** 키의 값을 VM으로 가져온 TLS/SSL 인증서의 지 **문으로** 설정 합니다.

     지문을 가져오는 방법에는 여러 가지가 있으며 여기에는 보다 나은 방법이 있습니다. MMC의 **인증서** 스냅인에서 복사하는 경우 [이 지원 문서에 설명된 대로](https://support.microsoft.com/kb/2023869/)보이지 않는 선행 문자를 선택할 가능성이 있으며 이로 인해 연결을 시도할 때 오류가 발생합니다. 이 문제를 해결하기 위한 여러 가지 해결 방법이 존재합니다. 가장 쉬운 방법은 백스페이스 키로 덮은 후 지문의 첫 문자를 다시 입력하여 regedit의 키 값 필드에서 선행 문자를 제거하는 것입니다. 또는 다른 도구를 사용하여 지문을 복사할 수 있습니다.

1. 서비스 계정에 권한을 부여합니다. 

    SQL Server 서비스 계정에 TLS/SSL 인증서의 개인 키에 대 한 적절 한 권한이 부여 되어 있는지 확인 합니다. 이 단계를 무시하면 SQL Server가 시작되지 않습니다. 이 작업에 대해 **인증서** 스냅인 또는 **CertUtils** 를 사용할 수 있습니다.

1. SQL Server 서비스를 다시 시작합니다.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>VM에서 SQL Server 연결을 구성합니다.

Azure Cognitive Search에 필요한 암호화 된 연결을 설정한 후에는 Azure Vm의 SQL Server에 대 한 추가 구성 단계를 내장 합니다. 아직 수행 하지 않은 경우 다음 단계는 다음 문서 중 하나를 사용 하 여 구성을 완료 하는 것입니다.

+ **Resource Manager** VM인 경우 [Azure에서 Resource Manager를 사용하여 SQL Server Virtual Machine에 연결](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)을 참조하세요. 

+ **클래식** VM인 경우 [Azure 클래식에서 SQL Server Virtual Machine에 연결](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect)을 참조하세요.

특히 "인터넷을 통한 연결"의 각 문서에서 해당 섹션을 검토하세요.

## <a name="configure-the-network-security-group-nsg"></a>NSG(네트워크 보안 그룹) 구성

Azure VM에서 다른 대상에 액세스할 수 있게 하기 위해 NSG 및 해당 Azure 엔드포인트 또는 ACL(Access Control 목록)을 구성하는 것은 특별하지 않습니다. 이전에 이러한 구성을 수행하여 자체 애플리케이션 논리가 SQL Azure VM에 연결되도록 했을 것입니다. SQL Azure VM에 대 한 Azure Cognitive Search 연결에는 차이가 없습니다. 

아래 링크는 VM 배포를 위한 NSG 구성에 대한 지침을 제공합니다. 이러한 지침을 사용 하 여 IP 주소에 따라 Azure Cognitive Search 끝점에 ACL을 설정 합니다.

> [!NOTE]
> 배경 지식은 [네트워크 보안 그룹이란?](../virtual-network/network-security-groups-overview.md)

+ **Resource Manager** VM의 경우 [ARM 배포를 위해 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)을 참조하세요.

+ **클래식** VM의 경우 [클래식 배포를 위해 NSG를 만드는 방법](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps)을 참조하세요.

IP 주소 지정의 경우 몇 가지 문제를 내포할 수 있으며 사용자가 문제와 잠재적인 해결 방법을 인식하고 있는 경우 쉽게 극복할 수 있습니다. 나머지 섹션에서는 ACL에서 IP 주소와 관련된 문제 처리를 위한 권장 사항을 제공합니다.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 액세스 제한

`AzureCognitiveSearch`SQL Azure vm을 모든 연결 요청에 대해 열지 않고 검색 서비스의 ip 주소와 ACL에서 [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) 의 ip 주소 범위에 대 한 액세스를 제한 하는 것이 좋습니다.

검색 서비스의 FQDN (예:)을 ping 하 여 IP 주소를 확인할 수 있습니다 `<your-search-service-name>.search.windows.net` . 검색 서비스 IP 주소는 변경 될 수 있지만 변경 될 가능성은 거의 없습니다. IP 주소는 서비스의 수명 동안 정적입니다.

`AzureCognitiveSearch` [다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 을 사용 하거나 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview)를 통해 [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) 의 IP 주소 범위를 확인할 수 있습니다. IP 주소 범위는 매주 업데이트 됩니다.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Azure Cognitive Search 포털 IP 주소를 포함 합니다.

Azure Portal를 사용 하 여 인덱서를 만드는 경우 SQL Azure 가상 머신에 대 한 인바운드 액세스를 포털에 부여 해야 합니다. 방화벽의 인바운드 규칙을 사용 하려면 포털의 IP 주소를 제공 해야 합니다.

포털 IP 주소를 가져오려면 `stamp2.ext.search.windows.net` traffic manager의 도메인 인 ping을 수행 합니다. 요청 시간이 초과 되지만 IP 주소는 상태 메시지에 표시 됩니다. 예를 들어 "Ping azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48]" 메시지에서 IP 주소는 "52.252.175.48"입니다.

> [!NOTE]
> 다른 지역의 클러스터는 서로 다른 트래픽 관리자에 연결 됩니다. 도메인 이름에 상관 없이 ping에서 반환 된 IP 주소는 해당 지역의 Azure Portal에 대 한 인바운드 방화벽 규칙을 정의할 때 사용할 올바른 IP 주소입니다.

## <a name="next-steps"></a>다음 단계

이제 구성을 사용 하 여 azure VM의 SQL Server를 Azure Cognitive Search 인덱서의 데이터 원본으로 지정할 수 있습니다. 자세한 내용은 [인덱서를 사용 하 여 Azure Cognitive Search에 Azure SQL Database 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)을 참조 하세요.