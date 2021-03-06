---
ms.openlocfilehash: 4891a602e9fb5ebbdaadc980c1960b95816c6653
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113239"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK(Java Development Kit)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi) - 배포된 Communication Services 리소스 및 연결 문자열 [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-java-application"></a>새 Java 애플리케이션 만들기

터미널 또는 명령 창을 엽니다. Java 애플리케이션을 만들 디렉터리로 이동합니다. 아래 명령을 실행하여 maven-archetype-quickstart 템플릿에서 Java 프로젝트를 생성합니다.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

'생성' 작업에서 `artifactId`와 동일한 이름으로 디렉터리를 만들었습니다. 이 디렉터리 아래에서 src/main/java 디렉터리는 프로젝트 소스 코드를 포함하고, `src/test/java directory`는 테스트 원본을 포함하고, `pom.xml` 파일은 프로젝트의 프로젝트 개체 모델 또는 POM입니다.

### <a name="install-the-package"></a>패키지 설치

텍스트 편집기에서 **pom.xml** 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-phonenumbers</artifactId>
    <version>1.0.0-beta.7</version>
</dependency>

<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. */src/main/java/com/communication/quickstart* 디렉터리로 이동합니다.
1. 편집기에서 *App.java* 파일을 엽니다.
1. `System.out.println("Hello world!");` 문 바꾸기
1. `import` 지시문 추가

시작하려면 다음 코드를 사용합니다.

```java
import com.azure.communication.phonenumbers.*;
import com.azure.communication.phonenumbers.models.*;
import java.io.*;
import com.azure.core.util.Context;
import com.azure.core.util.polling.PollResponse;
import com.azure.identity.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Phone Numbers Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-phone-numbers-client"></a>전화 번호 클라이언트 인증

Azure Active Directory 인증을 사용하도록 PhoneNumberClientBuilder가 활성화되어 있습니다.
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L52-L62 -->
```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

또는 통신 리소스의 엔드포인트 및 액세스 키를 사용하여 인증할 수도 있습니다.
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L30-L41 -->
```java
// You can find your connection string from your resource in the Azure portal
String connectionString = "https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<ACCESS_KEY>";

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

## <a name="manage-phone-numbers"></a>전화 번호 관리

### <a name="search-for-available-phone-numbers"></a>사용 가능한 전화 번호 검색

전화 번호를 구매하려면 먼저 사용 가능한 전화 번호를 검색해야 합니다. 전화 번호를 검색하려면 지역 번호, 할당 유형, [전화 번호 기능](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [전화 번호 유형](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) 및 수량을 제공합니다. 수신자 부담 전화 번호 형식의 경우 지역 코드 제공은 선택 사항입니다.

```java
 PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities()
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PhoneNumberSearchOptions searchOptions = new PhoneNumberSearchOptions().setAreaCode("833").setQuantity(1);

PhoneNumberSearchResult searchResult = phoneNumberClient
    .beginSearchAvailablePhoneNumbers("US", PhoneNumberType.TOLL_FREE, PhoneNumberAssignmentType.APPLICATION, capabilities,  searchOptions, Context.NONE)
    .getFinalResult();

System.out.println("Searched phone numbers: " + searchResult.getPhoneNumbers());
System.out.println("Search expires by: " + searchResult.getSearchExpiresBy());
System.out.println("Phone number costs:" + searchResult.getCost().getAmount());
```

### <a name="purchase-phone-numbers"></a>전화 번호 구매

전화 번호 검색의 결과는 PhoneNumberSearchResult입니다. 여기에는 검색 시 숫자를 얻기 위해 번호 구매 API에 전달할 수 있는 `searchId`가 포함됩니다. 전화 번호 구매 API를 호출하면 Azure 계정에 요금이 부과됩니다.

```java
PollResponse<PhoneNumberOperation> purchaseResponse = phoneNumberClient.beginPurchasePhoneNumbers(searchResult.getSearchId(), Context.NONE).waitForCompletion();
System.out.println("Purchase phone numbers operation is: " + purchaseResponse.getStatus());
```

### <a name="get-phone-numbers"></a>전화 번호 가져오기

번호 구매 후에 클라이언트에서 검색할 수 있습니다.
```java
PurchasedPhoneNumber phoneNumber = phoneNumberClient.getPurchasedPhoneNumber("+14255550123");
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

구매한 전화 번호를 모두 검색할 수도 있습니다.
``` java
PagedIterable<PurchasedPhoneNumber> phoneNumbers = phoneNumberClient.listPurchasedPhoneNumbers(Context.NONE);
PurchasedPhoneNumber phoneNumber = phoneNumbers.iterator().next();
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

### <a name="update-phone-number-capabilities"></a>전화 번호 기능 업데이트

구매한 번호를 사용하면 기능을 업데이트할 수 있습니다.
```java
PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities();
capabilities
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PurchasedPhoneNumber phoneNumber = phoneNumberClient.beginUpdatePhoneNumberCapabilities("+14255550123", capabilities, Context.NONE).getFinalResult();

System.out.println("Phone Number Calling capabilities: " + phoneNumber.getCapabilities().getCalling()); //Phone Number Calling capabilities: inbound
System.out.println("Phone Number SMS capabilities: " + phoneNumber.getCapabilities().getSms()); //Phone Number SMS capabilities: inbound+outbound
```

### <a name="release-phone-number"></a>전화 번호 해제

구매한 전화 번호를 해제할 수 있습니다.
```java
PollResponse<PhoneNumberOperation> releaseResponse =
    phoneNumberClient.beginReleasePhoneNumber("+14255550123", Context.NONE).waitForCompletion();
System.out.println("Release phone number operation is: " + releaseResponse.getStatus());
```

## <a name="run-the-code"></a>코드 실행

*pom.xml* 파일이 포함된 디렉터리로 이동한 후 다음 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

```console
mvn compile
```

그런 다음, 패키지를 빌드합니다.

```console
mvn package
```

다음 `mvn` 명령을 실행하여 앱을 실행합니다.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

앱의 출력은 완료된 각 작업을 설명합니다.
<!---cSpell:disable --->
```console
Azure Communication Services - Phone Numbers Quickstart

Searched phone numbers: [+18001234567]

Purchase phone numbers operation is: SUCCESSFULLY_COMPLETED

Phone Number Country Code: US

Phone Number Calling capabilities: inbound

Phone Number SMS capabilities: inbound

Release phone number operation is: SUCCESSFULLY_COMPLETED

```
<!---cSpell:enable --->