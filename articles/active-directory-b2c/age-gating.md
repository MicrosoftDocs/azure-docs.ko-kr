---
title: Azure Active Directory B2C에서 나이 제한 사용 | Microsoft Docs
description: 애플리케이션을 사용하여 미성년자를 식별하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522434"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 나이 제한 사용

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C (Azure Active Directory B2C)를 사용 하면 응용 프로그램을 사용 하거나 자녀를 사용 하거나 사용 하지 않을 미성년자 미성년자를 식별할 수 있습니다. 응용 프로그램에 대 한 보조 로그인을 차단 하도록 선택할 수 있습니다. 또는에서를 사용 하 여 로그인을 완료 하 고 응용 프로그램에 보조 상태를 제공할 수 있습니다. 

>[!IMPORTANT]
>이 기능은 공개 미리 보기 상태입니다. 프로덕션 애플리케이션에는 이 기능을 사용하지 마세요.
>

사용자 흐름에 대해 age 게이팅를 사용 하는 경우 사용자에 게 생년월일 및 거주지 국가를 묻는 메시지가 표시 됩니다. 이전에 정보를 입력하지 않은 사용자가 로그인하는 경우 다음에 로그인할 때 해당 정보를 입력해야 합니다. 사용자가 로그인할 때마다 규칙이 적용됩니다.

![나이 제어 정보 수집 흐름의 스크린샷](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C는 사용자가 입력하는 정보를 사용하여 미성년자인지 여부를 식별합니다. 그런 다음, **ageGroup** 필드가 해당 계정에서 업데이트됩니다. 값은 `null`, `Undefined`, `Minor`, `Adult` 및 `NotAdult`일 수 있습니다.  그런 다음, **ageGroup** 및 **consentProvidedForMinor** 필드를 사용하여 **legalAgeGroupClassification** 의 값을 계산합니다.


## <a name="prerequisites"></a>필수 조건

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>나이 제한에 대한 테넌트 설정

사용자 흐름에서 나이 제어를 사용 하려면 추가 속성을 포함 하도록 테 넌 트를 구성 해야 합니다.

1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 위 모서리에서 **모든 서비스** 를 선택하고, **Azure AD B2C** 를 검색하여 선택합니다.
1. 왼쪽 메뉴에서 테넌트에 대한 **속성** 을 선택합니다.
1. **Age 게이팅** 아래에서 **구성** 을 선택 합니다.
1. 작업이 완료될 때까지 기다리면 테넌트에 대한 나이 제한이 설정됩니다.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>사용자 흐름에서 나이 제한 사용

테넌트에서 나이 제한을 사용하도록 설정되면 사용하도록 설정된 [사용자 흐름](user-flow-versions.md)에서 이 기능을 사용할 수 있습니다. 다음 단계에 따라 나이 게이팅을 사용하도록 설정합니다.

1. 나이 제한을 사용하도록 설정된 사용자 흐름을 만듭니다.
1. 사용자 흐름이 만들어지면 메뉴에서 **속성** 을 선택합니다.
1. **나이 제한** 섹션에서 **사용** 을 선택합니다.
1. **등록 또는 로그인** 의 경우 사용자를 관리 하는 방법을 선택 합니다.
    - 미성년자 미성년자가 응용 프로그램에 액세스할 수 있도록 허용 합니다.
    - 응용 프로그램에 액세스 하는 것을 차단 하는 미성년자 미성년자 차단 합니다.
    - 모든 미성년자 미성년자가 응용 프로그램에 액세스 하는 것을 차단 합니다.
1. **On Block에서** 다음 옵션 중 하나를 선택 합니다.
    - **JSON을 응용 프로그램으로 다시 보내기** -이 옵션을 선택 하면 사소한 내용이 차단 된 응용 프로그램으로 응답이 다시 전송 됩니다.
    - **오류 페이지 표시** -사용자에 게 응용 프로그램에 액세스할 수 없음을 알리는 페이지가 표시 됩니다.

## <a name="test-your-user-flow"></a>사용자 흐름 테스트

1. 정책을 테스트 하려면 **사용자 흐름 실행** 을 선택 합니다.
1. **응용 프로그램** 의 경우 이전에 등록 한 *testapp1-development* 이라는 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택 합니다.
1. 로컬 또는 소셜 계정으로 로그인 합니다. 그런 다음, 거주 국가를 선택 하 고, 부를 시뮬레이트하는 생년월일을 선택 합니다. 
1. 테스트를 반복 하 고 성인을 시뮬레이트하는 생년월일을 선택 합니다.  

부로 로그인 하면 다음과 같은 오류 메시지가 표시 됩니다. *아쉽게도 로그인이 차단 되었습니다. 해당 국가의 개인 정보 및 온라인 보안 법은 자녀에 속한 계정에 대 한 액세스를 방지 합니다.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>사용자 지정 정책에서 사용 기간 제어 사용

1. [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/age-gating)에 대 한 연령 제어 정책의 예를 가져옵니다.
1. 각 파일에서 `yourtenant` 문자열을 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예를 들어 B2C 테넌트의 이름이 *contosob2c* 인 경우 모든 `yourtenant.onmicrosoft.com` 인스턴스는 `contosob2c.onmicrosoft.com`이 됩니다.
1. 정책 파일을 업로드합니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C에서 사용자 액세스를 관리](manage-user-access.md)하는 방법을 알아봅니다.

