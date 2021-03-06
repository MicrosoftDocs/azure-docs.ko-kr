---
title: 'ML Studio (클래식): Azure Machine Learning로 마이그레이션-웹 서비스 다시 빌드'
description: Azure Machine Learning에서 파이프라인 끝점으로 Studio (클래식) 웹 서비스 다시 빌드
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565248"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Azure Machine Learning에서 Studio (클래식) 웹 서비스 다시 빌드

이 문서에서는 Azure Machine Learning의 **끝점** 으로 Studio (클래식) 웹 서비스를 다시 빌드하는 방법에 대해 알아봅니다.

Azure Machine Learning 파이프라인 끝점을 사용 하 여 예측을 만들거나, 모델을 다시 학습, 제네릭 파이프라인을 실행할 수 있습니다. REST 끝점을 사용 하면 모든 플랫폼에서 파이프라인을 실행할 수 있습니다. 

이 문서는 Azure Machine Learning 마이그레이션 시리즈에 대 한 스튜디오 (클래식)의 일부입니다. Azure Machine Learning로 마이그레이션하는 방법에 대 한 자세한 내용은 [마이그레이션 개요 문서](migrate-overview.md)를 참조 하세요.

> [!NOTE]
> 이 마이그레이션 시리즈는 끌어서 놓기 디자이너를 집중적으로 다룹니다. 프로그래밍 방식으로 모델을 배포 하는 방법에 대 한 자세한 내용은 [Azure에서 기계 학습 모델 배포](../how-to-deploy-and-where.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역을 만듭니다](../how-to-manage-workspace.md#create-a-workspace).
- Azure Machine Learning 교육 파이프라인입니다. 자세한 내용은 [Azure Machine Learning에서 Studio (클래식) 실험 다시 빌드](migrate-rebuild-experiment.md)를 참조 하세요.

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>실시간 끝점 vs 파이프라인 끝점

Studio (클래식) 웹 서비스는 Azure Machine Learning의 **끝점** 으로 대체 되었습니다. 다음 표를 사용 하 여 사용할 끝점 유형을 선택 합니다.

|Studio (클래식) 웹 서비스| Azure Machine Learning 교체
|---|---|
|웹 서비스 요청/응답 (실시간 예측)|실시간 엔드포인트|
|Batch 웹 서비스 (일괄 처리 예측)|파이프라인 엔드포인트|
|재 학습 웹 서비스 (재 학습)|파이프라인 엔드포인트| 


## <a name="deploy-a-real-time-endpoint"></a>실시간 엔드포인트 배포

Studio (클래식)에서는 **요청/응답 웹 서비스** 를 사용 하 여 실시간 예측에 대 한 모델을 배포 했습니다. Azure Machine Learning에서는 **실시간 끝점** 을 사용 합니다.

Azure Machine Learning에서 모델을 배포 하는 방법에는 여러 가지가 있습니다. 가장 간단한 방법 중 하나는 디자이너를 사용 하 여 배포 프로세스를 자동화 하는 것입니다. 모델을 실시간 끝점으로 배포 하려면 다음 단계를 사용 합니다.

1. 완료 된 교육 파이프라인을 한 번 이상 실행 합니다.
1. 실행이 완료 된 후 캔버스 위쪽에서 **유추 파이프라인 만들기**  >  **실시간 유추 파이프라인** 을 선택 합니다.

    ![실시간 유추 파이프라인 만들기](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    디자이너가 학습 파이프라인을 실시간 유추 파이프라인으로 변환 합니다. Studio (클래식) 에서도 비슷한 변환이 수행 됩니다.

    디자이너에서 변환 단계는 [학습 된 모델을 Azure Machine Learning 작업 영역에도 등록](../how-to-deploy-and-where.md#registermodel)합니다.

1. **제출** 을 선택 하 여 실시간 유추 파이프라인을 실행 하 고 성공적으로 실행 되는지 확인 합니다.

1. 유추 파이프라인을 확인 한 후 **배포** 를 선택 합니다.

1. 끝점의 이름과 계산 형식을 입력 합니다.

    다음 표에서는 디자이너의 배포 계산 옵션에 대해 설명 합니다.

    | 컴퓨팅 대상 | 사용 대상 | Description | 만들기 |
    | ----- |  ----- | ----- | -----  |
    |[AKS(Azure Kubernetes Service)](../how-to-deploy-azure-kubernetes-service.md) |실시간 유추|대규모 프로덕션 배포. 빠른 응답 시간 및 서비스 자동 크기 조정.| 사용자가 생성 했습니다. 자세한 내용은 [계산 대상 만들기](../how-to-create-attach-compute-studio.md#inference-clusters)를 참조 하세요. |
    |[Azure Container Instances ](../how-to-deploy-azure-container-instance.md)|테스트 또는 개발 | 48 GB 미만의 RAM이 필요한 작은 규모의 CPU 기반 워크 로드.| Azure Machine Learning에 의해 자동으로 생성 됩니다.

### <a name="test-the-real-time-endpoint"></a>실시간 엔드포인트 테스트

배포가 완료 되 면 자세한 정보를 확인 하 고 끝점을 테스트할 수 있습니다.

1. **끝점** 탭으로 이동 합니다.
1. 끝점을 선택 합니다.
1. **테스트** 탭을 선택합니다.
    
    ![테스트 끝점 단추가 있는 끝점 탭을 보여 주는 스크린샷](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>일괄 처리 예측 또는 재 학습을 위한 파이프라인 끝점 게시

또한 학습 파이프라인을 사용 하 여 실시간 끝점 대신 **파이프라인 끝점** 을 만들 수 있습니다. **파이프라인 끝점** 을 사용 하 여 일괄 처리 예측 또는 재 학습을 수행할 수 있습니다.

파이프라인 끝점은 Studio (클래식) **일괄 처리 실행 끝점**  을 대체 하 고 **웹 서비스** 를 다시 학습 합니다.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>일괄 처리 예측을 위한 파이프라인 끝점 게시

일괄 처리 예측 끝점 게시는 실시간 끝점과 비슷합니다.

일괄 처리 예측을 위해 파이프라인 끝점을 게시 하려면 다음 단계를 사용 합니다.

1. 완료 된 교육 파이프라인을 한 번 이상 실행 합니다.

1. 실행이 완료 된 후 캔버스 위쪽에서 **유추 파이프라인**  >  **일괄 처리 유추 파이프라인** 만들기를 선택 합니다.

    ![학습 파이프라인에서 유추 파이프라인 만들기 단추를 보여 주는 스크린샷](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    디자이너가 학습 파이프라인을 일괄 처리 유추 파이프라인으로 변환 합니다. Studio (클래식) 에서도 비슷한 변환이 수행 됩니다.

    디자이너에서이 단계 [는 학습 된 모델을 Azure Machine Learning 작업 영역에도 등록](../how-to-deploy-and-where.md#registermodel)합니다.

1. **제출** 을 선택 하 여 일괄 처리 유추 파이프라인을 실행 하 고 성공적으로 완료 되었는지 확인 합니다.

1. 유추 파이프라인을 확인 한 후 **게시** 를 선택 합니다.
 
1. 새 파이프라인 끝점을 만들거나 기존 파이프라인 끝점을 선택 합니다.
    
    새 파이프라인 끝점은 파이프라인에 대 한 새 REST 끝점을 만듭니다. 

    기존 파이프라인 끝점을 선택 하는 경우 기존 파이프라인을 덮어쓰지 않습니다. 대신 끝점의 각 파이프라인 버전을 Azure Machine Learning 합니다. REST 호출에서 실행할 버전을 지정할 수 있습니다. REST 호출에서 버전을 지정 하지 않는 경우에도 기본 파이프라인을 설정 해야 합니다.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>재 학습을 위한 파이프라인 끝점 게시

재 학습을 위한 파이프라인 끝점을 게시 하려면 모델을 학습 하는 파이프라인 초안이 이미 있어야 합니다. 학습 파이프라인을 빌드하는 방법에 대 한 자세한 내용은 [Studio (클래식) 실험 다시 빌드](migrate-rebuild-experiment.md)를 참조 하세요.

다시 학습을 위해 파이프라인 끝점을 다시 사용 하려면 입력 데이터 집합에 대 한 **파이프라인 매개 변수** 를 만들어야 합니다. 이렇게 하면 모델을 다시 학습 수 있도록 학습 데이터 집합을 동적으로 설정할 수 있습니다.

다음 단계를 사용 하 여 재 학습 파이프라인 끝점을 게시 합니다.

1. 한 번 이상 학습 파이프라인을 실행 합니다.
1. 실행이 완료 되 면 데이터 집합 모듈을 선택 합니다.
1. 모듈 세부 정보 창에서 **파이프라인 매개 변수로 설정** 을 선택 합니다.
1. "InputDataset"와 같은 설명이 포함 된 이름을 제공 합니다.    

    ![파이프라인 매개 변수를 만드는 방법을 강조 표시 하는 스크린샷](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    이렇게 하면 입력 데이터 집합에 대 한 파이프라인 매개 변수가 만들어집니다. 학습을 위해 파이프라인 끝점을 호출 하는 경우 새 데이터 집합을 지정 하 여 모델을 다시 학습 수 있습니다.

1. **게시** 를 선택합니다.

    ![학습 파이프라인에서 게시 단추를 강조 표시 하는 스크린샷](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>스튜디오에서 파이프라인 끝점 호출

일괄 처리 유추 나 재 학습 파이프라인 끝점을 만든 후 브라우저에서 직접 끝점을 호출할 수 있습니다.

1. **파이프라인 탭으로** 이동 하 고 **파이프라인 끝점** 을 선택 합니다.
1. 실행 하려는 파이프라인 끝점을 선택 합니다.
1. **제출** 을 선택합니다.

    **전송** 을 선택한 후 파이프라인 매개 변수를 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Machine Learning에서 Studio (클래식) 웹 서비스를 다시 빌드하는 방법에 대해 알아보았습니다. 다음 단계는 [웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)하는 것입니다.


Studio (클래식) 마이그레이션 시리즈의 다른 문서를 참조 하세요.

1. [마이그레이션 개요](migrate-overview.md).
1. [데이터 집합을 마이그레이션합니다](migrate-register-dataset.md).
1. [Studio (클래식) 교육 파이프라인을 다시 빌드합니다](migrate-rebuild-experiment.md).
1. **Studio (클래식) 웹 서비스를 다시 빌드합니다**.
1. [Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. [R 스크립트 실행을 마이그레이션합니다](migrate-execute-r-script.md).
