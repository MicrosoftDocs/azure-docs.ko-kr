---
title: Azure에서 OpenShift Container Platform 4.x 배포
description: Azure에서 OpenShift Container Platform 4.x를 배포 합니다.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: e8650802b4add9b33664205367bb3242b32b9754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670388"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Azure에서 OpenShift Container Platform 4.x 배포

이제 IPI (Installer-Provisioned 인프라) 모델을 통해 Azure에서 OCP (OpenShift Container Platform) 4.2 배포가 지원 됩니다.  OpenShift 4를 시도 하기 위한 방문 페이지는 [try.openshift.com](https://try.openshift.com/)입니다. Azure에서 OCP 4.2을 설치 하려면 [Red Hat OpenShift 클러스터 관리자](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) 페이지를 방문 하세요.  이 사이트에 액세스 하려면 red Hat 자격 증명이 필요 합니다.


## <a name="notes"></a>참고 

 - Azure에서 OCP 4.x를 설치 하 고 실행 하려면 AAD (Azure Active Directory) SP (서비스 사용자)가 필요 합니다.
     - SP는 Azure Active Directory Graph에 대해 OwnedBy의 API 권한을 부여 받아야 합니다 **.**
     - AAD 테넌트 관리자이 API 권한을 적용 하려면 관리자 동의를 부여 해야 합니다.
     - SP에 게 구독에 대 한 **참가자** 및 **사용자 액세스 관리자** 역할을 부여 해야 합니다.
 - OCP 4.x의 설치 모델은 3(sp3)과 다르며, Azure에서 OCP 4.x를 배포 하는 데 사용할 수 있는 Azure Resource Manager 템플릿이 없습니다.
 - 설치 프로세스 중에 문제가 발생 하는 경우 해당 회사 (Microsoft 또는 Red Hat)에 문의 하십시오.

| 문제 설명 | 접점 |
|-------------------|---------------|
| Azure 관련 문제 (AAD, SP, Azure 구독 등)                              | Microsoft |
| OpenShift 관련 문제 (설치 오류/오류, Red Hat 구독 등) |  Red Hat  |




## <a name="next-steps"></a>다음 단계

- [OpenShift Container Platform 시작](https://docs.openshift.com)
