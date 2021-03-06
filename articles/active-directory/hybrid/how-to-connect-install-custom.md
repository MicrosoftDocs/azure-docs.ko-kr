---
title: Azure Active Directory Connect 설치 사용자 지정
description: 이 문서에서는 Azure AD Connect에 대 한 사용자 지정 설치 옵션을 설명 합니다. Azure AD Connect를 통해 Active Directory를 설치하려면 다음 지침을 사용합니다.
services: active-directory
keywords: Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92096354"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Azure Active Directory Connect의 사용자 지정 설치
설치에 대 한 추가 옵션을 원하는 경우 Azure Active Directory (Azure AD) Connect에서 *사용자 지정 설정을* 사용 합니다. 포리스트가 여러 개인 경우 또는 선택적 기능을 구성 하려는 경우와 같이 이러한 설정을 사용 합니다. [빠른 설치가](how-to-connect-install-express.md) 배포 또는 토폴로지 요구를 충족 하지 않는 모든 경우에 사용자 지정 설정을 사용 합니다.

필수 조건:
- [Azure AD Connect 다운로드](https://go.microsoft.com/fwlink/?LinkId=615771).
- [Azure AD Connect: 하드웨어 및 필수 구성](how-to-connect-install-prerequisites.md)요소에서 필수 구성 요소 단계를 완료 합니다. 
- [Azure AD Connect 계정 및 사용 권한에](reference-connect-accounts-permissions.md)설명 된 계정이 있는지 확인 합니다.

## <a name="custom-installation-settings"></a>사용자 지정 설치 설정 

Azure AD Connect에 대 한 사용자 지정 설치를 설정 하려면 다음 섹션에서 설명 하는 마법사 페이지를 진행 합니다.

### <a name="express-settings"></a>Express 설정
**Express 설정** 페이지에서 **사용자 지정** 을 선택 하 여 사용자 지정 된 설정 설치를 시작 합니다.  이 문서의 나머지 부분에서는 사용자 지정 설치 프로세스를 안내 합니다. 특정 페이지에 대 한 정보를 신속 하 게 보려면 다음 링크를 사용 합니다.

- [필수 구성 요소](#install-required-components)
- [사용자 로그인](#user-sign-in)
- [Azure에 연결](#connect-to-azure-ad)
- [동기화](#sync-pages)

### <a name="install-required-components"></a>필요한 구성 요소 설치
동기화 서비스를 설치할 때 선택적 구성 섹션을 선택 하지 않은 상태로 둘 수 있습니다. Azure AD Connect는 모든 항목을 자동으로 설정 합니다. SQL Server 2012 Express LocalDB 인스턴스를 설정 하 고, 적절 한 그룹을 만들고, 사용 권한을 할당 합니다. 기본값을 변경 하려면 해당 확인란의 선택을 취소 합니다.  다음 표에서는 이러한 옵션을 요약 하 고 추가 정보에 대 한 링크를 제공 합니다. 

![Azure AD Connect에서 필수 설치 구성 요소에 대 한 선택적 선택을 보여 주는 스크린샷](./media/how-to-connect-install-custom/requiredcomponents2.png)

| 선택적 구성 | 설명 |
| --- | --- |
|사용자 지정 설치 위치 지정| Azure AD Connect에 대 한 기본 설치 경로를 변경할 수 있습니다.|
| 기존 SQL Server 사용 |SQL Server 이름 및 인스턴스 이름을 지정할 수 있습니다. 사용 하려는 데이터베이스 서버가 이미 있는 경우이 옵션을 선택 합니다. 인스턴스 **이름** 에 인스턴스 이름, 쉼표 및 포트 번호를 입력 합니다 (SQL Server 인스턴스에서 검색을 사용 하지 않는 경우).  그런 다음, Azure AD Connect 데이터베이스의 이름을 지정합니다.  SQL 권한에 따라 새 데이터베이스를 만들 수 있는지 또는 SQL 관리자가 미리 데이터베이스를 만들어야 하는지 여부가 결정 됩니다.  SQL Server 관리자 (SA) 권한이 있는 경우 [기존 데이터베이스를 사용 하 여 Azure AD Connect 설치](how-to-connect-install-existing-database.md)를 참조 하세요.  위임 된 권한 (DBO)을 사용 하는 경우 [SQL 위임 된 관리자 권한을 사용 하 여 Azure AD Connect 설치](how-to-connect-install-sql-delegation.md)를 참조 하세요. |
| 기존 서비스 계정 사용 |기본적으로 Azure AD Connect는 동기화 서비스에 대 한 가상 서비스 계정을 제공 합니다. SQL Server의 원격 인스턴스를 사용 하거나 인증이 필요한 프록시를 사용 하는 경우 도메인에서 *관리 서비스 계정* 또는 암호로 보호 된 서비스 계정을 사용할 수 있습니다. 이러한 경우 사용 하려는 계정을 입력 합니다. 설치를 실행 하려면 서비스 계정에 대 한 로그인 자격 증명을 만들 수 있도록 SQL의 SA가 필요 합니다. 자세한 내용은 [Azure AD Connect 계정 및 권한](reference-connect-accounts-permissions.md#adsync-service-account)을 참조하세요. </br></br>이제 SQL 관리자는 최신 빌드를 사용 하 여 대역 외에서 데이터베이스를 프로 비전 할 수 있습니다. 그런 다음 Azure AD Connect 관리자는 데이터베이스 소유자 권한으로 설치할 수 있습니다.  자세한 내용은 [SQL 위임된 관리자 권한을 사용하여 Azure AD Connect 설치](how-to-connect-install-sql-delegation.md)를 참조하세요.|
| 사용자 지정 동기화 그룹 지정 |기본적으로 동기화 서비스가 설치 될 때 Azure AD Connect는 서버에 로컬인 4 개의 그룹을 만듭니다. 이러한 그룹은 관리자, 운영자, 찾아보기 및 암호 재설정입니다. 여기서 사용자의 고유한 그룹을 지정할 수 있습니다. 그룹은 서버에서 로컬 이어야 합니다. 도메인에서 찾을 수 없습니다. |
|동기화 설정 가져오기 (미리 보기)|다른 버전의 Azure AD Connect에서 설정을 가져올 수 있습니다.  자세한 내용은 [Azure AD Connect 구성 설정 가져오기 및 내보내기](how-to-connect-import-export-config.md)를 참조 하세요.|

### <a name="user-sign-in"></a>사용자 로그인
필수 구성 요소를 설치한 후 사용자의 Single Sign-On 방법을 선택 합니다. 다음 표에서는 사용 가능한 옵션에 대해 간략하게 설명 합니다. 로그인 메서드에 대한 전체 설명은 [사용자 로그인](plan-connect-user-signin.md)을 참조하세요.

!["사용자 로그인" 페이지를 보여 주는 스크린샷 "암호 해시 동기화" 옵션이 선택 되어 있습니다.](./media/how-to-connect-install-custom/usersignin4.png)

| Single sign-on 옵션 | 설명 |
| --- | --- |
| 암호 해시 동기화 |사용자는 온-프레미스 네트워크에서 사용 하는 것과 동일한 암호를 사용 하 여 Microsoft 365 같은 Microsoft 클라우드 서비스에 로그인 할 수 있습니다. 사용자 암호는 암호 해시로 Azure AD에 동기화 됩니다. 클라우드에서 인증이 발생 합니다. 자세한 내용은 [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)를 참조 하세요. |
|통과 인증|사용자는 온-프레미스 네트워크에서 사용 하는 것과 동일한 암호를 사용 하 여 Microsoft 365 같은 Microsoft 클라우드 서비스에 로그인 할 수 있습니다.  온-프레미스 Active Directory 도메인 컨트롤러에 전달 하 여 사용자 암호의 유효성을 검사 합니다.
| AD FS로 페더레이션 |사용자는 온-프레미스 네트워크에서 사용 하는 것과 동일한 암호를 사용 하 여 Microsoft 365 같은 Microsoft 클라우드 서비스에 로그인 할 수 있습니다.  사용자는 로그인 하기 위해 온-프레미스 Azure AD FS (디렉터리 페더레이션 서비스) 인스턴스로 리디렉션됩니다. 인증은 온-프레미스에서 수행 됩니다. |
| PingFederate을 사용한 페더레이션|사용자는 온-프레미스 네트워크에서 사용 하는 것과 동일한 암호를 사용 하 여 Microsoft 365 같은 Microsoft 클라우드 서비스에 로그인 할 수 있습니다.  사용자는 로그인 하기 위해 온-프레미스 사용자 페더레이션 인스턴스로 리디렉션됩니다. 인증은 온-프레미스에서 수행 됩니다. |
| 구성하지 않음 |사용자 로그인 기능을 설치 하거나 구성 하지 않았습니다. 타사 페더레이션 서버 또는 다른 솔루션이 이미 있는 경우이 옵션을 선택 합니다. |
|Single Sign-On 사용|이 옵션은 암호 해시 동기화 및 통과 인증 모두에서 사용할 수 있습니다. 회사 네트워크의 데스크톱 사용자에 게 Single Sign-On 환경을 제공 합니다. 자세한 내용은 [Single sign-on](how-to-connect-sso.md)을 참조 하세요. </br></br>**참고:** AD FS 고객의 경우이 옵션을 사용할 수 없습니다. AD FS는 이미 같은 수준의 Single Sign-On를 제공 합니다.</br>

### <a name="connect-to-azure-ad"></a>Azure에 연결
**AZURE AD에 연결** 페이지에서 전역 관리자 계정 및 암호를 입력 합니다. 이전 페이지에서 **AD FS 페더레이션** 을 선택한 경우 페더레이션을 사용 하도록 설정 하려는 도메인의 계정으로 로그인 하지 마세요. 

Azure AD 테 넌 트와 함께 제공 되는 기본 *onmicrosoft.com* 도메인의 계정을 사용 하는 것이 좋습니다. 이 계정은 Azure AD에서 서비스 계정을 만드는 데만 사용 됩니다. 설치가 완료 된 후에는 사용 되지 않습니다.
  
!["Azure AD에 연결" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/connectaad.png)

전역 관리자 계정에 다단계 인증을 사용 하도록 설정한 경우 로그인 창에서 암호를 다시 제공 하 고 다단계 인증 챌린지를 완료 해야 합니다. 챌린지는 확인 코드 또는 전화 통화 일 수 있습니다.  

!["Azure AD에 연결" 페이지를 보여 주는 스크린샷 다단계 인증 필드는 사용자에 게 코드를 묻는 메시지를 표시 합니다.](./media/how-to-connect-install-custom/connectaadmfa.png)

전역 관리자 계정에 [권한 있는 id 관리](../privileged-identity-management/pim-getting-started.md) 를 사용 하도록 설정할 수도 있습니다.

오류가 표시 되거나 연결에 문제가 있는 경우 [연결 문제 해결](tshoot-connect-connectivity.md)을 참조 하세요.

## <a name="sync-pages"></a>페이지 동기화

다음 섹션에서는 **동기화** 섹션의 페이지에 대해 설명 합니다.

### <a name="connect-your-directories"></a>디렉터리에 연결
Active Directory Domain Services (Azure AD DS)에 연결 하려면 충분 한 권한이 있는 계정의 포리스트 이름과 자격 증명이 필요 Azure AD Connect.

!["디렉터리 연결" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/connectdir01.png)

포리스트 이름을 입력 하 고  **디렉터리 추가** 를 선택 하면 창이 표시 됩니다. 다음 표에서는 옵션에 대해 설명 합니다.

| 옵션 | Description |
| --- | --- |
| 새 계정 만들기 | 디렉터리 동기화 중에 Active Directory 포리스트에 연결 해야 하는 Azure AD Connect Azure AD DS 계정을 만듭니다. 이 옵션을 선택한 후 엔터프라이즈 관리자 계정의 사용자 이름 및 암호를 입력 합니다.  Azure AD Connect는 제공 된 엔터프라이즈 관리자 계정을 사용 하 여 필요한 Azure AD DS 계정을 만듭니다. NetBIOS 형식 또는 FQDN 형식으로 도메인 파트를 입력할 수 있습니다. 즉, *FABRIKAM\administrator* 또는 *FABRIKAM. com\administrator* 를 입력 합니다. |
| 기존 계정 사용 | 디렉터리 동기화 중에 Active Directory 포리스트에 연결 하는 데 사용할 수 Azure AD Connect 기존 Azure AD DS 계정을 제공 합니다. NetBIOS 형식 또는 FQDN 형식으로 도메인 파트를 입력할 수 있습니다. 즉, *FABRIKAM\syncuser* 또는 *FABRIKAM. com\syncuser* 를 입력 합니다. 이 계정은 기본 읽기 권한만 필요 하므로 일반 사용자 계정일 수 있습니다. 그러나 시나리오에 따라 더 많은 권한이 필요할 수 있습니다. 자세한 내용은 [Azure AD Connect 계정 및 권한](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account)을 참조하세요. |

![새 계정을 만들거나 기존 계정을 사용 하도록 선택할 수 있는 "연결 디렉터리" 페이지와 A D 포리스트 계정 창을 보여 주는 스크린샷](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> 빌드 1.4.18.0에서 엔터프라이즈 관리자 또는 도메인 관리자 계정을 Azure AD DS 커넥터 계정으로 사용할 수 없습니다. **기존 계정 사용** 을 선택 하는 경우 엔터프라이즈 관리자 계정 또는 도메인 관리자 계정을 입력 하려고 하면 다음과 같은 오류가 표시 됩니다. "AD 포리스트 계정에 엔터프라이즈 또는 도메인 관리자 계정을 사용 하는 것은 허용 되지 않습니다. 계정을 만들거나 올바른 권한이 있는 동기화 계정을 지정 Azure AD Connect 합니다. "
>

### <a name="azure-ad-sign-in-configuration"></a>Azure AD 로그인 구성
**AZURE AD 로그인 구성** 페이지에서 온-프레미스 Azure AD DS의 UPN (사용자 계정 이름) 도메인을 검토 합니다. 이러한 UPN 도메인은 Azure AD에서 확인 되었습니다. 이 페이지에서 userPrincipalName에 사용할 특성을 구성 합니다.

!["Azure A D 로그인 구성" 페이지에서 확인 되지 않은 도메인을 보여 주는 스크린샷](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

**추가 안 함** 또는 **확인 되지** 않음으로 표시 된 모든 도메인을 검토 합니다. Azure AD에서 사용 하는 도메인이 확인 되었는지 확인 합니다. 도메인을 확인 한 후에는 순환 새로 고침 아이콘을 선택 합니다. 자세한 내용은 [도메인 추가 및 확인](../fundamentals/add-custom-domain.md)을 참조 하세요.

사용자가 Azure AD에 로그인 하 고 Microsoft 365 때 *userPrincipalName* 특성을 사용 합니다. 사용자가 동기화 되기 전에 Azure AD에서 UPN-접미사가 라고도 하는 도메인을 확인 해야 합니다. 기본 특성 userPrincipalName을 유지 하는 것이 좋습니다. 

UserPrincipalName 특성을 라우팅할 수 없고 확인할 수 없는 경우에는 다른 특성을 선택할 수 있습니다. 예를 들어 로그인 ID를 보유 하는 특성으로 전자 메일을 선택할 수 있습니다. UserPrincipalName 이외의 특성을 사용 하는 경우이를 *대체 ID* 라고 합니다. 

대체 ID 특성 값은 RFC 822 표준을 따라야 합니다. 암호 해시 동기화, 통과 인증 및 페더레이션이 있는 대체 ID를 사용할 수 있습니다. Active Directory에는 단일 값만 있는 경우에도 특성을 다중값으로 정의할 수 없습니다. 대체 ID에 대 한 자세한 내용은 [통과 인증: 질문과 대답](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname)을 참조 하세요.

>[!NOTE]
> 통과 인증을 사용 하도록 설정 하는 경우 사용자 지정 설치 프로세스를 계속 진행 하려면 하나 이상의 확인 된 도메인이 있어야 합니다.

> [!WARNING]
> 대체 Id는 모든 Microsoft 365 워크 로드와 호환 되지 않습니다. 자세한 내용은 [대체 로그인 Id 구성](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)을 참조 하세요.
>

### <a name="domain-and-ou-filtering"></a>도메인 및 OU 필터링
기본적으로 모든 도메인 및 Ou (조직 구성 단위)가 동기화 됩니다. 일부 도메인 또는 Ou를 Azure AD에 동기화 하지 않으려는 경우 적절 한 선택 항목을 지울 수 있습니다.  

![도메인 및 O U 필터링 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/domainoufiltering.png)  

이 페이지에서는 도메인 기반 및 OU 기반 필터링을 구성 합니다. 변경 하려는 경우 [도메인 기반 필터링](how-to-connect-sync-configure-filtering.md#domain-based-filtering) 및 [OU 기반 필터링](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)을 참조 하세요. 일부 Ou는 기능에 필수적 이므로 선택 된 상태로 두어야 합니다.

1.1.524.0 이상 보다 오래 된 Azure AD Connect 버전의 OU 기반 필터링을 사용 하는 경우 새 Ou는 기본적으로 동기화 됩니다. 새 Ou를 동기화 하지 않으려면 [ou 기반 필터링](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) 단계 후에 기본 동작을 조정할 수 있습니다. Azure AD Connect 1.1.524.0 이상 이상에서는 새 Ou를 동기화할지 여부를 지정할 수 있습니다.

[그룹 기반 필터링](#sync-filtering-based-on-groups)을 사용 하려면 그룹이 포함 된 ou가 포함 되어 있고 ou 필터링을 사용 하 여 필터링 되지 않았는지 확인 합니다. OU 필터링은 그룹 기반 필터링이 평가 되기 전에 평가 됩니다.

방화벽 제한으로 인해 일부 도메인에 연결할 수 없는 경우도 있습니다. 이러한 도메인은 기본적으로 선택 되지 않으며 경고를 표시 합니다.  

![연결할 수 없는 도메인을 보여 주는 스크린샷](./media/how-to-connect-install-custom/unreachable.png)  

이 경고가 표시 되는 경우 이러한 도메인에 실제로 연결할 수 없고 경고가 예상 되는지 확인 합니다.

### <a name="uniquely-identifying-your-users"></a>사용자를 고유하게 식별

**사용자** 식별 페이지에서 온-프레미스 디렉터리의 사용자를 식별 하는 방법 및 sourceanchor 특성을 사용 하 여 해당 디렉터리를 식별 하는 방법을 선택 합니다.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>온-프레미스 디렉터리에서 사용자를 식별하는 방법 선택
*포리스트 간 일치* 기능을 사용 하 여 azure AD DS 포리스트의 사용자가 azure AD에 표시 되는 방식을 정의할 수 있습니다. 사용자는 모든 포리스트에서 한 번만 표시 되거나 사용 및 사용 안 함 계정 조합이 있을 수 있습니다. 사용자가 일부 포리스트 내에서 연락처로 표시될 수 있습니다.

![사용자를 고유 하 게 식별할 수 있는 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/unique2.png)

| 설정 | 설명 |
| --- | --- |
| [사용자는 모든 포리스트에서 한 번만 표시 됩니다.](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |모든 사용자가 Azure AD에 개별 개체로 만들어집니다. 개체는 메타 버스에서 조인 되지 않습니다. |
| [Mail 특성](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |메일 특성에 다른 포리스트의 동일한 값이 있는 경우 이 옵션은 사용자 및 연락처를 연결합니다. GALSync를 사용 하 여 연락처를 만든 경우이 옵션을 사용 합니다. 이 옵션을 선택 하면 메일 특성이 채워지지 않은 인 사용자 개체가 Azure AD에 동기화 되지 않습니다. |
| [ObjectSID 및 msExchangeMasterAccountSID/Msrtcsip-originatorsid 특성](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |이 옵션은 계정 포리스트에서 활성화된 사용자를 리소스 포리스트에서 비활성화된 사용자와 조인합니다. Exchange의 경우 이 구성을 연결된 된 사서함이라고 합니다. Lync만 사용 하 고 Exchange가 리소스 포리스트에 없는 경우이 옵션을 사용할 수 있습니다. |
| SAMAccountName 및 MailNickName 특성 |이 옵션은 사용자의 로그인 ID를 찾을 것으로 예상 되는 특성에 대해 조인 합니다. |
| 특정 특성 선택 |이 옵션을 사용하면 고유한 특성을 선택할 수 있습니다. 이 옵션을 선택 하는 경우 (selected) 특성이 채워지지 않은 인 사용자 개체는 Azure AD에 동기화 되지 않습니다. **제한 사항:** 메타 버스에 이미 있는 특성만이 옵션에 사용할 수 있습니다. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>원본 앵커를 사용 하 여 사용자를 식별 하는 방법 선택
*Sourceanchor* 특성은 사용자 개체의 수명 동안 변경할 수 없습니다. Azure AD의 사용자와 온-프레미스 사용자를 연결 하는 기본 키입니다.

| 설정 | 설명 |
| --- | --- |
| Azure에서 원본 앵커를 관리 하도록 허용 | Azure AD에서 이 특성을 선택하게 하려면 이 옵션을 선택합니다. 이 옵션을 선택 하는 경우 Azure AD Connect는 sourceanchor [로 Msds-consistencyguid 사용](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)에 설명 된 sourceanchor 특성 선택 논리를 적용 합니다. 사용자 지정 설치가 완료 되 면 sourceAnchor 특성으로 선택 된 특성이 표시 됩니다. |
| 특정 특성 선택 | 기존 AD 특성을 sourceAnchor 특성으로 지정 하려면이 옵션을 선택 합니다. |

SourceAnchor 특성을 변경할 수 없기 때문에 적절 한 특성을 선택 해야 합니다. 좋은 후보는 objectGUID입니다. 이 특성은 포리스트 또는 도메인 간에 사용자 계정을 이동 하지 않으면 변경 되지 않습니다. 사용자가 할당을 결혼 하거나 변경할 때 변경 될 수 있는 특성을 선택 하지 마세요. 

At 기호 (@)를 포함 하는 특성은 사용할 수 없으므로 email 및 userPrincipalName를 사용할 수 없습니다. 특성은 대/소문자를 구분 하므로 포리스트 간에 개체를 이동할 때 대 문자와 소문자를 유지 해야 합니다. 이진 특성은 b a s e 64로 인코딩되고 다른 특성 형식은 인코딩되지 않은 상태로 유지 됩니다. 

페더레이션 시나리오와 일부 Azure AD 인터페이스에서 sourceAnchor 특성을 *immutableID* 라고도 합니다. 

원본 앵커에 대 한 자세한 내용은 [디자인 개념](plan-connect-design-concepts.md#sourceanchor)을 참조 하세요.

### <a name="sync-filtering-based-on-groups"></a>그룹에 따라 동기화 필터링
그룹에 대 한 필터링 기능을 사용 하면 파일럿의 일부 개체만 동기화 할 수 있습니다. 이 기능을 사용 하려면 Active Directory의 온-프레미스 인스턴스에서이 목적에 대 한 그룹을 만듭니다. 그런 다음 Azure AD에 직접 구성원으로 동기화해야 하는 사용자와 그룹을 추가합니다. 나중에이 그룹에서 사용자를 추가 하거나 제거 하 여 Azure AD에 표시 되어야 하는 개체 목록을 유지 관리할 수 있습니다. 

동기화 하려는 모든 개체는 그룹의 직접 구성원 이어야 합니다. 사용자, 그룹, 연락처, 컴퓨터 또는 장치는 모두 직접 구성원 이어야 합니다. 중첩 된 그룹 멤버 자격이 확인 되지 않았습니다. 그룹을 멤버로 추가 하면 그룹 자체만 추가 됩니다. 해당 멤버는 추가 되지 않습니다.

![사용자 및 장치를 필터링 하는 방법을 선택할 수 있는 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> 이 기능은 파일럿 배포만 지원 하기 위한 것입니다. 전체 프로덕션 배포에서는 사용 하지 마세요.
>

전체 프로덕션 배포에서는 단일 그룹과 동기화 할 모든 개체를 유지 관리 하기가 어렵습니다. 그룹 필터링 기능 대신 [필터링 구성](how-to-connect-sync-configure-filtering.md)에 설명 된 방법 중 하나를 사용 합니다.

### <a name="optional-features"></a>선택적 기능
다음 페이지에서 시나리오에 대 한 선택적 기능을 선택할 수 있습니다.

>[!WARNING]
>Azure AD Connect 버전 1.0.8641.0 이전 버전은 Azure Access Control Service를 사용 하 여 비밀 번호 쓰기 저장을 사용 합니다.  이 서비스는 2018 년 11 월 7 일에 사용이 중지 되었습니다.  이러한 버전의 Azure AD Connect을 사용 하 고 비밀 번호 쓰기 저장을 사용 하도록 설정 하는 경우 서비스를 사용 중지 하는 경우 사용자가 암호를 변경 하거나 다시 설정 하는 기능이 손실 될 수 있습니다. 이러한 버전의 Azure AD Connect는 비밀 번호 쓰기 저장을 지원 하지 않습니다.
>
>자세한 내용은 [Azure Access Control Service에서 마이그레이션](../azuread-dev/active-directory-acs-migration.md)을 참조 하세요.
>
>비밀 번호 쓰기 저장을 사용 하려면 [최신 버전의 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)을 다운로드 합니다.

!["선택적 기능" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Azure AD Sync 또는 직접 동기화 (DirSync)가 활성화 된 경우 Azure AD Connect에서 쓰기 저장 기능을 활성화 하지 않습니다.



| 선택적 기능 | 설명 |
| --- | --- |
| Exchange 하이브리드 배포 |Exchange 하이브리드 배포 기능을 사용 하면 온-프레미스와 Microsoft 365 모두 Exchange 사서함을 함께 사용할 수 있습니다. Azure AD Connect Azure AD의 특정 [특성](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) 집합을 온-프레미스 디렉터리에 다시 동기화 합니다. |
| Exchange 메일 공용 폴더 | Exchange 메일 공용 폴더 기능을 사용 하면 Active Directory 온-프레미스 인스턴스에서 Azure AD로 메일 사용이 가능한 공용 폴더 개체를 동기화 할 수 있습니다. |
| Azure AD 앱 및 특성 필터링 |Azure AD 앱 및 특성 필터링을 사용 하도록 설정 하면 동기화 된 특성 집합을 조정할 수 있습니다. 이 옵션은 마법사에 구성 페이지를 두 개 더 추가합니다. 자세한 내용은 [Azure AD 앱 및 특성 필터링](#azure-ad-app-and-attribute-filtering)을 참조하세요. |
| 암호 해시 동기화 |로그인 솔루션으로 페더레이션을 선택한 경우 암호 해시 동기화를 사용 하도록 설정할 수 있습니다. 그런 다음 백업 옵션으로 사용할 수 있습니다.  </br></br>통과 인증을 선택한 경우이 옵션을 사용 하도록 설정 하 여 레거시 클라이언트에 대 한 지원을 보장 하 고 백업을 제공할 수 있습니다.</br></br> 자세한 내용은 [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)를 참조 하세요.|
| 비밀번호 쓰기 저장 |이 옵션을 사용 하 여 Azure AD에서 발생 하는 암호 변경 내용이 온-프레미스 디렉터리에 다시 기록 되도록 합니다. 자세한 내용은 [암호 관리 시작](../authentication/tutorial-enable-sspr.md)을 참조하세요. |
| 그룹 쓰기 저장 |Microsoft 365 그룹을 사용 하는 경우 Active Directory의 온-프레미스 인스턴스에서 그룹을 나타낼 수 있습니다. 이 옵션은 Active Directory의 온-프레미스 인스턴스에 Exchange가 있는 경우에만 사용할 수 있습니다. 자세한 내용은 [Azure AD Connect 그룹 쓰기 저장](how-to-connect-group-writeback.md)을 참조 하세요.|
| 디바이스 쓰기 저장 |조건부 액세스 시나리오의 경우이 옵션을 사용 하 여 Azure AD의 장치 개체를 Active Directory의 온-프레미스 인스턴스에 다시 씁니다. 자세한 내용은 [Azure AD Connect에서 디바이스 쓰기 저장 사용](how-to-connect-device-writeback.md)을 참조하세요. |
| 디렉터리 확장 특성 동기화 |지정 된 특성을 Azure AD에 동기화 하려면이 옵션을 선택 합니다. 자세한 내용은 [디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md)을 참조하세요. |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD 앱 및 특성 필터링
Azure AD와 동기화 되는 특성을 제한 하려는 경우 사용 하는 서비스를 선택 하 여 시작 합니다. 이 페이지에서 선택 항목을 변경 하는 경우 설치 마법사를 다시 실행 하 여 새 서비스를 명시적으로 선택 해야 합니다.

![선택적인 Azure A D 앱 기능을 보여 주는 스크린샷](./media/how-to-connect-install-custom/azureadapps2.png)

이전 단계에서 선택한 서비스에 따라이 페이지에는 동기화 되는 모든 특성이 표시 됩니다. 이 목록은 동기화 중인 모든 개체 유형의 조합입니다. 일부 특성이 동기화 되지 않은 상태로 유지 되어야 하는 경우 해당 특성에서 선택을 지울 수 있습니다.

![선택적 Azure A D 특성 기능을 보여 주는 스크린샷](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> 특성을 제거 하면 기능에 영향을 줄 수 있습니다. 모범 사례 및 권장 사항은 [동기화 할 특성](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize)을 참조 하세요.
>

### <a name="directory-extension-attribute-sync"></a>디렉터리 확장 특성 동기화
조직에서 추가 된 사용자 지정 특성을 사용 하거나 Active Directory의 다른 특성을 사용 하 여 Azure AD에서 스키마를 확장할 수 있습니다. 이 기능을 사용 하려면 **선택적 기능** 페이지에서 **디렉터리 확장 특성 동기화** 를 선택 합니다. **디렉터리 확장** 페이지에서 더 많은 특성을 선택 하 여 동기화 할 수 있습니다.

>[!NOTE]
>**사용 가능한 특성** 필드는 대/소문자를 구분 합니다.

!["디렉터리 확장" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/extension2.png)

자세한 내용은 [디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md)을 참조하세요.

### <a name="enabling-single-sign-on"></a>Single Sign-On 사용
**Single sign-on** 페이지에서 암호 동기화 또는 통과 인증에 사용할 Single Sign-On를 구성 합니다. Azure AD에 동기화 되는 각 포리스트에 대해 한 번씩이 단계를 수행 합니다. 구성에는 두 단계가 포함 됩니다.

1.  Active Directory의 온-프레미스 인스턴스에서 필요한 컴퓨터 계정을 만듭니다.
2.  Single Sign-On를 지원 하도록 클라이언트 컴퓨터의 인트라넷 영역을 구성 합니다.

#### <a name="create-the-computer-account-in-active-directory"></a>Active Directory에서 컴퓨터 계정 만들기
Azure AD Connect에 추가 된 각 포리스트에 대해 도메인 관리자 자격 증명을 제공 하 여 각 포리스트에 컴퓨터 계정을 만들 수 있도록 해야 합니다. 자격 증명은 계정을 만드는 데만 사용 됩니다. 이러한 작업은 다른 작업에 저장 되거나 사용 되지 않습니다. 다음 그림에 나와 있는 것 처럼 **Single Sign-On 사용** 페이지에서 자격 증명을 추가 합니다.

!["Single Sign-On 사용" 페이지를 보여 주는 스크린샷 포리스트 자격 증명이 추가 됩니다.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Single Sign-On를 사용 하지 않으려는 포리스트를 건너뛸 수 있습니다.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>클라이언트 컴퓨터에 대 한 인트라넷 영역 구성
클라이언트가 인트라넷 영역에서 자동으로 로그인 하도록 하려면 URL이 인트라넷 영역에 포함 되어 있는지 확인 합니다. 이 단계를 수행 하면 도메인에 가입 된 컴퓨터가 회사 네트워크에 연결 될 때 자동으로 Kerberos 티켓을 Azure AD에 보냅니다.

그룹 정책 관리 도구가 있는 컴퓨터에서 다음을 수행 합니다.

1.  그룹 정책 관리 도구를 엽니다.
2.  모든 사용자에 게 적용 되는 그룹 정책을 편집 합니다. 예를 들어 기본 도메인 정책입니다.
3.  **사용자 구성**  >  **관리 템플릿**  >  **Windows 구성 요소**  >  **internet Explorer**  >  **인터넷 제어판**  >  **보안 페이지** 로 이동 합니다. 그런 다음 **영역에 사이트 할당 목록** 을 선택합니다.
4.  정책을 사용 하도록 설정 합니다. 그런 다음 대화 상자에서 값의 `https://autologon.microsoftazuread-sso.com` 및 값을 입력 `1` 합니다. 설정은 다음 이미지와 같습니다.
  
    ![인트라넷 영역을 보여 주는 스크린샷](./media/how-to-connect-install-custom/sitezone.png)

6.  **확인을** 두 번 선택 합니다.

## <a name="configuring-federation-with-ad-fs"></a>AD FS로 페더레이션 구성
몇 번의 클릭 만으로 Azure AD Connect를 사용 하 여 AD FS를 구성할 수 있습니다. 시작 하기 전에 다음이 필요 합니다.

* 페더레이션 서버에 대 한 Windows Server 2012 R2 이상 원격 관리를 사용 하도록 설정 해야 합니다.
* 웹 응용 프로그램 프록시 서버에 대 한 Windows Server 2012 R2 이상 원격 관리를 사용 하도록 설정 해야 합니다.
* 사용 하려는 페더레이션 서비스 이름에 대 한 TLS/SSL 인증서 (예: sts.contoso.com)입니다.

>[!NOTE]
>페더레이션 트러스트를 관리 하는 데 사용 하지 않는 경우에도 Azure AD Connect를 사용 하 여 AD FS 팜에 대 한 TLS/SSL 인증서를 업데이트할 수 있습니다.

### <a name="ad-fs-configuration-prerequisites"></a>구성 필수 구성 요소 AD FS
Azure AD Connect를 사용 하 여 AD FS 팜을 구성 하려면 원격 서버에서 WinRM이 사용 하도록 설정 되어 있는지 확인 합니다. [페더레이션 전제 조건](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration)에서 다른 작업을 완료 했는지 확인 합니다. 또한 [Azure AD Connect 및 페더레이션/WAP 서버](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) 표에 나열 된 포트 요구 사항을 준수 해야 합니다.

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>새 AD FS 팜을 만들거나 기존 AD FS 팜 사용
기존 AD FS 팜을 사용 하거나 새 팜을 만들 수 있습니다. 새 항목을 만들도록 선택 하는 경우 TLS/SSL 인증서를 제공 해야 합니다. TLS/SSL 인증서가 암호로 보호 되는 경우 암호를 입력 하 라는 메시지가 표시 됩니다.

!["A D F S 팜" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/adfs1.png)

기존 AD FS 팜을 사용 하도록 선택 하는 경우 AD FS와 Azure AD 간의 트러스트 관계를 구성할 수 있는 페이지가 표시 됩니다.

>[!NOTE]
>Azure AD Connect를 사용 하 여 AD FS 팜을 하나만 관리할 수 있습니다. 선택한 AD FS 팜에서 Azure AD가 구성 된 기존 페더레이션 트러스트가 있는 경우 Azure AD Connect에서 트러스트를 처음부터 다시 만듭니다.

### <a name="specify-the-ad-fs-servers"></a>AD FS 서버 지정
AD FS를 설치 하려는 서버를 지정 합니다. 용량 요구 사항에 따라 하나 이상의 서버를 추가할 수 있습니다. 이 구성을 설정 하기 전에 모든 AD FS 서버를 Active Directory에 조인 합니다. 웹 응용 프로그램 프록시 서버에는이 단계가 필요 하지 않습니다. 

Microsoft에서는 테스트 및 파일럿 배포를 위해 단일 AD FS 서버를 설치하는 것이 좋습니다. 초기 구성 후에는 Azure AD Connect를 다시 실행 하 여 크기 조정 요구를 충족 하기 위해 더 많은 서버를 추가 하 고 배포할 수 있습니다.

> [!NOTE]
> 이 구성을 설정 하기 전에 모든 서버가 Azure AD 도메인에 가입 되어 있는지 확인 합니다.
>


!["페더레이션 서버" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>웹 애플리케이션 프록시 서버 지정
웹 응용 프로그램 프록시 서버를 지정 합니다. 웹 응용 프로그램 프록시 서버는 엑스트라넷을 향하는 경계 네트워크에 배포 됩니다. 엑스트라넷의 인증 요청을 지원 합니다. 용량 요구 사항에 따라 하나 이상의 서버를 추가할 수 있습니다. 

테스트 및 파일럿 배포를 위해 단일 웹 응용 프로그램 프록시 서버를 설치 하는 것이 좋습니다. 초기 구성 후에는 Azure AD Connect를 다시 실행 하 여 크기 조정 요구를 충족 하기 위해 더 많은 서버를 추가 하 고 배포할 수 있습니다. 인트라넷에서 인증을 충족 하려면 동일한 수의 프록시 서버를 갖는 것이 좋습니다.

> [!NOTE]
> - 사용 하는 계정이 웹 응용 프로그램 프록시 서버에서 로컬 관리자가 아닌 경우 관리자 자격 증명을 입력 하 라는 메시지가 표시 됩니다.
> - 웹 응용 프로그램 프록시 서버를 지정 하기 전에 Azure AD Connect 서버와 웹 응용 프로그램 프록시 서버 간에 HTTP/HTTPS 연결이 있는지 확인 합니다.
> - 인증 요청이 통과할 수 있도록 웹 응용 프로그램 서버와 AD FS 서버 간에 HTTP/HTTPS 연결이 있는지 확인 합니다.
>


![웹 응용 프로그램 프록시 서버 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/adfs3.png)

웹 응용 프로그램 서버가 AD FS 서버에 대 한 보안 연결을 설정할 수 있도록 자격 증명을 입력 하 라는 메시지가 표시 됩니다. 이러한 자격 증명은 AD FS 서버의 로컬 관리자 계정에 대 한 자격 증명 이어야 합니다.

!["자격 증명" 페이지를 보여 주는 스크린샷 관리자 자격 증명은 사용자 이름 필드 및 암호 필드에 입력 됩니다.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>AD FS 서비스에 대한 서비스 계정 지정
AD FS 서비스에는 사용자를 인증 하 고 Active Directory에서 사용자 정보를 조회 하는 도메인 서비스 계정이 필요 합니다. 두 종류의 서비스 계정을 지원할 수 있습니다.

* **그룹 관리 서비스 계정**:이 계정 유형은 Windows Server 2012에 의해 AD DS에 도입 되었습니다. 이 계정 유형은 AD FS와 같은 서비스를 제공 합니다. 정기적으로 암호를 업데이트할 필요가 없는 단일 계정입니다. AD FS 서버가 속해 있는 도메인에 Windows Server 2012 도메인 컨트롤러가 이미 있는 경우 이 옵션을 사용합니다.
* **도메인 사용자 계정**:이 유형의 계정은 암호를 제공 하 고 만료 될 때 정기적으로 업데이트 해야 합니다. AD FS 서버가 속해 있는 도메인에 Windows Server 2012 도메인 컨트롤러가 없는 경우에만이 옵션을 사용 합니다.

**그룹 관리 서비스 계정 만들기** 를 선택 하 고이 기능을 Active Directory에서 사용한 적이 없는 경우 엔터프라이즈 관리자 자격 증명을 입력 합니다. 이러한 자격 증명은 키 저장소를 시작하고 Active Directory에서 기능을 사용하도록 설정하는 데 사용됩니다.

> [!NOTE]
> Azure AD Connect는 AD FS 서비스가 도메인에 SPN (서비스 사용자 이름)으로 이미 등록 되어 있는지 여부를 확인 합니다.  Azure AD DS은 중복 Spn을 동시에 등록할 수 없습니다.  중복 된 SPN이 있는 경우 SPN이 제거 될 때까지 계속 진행할 수 없습니다.

!["A D F S 서비스 계정" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>페더레이션 하려는 Azure AD 도메인을 선택 합니다.
**AZURE Ad 도메인** 페이지를 사용 하 여 AD FS와 azure ad 간의 페더레이션 관계를 설정 합니다. 여기에서 Azure AD에 보안 토큰을 제공 하도록 AD FS를 구성 합니다. 또한이 AD FS 인스턴스의 토큰을 신뢰 하도록 Azure AD를 구성 합니다. 

이 페이지에서는 초기 설치에서 단일 도메인만 구성할 수 있습니다. 나중에 Azure AD Connect를 다시 실행하여 더 많은 도메인을 구성할 수 있습니다.

!["Azure A D 도메인" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>페더레이션에 선택한 Azure AD 도메인 확인
페더레이션 하려는 도메인을 선택 하는 경우 Azure AD Connect 확인 되지 않은 도메인을 확인 하는 데 사용할 수 있는 정보를 제공 합니다. 자세한 내용은 [도메인 추가 및 확인](../fundamentals/add-custom-domain.md)을 참조 하세요.

![도메인을 확인 하는 데 사용할 수 있는 정보를 포함 하 여 "Azure A D 도메인" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect 구성 단계에서 도메인을 확인 하려고 시도 합니다. 필요한 DNS (Domain Name System) 레코드를 추가 하지 않으면 구성을 완료할 수 없습니다.
>


## <a name="configuring-federation-with-pingfederate"></a>PingFederate로 페더레이션 구성
몇 번의 클릭 만으로 Azure AD Connect를 사용 하 여를 구성할 수 있습니다. 필요한 필수 구성 요소는 다음과 같습니다.
- 8.4 이상을 페더레이션 합니다.  자세한 내용은 [Azure Active Directory 및 Microsoft 365와의 통합](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html)을 참조 하세요.
- 사용 하려는 페더레이션 서비스 이름에 대 한 TLS/SSL 인증서 (예: sts.contoso.com)입니다.

### <a name="verify-the-domain"></a>도메인 확인
을 (를) 사용 하 여 페더레이션을 설정 하도록 선택한 후 페더레이션 하려는 도메인을 확인 하 라는 메시지가 표시 됩니다.  드롭다운 메뉴에서 도메인을 선택 합니다.

!["Azure A D 도메인" 페이지를 보여 주는 스크린샷 예 도메인 "contoso.com"을 선택 합니다.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>PingFederate 설정 내보내기


각 페더레이션된 Azure 도메인의 페더레이션 서버로 연결 페더레이션을 구성 합니다.  **설정 내보내기** 를 선택 하 여이 정보를 해당 관리자와 공유 합니다.  페더레이션 서버 관리자는 구성을 업데이트 한 다음 Azure AD Connect 메타 데이터 설정을 확인할 수 있도록 서버 URL 및 포트 번호를 제공 합니다.  

!["페더레이션 설정" 페이지를 보여 주는 스크린샷 페이지 위쪽 근처에 "설정 내보내기" 단추가 표시 됩니다.](./media/how-to-connect-install-custom/ping2.png)

유효성 검사 문제를 해결하려면 PingFederate 관리자에게 문의하세요.  다음 이미지는 Azure와의 유효한 트러스트 관계가 없는 Azure 서버에 대 한 정보를 보여 줍니다.

![서버 정보를 보여 주는 스크린샷: 연결 페더레이션 서버를 찾았지만 Azure에 대 한 서비스 공급자 연결이 없거나 사용 하지 않도록 설정 되었습니다.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>페더레이션 연결 확인
Azure AD Connect는 이전 단계에서 해당 메타 데이터 원본에서 검색 한 인증 끝점의 유효성을 검사 하려고 합니다.  Azure AD Connect 먼저 로컬 DNS 서버를 사용 하 여 끝점을 확인 하려고 시도 합니다.  그런 다음 외부 DNS 공급자를 사용 하 여 끝점을 확인 하려고 시도 합니다.  유효성 검사 문제를 해결하려면 PingFederate 관리자에게 문의하세요.  

!["연결 확인" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>페더레이션 로그인 확인
마지막으로, 페더레이션된 도메인에 로그인하여 새로 구성된 페더레이션된 로그인 흐름을 확인할 수 있습니다. 로그인에 성공 하면를 사용 하 여 페더레이션이 성공적으로 구성 된 것입니다.

!["페더레이션된 로그인 확인" 페이지를 보여 주는 스크린샷 맨 아래에 있는 메시지는 성공적인 로그인을 나타냅니다.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>페이지 구성 및 확인
구성은 **구성** 페이지에서 발생 합니다.

> [!NOTE]
> 페더레이션을 구성한 경우 설치를 계속 하기 전에 [페더레이션 서버에 대 한 이름 확인](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) 도 구성 했는지 확인 합니다.
>



!["구성 준비 완료" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>준비 모드 사용
스테이징 모드와 병렬로 새 동기화 서버를 설정할 수 있습니다. 이 설치 프로그램을 사용 하려는 경우 하나의 동기화 서버만 클라우드의 한 디렉터리에 내보낼 수 있습니다. 그러나 DirSync를 실행 하는 서버와 같이 다른 서버에서 이동 하려면 준비 모드에서 Azure AD Connect를 사용 하도록 설정 하면 됩니다. 

스테이징 설정을 사용 하도록 설정 하면 동기화 엔진이 데이터를 정상적으로 가져오고 동기화 합니다. 그러나 Azure AD 또는 Active Directory로 데이터를 내보냅니다. 준비 모드에서는 암호 동기화 기능 및 비밀 번호 쓰기 저장 기능을 사용할 수 없습니다.

!["준비 모드 사용" 옵션을 보여 주는 스크린샷](./media/how-to-connect-install-custom/stagingmode.png)

준비 모드에서는 동기화 엔진에 필요한 변경을 수행 하 고 내보낼 항목을 검토할 수 있습니다. 구성마음에 드는 경우, 설치 마법사를 다시 실행하고 스테이징 모드를 사용하지 않도록 설정합니다. 

이제 데이터를 서버에서 Azure AD로 내보냅니다. 한 서버만이 내보낼 수 있으므로 동시에 다른 서버를 사용하지 않도록 설정했는지 확인합니다.

자세한 내용은 [준비 모드](how-to-connect-sync-staging-server.md)를 참조하세요.

### <a name="verify-your-federation-configuration"></a>페더레이션 구성 확인
**확인** 단추를 선택 하면 DNS 설정을 확인할 Azure AD Connect. 다음 설정을 확인 합니다.

* **인트라넷 연결**
    * 페더레이션 FQDN 확인: Azure AD Connect DNS가 연결을 보장 하기 위해 페더레이션 FQDN을 확인할 수 있는지 여부를 확인 합니다. Azure AD Connect FQDN을 확인할 수 없는 경우 확인이 실패 합니다. 확인을 완료 하려면 페더레이션 서비스 FQDN에 대 한 DNS 레코드가 있는지 확인 합니다.
    * DNS A 레코드: Azure AD Connect 페더레이션 서비스에 A 레코드가 있는지 여부를 확인 합니다. A 레코드가 없는 경우 확인이 실패 합니다. 확인을 완료 하려면 페더레이션 FQDN에 대 한 A 레코드 (CNAME 레코드 아님)를 만듭니다.
* **엑스트라넷 연결**
    * 페더레이션 FQDN 확인: Azure AD Connect DNS가 연결을 보장 하기 위해 페더레이션 FQDN을 확인할 수 있는지 여부를 확인 합니다.

      !["설치 완료" 페이지를 보여 주는 스크린샷](./media/how-to-connect-install-custom/completed.png)

      !["설치 완료" 페이지를 보여 주는 스크린샷 인트라넷 구성이 확인 되었음을 나타내는 메시지가 표시 됩니다.](./media/how-to-connect-install-custom/adfs7.png)

종단 간 인증의 유효성을 검사 하려면 다음 테스트 중 하나 이상을 수동으로 수행 합니다.

* 동기화가 완료 되 면 Azure AD Connect에서 **페더레이션 로그인** 추가 작업 확인을 사용 하 여 선택한 온-프레미스 사용자 계정에 대 한 인증을 확인 합니다.
* 인트라넷의 도메인에 가입 된 컴퓨터에서 브라우저를 통해 로그인 할 수 있는지 확인 합니다. https://myapps.microsoft.com에 연결합니다. 그런 다음 로그온 한 계정을 사용 하 여 로그인을 확인 합니다. 기본 제공 Azure AD DS administrator 계정은 동기화 되지 않으며 확인에 사용할 수 없습니다.
* 엑스트라넷의 장치에서 로그인 할 수 있는지 확인 합니다. 홈 컴퓨터 또는 모바일 장치에서에 연결 https://myapps.microsoft.com 합니다. 그런 다음 자격 증명을 제공 합니다.
* 리치 클라이언트 로그인 유효성을 검사합니다. https://testconnectivity.microsoft.com에 연결합니다. 그런 다음 **office 365**  >  **office 365 Single Sign-On Test** 를 선택 합니다.

## <a name="troubleshoot"></a>문제 해결
이 섹션에는 Azure AD Connect를 설치 하는 동안 문제가 발생 하는 경우 사용할 수 있는 문제 해결 정보가 포함 되어 있습니다.

Azure AD Connect 설치를 사용자 지정 하는 경우 **필수 구성 요소 설치** 페이지에서 **기존 SQL Server 사용** 을 선택할 수 있습니다. 다음과 같은 오류가 표시 될 수 있습니다. "ADSync 데이터베이스에 이미 데이터가 포함 되어 있으므로 덮어쓸 수 없습니다. 기존 데이터베이스를 제거 하 고 다시 시도 하십시오. "

!["필수 구성 요소 설치" 페이지를 보여 주는 스크린샷 페이지 아래쪽에 오류가 표시 됩니다.](./media/how-to-connect-install-custom/error1.png)

지정한 SQL Server의 SQL 인스턴스에 *Adsync* 라는 데이터베이스가 이미 있기 때문에이 오류가 표시 됩니다.

일반적으로 Azure AD Connect를 제거한 후에이 오류가 표시 됩니다.  Azure AD Connect를 제거할 때 SQL Server를 실행 하는 컴퓨터에서 데이터베이스가 삭제 되지 않습니다.

이 문제를 해결하려면 다음을 수행합니다.

1. 제거 되기 전에 사용 Azure AD Connect 된 ADSync 데이터베이스를 확인 합니다. 데이터베이스가 더 이상 사용 되 고 있지 않은지 확인 합니다.

2. 데이터베이스를 백업합니다.

3. 데이터베이스를 삭제 합니다.
    1. **Microsoft SQL Server Management Studio** 를 사용 하 여 SQL 인스턴스에 연결 합니다. 
    1. **Adsync** 데이터베이스를 찾아서 마우스 오른쪽 단추로 클릭 합니다.
    1. 상황에 맞는 메뉴에서 **삭제** 를 선택 합니다.
    1. **확인** 을 선택 하 여 데이터베이스를 삭제 합니다.

![Microsoft SQL Server Management Studio를 보여 주는 스크린샷 D 동기화가 선택 됩니다.](./media/how-to-connect-install-custom/error2.png)

ADSync 데이터베이스를 삭제 한 **후 설치를 선택 하** 여 설치를 다시 시도 합니다.

## <a name="next-steps"></a>다음 단계
설치가 완료 되 면 Windows에서 로그 아웃 합니다. 그런 다음 Synchronization Service Manager 또는 동기화 규칙 편집기를 사용 하기 전에 다시 로그인 합니다.

Azure AD Connect를 설치 했으므로 [설치를 확인 하 고 라이선스를 할당할](how-to-connect-post-installation.md)수 있습니다.

설치 중에 사용 하도록 설정 된 기능에 대 한 자세한 내용은 [실수로 인 한 삭제 방지](how-to-connect-sync-feature-prevent-accidental-deletes.md) 및 [Azure AD Connect Health](how-to-connect-health-sync.md)을 참조 하세요.

다른 일반적인 항목에 대 한 자세한 내용은 [Azure AD Connect sync: Scheduler](how-to-connect-sync-feature-scheduler.md) 및 [Azure AD와 온-프레미스 id 통합](whatis-hybrid-identity.md)을 참조 하세요.
