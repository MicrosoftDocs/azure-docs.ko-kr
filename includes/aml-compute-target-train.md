---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: 3eb5ea468a234aea228539c2390ab6cae9352948
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630175"
---
**컴퓨팅 대상은 한 학습 작업에서 다음 학습 작업으로 재사용할 수 없습니다.** 예를 들어 원격 VM을 작업 영역에 연결한 후 여러 작업에 다시 사용할 수 있습니다. 기계 학습 파이프라인의 경우 각 컴퓨팅 대상에 적절한 [파이프라인 단계](/python/api/azureml-pipeline-steps/azureml.pipeline.steps)를 사용합니다.

대부분의 작업에 대한 학습 컴퓨팅 대상에 다음 리소스를 사용할 수 있습니다. 모든 리소스를 자동화된 기계 학습, 기계 학습 파이프라인 또는 디자이너에 사용할 수 있는 것은 아닙니다.

|학습 &nbsp;대상|[자동화된 기계 학습](../articles/machine-learning/concept-automated-ml.md) | [기계 학습 파이프라인](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning 디자이너](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[로컬 컴퓨터](../articles/machine-learning/how-to-attach-compute-targets.md#local)| 예 | &nbsp; | &nbsp; |
|[Azure Machine Learning 컴퓨팅 클러스터](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| 예 | 예 | 예 |
|[Azure Machine Learning 컴퓨팅 인스턴스](../articles/machine-learning/how-to-create-manage-compute-instance.md) | 예(SDK를 통해)  | 예 |  |
|[원격 VM](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | 예  | 예 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| 예(SDK 로컬 모드 전용) | 예 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | 예 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | 예 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | 예 | &nbsp; |

> [!TIP]
> 컴퓨팅 인스턴스에 120GB OS 디스크가 있습니다. 디스크 공간이 부족한 경우에는 [터미널을 사용](../articles/machine-learning/how-to-access-terminal.md)하여 최소 1~2GB를 지운 후 컴퓨팅 인스턴스를 [중지하거나 다시 시작](../articles/machine-learning/how-to-create-manage-compute-instance.md#manage)해야 합니다.
