---
title: '신경망 회귀: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 신경망 회귀 모듈을 사용 하 여 사용자 지정 가능한 신경망 알고리즘을 사용 하 여 회귀 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 403576454615effeb53651b51679681422b08e9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890360"
---
# <a name="neural-network-regression-module"></a>신경망 회귀 모듈

*신경망 알고리즘을 사용하여 회귀 모델 만들기*  
  
 범주: 모델/회귀 Machine Learning/초기화
  
## <a name="module-overview"></a>모듈 개요  

이 문서에서는 Azure Machine Learning 디자이너의 모듈을 설명 합니다.

이 모듈을 사용 하 여 사용자 지정 가능한 신경망 알고리즘을 사용 하 여 회귀 모델을 만듭니다.
  
 신경망은 이미지 인식과 같은 복잡한 문제의 모델링과 심층 학습에서 사용되는 기술로 널리 알려져 있지만 회귀 문제에 맞게 쉽게 조정됩니다. 적응 가중치를 사용하고 입력의 비선형 함수 근사치를 계산할 수 있는 모든 클래스의 통계 모델을 신경망이라고 할 수 있습니다. 따라서 신경망 회귀는 보다 전통적인 회귀 모델로 해결할 수 없는 문제에 적합합니다.
  
 신경망 회귀는 감독 된 학습 메서드 이므로 레이블 열을 포함 하는 *태그가 지정 된 데이터 집합이* 필요 합니다. 회귀 모델은 숫자 값을 예측하기 때문에 레이블 열은 숫자 데이터 형식이어야 합니다.  
  
 모델 [학습](./train-model.md)을 위한 입력으로 모델 및 태그가 지정 된 데이터 집합을 제공 하 여 모델을 학습할 수 있습니다. 그러면 학습 된 모델을 사용 하 여 새 입력 예제에 대 한 값을 예측할 수 있습니다.  
  
## <a name="configure-neural-network-regression"></a>신경망 회귀 구성 

신경망은 광범위 하 게 사용자 지정할 수 있습니다. 이 섹션에서는 다음 두 가지 방법을 사용 하 여 모델을 만드는 방법에 대해 설명 합니다.
  
