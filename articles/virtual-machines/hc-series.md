---
title: HC 시리즈-Azure Virtual Machines
description: HC 시리즈 Vm에 대 한 사양입니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: e23a6351b26cc35679bc879e2b62dd76c74f9962
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798343"
---
# <a name="hc-series"></a>HC 시리즈

HC 시리즈 Vm은 암시적 유한 요소 분석, 분자 dynamics 및 계산 연금술과 같이 조밀한 계산으로 구동 되는 응용 프로그램에 최적화 되어 있습니다. HC Vm 기능 44 Intel Xeon 플래티넘 8168 프로세서 코어, CPU 코어 당 8gb RAM, 하이퍼스레딩을 없음 Intel Xeon Platinum 플랫폼은 intel 수학 커널 라이브러리 및 AVX-512와 같은 고급 벡터 처리 기능과 같은 다양 한 소프트웨어 도구 에코 시스템을 지원 합니다.

HC 시리즈 Vm 기능 100 g b/초 Mellanox EDR InfiniBand. 이러한 Vm은 최적화 되 고 일관 된 RDMA 성능을 위해 차단 되지 않는 fat 트리에 연결 됩니다. 이러한 Vm은 적응 라우팅 및 DCT (표준 RC 및 UD 전송에 대 한 추가)를 지원 합니다. 이러한 기능은 응용 프로그램 성능, 확장성 및 일관성을 향상 시키고 사용을 권장 합니다. 

[Acu](acu.md): 297-315<br>
[Premium Storage](premium-storage-performance.md): 지원 됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원 됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원 되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원 되지 않음<br>
[VM 생성 지원](generation-2.md): 1 세대 및 2 세대<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원 됨 (성능 및 잠재적인 문제에 대 한[자세한](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) 정보)<br>
[삭제 되는 OS 디스크](ephemeral-os-disks.md): 지원 됨 <br>
<br>

| 크기 | vCPU | 프로세서 | 메모리(GiB) | 메모리 대역폭 (GB/초) | 기본 CPU 빈도 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (Gb/s) | MPI 지원 | 임시 스토리지(GiB) | 최대 데이터 디스크 수 | 최대 이더넷 vNICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon 플래티넘 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 모두 | 700 | 4 | 8 |

에 대해 자세히 알아보세요.
- [아키텍처 및 VM 토폴로지](./workloads/hpc/hc-series-overview.md)
- 지원 되는 OS를 포함 하 여 지원 되는 [소프트웨어 스택](./workloads/hpc/hc-series-overview.md#software-specifications)
- HC 시리즈 VM의 예상 [성능](./workloads/hpc/hc-series-performance.md)

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

- [Azure Compute 기술 커뮤니티 블로그에서](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)최신 발표, HPC 워크 로드 예제 및 성능 결과에 대해 알아보세요.
- 실행 중인 HPC 워크 로드에 대 한 개략적인 아키텍처 보기는 [Azure의 hpc (고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조 하세요.
- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
