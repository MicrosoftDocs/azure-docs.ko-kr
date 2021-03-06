---
title: 사용자 지정 정책에 대 한 StringCollection 클레임 변환 예제
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 IEF (Identity Experience Framework) 스키마에 대 한 StringCollection 클레임 변환 예입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9104fb4598eb62ed96d0b21734053fa118b5237
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120285"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 Id 경험 프레임 워크 스키마의 문자열 컬렉션 클레임 변환을 사용 하는 예제를 제공 합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="additemtostringcollection"></a>AddItemToStringCollection

새 고유 값 stringCollection 클레임에 문자열 클레임을 추가 합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 항목 | 문자열 | 출력 클레임에 추가할 ClaimType입니다. |
| InputClaim | collection | stringCollection | 출력 클레임에 추가 되는 문자열 컬렉션입니다. 컬렉션에 항목이 포함 되어 있으면 클레임 변환은 항목을 복사 하 고 출력 컬렉션 클레임의 끝에 항목을 추가 합니다. |
| OutputClaim | collection | stringCollection | 이 클레임 변환 후에 생성 된 ClaimType이 입력 클레임에 지정 된 값을 사용 하 여 호출 된 경우 |

신규 또는 기존 stringCollection에 문자열을 추가하려면 이 클레임 변환을 사용합니다. 이 클레임 변환은 일반적으로 **AAD-UserWriteUsingAlternativeSecurityId** 기술 프로필에서 사용됩니다. 새 소셜 계정을 만들기 전에 **CreateOtherMailsFromEmail** 클레임 변환은 ClaimType을 읽고 값을 **otherMails** ClaimType에 추가합니다.

다음 클레임 변환에서는 **email** ClaimType을 **otherMails** ClaimType에 추가합니다.

```xml
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
  - **컬렉션**: [" someone@outlook.com "]
  - **항목**: " admin@contoso.com "
- 출력 클레임:
  - **컬렉션**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

새 고유 값 stringCollection 클레임에 문자열 매개 변수를 추가 합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 출력 클레임에 추가 되는 문자열 컬렉션입니다. 컬렉션에 항목이 포함 되어 있으면 클레임 변환은 항목을 복사 하 고 출력 컬렉션 클레임의 끝에 항목을 추가 합니다. |
| InputParameter | 항목 | 문자열 | 출력 클레임에 추가할 값입니다. |
| OutputClaim | collection | stringCollection | 입력 매개 변수에 지정된 값을 사용하여 이 클레임 변환을 호출하고 나면 생성되는 ClaimType입니다. |

신규 또는 기존 stringCollection에 문자열 값을 추가하려면 이 클레임 변환을 사용합니다. 다음 예제에서는 상수 전자 메일 주소(admin@contoso.com)를 **otherMails** 클레임에 추가합니다.

```xml
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
  - **컬렉션**: [" someone@outlook.com "]
- 입력 매개 변수
  - **항목**: " admin@contoso.com "
- 출력 클레임:
  - **컬렉션**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

제공된 문자열 컬렉션에서 첫 번째 항목을 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 클레임 변환에서 항목을 가져오는 데 사용하는 ClaimType입니다. |
| OutputClaim | extractedItem | 문자열 | 이 ClaimsTransformation을 호출한 후 생성되는 ClaimType입니다. 컬렉션의 첫 번째 항목입니다. |

다음 예제에서는 **otherMails** 클레임을 읽고 첫 번째 항목을 **email** 클레임에 반환합니다.

```xml
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
  - **컬렉션**: [" someone@outlook.com ", " someone@contoso.com "]
- 출력 클레임:
  - **extractedItem**: " someone@outlook.com "


## <a name="stringcollectioncontains"></a>StringCollectionContains

StringCollection 클레임 형식에 요소가 포함 되어 있는지 확인 합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | 검색할 클레임 유형입니다. |
|InputParameter|항목|문자열|검색할 값입니다.|
|InputParameter|ignoreCase|문자열|이 비교에서 비교할 문자열의 대/소문자를 무시해야 하는지 여부를 지정합니다.|
| OutputClaim | outputClaim | boolean | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. 컬렉션에 이러한 문자열이 포함 된 경우의 부울 표시기 |

다음 예에서는 `roles` stringCollection 클레임 형식에 **admin** 값이 포함 되어 있는지 여부를 확인 합니다.

```xml
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- 입력 클레임:
    - **Inputclaim**: ["reader", "author", "admin"]
- 입력 매개 변수:
    - **항목**: "Admin"
    - **ignoreCase**: "true"
- 출력 클레임:
    - **Outputclaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

StringCollection 클레임 형식에 클레임 값이 포함 되어 있는지 확인 합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 검색할 클레임 유형입니다. |
| InputClaim | 항목|문자열| 검색할 값을 포함 하는 클레임 형식입니다.|
|InputParameter|ignoreCase|문자열|이 비교에서 비교할 문자열의 대/소문자를 무시해야 하는지 여부를 지정합니다.|
| OutputClaim | outputClaim | boolean | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. 컬렉션에 이러한 문자열이 포함 된 경우의 부울 표시기 |

다음 예에서는 `roles` stringCollection 클레임 유형에 클레임 유형 값이 포함 되어 있는지 여부를 확인 합니다 `role` .

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- 입력 클레임:
    - **컬렉션**: ["reader", "author", "admin"]
    - **항목**: "Admin"
- 입력 매개 변수:
    - **ignoreCase**: "true"
- 출력 클레임:
    - **Outputclaim**: "true"
