---
title: 'Azure Express 경로: 라우터 구성 샘플-NAT'
description: 이 페이지는 Cisco 및 Juniper 라우터에 대한 라우터 구성 샘플을 제공합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 01/07/2021
ms.author: duau
ms.openlocfilehash: ae0a39d65bf0f1bc5221cd5e46493c489f7630f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012668"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>NAT 설정 및 관리를 위한 라우터 구성 샘플

이 문서에서는 Express 경로를 사용 하 여 작업할 때 Cisco GLOBAL.ASA 및 곱 향나무 SRX 시리즈 라우터 용 NAT 구성 샘플을 제공 합니다. 이러한 라우터 구성은 참조용 으로만 사용 되며 그대로 사용 하면 안 됩니다. 네트워크에 대 한 적절 한 구성을 제공 하려면 공급 업체와 함께 작업 해야 합니다.

> [!IMPORTANT]
> 이 페이지에 있는 샘플은 참조용입니다. 공급업체의 영업/기술 팀 및 네트워킹 팀과 함께 작업하면서 필요에 맞게 적절히 구성해야 합니다. Microsoft는 이 페이지에 나열된 구성과 관련된 문제를 지원하지 않습니다. 지원 문제는 디바이스 공급업체에 문의해야 합니다.
> 
> 

* 아래의 라우터 구성 샘플은 Azure 공용 및 Microsoft 피어링에 적용됩니다. Azure 개인 피어 링에 대 한 NAT를 구성 하지 않습니다. 자세한 내용은 [ExpressRoute 피어링](expressroute-circuit-peerings.md) 및 [ExpressRoute NAT 요구 사항](expressroute-nat.md)을 검토하세요.

* 인터넷 및 ExpressRoute에 대한 연결은 별도의 NAT IP 풀을 사용해야 합니다. 인터넷 및 ExpressRoute에서 동일한 NAT IP 풀을 사용하면 비대칭 라우팅 및 연결 손실이 발생합니다.


## <a name="cisco-asa-firewalls"></a>Cisco ASA 방화벽
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>고객 네트워크에서 Microsoft로의 트래픽을 위한 PAT 구성

```console
object network MSFT-PAT
  range <SNAT-START-IP> <SNAT-END-IP>


object-group network MSFT-Range
  network-object <IP> <Subnet_Mask>

object-group network on-prem-range-1
  network-object <IP> <Subnet-Mask>

object-group network on-prem-range-2
  network-object <IP> <Subnet-Mask>

object-group network on-prem
  network-object object on-prem-range-1
  network-object object on-prem-range-2

nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range
```

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Microsoft에서 고객 네트워크로 트래픽 PAT 구성

**인터페이스 및 방향:**

원본 인터페이스 (트래픽이 GLOBAL.ASA로 들어가는 위치): 대상 인터페이스 내부 (트래픽이이를 종료 하는 위치): 외부

**구성:**

NAT 풀:

```console
object network outbound-PAT
    host <NAT-IP>
```

대상 서버:

```console
object network Customer-Network
    network-object <IP> <Subnet-Mask>
```

고객 IP 주소에 대 한 개체 그룹:

```console
object-group network MSFT-Network-1
    network-object <MSFT-IP> <Subnet-Mask>

object-group network MSFT-PAT-Networks
    network-object object MSFT-Network-1
```

NAT 명령:

```console
nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network
```


## <a name="juniper-srx-series-routers"></a>Juniper SRX 시리즈 라우터
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. 클러스터에 대 한 중복 이더넷 인터페이스 만들기

```console
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }
```

### <a name="2-create-two-security-zones"></a>2. 두 개의 보안 영역을 만듭니다.
* 내부 네트워크에 대한 신뢰 영역 및 에지 라우터 방향의 외부 네트워크에 대한 신뢰할 수 없는 영역
* 영역에 적절한 인터페이스 할당
* 인터페이스에서 서비스 허용

```console
    security {
        zones {
            security-zone Trust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth0.100;
                }
            }
            security-zone Untrust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth1.100;
                }
            }
        }
    }
```


### <a name="3-create-security-policies-between-zones"></a>3. 영역 간에 보안 정책 만들기

```console
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }
```

### <a name="4-configure-nat-policies"></a>4. NAT 정책 구성
* 두 NAT 풀을 만듭니다. 하나는 Microsoft로의 NAT 트래픽 아웃바운드에 사용되며 다른 하나는 Microsoft에서 고객에게로의 NAT 트래픽 아웃바운드에 사용됩니다.
* 각 트래픽에 대해 NAT를 수행하기 위한 규칙 만들기

```console
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }
```

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. 각 방향에서 선택적 접두사를 알리도록 BGP를 구성 합니다.
[라우팅 구성 샘플](expressroute-config-samples-routing.md) 페이지의 샘플을 참조 하세요.

### <a name="6-create-policies"></a>6. 정책 만들기

```console
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }
```

## <a name="next-steps"></a>다음 단계
자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.

