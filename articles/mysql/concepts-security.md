---
title: 보안-Azure Database for MySQL
description: Azure Database for MySQL의 보안 기능에 대 한 개요입니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 90855059461fcd5f8ed8d2733d2b6d4addaccde3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535047"
---
# <a name="security-in-azure-database-for-mysql"></a>Azure Database for MySQL의 보안

Azure Database for MySQL 서버에서 데이터를 보호 하는 데 사용할 수 있는 여러 보안 계층이 있습니다. 이 문서에서는 이러한 보안 옵션을 설명 합니다.

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="in-transit"></a>전송 중
Azure Database for MySQL 전송 계층 보안을 사용 하 여 전송 중인 데이터를 암호화 하 여 데이터를 보호 합니다. 암호화 (SSL/TLS)는 기본적으로 적용 됩니다.

### <a name="at-rest"></a>휴지 상태의
Azure Database for MySQL 서비스는 미사용 데이터의 스토리지 암호화를 위해 FIPS 140-2 유효성 검사 암호화 모듈을 사용합니다. 백업을 비롯 한 데이터는 쿼리를 실행 하는 동안 생성 된 임시 파일을 포함 하 여 디스크에 암호화 됩니다. 이 서비스는 Azure 스토리지 암호화에 포함된 AES 256비트 암호화를 사용하며, 키는 시스템에서 관리됩니다. 스토리지 암호화는 항상 켜져 있고 해제할 수 없습니다.


## <a name="network-security"></a>네트워크 보안
Azure Database for MySQL 서버에 대 한 연결은 먼저 지역 게이트웨이를 통해 라우팅됩니다. 게이트웨이는 공개적으로 액세스할 수 있는 IP를 포함 하지만 서버 IP 주소는 보호 됩니다. 게이트웨이에 대 한 자세한 내용은 [연결 아키텍처 문서](concepts-connectivity-architecture.md)를 참조 하세요.  

새로 만든 Azure Database for MySQL 서버에는 모든 외부 연결을 차단 하는 방화벽이 있습니다. 게이트웨이는 게이트웨이에 연결 되지만 서버에 연결할 수는 없습니다. 

### <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 방화벽 규칙은 각 요청의 원래 IP 주소에 따라 서버에 대 한 액세스 권한을 부여 합니다. 자세한 내용은 [방화벽 규칙 개요](concepts-firewall-rules.md) 를 참조 하세요.

### <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙
가상 네트워크 서비스 끝점은 Azure 백본을 통해 가상 네트워크 연결을 확장 합니다. 가상 네트워크 규칙을 사용 하 여 가상 네트워크에서 선택한 서브넷의 연결을 허용 하도록 Azure Database for MySQL 서버를 설정할 수 있습니다. 자세한 내용은 [가상 네트워크 서비스 끝점 개요](concepts-data-access-and-security-vnet.md)를 참조 하세요.

### <a name="private-ip"></a>프라이빗 IP
개인 링크를 사용 하면 개인 끝점을 통해 Azure의 Azure Database for MySQL에 연결할 수 있습니다. Azure Private Link는 기본적으로 개인 VNet(Virtual Network) 내에 Azure 서비스를 제공합니다. PaaS 리소스는 VNet의 다른 리소스와 마찬가지로 개인 IP 주소를 사용하여 액세스할 수 있습니다. 자세한 내용은 [개인 링크 개요](concepts-data-access-security-private-link.md) 를 참조 하세요.

## <a name="access-management"></a>액세스 관리

Azure Database for MySQL 서버를 만드는 동안 관리자 사용자에 대 한 자격 증명을 제공 합니다. 이 관리자를 사용 하 여 추가 MySQL 사용자를 만들 수 있습니다.


## <a name="threat-protection"></a>위협 보호

비정상적인 활동을 검색 하는 [고급 위협 방지](concepts-data-access-and-security-threat-protection.md) 를 옵트인 (opt in) 하 여 서버에 액세스 하거나 악용 하려는 비정상적인 시도를 발견할 수 있습니다.

[감사 로깅은](concepts-audit-logs.md) 데이터베이스의 활동을 추적 하는 데 사용할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
- [Ip](concepts-firewall-rules.md) 또는 [가상 네트워크](concepts-data-access-and-security-vnet.md) 에 대 한 방화벽 규칙 사용
