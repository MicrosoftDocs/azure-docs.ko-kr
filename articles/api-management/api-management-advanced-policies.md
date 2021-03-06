---
title: Azure API Management 고급 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 고급 정책에 대해 알아봅니다. 예제를 참조 하 고 사용 가능한 추가 리소스를 봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 03529fd3c0231617c477f4f16773039a02386683
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562487"
---
# <a name="api-management-advanced-policies"></a>API Management 고급 정책

이 문서에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](./api-management-policies.md)을 참조하세요.

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a> 고급 정책

-   [흐름 제어](api-management-advanced-policies.md#choose) - 부울 [식](api-management-policy-expressions.md)의 평가 결과에 따라 정책 문을 조건부로 적용합니다.
-   [요청 전달](#ForwardRequest) - 백 엔드 서비스에 요청을 전달합니다.
-   [동시성 제한](#LimitConcurrency) - 지정된 정책이 한 번에 지정된 요청 수를 초과해서 실행하지 못하게 합니다.
-   [이벤트 허브에 기록](#log-to-eventhub) - 로거 엔터티가 정의한 이벤트 허브에 지정된 형식으로 메시지를 보냅니다.
-   [모의 응답](#mock-response) - 파이프라인 실행을 중단하고 호출자에게 직접 모의 응답을 반환합니다.
-   [다시 시도](#Retry) - 조건이 충족될 때까지 포함된 정책 문을 실행하도록 다시 시도합니다. 실행은 지정된 시간 간격으로 최대 지정된 재시도 횟수까지 반복됩니다.
-   [응답 반환](#ReturnResponse) - 파이프라인 실행을 중단하고 호출자에게 직접 지정된 응답을 반환합니다.
-   [단방향 요청 전송](#SendOneWayRequest) - 지정된 URL에 대한 응답을 기다리지 않고 요청을 보냅니다.
-   [요청 전송](#SendRequest) - 지정된 URL로 요청을 보냅니다.
-   [HTTP 프록시 설정](#SetHttpProxy) - HTTP 프록시를 통해 전달되는 요청을 라우팅할수 있습니다.
-   [요청 메서드 설정](#SetRequestMethod) - 요청에 대한 HTTP 메서드를 변경할 수 있습니다.
-   [상태 코드 설정](#SetStatus) - 지정된 값으로 HTTP 상태 코드를 변경합니다.
-   [변수 설정](api-management-advanced-policies.md#set-variable) - 나중에 액세스할 수 있도록 명명된 [context](api-management-policy-expressions.md#ContextVariables) 변수의 값을 유지합니다.
-   [Trace](#Trace) -사용자 지정 추적을 [API 검사기](./api-management-howto-api-inspector.md) 출력, Application Insights 원격 분석 및 리소스 로그에 추가 합니다.
-   [대기](#Wait) -계속 하기 전에 포함 되는 [보내기 요청](api-management-advanced-policies.md#SendRequest), [캐시에서 값 가져오기](api-management-caching-policies.md#GetFromCacheByKey)또는 [제어 흐름](api-management-advanced-policies.md#choose) 정책에 대해 대기 합니다.

## <a name="control-flow"></a><a name="choose"></a> 제어 흐름

`choose` 정책은 프로그래밍 언어의 if-then-else 또는 switch 생성과 마찬가지로 Boolean 식의 평가 결과에 따라 포함된 정책 문을 적용합니다.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a> 정책 문

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

제어 흐름 정책에는 `<when/>` 요소가 하나 이상 포함되어 있어야 합니다. `<otherwise/>` 요소는 선택적입니다. 정책 내에 표시되는 순서대로 `<when/>` 요소의 조건이 평가됩니다. 조건 특성이 `true`인 첫 번째 `<when/>` 요소 내에 포함된 정책 문이 적용됩니다. 모든 `<when/>` 요소 조건 특성이 `false`인 경우 `<otherwise/>` 요소 내에 포함된 정책(있는 경우)이 적용됩니다.

### <a name="examples"></a>예제

#### <a name="example"></a><a name="ChooseExample"></a> 예

다음 예제에서는 [set-variable](api-management-advanced-policies.md#set-variable) 정책과 제어 흐름 정책 두 개를 보여 줍니다.

inbound 섹션에 있는 set-variable 정책은 `isMobile` 요청 헤더에 `User-Agent` 또는 `iPad` 텍스트가 있으면 true로 설정되는 `iPhone` 부울 [컨텍스트](api-management-policy-expressions.md#ContextVariables) 변수를 만듭니다.

첫 번째 제어 흐름 정책도 인바운드 섹션에 있으며 `isMobile` 컨텍스트 변수 값에 따라 두 [쿼리 문자열 매개 변수 설정](api-management-transformation-policies.md#SetQueryStringParameter) 정책 중 하나를 조건부로 적용합니다.

두 번째 제어 흐름 정책은 아웃바운드 섹션에 있으며 `isMobile`이 `true`로 설정될 때 [XML을 JSON으로 변환](api-management-transformation-policies.md#ConvertXMLtoJSON) 정책을 조건부로 적용합니다.

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPad") || context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>예제

이 예제에서는 `Starter` 제품을 사용할 때 백 엔드 서비스에서 받은 응답의 데이터 요소를 제거하여 콘텐츠 필터링을 수행하는 방법을 보여 줍니다. 이 정책을 구성하고 사용하는 데모는 [클라우드 표지 에피소드 177: Vlad Vinogradsky와 함께 하는 추가 API Management 기능](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)(영문)에서 34분 30초 재생 시점까지 빨리 진행하면서 참조하세요. 31:50에서 시작 하 여이 데모에 사용 되는 [진한 하늘 예측 API](https://developer.forecast.io/) 의 개요를 확인 합니다.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="elements"></a>요소

| 요소   | Description                                                                                                                                                                                                                                                               | 필수 |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| choose    | 루트 요소입니다.                                                                                                                                                                                                                                                             | 예      |
| when      | `choose` 정책의 `if` 또는 `ifelse` 부분에 사용할 조건입니다. `choose` 정책에 여러 `when` 섹션이 있는 경우 순차적으로 평가됩니다. when 요소의 `condition`이 `true`로 평가되면 `when` 조건이 더 이상 평가되지 않습니다. | 예      |
| otherwise | `true`로 평가되는 `when` 조건이 없으면 사용할 정책 조각을 포함합니다.                                                                                                                                                                               | 아니요       |

### <a name="attributes"></a>특성

| 특성                                              | Description                                                                                               | 필수 |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| condition="Boolean expression &#124; Boolean constant" | 포함하는 `when` 정책 문이 평가될 때 평가할 Boolean 식 또는 상수입니다. | 예      |

### <a name="usage"></a><a name="ChooseUsage"></a> 보려면

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** 모든 범위

## <a name="forward-request"></a><a name="ForwardRequest"></a> 요청 전달

`forward-request` 정책은 들어오는 요청을 요청 [컨텍스트](api-management-policy-expressions.md#ContextVariables)에 지정된 백 엔드 서비스에 전달합니다. 백 엔드 서비스 URL은 API [설정](./import-and-publish.md) 에 지정 되며 [백 엔드 서비스 설정](api-management-transformation-policies.md) 정책을 사용 하 여 변경할 수 있습니다.

> [!NOTE]
> 요청에서 이 정책을 제거하면 백 엔드 서비스로 전달되지 않고 인바운드 섹션에 있는 정책이 성공적으로 완료되는 즉시 아웃바운드 섹션에 있는 정책이 평가됩니다.

### <a name="policy-statement"></a>정책 문

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" buffer-response="true | false" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>예

#### <a name="example"></a>예제

다음 API 수준 정책은 시간 제한 간격을 60 초로 하 여 백 엔드 서비스에 모든 API 요청을 전달 합니다.

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>예제

이 작업 수준 정책은 `base` 요소를 사용하여 상위 API 수준 범위에서 백 엔드 정책을 상속합니다.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>예제

이 작업 수준 정책은 120의 시간 초과로 모든 요청을 백 엔드 서비스로 명시적으로 전달하며 상위 API 수준 백 엔드 정책을 상속하지 않습니다. 백 엔드 서비스가 오류 상태 코드 400에서 599 포함으로 응답 하는 경우 [에는 오류 발생](api-management-error-handling-policies.md) 섹션이 트리거됩니다.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>예제

이 작업 수준 정책은 요청을 백 엔드 서비스로 전달하지 않습니다.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>요소

| 요소         | Description   | 필수 |
| --------------- | ------------- | -------- |
| forward-request | 루트 요소입니다. | 예      |

### <a name="attributes"></a>특성

| 특성                                     | Description                                                                                                                                                                                                                                                                                                    | 필수 | 기본값 |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout="integer"                             | 시간 초과 오류가 발생 하기 전에 백 엔드 서비스에서 HTTP 응답 헤더가 반환 될 때까지 대기 하는 시간 (초)입니다. 최소값은 0 초입니다. 기본 네트워크 인프라가이 시간 이후에 유휴 연결을 삭제할 수 있으므로 240 초 보다 큰 값은 적용 되지 않을 수 있습니다. | 예       | None    |
| 따르기-리디렉션 = "false &#124; true"          | 백 엔드 서비스의 리디렉션 뒤에 게이트웨이가 있는지 또는 호출자에게 반환되는지 여부를 지정합니다.                                                                                                                                                                                                    | 아니요       | false   |
| 버퍼 요청-본문 = "false &#124; true"       | "True"로 설정 하면 요청은 버퍼링 되며 [다시 시도](api-management-advanced-policies.md#Retry)시 다시 사용 됩니다.                                                                                                                                                                                               | 아니요       | false   |
| buffer-response = "false &#124; true" | 청크 분할 응답의 처리에 영향을 줍니다. "False"로 설정 된 경우 백 엔드에서 받은 각 청크는 호출자에 게 즉시 반환 됩니다. "True"로 설정 된 청크 (스트림의 끝이 검색 되지 않는 경우 8KB)가 버퍼링 된 후에만 호출자에 게 반환 됩니다. | 아니요 | true |
| 오류 발생 시 상태-코드 = "false &#124; true" | True로 설정 하면 400에서 599 까지의 범위에 있는 응답 코드에 대해 [오류 발생 시](api-management-error-handling-policies.md) 트리거를 트리거합니다.                                                                                                                                                                      | 아니요       | false   |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** backend
-   **정책 범위:** 모든 범위

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a> 동시성 제한

`limit-concurrency` 정책은 포함된 정책이 한 번에 지정된 개수보다 많은 요청을 실행할 수 없도록 차단합니다. 해당 숫자를 초과할 경우 새 요청이 “429 요청이 너무 많음” 상태 코드를 나타내며 즉시 실패합니다.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a> 정책 문

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>예

#### <a name="example"></a>예제

다음 예제에서는 컨텍스트 변수 값에 따라 백 엔드로 전달되는 요청 수를 제한하는 방법을 보여 줍니다.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    </limit-concurrency>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>요소

| 요소           | Description   | 필수 |
| ----------------- | ------------- | -------- |
| limit-concurrency | 루트 요소입니다. | 예      |

### <a name="attributes"></a>특성

| 특성 | Description                                                                                        | 필수 | 기본값 |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | 문자열 허용되는 식입니다. 동시성 범위를 지정합니다. 여러 정책에서 공유될 수 있습니다. | 예      | 해당 없음     |
| max-count | 정수입니다. 정책에 들어올 수 있는 요청의 최대 수를 지정합니다.           | 예      | 해당 없음     |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** 모든 범위

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a> 이벤트 허브에 기록

`log-to-eventhub` 정책은 로거 엔터티가 정의한 이벤트 허브에 지정된 형식으로 메시지를 보냅니다. 이름에서 알 수 있듯이 이 정책은 온라인 또는 오프라인 분석을 위해 선택한 요청 또는 응답 컨텍스트 정보를 저장하는 데 사용됩니다.

> [!NOTE]
> 이벤트 허브 구성 및 이벤트 로깅에 대한 단계별 가이드는 [Azure Event Hubs로 API Management 이벤트를 기록하는 방법](./api-management-howto-log-event-hubs.md)을 참조하세요.

### <a name="policy-statement"></a>정책 문

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>예제

Event Hubs에 기록할 값으로 모든 문자열을 사용할 수 있습니다. 이 예제에서는 모든 인바운드 호출에 대 한 날짜 및 시간, 배포 서비스 이름, 요청 ID, IP 주소 및 작업 이름이 ID로 등록 된 이벤트 허브로 거에 기록 됩니다. `contoso-logger`

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>요소

| 요소         | Description                                                                     | 필수 |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-to-eventhub | 루트 요소입니다. 이 요소 값은 이벤트 허브에 기록할 문자열입니다. | 예      |

### <a name="attributes"></a>특성

| 특성     | Description                                                               | 필수                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | API Management 서비스에 등록 된로 거 ID입니다.         | 예                                                                  |
| partition-id  | 메시지가 전송된 파티션의 인덱스를 지정합니다.             | 선택 사항입니다. `partition-key`가 사용된 경우에는 이 특성을 사용할 수 없습니다. |
| 파티션 키 | 메시지가 전송된 파티션 할당에 사용된 값을 지정합니다. | 선택 사항입니다. `partition-id`가 사용된 경우에는 이 특성을 사용할 수 없습니다.  |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** 모든 범위

## <a name="mock-response"></a><a name="mock-response"></a> 모의 응답

이름에서 알 수 있듯이 `mock-response`는 모의 API 및 작업에 사용됩니다. 정상적인 파이프라인 실행을 중단하고 호출자에게 모의 응답을 반환합니다. 정책에서 항상 최고 충실도의 응답을 반환하려고 시도합니다. 가능한 경우 응답 콘텐츠 예제를 선호합니다. 스키마가 제공되고 예제가 제공되지 않은 경우 스키마에서 샘플 응답을 생성합니다. 예제와 스키마가 둘 다 없는 경우 콘텐츠가 없는 응답이 반환됩니다.

### <a name="policy-statement"></a>정책 문

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>예제

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>요소

| 요소       | Description   | 필수 |
| ------------- | ------------- | -------- |
| mock-response | 루트 요소입니다. | 예      |

### <a name="attributes"></a>특성

| 특성    | Description                                                                                           | 필수 | 기본값 |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| status-code  | 응답 상태 코드를 지정하며, 해당 예제 또는 스키마를 선택하는 데 사용됩니다.                 | 예       | 200     |
| content-type | `Content-Type` 응답 헤더 값을 지정하며, 해당 예제 또는 스키마를 선택하는 데 사용됩니다. | 예       | None    |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, on-error

-   **정책 범위:** 모든 범위

## <a name="retry"></a><a name="Retry"></a> Retry

`retry`정책은 자식 정책을 한 번 실행 한 다음 다시 시도를 `condition` `false` 하거나 다시 시도할 때까지 실행을 다시 시도 합니다 `count` .

### <a name="policy-statement"></a>정책 문

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>예제

다음 예제에서는 지수 재시도 알고리즘을 사용하여 요청 전달을 최대 10회까지 다시 시도합니다. `first-fast-retry`가 false로 설정되므로 모든 재시도 횟수에는 지수 재시도 알고리즘이 적용됩니다.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>요소

| 요소 | Description                                                         | 필수 |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | 루트 요소입니다. 자식 요소로 다른 정책을 포함할 수 있습니다. | 예      |

### <a name="attributes"></a>특성

| 특성        | Description                                                                                                                                           | 필수 | 기본값 |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| condition(조건)        | 재시도를 중지(`false`) 또는 진행(`true`)해야 하는지 여부를 지정하는 부울 리터럴 또는 [식](api-management-policy-expressions.md)입니다.      | 예      | 해당 없음     |
| count            | 최대 재시도 횟수를 지정하는 양수입니다.                                                                                | 예      | 해당 없음     |
| interval         | 재시도 횟수 간에 대기 간격을 지정하는 양수(초)입니다.                                                                 | 예      | 해당 없음     |
| max-interval     | 재시도 횟수 간에 최대 대기 간격을 지정하는 양수(초)입니다. 지수 재시도 알고리즘을 구현하는 데 사용됩니다. | 아니요       | 해당 없음     |
| delta            | 대기 간격 증분을 지정하는 양수(초)입니다. 선형 및 지수 재시도 알고리즘을 구현하는 데 사용됩니다.             | 아니요       | 해당 없음     |
| first-fast-retry | 로 설정 `true` 되 면 첫 번째 다시 시도가 즉시 수행 됩니다.                                                                                  | 아니요       | `false` |

> [!NOTE]
> `interval`만 지정된 경우, **고정된** 간격 재시도가 수행됩니다.
> `interval` 및 `delta`만 지정된 경우 **선형** 간격 재시도 알고리즘이 사용되며 이 경우 재시도 간 대기 시간은 `interval + (count - 1)*delta` 수식에 따라 계산됩니다.
> `interval`, `max-interval` 및 `delta`가 지정된 경우 **지수** 간격 재시도 알고리즘이 적용되며 이 경우 재시도 간 대기 시간은 `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)` 수식에 따라 `interval` 값에서 `max-interval` 값으로 기하급수적으로 증가합니다.

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다. 이 정책에 의해 자식 정책 사용 제한이 상속됩니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** 모든 범위

## <a name="return-response"></a><a name="ReturnResponse"></a> 반환 응답

`return-response` 정책은 파이프라인 실행을 중단하고 호출자에게 기본 또는 사용자 지정된 응답을 반환합니다. 기본 응답은 본문 없는 `200 OK`입니다. 컨텍스트 변수나 정책 문을 통해 사용자 지정 응답을 지정할 수 있습니다. 둘 다 제공되는 경우 컨텍스트 변수 내에 포함된 응답은 호출자로 반환하기 전에 정책 문으로 수정합니다.

### <a name="policy-statement"></a>정책 문

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>예제

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>요소

| 요소         | Description                                                                               | 필수 |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| return-response | 루트 요소입니다.                                                                             | 예      |
| set-header      | [set-header](api-management-transformation-policies.md#SetHTTPheader) 정책 문. | 아니요       |
| set-body        | [set-body](api-management-transformation-policies.md#SetBody) 정책 문.         | 아니요       |
| set-status      | [set-status](api-management-advanced-policies.md#SetStatus) 정책 문.           | 아니요       |

### <a name="attributes"></a>특성

| 특성              | Description                                                                                                                                                                          | 필수  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| response-variable-name | 참조하는 컨텍스트 변수 이름(예: 업스트림 [send-request](api-management-advanced-policies.md#SendRequest) 정책 및 `Response` 개체 포함) | 선택 사항입니다. |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** 모든 범위

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a> 단방향 요청 전송

`send-one-way-request` 정책은 지정된 URL에 대한 제공된 응답을 기다리지 않고 요청을 보냅니다.

### <a name="policy-statement"></a>정책 문

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>예제

이 샘플 정책에서는 HTTP 응답 코드가 500 이상인 경우 `send-one-way-request` 정책을 사용하여 Slack 대화방에 메시지를 보내는 방법에 대한 예를 보여줍니다. 이 샘플에 대한 자세한 내용은 [Azure API Management 서비스에서 외부 서비스 사용](./api-management-sample-send-request.md)을 참조하세요.

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>요소

| 요소                    | Description                                                                                                 | 필수                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| send-one-way-request       | 루트 요소입니다.                                                                                               | 예                             |
| url                        | 요청의 URL입니다.                                                                                     | mode=copy인 경우 아니요이고 그렇지 않은 경우 예입니다. |
| method                     | 요청에 대한 HTTP 메서드입니다.                                                                            | mode=copy인 경우 아니요이고 그렇지 않은 경우 예입니다. |
| header                     | 요청 헤더. 여러 요청 헤더에 여러 헤더 요소를 사용합니다.                                  | 아니요                              |
| 본문                       | 요청 본문.                                                                                           | 아니요                              |
| 인증-인증서 | [클라이언트 인증에 사용할 인증서](api-management-authentication-policies.md#ClientCertificate) | 아니요                              |

### <a name="attributes"></a>특성

| 특성     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | 새 요청인지 현재 요청의 복사본인지 여부를 결정합니다. 아웃바운드 모드에서 mode=copy는 요청 본문을 초기화하지 않습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | 아니요       | 새로 만들기      |
| name          | 설정할 헤더의 이름을 지정합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | 예      | 해당 없음      |
| exists-action | 헤더가 이미 지정되어 있는 경우 수행할 작업을 지정합니다. 이 특성에는 다음 값 중 하나가 있어야 합니다.<br /><br /> -override-기존 헤더 값을 바꿉니다.<br />-skip-기존 헤더 값을 바꾸지 않습니다.<br />-append-기존 헤더 값에 값을 추가 합니다.<br />-delete-요청에서 헤더를 제거 합니다.<br /><br /> `override`로 설정할 때 동일한 이름의 여러 항목을 등록하면 모든 항목(여러 번 나열됨)에 따라 헤더가 설정되며, 나열된 값만 결과에 설정됩니다. | 아니요       | override |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** 모든 범위

## <a name="send-request"></a><a name="SendRequest"></a> 요청 전송

`send-request` 정책은 지정된 URL에 대한 제공된 응답을 설정된 시간 초과 값 이상으로 기다리지 않고 요청을 보냅니다.

### <a name="policy-statement"></a>정책 문

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>예제

이 예에서는 권한 부여 서버에서 참조 토큰을 확인하는 한 가지 방법을 보여 줍니다. 이 샘플에 대한 자세한 내용은 [Azure API Management 서비스에서 외부 서비스 사용](./api-management-sample-send-request.md)을 참조하세요.

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>요소

| 요소                    | Description                                                                                                 | 필수                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| send-request               | 루트 요소입니다.                                                                                               | 예                             |
| url                        | 요청의 URL입니다.                                                                                     | mode=copy인 경우 아니요이고 그렇지 않은 경우 예입니다. |
| method                     | 요청에 대한 HTTP 메서드입니다.                                                                            | mode=copy인 경우 아니요이고 그렇지 않은 경우 예입니다. |
| header                     | 요청 헤더. 여러 요청 헤더에 여러 헤더 요소를 사용합니다.                                  | 아니요                              |
| 본문                       | 요청 본문.                                                                                           | 아니요                              |
| 인증-인증서 | [클라이언트 인증에 사용할 인증서](api-management-authentication-policies.md#ClientCertificate) | 아니요                              |

### <a name="attributes"></a>특성

| 특성                       | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | 새 요청인지 현재 요청의 복사본인지 여부를 결정합니다. 아웃바운드 모드에서 mode=copy는 요청 본문을 초기화하지 않습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | 아니요       | 새로 만들기      |
| response-variable-name="string" | 응답 개체를 받을 컨텍스트 변수의 이름입니다. 변수가 없는 경우 정책이 성공적으로 실행 될 때 생성 되며 컬렉션을 통해 액세스할 수 있게 됩니다 [`context.Variable`](api-management-policy-expressions.md#ContextVariables) .                                                                                                                                                                                                                                                                                                                          | 예      | 해당 없음      |
| timeout="integer"               | URL 호출이 실패하는 시간 초과 간격(초)입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | 아니요       | 60       |
| ignore-error                    | true인 경우 요청 결과 오류가 발생합니다.<br /><br /> -응답이 지정 된 경우 null 값을 포함 하는 변수 이름입니다.<br />-응답-변수 이름이 지정 되지 않은 경우 컨텍스트입니다. 요청은 업데이트 되지 않습니다.                                                                                                                                                                                                                                                                                                                                                                                   | 아니요       | false    |
| name                            | 설정할 헤더의 이름을 지정합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | 예      | 해당 없음      |
| exists-action                   | 헤더가 이미 지정되어 있는 경우 수행할 작업을 지정합니다. 이 특성에는 다음 값 중 하나가 있어야 합니다.<br /><br /> -override-기존 헤더 값을 바꿉니다.<br />-skip-기존 헤더 값을 바꾸지 않습니다.<br />-append-기존 헤더 값에 값을 추가 합니다.<br />-delete-요청에서 헤더를 제거 합니다.<br /><br /> `override`로 설정할 때 동일한 이름의 여러 항목을 등록하면 모든 항목(여러 번 나열됨)에 따라 헤더가 설정되며, 나열된 값만 결과에 설정됩니다. | 아니요       | override |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** 모든 범위

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a> HTTP 프록시 설정

`proxy` 정책은 HTTP 프록시를 통해 백 엔드에 전달된 요청을 라우팅하도록 허용합니다. 게이트웨이와 프록시 간에 HTTP(HTTPS 아님)만 지원됩니다. 기본 및 NTLM 인증만 해당됩니다.

### <a name="policy-statement"></a>정책 문

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>예제

사용자 이름 및 암호의 값으로 [속성](api-management-howto-properties.md)을 사용하면 정책 문서에 중요한 정보를 저장하지 않도록 합니다.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>요소

| 요소 | Description  | 필수 |
| ------- | ------------ | -------- |
| proxy   | 루트 요소 | 예      |

### <a name="attributes"></a>특성

| 특성         | Description                                            | 필수 | 기본값 |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="문자열"      | http://host:port 형식의 프록시 URL입니다.             | 예      | 해당 없음     |
| 사용자 이름="문자열" | 프록시 인증에 사용할 사용자 이름입니다. | 아니요       | 해당 없음     |
| 암호="문자열" | 프록시 인증에 사용할 암호입니다. | 아니요       | 해당 없음     |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

## <a name="set-request-method"></a><a name="SetRequestMethod"></a> 요청 메서드 설정

`set-method` 정책을 통해 요청에 대한 HTTP 메서드를 변경할 수 있습니다.

### <a name="policy-statement"></a>정책 문

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>예제

이 샘플 정책은 `set-method` 정책을 사용하며 HTTP 응답 코드가 500 이상인 경우 Slack 대화방에 메시지를 보내는 예를 보여줍니다. 이 샘플에 대한 자세한 내용은 [Azure API Management 서비스에서 외부 서비스 사용](./api-management-sample-send-request.md)을 참조하세요.

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>요소

| 요소    | Description                                                       | 필수 |
| ---------- | ----------------------------------------------------------------- | -------- |
| set-method | 루트 요소입니다. 이 요소 값은 HTTP 메서드를 지정합니다. | 예      |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, on-error

-   **정책 범위:** 모든 범위

## <a name="set-status-code"></a><a name="SetStatus"></a> 상태 코드 설정

`set-status` 정책은 HTTP 상태 코드를 지정된 값으로 설정합니다.

### <a name="policy-statement"></a>정책 문

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>예제

이 예에서는 인증 토큰이 유효하지 않은 경우 401 응답을 반환하는 방법을 보여 줍니다. 자세한 내용은 [Azure API Management 서비스에서 외부 서비스 사용](./api-management-sample-send-request.md)을 참조하세요.

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>요소

| 요소    | Description   | 필수 |
| ---------- | ------------- | -------- |
| set-status | 루트 요소입니다. | 예      |

### <a name="attributes"></a>특성

| 특성       | Description                                                | 필수 | 기본값 |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="integer"  | 반환할 HTTP 상태 코드입니다.                            | 예      | 해당 없음     |
| reason="string" | 상태 코드를 반환하는 이유에 대한 설명입니다. | 예      | 해당 없음     |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** outbound, backend, on-error
-   **정책 범위:** 모든 범위

## <a name="set-variable"></a><a name="set-variable"></a> 변수 설정

`set-variable` 정책은 [컨텍스트](api-management-policy-expressions.md#ContextVariables) 변수를 선언하고 [식](api-management-policy-expressions.md) 또는 문자열 리터럴을 통해 지정된 값을 할당합니다. 식에 리터럴이 포함된 경우 리터럴은 문자열로 변환되고 값 형식은 `System.String`이 됩니다.

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a> 정책 문

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a> 예

다음 예는 인바운드 섹션에 변수 설정 정책이 있는 것을 보여 줍니다. 이 set-variable 정책은 `isMobile` 요청 헤더에 `User-Agent` 또는 `iPad` 텍스트가 있으면 true로 설정되는 `iPhone` Boolean [컨텍스트](api-management-policy-expressions.md#ContextVariables) 변수를 만듭니다.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPad") || context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPhone"))" />
```

### <a name="elements"></a>요소

| 요소      | Description   | 필수 |
| ------------ | ------------- | -------- |
| set-variable | 루트 요소입니다. | 예      |

### <a name="attributes"></a>특성

| 특성 | Description                                                              | 필수 |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | 변수의 이름입니다.                                                | 예      |
| 값     | 변수의 값입니다. 식 또는 리터럴 값일 수 있습니다. | 예      |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error
-   **정책 범위:** 모든 범위

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a> 허용 형식

`set-variable` 정책에 사용된 식은 다음 기본 형식 중 하나를 반환해야 합니다.

-   System.Boolean
-   System.SByte
-   System.Byte
-   System.UInt16
-   System.UInt32
-   System.UInt64
-   System.Int16
-   System.Int32
-   System.Int64
-   System.Decimal
-   System.Single
-   System.Double
-   System.Guid
-   System.String
-   System.Char
-   System.DateTime
-   System.TimeSpan
-   System.Byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   System.Decimal?
-   System.Single?
-   System.Double?
-   System.Guid?
-   System.String?
-   System.Char?
-   System.DateTime?

## <a name="trace"></a><a name="Trace"></a> 추적

`trace`정책은 API 검사기 출력, Application Insights 원격 분석 및/또는 리소스 로그에 사용자 지정 추적을 추가 합니다.

-   이 정책은 추적이 트리거될 때 [API 검사기](./api-management-howto-api-inspector.md) 출력에 사용자 지정 추적을 추가 합니다. 즉, `Ocp-Apim-Trace` 요청 헤더가 있고 true로 설정 되어 있으며 `Ocp-Apim-Subscription-Key` 요청 헤더가 있고 추적을 허용 하는 유효한 키를 보유 합니다.
-   정책은 [Application Insights 통합](./api-management-howto-app-insights.md) 이 [](../azure-monitor/app/data-model-trace-telemetry.md) 사용 하도록 설정 되어 있고 `severity` 정책에 지정 된이 `verbosity` 진단 설정에 지정 된 값 보다 크거나 같은 경우 Application Insights에서 추적 원격 분석을 만듭니다.
-   [리소스 로그](./api-management-howto-use-azure-monitor.md#activity-logs) 를 사용 하도록 설정 하 고 정책에 지정 된 심각도 수준이 진단 설정에 지정 된 세부 정보 표시 수준 보다 높거나 높은 경우 정책이 로그 항목에 속성을 추가 합니다.

### <a name="policy-statement"></a>정책 문

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a> 예

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>요소

| 요소  | Description                                                                                                                                          | 필수 |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| 추적    | 루트 요소입니다.                                                                                                                                        | 예      |
| message  | 로깅할 문자열이 나 식입니다.                                                                                                                 | 예      |
| metadata | Application Insights [추적](../azure-monitor/app/data-model-trace-telemetry.md) 원격 분석에 사용자 지정 속성을 추가 합니다. | 아니요       |

### <a name="attributes"></a>특성

| 특성 | Description                                                                                                               | 필수 | 기본값 |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | 추적 뷰어에 의미있고 메시지 원본을 지정하는 문자열 리터럴입니다.                                   | 예      | 해당 없음     |
| severity  | 추적의 심각도 수준을 지정 합니다. 허용 되는 값은 `verbose` , `information` , `error` (가장 낮은 값에서 가장 높은 값)입니다. | 아니요       | 자세히 |
| name      | 속성의 이름입니다.                                                                                                     | 예      | 해당 없음     |
| 값     | 속성의 값입니다.                                                                                                    | 예      | 해당 없음     |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** 모든 범위

## <a name="wait"></a><a name="Wait"></a> 대기한

`wait` 정책은 직계 자식 정책을 병렬로 실행하고 정책이 완료되기 전에 직계 자식 정책 전체 또는 하나가 완료될 때까지 대기합니다. 대기 정책은 직계 자식 정책으로 [요청 전송](api-management-advanced-policies.md#SendRequest), [캐시에서 값 가져오기](api-management-caching-policies.md#GetFromCacheByKey), [제어 흐름](api-management-advanced-policies.md#choose) 정책을 포함할 수 있습니다.

### <a name="policy-statement"></a>정책 문

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>예제

다음 예에는 `wait` 정책의 직계 자식 정책으로 두 개의 `choose` 정책이 있습니다. 이러한 각 `choose` 정책이 병렬로 실행됩니다. 각 `choose` 정책은 캐시된 값의 검색을 시도합니다. 캐시가 누락된 경우 값을 제공하기 위해 백 엔드 서비스가 호출됩니다. 이 예에서는 `for` 특성이 `all`로 설정되어 있으므로 `wait` 정책은 모든 직계 자식 정책이 완료되어야 완료됩니다. 이 예에서 컨텍스트 변수 (`execute-branch-one`, `value-one`, `execute-branch-two`, `value-two`)는 이 예제 정책 범위 밖에 선언됩니다.

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>요소

| 요소 | Description                                                                                                   | 필수 |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| wait    | 루트 요소입니다. 자식 요소로 `send-request`, `cache-lookup-value`, `choose` 정책만 포함할 수 있습니다. | 예      |

### <a name="attributes"></a>특성

| 특성 | Description                                                                                                                                                                                                                                                                                                                                                                                                            | 필수 | 기본값 |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| for       | `wait` 정책에서 모든 직계 자식 정책 또는 한 정책이 완료될 때까지 대기할지 여부를 결정합니다. 허용된 값은 다음과 같습니다.<br /><br /> - `all` - 모든 직계 자식 정책이 완료될 때까지 대기합니다.<br />-any-모든 직계 자식 정책이 완료 될 때까지 대기 합니다. 첫 번째 직계 자식 정책이 완료되면 `wait` 정책이 완료되고 다른 직계 자식 정책의 실행이 종료됩니다. | 아니요       | 모두     |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend
-   **정책 범위:** 모든 범위

## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

-   [API Management의 정책](api-management-howto-policies.md)
-   [정책 식](api-management-policy-expressions.md)
-   [정책 참조](./api-management-policies.md)(정책 문 및 해당 설정에 대한 전체 목록)
-   [정책 샘플](./policy-reference.md)
