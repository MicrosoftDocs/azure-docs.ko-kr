---
title: 전용 SQL 풀에 대 한 지속적인 통합 및 배포
description: Azure Pipelines를 사용한 연속 통합 및 배포에 대 한 기본 제공 지원을 통해 Azure Synapse Analytics의 전용 SQL 풀에 대 한 엔터프라이즈급 데이터베이스 DevOps 환경
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d43039c98e991cd23a8e5c4fb866a8e3dcab6fc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585641"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀에 대 한 연속 통합 및 배포

이 간단한 자습서에서는 SSDT(SQL Server Data Tools) 데이터베이스 프로젝트를 Azure DevOps와 통합하고, Azure Pipelines를 활용하여 지속적인 통합 및 배포를 설정하는 방법에 대해 간략히 설명합니다. 이 자습서는 데이터 웨어하우징에 대 한 연속 통합 및 배포 파이프라인을 구축 하는 두 번째 단계입니다.

## <a name="before-you-begin"></a>시작하기 전에

- [원본 제어 통합 자습서](sql-data-warehouse-source-control-integration.md) 살펴보기

- Azure DevOps 설정 및 연결

## <a name="continuous-integration-with-visual-studio-build"></a>Visual Studio 빌드와의 지속적인 통합

1. Azure Pipelines로 이동하여 새 빌드 파이프라인을 만듭니다.

      ![새 파이프라인](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "새 파이프라인")

2. 소스 코드 리포지토리(Azure Repos Git)를 선택하고, .NET 데스크톱 앱 템플릿을 선택합니다.

      ![파이프라인 설정](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "파이프라인 설정")

3. 적절한 에이전트 풀을 사용하도록 YAML 파일을 편집합니다. YAML 파일은 다음과 같습니다.

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

이 시점에서는 소스 제어 리포지토리 주 분기에 대 한 체크 인이 데이터베이스 프로젝트의 성공적인 Visual Studio 빌드를 자동으로 트리거하는 간단한 환경을 만들었습니다. 로컬 데이터베이스 프로젝트를 변경 하 고 주 분기에 대 한 변경 내용을 체크 인하여 자동화가 종단 간 작업을 수행 하는지 확인 합니다.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Azure Synapse Analytics (또는 데이터베이스) 배포 작업을 사용한 연속 배포

1. [Azure SQL Database 배포 작업](/azure/devops/pipelines/targets/azure-sqldb)을 사용하여 새 작업을 추가하고, 대상 데이터 웨어하우스에 연결하는 데 필요한 필드를 채웁니다. 이 작업이 실행되면 이전 빌드 프로세스에서 생성된 DACPAC가 대상 데이터 웨어하우스에 배포됩니다. [Azure Synapse Analytics 배포 작업](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)을 사용할 수도 있습니다.

      ![배포 작업](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "배포 작업")

2. 자체 호스팅 에이전트를 사용 하는 경우 Azure Synapse Analytics에 올바른 SqlPackage.exe를 사용 하도록 환경 변수를 설정 해야 합니다. 경로는 다음과 같습니다.

      ![환경 변수](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "환경 변수")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   파이프라인을 실행하고 유효성을 검사합니다. 로컬에서 변경하고 자동 빌드 및 배포를 생성해야 하는 원본 제어에 대한 변경 내용을 체크 인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [전용 sql 풀 (이전의 SQL DW) 아키텍처](massively-parallel-processing-mpp-architecture.md) 탐색
- [전용 sql 풀 빠르게 만들기 (이전의 SQL DW)](create-data-warehouse-portal.md)
- [샘플 데이터 로드](./load-data-from-azure-blob-storage-using-copy.md)
- [비디오](sql-data-warehouse-videos.md) 살펴보기