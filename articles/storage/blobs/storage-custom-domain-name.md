---
title: Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑
titleSuffix: Azure Storage
description: Azure Storage 계정의 Blob Storage 또는 웹 끝점에 사용자 지정 도메인을 매핑합니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 52fc7b9c1229421fd46b8110857a0a7a8a4f916a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520428"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑

사용자 지정 도메인을 blob service 끝점 또는 [정적 웹 사이트](storage-blob-static-website.md) 끝점에 매핑할 수 있습니다. 

> [!NOTE] 
> 이 매핑은 하위 도메인 (예:)에 대해서만 작동 `www.contoso.com` 합니다. 웹 끝점을 루트 도메인 (예:)에서 사용할 수 있도록 하려면 `contoso.com` Azure CDN를 사용 해야 합니다. 지침은이 문서의 HTTPS를 [사용 하 여 사용자 지정 도메인 매핑](#enable-https) 섹션을 참조 하세요. 이 문서의 해당 섹션에서 사용자 지정 도메인의 루트 도메인을 사용 하도록 설정 하기 때문에 HTTPS를 사용 하도록 설정 하는 단계는 선택 사항입니다. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>HTTP만 사용 하는 사용자 지정 도메인 매핑

이 방법은 더 쉽지만 HTTP 액세스만 가능 합니다. 저장소 계정이 HTTPS를 통한 [보안 전송을 요구](../common/storage-require-secure-transfer.md) 하도록 구성 된 경우 사용자 지정 도메인에 대 한 https 액세스를 사용 하도록 설정 해야 합니다. 

HTTPS 액세스를 사용 하도록 설정 하려면이 문서의 [https를 사용 하도록 설정 된 사용자 지정 도메인 매핑](#enable-https) 섹션을 참조 하세요. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>사용자 지정 도메인 매핑

> [!IMPORTANT]
> 구성을 완료 하는 동안 사용자는 사용자 지정 도메인을 잠깐 사용할 수 없습니다. 현재 도메인에서 가동 중지 시간이 0 인 SLA (서비스 수준 계약)를 사용 하는 응용 프로그램을 지 원하는 경우이 문서의 [가동 중지 시간이 없는 사용자 지정 도메인 매핑](#zero-down-time) 섹션의 단계에 따라 DNS 매핑이 수행 되는 동안 사용자가 도메인에 액세스할 수 있는지 확인 합니다.

사용자가 도메인을 일시적으로 사용할 수 없는 경우 다음 단계를 수행 합니다.

: heavy_check_mark: 1 단계: 저장소 끝점의 호스트 이름을 가져옵니다.

: heavy_check_mark: 2 단계: 도메인 공급자를 사용 하 여 CNAME (정식 이름) 레코드를 만듭니다.

: heavy_check_mark: 3 단계: Azure에 사용자 지정 도메인을 등록 합니다. 

: heavy_check_mark: 4 단계: 사용자 지정 도메인을 테스트 합니다.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1 단계: 저장소 끝점의 호스트 이름 가져오기 

호스트 이름은 프로토콜 식별자 및 후행 슬래시가 없는 저장소 끝점 URL입니다. 

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **설정** 에서 **속성** 을 선택 합니다.  

3. **주 Blob Service 끝점** 또는 **기본 정적 웹 사이트 끝점** 의 값을 텍스트 파일에 복사 합니다. 
  
   > [!NOTE]
   > Data Lake 저장소 끝점은 지원 되지 않습니다 (예: `https://mystorageaccount.dfs.core.windows.net/` ).

4. 프로토콜 식별자 (예: `HTTPS` )와 해당 문자열에서 후행 슬래시를 제거 합니다. 다음 표에는 예제가 나와 있습니다.

   | 끝점의 유형입니다. |  엔드포인트(endpoint) | 호스트 이름 |
   |------------|-----------------|-------------------|
   |blob 서비스  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |정적 웹 사이트  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   나중에이 값을 따로 설정 합니다.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>2 단계: 도메인 공급자를 사용 하 여 정식 이름 (CNAME) 레코드 만들기

호스트 이름을 가리키는 CNAME 레코드를 만듭니다. CNAME 레코드는 원본 도메인 이름을 대상 도메인 이름에 매핑하는 DNS (Domain Name System) 레코드의 유형입니다.

1. 도메인 등록자의 웹 사이트에 로그인 하 고 DNS 설정 관리 페이지로 이동 합니다.

   **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 섹션에서 해당 페이지를 찾을 수 있습니다.

2. CNAME 레코드 관리에 대 한 섹션을 찾습니다. 

   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인** 을 찾아야 할 수 있습니다.

3. CNAME 레코드를 만듭니다. 해당 레코드의 일부로 다음 항목을 제공 합니다. 

   - 또는와 같은 하위 도메인 `www` 별칭 `photos` 입니다. 하위 도메인은 필수 이며 루트 도메인은 지원 되지 않습니다. 
      
   - 이 문서 앞부분의 [저장소 끝점의 호스트 이름 가져오기](#endpoint) 섹션에서 가져온 호스트 이름 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>3 단계: Azure에 사용자 지정 도메인 등록

##### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **Blob Service** 아래에서 **사용자 지정 도메인** 을 선택합니다.

   > [!NOTE]
   > 이 옵션은 계층 구조 네임 스페이스 기능을 사용 하도록 설정 된 계정에는 표시 되지 않습니다. 이러한 계정에 대해 PowerShell 또는 Azure CLI를 사용 하 여이 단계를 완료 합니다.

   ![사용자 지정 도메인 옵션](./media/storage-custom-domain-name/custom-domain-button.png "사용자 지정 도메인")

   **사용자 지정 도메인** 창이 열립니다.

3. **도메인 이름** 텍스트 상자에 하위 도메인을 포함 하 여 사용자 지정 도메인의 이름을 입력 합니다.  
   
   예를 들어 도메인이 *contoso.com* 이 고 하위 도메인 별칭이 *www* 인 경우을 입력 `www.contoso.com` 합니다. 하위 도메인이 *사진이* 면을 입력 `photos.contoso.com` 합니다.

4. 사용자 지정 도메인을 등록 하려면 **저장** 단추를 선택 합니다.

   CNAME 레코드가 DNS (도메인 이름 서버)를 통해 전파 된 후 사용자에 게 적절 한 권한이 있는 경우 사용자 지정 도메인을 사용 하 여 blob 데이터를 볼 수 있습니다.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 명령을 실행 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- `<resource-group-name>`자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

- `<storage-account-name>`자리 표시자를 저장소 계정의 이름으로 바꿉니다.

- 자리 표시자를 하위 도메인 `<custom-domain-name>` 을 포함 하는 사용자 지정 도메인의 이름으로 바꿉니다.

  예를 들어 도메인이 *contoso.com* 이 고 하위 도메인 별칭이 *www* 인 경우을 입력 `www.contoso.com` 합니다. 하위 도메인이 *사진이* 면을 입력 `photos.contoso.com` 합니다.

CNAME 레코드가 DNS (도메인 이름 서버)를 통해 전파 된 후 사용자에 게 적절 한 권한이 있는 경우 사용자 지정 도메인을 사용 하 여 blob 데이터를 볼 수 있습니다.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 PowerShell 명령을 실행 합니다.

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- `<resource-group-name>`자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

- `<storage-account-name>`자리 표시자를 저장소 계정의 이름으로 바꿉니다.

- 자리 표시자를 하위 도메인 `<custom-domain-name>` 을 포함 하는 사용자 지정 도메인의 이름으로 바꿉니다.

  예를 들어 도메인이 *contoso.com* 이 고 하위 도메인 별칭이 *www* 인 경우을 입력 `www.contoso.com` 합니다. 하위 도메인이 *사진이* 면을 입력 `photos.contoso.com` 합니다.

CNAME 레코드가 DNS (도메인 이름 서버)를 통해 전파 된 후 사용자에 게 적절 한 권한이 있는 경우 사용자 지정 도메인을 사용 하 여 blob 데이터를 볼 수 있습니다.

---

#### <a name="step-4-test-your-custom-domain"></a>4 단계: 사용자 지정 도메인 테스트

사용자 지정 도메인이 Blob 서비스 엔드포인트에 매핑되었는지 확인하려면 스토리지 계정 내의 공용 컨테이너에서 Blob을 만듭니다. 그런 다음, 웹 브라우저에서 `http://<subdomain.customdomain>/<mycontainer>/<myblob>` 형식의 URI를 사용하여 Blob에 액세스합니다.

예를 들어 `myforms` *photos.contoso.com* 사용자 지정 하위 도메인의 컨테이너에서 웹 양식에 액세스 하려면 다음 URI를 사용할 수 있습니다. `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>가동 중지 시간이 0 인 사용자 지정 도메인 매핑

> [!NOTE]
> 사용자가 도메인을 일시적으로 사용할 수 없는 경우이 문서의 [사용자 지정 도메인 매핑](#map-a-domain) 섹션에 나오는 단계를 사용 하는 것이 좋습니다. 더 간단한 단계를 포함 하는 간단한 방법입니다.  

현재 도메인에서 가동 중지 시간이 0 인 SLA (서비스 수준 계약)를 사용 하는 응용 프로그램을 지 원하는 경우 DNS 매핑이 수행 되는 동안 사용자가 도메인에 액세스할 수 있는지 확인 하려면 다음 단계를 수행 합니다. 

: heavy_check_mark: 1 단계: 저장소 끝점의 호스트 이름을 가져옵니다.

: heavy_check_mark: 2 단계: 도메인 공급자를 사용 하 여 중간 정식 이름 (CNAME) 레코드를 만듭니다.

: heavy_check_mark: 3 단계: Azure를 사용 하 여 사용자 지정 도메인을 미리 등록 합니다.

: heavy_check_mark: 4 단계: 도메인 공급자를 사용 하 여 CNAME 레코드를 만듭니다.

: heavy_check_mark: 5 단계: 사용자 지정 도메인을 테스트 합니다.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1 단계: 저장소 끝점의 호스트 이름 가져오기 

호스트 이름은 프로토콜 식별자 및 후행 슬래시가 없는 저장소 끝점 URL입니다. 

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **설정** 에서 **속성** 을 선택 합니다.  

3. **주 Blob Service 끝점** 또는 **기본 정적 웹 사이트 끝점** 의 값을 텍스트 파일에 복사 합니다. 

   > [!NOTE]
   > Data Lake 저장소 끝점은 지원 되지 않습니다 (예: `https://mystorageaccount.dfs.core.windows.net/` ).

4. 프로토콜 식별자 (예: `HTTPS` )와 해당 문자열에서 후행 슬래시를 제거 합니다. 다음 표에는 예제가 나와 있습니다.

   | 끝점의 유형입니다. |  엔드포인트(endpoint) | 호스트 이름 |
   |------------|-----------------|-------------------|
   |blob 서비스  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |정적 웹 사이트  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   나중에이 값을 따로 설정 합니다.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>2 단계: 도메인 공급자를 사용 하 여 중간 정식 이름 (CNAME) 레코드 만들기

호스트 이름을 가리키는 임시 CNAME 레코드를 만듭니다. CNAME 레코드는 원본 도메인을 대상 도메인 이름에 매핑하는 DNS 레코드의 형식입니다.

1. 도메인 등록자의 웹 사이트에 로그인 하 고 DNS 설정 관리 페이지로 이동 합니다.

   **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 섹션에서 해당 페이지를 찾을 수 있습니다.

2. CNAME 레코드 관리에 대 한 섹션을 찾습니다. 

   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인** 을 찾아야 할 수 있습니다.

3. CNAME 레코드를 만듭니다. 해당 레코드의 일부로 다음 항목을 제공 합니다. 

   - 또는와 같은 하위 도메인 `www` 별칭 `photos` 입니다. 하위 도메인은 필수 이며 루트 도메인은 지원 되지 않습니다.

     하위 `asverify` 도메인을 별칭에 추가 합니다. 예를 들어 `asverify.www` 또는 `asverify.photos`입니다.
       
   - 이 문서 앞부분의 [저장소 끝점의 호스트 이름 가져오기](#endpoint) 섹션에서 가져온 호스트 이름 

     `asverify`호스트 이름에 하위 도메인을 추가 합니다. 예를 들어 `asverify.mystorageaccount.blob.core.windows.net`을 참조하십시오.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>3 단계: Azure를 사용 하 여 사용자 지정 도메인 미리 등록

Azure에 사용자 지정 도메인을 미리 등록 하는 경우 도메인에 대 한 DNS 레코드를 수정 하지 않고도 Azure에서 사용자 지정 도메인을 인식할 수 있도록 허용 합니다. 이런 방식으로 도메인에 대 한 DNS 레코드를 수정 하면 가동 중지 시간 없이 blob 끝점에 매핑됩니다.

##### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **Blob Service** 아래에서 **사용자 지정 도메인** 을 선택합니다.

   > [!NOTE]
   > 이 옵션은 계층 구조 네임 스페이스 기능을 사용 하도록 설정 된 계정에는 표시 되지 않습니다. 이러한 계정에 대해 PowerShell 또는 Azure CLI를 사용 하 여이 단계를 완료 합니다.

   ![사용자 지정 도메인 옵션](./media/storage-custom-domain-name/custom-domain-button.png "사용자 지정 도메인")

   **사용자 지정 도메인** 창이 열립니다.

3. **도메인 이름** 텍스트 상자에 하위 도메인을 포함 하 여 사용자 지정 도메인의 이름을 입력 합니다.  
   
   예를 들어 도메인이 *contoso.com* 이 고 하위 도메인 별칭이 *www* 인 경우을 입력 `www.contoso.com` 합니다. 하위 도메인이 *사진이* 면을 입력 `photos.contoso.com` 합니다.

4. **간접 CNAME 유효성 검사 사용** 확인란을 선택합니다.

5. 사용자 지정 도메인을 등록 하려면 **저장** 단추를 선택 합니다.
  
   등록에 성공한 경우 포털에서 스토리지 계정이 성공적으로 업데이트되었음을 알립니다. Azure에서 사용자 지정 도메인을 확인 했지만 도메인 공급자를 사용 하 여 CNAME 레코드를 만들 때까지 도메인에 대 한 트래픽이 저장소 계정으로 아직 라우팅되지 않습니다. 다음 섹션에서 구성을 완료합니다.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 명령을 실행 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- `<resource-group-name>`자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

- `<storage-account-name>`자리 표시자를 저장소 계정의 이름으로 바꿉니다.

- 자리 표시자를 하위 도메인 `<custom-domain-name>` 을 포함 하는 사용자 지정 도메인의 이름으로 바꿉니다.

  예를 들어 도메인이 *contoso.com* 이 고 하위 도메인 별칭이 *www* 인 경우을 입력 `www.contoso.com` 합니다. 하위 도메인이 *사진이* 면을 입력 `photos.contoso.com` 합니다.

도메인에 대 한 트래픽은 도메인 공급자를 사용 하 여 CNAME 레코드를 만들 때까지 아직 저장소 계정으로 라우팅되지 않습니다. 다음 섹션에서 구성을 완료합니다.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 PowerShell 명령을 실행 합니다.

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- `<resource-group-name>`자리 표시자를 리소스 그룹의 이름으로 바꿉니다.

- `<storage-account-name>`자리 표시자를 저장소 계정의 이름으로 바꿉니다.

- 자리 표시자를 하위 도메인 `<custom-domain-name>` 을 포함 하는 사용자 지정 도메인의 이름으로 바꿉니다.

  예를 들어 도메인이 *contoso.com* 이 고 하위 도메인 별칭이 *www* 인 경우을 입력 `www.contoso.com` 합니다. 하위 도메인이 *사진이* 면을 입력 `photos.contoso.com` 합니다.

도메인에 대 한 트래픽은 도메인 공급자를 사용 하 여 CNAME 레코드를 만들 때까지 아직 저장소 계정으로 라우팅되지 않습니다. 다음 섹션에서 구성을 완료합니다.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>4 단계: 도메인 공급자를 사용 하 여 CNAME 레코드 만들기

호스트 이름을 가리키는 임시 CNAME 레코드를 만듭니다.

1. 도메인 등록자의 웹 사이트에 로그인 하 고 DNS 설정 관리 페이지로 이동 합니다.

   **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 섹션에서 해당 페이지를 찾을 수 있습니다.

2. CNAME 레코드 관리에 대 한 섹션을 찾습니다. 

   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인** 을 찾아야 할 수 있습니다.

3. CNAME 레코드를 만듭니다. 해당 레코드의 일부로 다음 항목을 제공 합니다. 

   - 또는와 같은 하위 도메인 `www` 별칭 `photos` 입니다. 하위 도메인은 필수 이며 루트 도메인은 지원 되지 않습니다.
      
   - 이 문서 앞부분의 [저장소 끝점의 호스트 이름 가져오기](#endpoint-2) 섹션에서 가져온 호스트 이름 

#### <a name="step-5-test-your-custom-domain"></a>5 단계: 사용자 지정 도메인 테스트

사용자 지정 도메인이 Blob 서비스 엔드포인트에 매핑되었는지 확인하려면 스토리지 계정 내의 공용 컨테이너에서 Blob을 만듭니다. 그런 다음, 웹 브라우저에서 `http://<subdomain.customdomain>/<mycontainer>/<myblob>` 형식의 URI를 사용하여 Blob에 액세스합니다.

예를 들어 `myforms` *photos.contoso.com* 사용자 지정 하위 도메인의 컨테이너에서 웹 양식에 액세스 하려면 다음 URI를 사용할 수 있습니다. `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>사용자 지정 도메인 매핑 제거

사용자 지정 도메인 매핑을 제거 하려면 사용자 지정 도메인의 등록을 취소 합니다. 다음 절차 중 하나를 수행하십시오.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **Blob Service** 아래에서 **사용자 지정 도메인** 을 선택합니다.  
   **사용자 지정 도메인** 창이 열립니다.

3. 사용자 지정 도메인 이름이 포함된 텍스트 상자의 콘텐츠를 지웁니다.

4. **저장** 단추를 선택합니다.

사용자 지정 도메인이 성공적으로 제거된 경우 스토리지 계정이 성공적으로 업데이트되었다는 포털 알림이 표시됩니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

사용자 지정 도메인 등록을 제거하려면 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet을 사용한 다음, `-CustomDomainName` 인수 값에 빈 문자열(`""`)을 지정합니다.

* 명령 형식:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* 명령 예:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

사용자 지정 도메인 등록을 제거하려면 [az storage account update](/cli/azure/storage/account) CLI 명령을 사용한 다음, `--custom-domain` 인수 값에 빈 문자열(`""`)을 지정합니다.

* 명령 형식:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* 명령 예:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>HTTPS를 사용 하도록 설정 된 사용자 지정 도메인 매핑

이 방법은 더 많은 단계를 포함 하지만 HTTPS 액세스를 가능 하 게 합니다. 

사용자가 HTTPS를 사용 하 여 blob 또는 웹 콘텐츠에 액세스 하지 않아도 되는 경우이 문서의 [HTTP를 사용 하도록 설정 된 사용자 지정 도메인 매핑](#enable-http) 섹션을 참조 하세요. 

1. Blob 또는 웹 끝점에서 [Azure CDN](../../cdn/cdn-overview.md) 를 사용 하도록 설정 합니다. 

   Blob Storage 끝점은 [Azure CDN와 Azure Storage 계정 통합](../../cdn/cdn-create-a-storage-account-with-cdn.md)을 참조 하세요. 

   정적 웹 사이트 끝점의 경우 [Azure CDN와 정적 웹 사이트 통합](static-website-content-delivery-network.md)을 참조 하세요.

2. [Azure CDN 콘텐츠를 사용자 지정 도메인에 매핑합니다](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Azure CDN 사용자 지정 도메인에서 HTTPS를 사용 하도록 설정](../../cdn/cdn-custom-ssl.md)합니다.

   > [!NOTE] 
   > 정적 웹 사이트를 업데이트 하는 경우 CDN 끝점을 제거 하 여 CDN에 지 서버에서 캐시 된 콘텐츠를 지워야 합니다. 자세한 내용은 [Azure CDN 엔드포인트 제거](../../cdn/cdn-purge-endpoint.md)를 참조하세요.

4. 필드 다음 지침을 검토 합니다.

   * [Azure CDN를 사용 하는 SAS (공유 액세스 서명) 토큰](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures)입니다.

   * [Azure CDN를 사용 하는 HTTP에서 HTTPS로의 리디렉션](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection)입니다.

   * [Azure CDN에서 Blob Storage를 사용 하는 경우 가격 책정 및 청구](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection)

## <a name="next-steps"></a>다음 단계

* [Azure Blob storage의 정적 웹 사이트 호스팅에 대해 알아보기](storage-blob-static-website.md)