+ [기본 아키텍처를 사용 하 여 신경망 모델 만들기](#bkmk_DefaultArchitecture)  
  
    기본 신경망 아키텍처를 적용 하는 경우 **속성** 창을 사용 하 여 숨겨진 계층의 노드 수, 학습 율 및 정규화와 같은 신경망의 동작을 제어 하는 매개 변수를 설정 합니다.

    신경망을 처음 접하는 경우 여기서 시작 하세요. 모듈은 신경망에 대 한 심층 지식이 없어도 모델 튜닝 뿐만 아니라 많은 사용자 지정을 지원 합니다. 

+ 신경망의 사용자 지정 아키텍처 정의 

    숨겨진 계층을 더 추가 하거나 네트워크 아키텍처, 해당 연결 및 활성화 기능을 완전히 사용자 지정 하려면이 옵션을 사용 합니다.
    
    이 옵션은 신경망에 대해 이미 잘 알고 있는 경우에 가장 적합 합니다. Net # 언어를 사용 하 여 네트워크 아키텍처를 정의 합니다.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a> 기본 아키텍처를 사용 하 여 신경망 모델 만들기

1.  디자이너에서 파이프라인에 **신경망 회귀** 모듈을 추가 합니다. **회귀** 범주의 **Machine Learning**, **초기화** 에서이 모듈을 찾을 수 있습니다. 
  
2. **강사 모드 만들기** 옵션을 설정 하 여 모델을 학습 하는 방법을 지정 합니다.  
  
    -   **단일 매개 변수**: 모델을 구성 하려는 방법을 이미 알고 있는 경우이 옵션을 선택 합니다.

    -   **매개 변수 범위**: 가장 적합 한 매개 변수를 잘 모르겠으면 매개 변수 스윕을 실행 하려는 경우이 옵션을 선택 합니다. 반복할 값의 범위를 선택 하 고 [모델 조정 하이퍼 매개 변수 변수](tune-model-hyperparameters.md) 는 제공 된 모든 설정의 가능한 조합에 대해 반복 하 여 최적의 결과를 생성 하는 하이퍼 매개 변수를 결정 합니다.   

3.  **숨겨진 계층 사양** 에서 **전체 연결 된 사례** 를 선택 합니다. 이 옵션은 신경망 회귀 모델에 대 한 기본 신경망 아키텍처를 사용 하 여 모델을 만듭니다. 여기에는 다음과 같은 특성이 있습니다.  
  
    + 네트워크에는 숨겨진 계층이 하나만 있습니다.
    + 출력 계층은 숨겨진 계층에 완전히 연결되며 숨겨진 계층은 입력 계층에 완전히 연결됩니다.
    + 숨겨진 계층의 노드 수는 사용자가 설정할 수 있습니다(기본값: 100).  
  
    입력 계층의 노드 수는 학습 데이터의 기능 수에 따라 결정 되기 때문에 회귀 모델에서는 출력 계층에 노드가 하나만 있을 수 있습니다.  
  
4. **숨겨진 노드 수** 에는 숨겨진 노드 수를 입력 합니다. 기본값은 100 노드를 포함 하는 하나의 숨겨진 계층입니다. Net #을 사용 하 여 사용자 지정 아키텍처를 정의 하는 경우에는이 옵션을 사용할 수 없습니다.
  
5.  **학습 속도** 의 경우 수정 하기 전에 각 반복에서 수행 되는 단계를 정의 하는 값을 입력 합니다. 학습 속도 값이 크면 모델이 빠르게 수렴되지만 로컬 최소값이 과도해질 수 있습니다.

6.  **학습 반복 횟수** 에 대해 알고리즘이 학습 사례를 처리 하는 최대 횟수를 지정 합니다.


8.  **모멘텀** 의 경우 학습 중에 이전 반복의 노드에 대 한 가중치로 적용할 값을 입력 합니다.

10. **예** 를 선택 하 여 반복 간의 사례 순서를 변경 합니다. 이 옵션의 선택을 취소 하면 파이프라인을 실행할 때마다 정확히 동일한 순서로 사례가 처리 됩니다.
  
11. **난수 초기값** 의 경우 필요에 따라 초기값으로 사용할 값을 입력할 수 있습니다. 초기값을 지정 하는 것은 동일한 파이프라인의 실행 간에 반복성을 보장 하려는 경우에 유용 합니다.
  
13. 학습 데이터 집합을 연결 하 고 모델을 학습 합니다.

    + 담당자 **모드 만들기** 를 **단일 매개 변수로** 설정한 경우 태그가 지정 된 데이터 집합 및 [모델 학습](train-model.md) 모듈을 연결 합니다.  
  
    + **만든이 모드** 를 **매개 변수 범위** 로 설정 하는 경우에는 태그가 지정 된 데이터 집합을 연결 하 고 [모델 모델링 hyperparameters](tune-model-hyperparameters.md)를 사용 하 여 모델을 학습 합니다.  
  
    > [!NOTE]
    > 
    > [모델 학습](train-model.md)에 매개 변수 범위를 전달 하는 경우 단일 매개 변수 목록의 기본값만 사용 합니다.  
    > 
    > 단일 매개 변수 값 집합을 [모델 하이퍼 매개 변수 조정](tune-model-hyperparameters.md) 모듈에 전달 하는 경우 각 매개 변수에 대 한 설정 범위가 필요한 경우 값을 무시 하 고 학습자에 대 한 기본값을 사용 합니다.  
    > 
    > **매개 변수 범위** 옵션을 선택 하 고 매개 변수에 대해 단일 값을 입력 하는 경우 다른 매개 변수가 값 범위에서 변경 되더라도 지정한 단일 값은 스윕 전체에서 사용 됩니다.  
  
   
14. 파이프라인을 제출합니다.  

## <a name="results"></a>결과

학습 완료 후:

- 학습 된 모델의 스냅숏을 저장 하려면 **모델 학습** 모듈의 오른쪽 패널에서 **출력** 탭을 선택 합니다. **데이터 집합 등록** 아이콘을 선택 하 여 모델을 재사용 가능한 모듈로 저장 합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 