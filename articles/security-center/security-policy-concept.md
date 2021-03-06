---
title: Azure Security Center의 보안 정책, 이니셔티브 및 권장 사항 이해
description: Azure Security Center의 보안 정책, 이니셔티브 및 권장 사항에 대해 알아봅니다.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176436"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>보안 정책, 이니셔티브 및 권장 사항은 무엇 인가요?

Security Center 보안 이니셔티브를 구독에 적용 합니다. 이러한 이니셔티브에는 하나 이상의 보안 정책이 포함 됩니다. 이러한 각 정책을 통해 보안 상태를 개선 하기 위한 보안 권장 사항이 발생 합니다. 이 페이지에서는 이러한 각 아이디어에 대해 자세히 설명 합니다.


## <a name="what-is-a-security-policy"></a>보안 정책이란?

Azure Policy에서 만든 Azure 정책 정의는 제어 하려는 특정 보안 조건에 대 한 규칙입니다. 기본 제공 정의에는 배포할 수 있는 리소스의 유형을 제어 하거나 모든 리소스에서 태그 사용을 적용 하는 등의 작업이 포함 됩니다. 사용자 고유의 사용자 지정 정책 정의를 만들 수도 있습니다.

이러한 정책 정의 (기본 제공 또는 사용자 지정)를 구현 하려면 해당 정의를 할당 해야 합니다. Azure Portal, PowerShell 또는 Azure CLI를 통해 이러한 정책 중 하나를 할당할 수 있습니다.

Azure Policy에는 다양 한 유형의 정책이 있습니다. Security Center는 주로 특정 조건 및 구성을 확인 하는 ' 감사 ' 정책을 사용 하 고 규정 준수에 대해 보고 합니다. 보안 설정을 적용 하는 데 사용할 수 있는 "적용" 정책도 있습니다.

## <a name="what-is-a-security-initiative"></a>보안 이니셔티브 란?

Azure 이니셔티브는 특정 목표 나 목적으로 그룹화 되는 Azure 정책 정의 또는 규칙의 컬렉션입니다. Azure 이니셔티브는 정책 집합을 단일 항목으로 논리적으로 그룹화 하 여 정책 관리를 간소화 합니다.

보안 이니셔티브는 원하는 워크 로드 구성을 정의 하 고 회사 또는 조정기의 보안 요구 사항을 준수 하는지 확인 하는 데 도움이 됩니다.

보안 정책과 마찬가지로 Security Center 이니셔티브는 Azure Policy 에서도 생성 됩니다. [Azure Policy](../governance/policy/overview.md) 를 사용 하 여 정책을 관리 하 고, 이니셔티브를 작성 하 고, 여러 구독 또는 전체 관리 그룹에 이니셔티브를 할당할 수 있습니다.

