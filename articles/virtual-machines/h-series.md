---
title: H 시리즈-Azure Virtual Machines
description: H 시리즈 Vm에 대 한 사양입니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b3e3beb51256bbf22d29d74b51c52ca3a6bee0c4
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774782"
---
# <a name="h-series"></a>H 시리즈

H 시리즈 Vm은 높은 CPU 주파수 또는 핵심 요구 사항 당 대용량 메모리를 기반으로 하는 응용 프로그램에 최적화 되어 있습니다. H 시리즈 Vm은 8 또는 16 Intel Xeon E5 2667 v3 프로세서 코어, CPU 코어 당 최대 14gb RAM, 하이퍼스레딩을 갖지 않습니다. H 시리즈 기능은 일관 된 RDMA 성능을 위해 비 블로킹 된 fat 트리 구성에서 56 g b/초 Mellanox Mellanox DR InfiniBand을 제공 합니다. H 시리즈 Vm은 현재 SR-IOV를 사용 하도록 설정 되지 않으며 Intel MPI 5.x 및 MS MPI를 지원 합니다.

[Acu](acu.md): 290-300<br>
[Premium Storage](premium-storage-performance.md): 지원 되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원 되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원 되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원 되지 않음<br>
[VM 생성 지원](generation-2.md): 1 세대<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원 되지 않음<br>
[삭제 되는 OS 디스크](ephemeral-os-disks.md): 지원 되지 않음 <br>
<br>

| 크기 | vCPU | 프로세서 | 메모리(GiB) | 메모리 대역폭 (GB/초) | 기본 CPU 빈도 (GHz) | 모든 코어 빈도 (GHz, 최고) | 단일 코어 빈도 (GHz, 최고) | RDMA 성능 (Gb/s) | MPI 지원 | 임시 스토리지(GiB) | 최대 데이터 디스크 수 | 최대 디스크 처리량: IOPS | 최대 이더넷 vNICs |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> MPI 응용 프로그램의 경우 Fdr InfiniBand 네트워크를 통해 전용 RDMA 백 엔드 네트워크를 사용할 수 있습니다.

> [!NOTE]
> [RDMA 지원 vm](sizes-hpc.md#rdma-capable-instances)중에서 H 시리즈는 sr-iov를 사용 하도록 설정 되어 있지 않습니다. 따라서 지원 되는 [Vm 이미지](./workloads/hpc/configure.md#vm-images), [InfiniBand 드라이버](./workloads/hpc/enable-infiniband.md) 요구 사항 및 지원 되는 [MPI 라이브러리](./workloads/hpc/setup-mpi.md) 는 sr-iov 지원 vm과 다릅니다.

## <a name="software-specifications"></a>소프트웨어 사양

| 소프트웨어 사양     |HC 시리즈 VM           |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 4800 코어 (단일 가상 머신 확장 집합에서 단일 가상 머신 확장 집합의 300 Vm (Singleementgroup = true))  |
| MPI 지원                 | Intel MPI 5.x, MS MPI  |
| 비 SRIOV RDMA에 대 한 OS 지원   | CentOS/RHEL 6.5-7.4, SLES 12 SP4 +, WinServer 2012-2016  |
| Orchestrator 지원        | CycleCloud, Batch, AKS  |

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

- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크 로드 예제 및 성능 결과에 대해 읽어 보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