Azure Security Center의 모든 구독에 자동으로 할당 되는 기본 이니셔티브는 Azure 보안 벤치 마크입니다. 이 벤치 마크는 일반적인 규정 준수 프레임 워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대 한 Microsoft에서 작성 한 Azure 관련 지침 집합입니다. 널리 인정받는 이 벤치마크는 클라우드 중심 보안에 초점을 맞춘 [CIS(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) 및 [NIST(National Institute of Standards and Technology)](https://www.nist.gov/)의 컨트롤을 기반으로 합니다. [Azure 보안 벤치마크](../security/benchmarks/introduction.md)에 대해 자세히 알아보세요.

Security Center에서는 보안 이니셔티브 및 정책을 사용 하기 위한 다음과 같은 옵션을 제공 합니다.

- 기본 **제공 기본 이니셔티브 보기 및 편집** -Security Center를 사용 하도록 설정 하면 ' Azure Security 벤치 마크 ' 라는 이니셔티브는 등록 된 모든 Security Center 구독에 자동으로 할당 됩니다. 이 이니셔티브를 사용자 지정 하기 위해 정책의 매개 변수를 편집 하 여 내에서 개별 정책을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. [기본 제공 보안 정책](./policy-reference.md) 목록을 참조 하 여 기본적으로 제공 되는 옵션을 이해 합니다.

- **사용자 고유의 사용자 지정 이니셔티브 추가** -구독에 적용 되는 보안 이니셔티브를 사용자 지정 하려는 경우 Security Center 내에서 수행할 수 있습니다. 그러면 컴퓨터가 만든 정책을 따르지 않는 경우 권장 사항을 받게 됩니다. 사용자 지정 정책 빌드 및 할당에 대 한 지침은 [사용자 지정 보안 이니셔티브 및 정책 사용](custom-security-policies.md)을 참조 하세요.

- **규정 준수 표준 추가** Security Center의 규정 준수 대시보드는 특정 표준 또는 규정 (예: Azure CIS, NIST SP 800-53 4, SWIFT CSP CSCF-v2020)의 컨텍스트에서 환경 내의 모든 평가 상태를 표시 합니다. 자세한 내용은 [규정 준수 향상](security-center-compliance-dashboard.md)을 참조 하세요.

## <a name="what-is-a-security-recommendation"></a>보안 권장 사항이란?

정책을 사용 하 여 Security Center 리소스의 준수 상태를 주기적으로 분석 하 여 잠재적인 보안 잘못 된 문제 및 약점을 식별 합니다. 그런 다음 이러한 문제를 해결 하는 방법에 대 한 권장 사항을 제공 합니다. 권장 사항은 관련 정책에 대해 리소스를 평가 하 고 정의 된 요구 사항을 충족 하지 않는 리소스를 식별 하는 결과입니다.

Security Center은 선택한 이니셔티브에 따라 보안 권장 사항을 적용 합니다. 이니셔티브의 정책을 리소스와 비교 하 여 호환 되지 않는 정책을 하나 이상 찾으면 Security Center 권장 사항으로 제공 됩니다.

권장 사항은 리소스를 보호 하 고 강화 하기 위해 수행할 수 있는 작업입니다. 각 권장 사항은 다음 정보를 제공 합니다.

- 문제에 대 한 간단한 설명입니다.
- 권장 사항을 구현 하기 위해 수행할 수정 단계
- 영향을 받는 리소스

실제로 다음과 같이 작동 합니다.

1. Azure 보안 벤치 마크는 ***이니셔티브*** 입니다.
1. 여기에는 모든 Azure Storage 계정에서 네트워크 액세스를 제한 하 여 공격 노출 영역을 줄이도록 요구 하는 ***정책이*** 포함 됩니다. 이 정책을 "저장소 계정은 가상 네트워크 규칙을 사용 하 여 네트워크 액세스를 제한 해야 합니다." 라고 하며, 사용 하지 않도록 설정 하거나 사용 하도록 설정할 수 있습니다 Azure Policy
1. Azure Security Center 보호 된 구독에서 Azure Storage 계정을 찾은 경우 해당 계정을 평가 하 여 가상 네트워크 규칙을 사용 하 여 보호 되 고 있는지 여부를 확인 합니다. 그렇지 않은 경우 해당 상황을 해결 하 고 해당 리소스의 보안을 강화 하는 ***권장 사항을*** 표시 합니다. 

따라서 이니셔티브 (1)에는 적절 한 경우 권장 사항을 생성 하는 정책 (2)이 포함 됩니다 (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>권장 사항 및 정책 간의 관계 보기

위에서 설명한 것 처럼 Security Center의 기본 제공 권장 사항은 Azure 보안 벤치 마크를 기준으로 합니다. 거의 모든 권장 사항에는 벤치 마크의 요구 사항에서 파생 되는 기본 정책이 있습니다.

권장 사항에 대한 세부 정보를 검토할 때 기본 정책을 볼 수 있으면 도움이 되는 경우가 많습니다. 정책에서 지 원하는 모든 권장 사항에 대해 권장 사항 세부 정보 페이지의 **정책 정의 보기** 링크를 사용 하 여 관련 정책에 대 한 Azure Policy 항목으로 직접 이동 합니다.

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="권장 사항을 지원하는 특정 정책에 대한 Azure Policy 페이지로 연결":::

이 링크를 사용하여 정책 정의를 확인하고 평가 논리를 검토합니다. 

[보안 권장 사항 참조 가이드](recommendations-reference.md)에서 권장 사항 목록을 검토하는 경우 정책 정의 페이지에 대한 링크도 표시됩니다.

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Azure Security Center 권장 사항 참조 페이지에서 직접 특정 정책에 대한 Azure Policy 페이지에 액세스":::


## <a name="next-steps"></a>다음 단계

이 페이지에서는 높은 수준에서 정책, 이니셔티브 및 권장 사항 간의 기본 개념 및 관계에 대해 설명 했습니다. 관련 정보는 다음을 참조하세요.

- [사용자 지정 이니셔티브 만들기](custom-security-policies.md)
- [권장 사항을 사용 하지 않도록 설정 하는 보안 정책 사용 안 함](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Azure Policy에서 보안 정책을 편집 하는 방법에 대해 알아봅니다.](../governance/policy/tutorials/create-and-manage.md)