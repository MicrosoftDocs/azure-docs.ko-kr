---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 40d9f03526e5232c0a7b33f64ff35a8501702609
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107749"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 `User Access Token`입니다. [`User Access Token`를 가져오는 방법](../../access-tokens.md)에 대한 자세한 정보
- 선택 사항: [애플리케이션에 통화 추가 시작](../getting-started-with-calling.md)에 대한 빠른 시작을 완료합니다.

## <a name="setting-up"></a>설치

### <a name="install-the-package"></a>패키지 설치

> [!NOTE]
> 이 문서에서는 호출 SDK의 버전 1.0.0-beta. 8을 사용 합니다.

프로젝트 수준 build.gradle을 찾고, `mavenCentral()`을 `buildscript` 및 `allprojects` 아래의 리포지토리 목록에 추가해야 합니다.
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
그런 다음, 모듈 수준 build.gradle에서 dependencies 섹션에 다음 줄을 추가합니다.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}

```

## <a name="object-model"></a>개체 모델

다음 클래스와 인터페이스는 SDK를 호출 하는 Azure Communication Services의 주요 기능 중 일부를 처리 합니다.

| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient는 호출 SDK의 기본 진입점입니다.|
| CallAgent | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다. |
| CommunicationTokenCredential | CommunicationTokenCredential은 CallAgent를 인스턴스화하기 위한 토큰 자격 증명으로 사용됩니다.|
| CommunicationIdentifier | CommunicationIdentifier는 통화에 포함될 수 있는 다른 유형의 참가자로 사용됩니다.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>CallClient 초기화, CallAgent 만들기 및 DeviceManager 액세스

`CallAgent` 인스턴스를 만들려면 `CallClient` 인스턴스에서 `createCallAgent` 메서드를 호출해야 합니다. 이렇게 하면 `CallAgent` 인스턴스 개체가 비동기적으로 반환됩니다.
`createCallAgent` 메서드는 `CommunicationUserCredential`을 인수로 사용하여 [액세스 토큰](../../access-tokens.md)을 캡슐화합니다.
`DeviceManager`에 액세스하려면 먼저 callAgent 인스턴스를 만든 다음, `CallClient.getDeviceManager` 메서드를 사용하여 DeviceManager를 가져오면 됩니다.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```
호출자의 표시 이름을 설정하려면 다음과 같은 대체 방법을 사용합니다.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>발신 전화를 걸고 그룹 통화에 참가

통화를 만들고 시작하려면 `CallAgent.startCall()` 메서드를 호출하고 호출 수신자의 `Identifier`를 제공해야 합니다.
그룹 통화에 참가하려면 `CallAgent.join()` 메서드를 호출하고 groupId를 제공해야 합니다. 그룹 ID는 GUID 또는 UUID 형식이어야 합니다.

통화 만들기와 시작은 동기적입니다. 통화 인스턴스를 사용하여 통화의 모든 이벤트를 구독할 수 있습니다.

### <a name="place-a-11-call-to-a-user"></a>사용자에게 1:1 전화 걸기
다른 Communication Services 사용자에게 전화를 걸려면 `callAgent`에서 `call` 메서드를 호출하고 `communicationUserId` 키를 사용하여 개체를 전달합니다.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>사용자 및 PSTN과 1:n 전화 걸기
> [!WARNING]
> 현재는 PSTN 통화를 사용할 수 없습니다.

사용자 및 PSTN 번호로 1:n 전화를 걸려면 호출 수신자의 전화 번호를 지정해야 합니다.
PSTN 통화를 허용하도록 Communication Services 리소스를 구성해야 합니다.
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>비디오 카메라로 1:1 전화 걸기
> [!WARNING]
> 현재는 발신 로컬 비디오 스트림 하나만 지원됩니다. 비디오를 통해 전화를 걸려면 `deviceManager` `getCameras` API를 사용하여 로컬 카메라를 열거해야 합니다.
원하는 카메라를 선택한 후에는 해당 카메라를 사용하여 `LocalVideoStream` 인스턴스를 생성하고 `call` 메서드에 대한 `localVideoStream` 배열의 항목으로 `videoOptions`에 전달합니다.
전화가 연결되면 선택한 카메라에서 다른 참가자로 비디오 스트림을 자동으로 보내기 시작합니다.

> [!NOTE]
> 개인 정보 보호 문제 때문에 비디오를 로컬로 미리 보지 않는 경우에는 비디오가 통화에서 공유되지 않습니다.
자세한 내용은 [로컬 카메라 미리 보기](#local-camera-preview)를 참조하세요.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>그룹 통화 참가
새 그룹 통화를 시작하거나 진행 중인 그룹 통화에 참가하려면 'join' 메서드를 호출하고 `groupId` 속성을 통해 개체를 전달해야 합니다. 값은 GUID여야 합니다.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>통화 수락
통화를 수락하려면 통화 개체에서 'accept' 메서드를 호출합니다.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

비디오 카메라를 켠 상태에서 통화를 수락하려면 다음을 수행합니다.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

수신 통화는 `callAgent` 개체에서 `onIncomingCall` 이벤트를 구독하여 가져올 수 있습니다.

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>푸시 알림

### <a name="overview"></a>개요
모바일 푸시 알림은 모바일 디바이스에 표시되는 팝업 알림입니다. 통화에서는 VoIP(Voice over Internet Protocol) 푸시 알림을 집중적으로 다루겠습니다. 푸시 알림을 등록하고 푸시 알림을 처리한 다음, 푸시 알림을 등록 취소하겠습니다.

### <a name="prerequisites"></a>사전 요구 사항

Cloud Messaging(FCM)을 사용하도록 설정되었으며 Firebase Cloud Messaging 서비스가 Azure Notification Hub 인스턴스에 연결된 Firebase 계정 자세한 내용은 [Communication Services 알림](../../../concepts/notifications.md)을 참조하세요.
또한 이 자습서에서는 Android Studio 버전 3.6 이상을 사용하여 애플리케이션을 빌드하는 것으로 가정합니다.

Firebase Cloud Messaging 서비스에서 알림 메시지를 받으려면 Android 애플리케이션에 대한 권한 세트가 필요합니다. `AndroidManifest.xml` 파일에서 *<manifest ...>* 바로 뒤에 또는 *</application>* 태그 바로 아래에 다음 권한 세트를 추가합니다.

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>푸시 알림 등록

푸시 알림을 등록하려면 애플리케이션이 디바이스 등록 토큰을 사용하여 *CallAgent* 인스턴스에서 `registerPushNotification()`을 호출해야 합니다.

장치 등록 토큰을 가져오려면 섹션에 다음 줄을 추가 하 여 Firebase SDK를 응용 프로그램 모듈의 *gradle* 파일에 추가 합니다 ( `dependencies` 아직 없는 경우).

```
    // Add the SDK for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

프로젝트 수준의 *build.gradle* 파일에서 다음을 `dependencies` 섹션에 추가합니다(아직 없는 경우).

```
    classpath 'com.google.gms:google-services:4.3.3'
```

다음 플러그 인이 아직 없는 경우 파일 첫 부분에 추가합니다.

```
apply plugin: 'com.google.gms.google-services'
```

도구 모음에서 *지금 동기화* 를 선택합니다. 다음 코드 조각을 추가 하 여 클라이언트 응용 프로그램 인스턴스에 대 한 Firebase Cloud Messaging SDK에서 생성 된 장치 등록 토큰을 가져옵니다. 인스턴스에 대 한 주 활동의 헤더에 아래 가져오기를 추가 해야 합니다. 코드 조각이 토큰을 검색하는 데 필요합니다.

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

다음 코드 조각을 추가하여 토큰을 검색합니다.

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
들어오는 통화 푸시 알림에 대 한 호출 서비스 SDK에 장치 등록 토큰을 등록 합니다.

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>푸시 알림 처리

수신 통화 푸시 알림을 받으려면 페이로드가 있는 *CallAgent* 인스턴스에서 *handlePushNotification()* 을 호출합니다.

Firebase 클라우드 메시징에서 페이로드를 가져오려면 *FirebaseMessagingService* Firebase SDK 클래스를 확장 하 고 메서드를 재정의 하는 새 서비스 (파일 > > 새 서비스 > 서비스)를 만들어 시작 `onMessageReceived` 합니다. 이 메서드는 Firebase Cloud Messaging에서 애플리케이션에 푸시 알림을 전달할 때 호출되는 이벤트 처리기입니다.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
`AndroidManifest.xml` 파일의 <application> 태그 내부에 다음 서비스 정의를 추가합니다.

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- 페이로드를 검색 한 후에는 *Callagent* 인스턴스에서 *handlepushnotification* 메서드를 호출 하 여 처리할 내부 *IncomingCallInformation* 개체로 구문 분석 되도록 *통신 서비스* SDK에 전달할 수 있습니다. `CallAgent` 인스턴스는 `CallClient` 클래스에서 `createCallAgent(...)` 메서드를 호출하여 만듭니다.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

푸시 알림 메시지가 성공적으로 처리되고 모든 이벤트 처리기가 제대로 등록되면 애플리케이션이 전화를 겁니다.

### <a name="unregister-push-notifications"></a>푸시 알림 등록 취소

애플리케이션에서 언제든지 푸시 알림을 등록 취소할 수 있습니다. 등록을 취소하려면 callAgent에서 `unregisterPushNotification()` 메서드를 호출합니다.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>통화 관리
통화 중에 통화 속성에 액세스하여 비디오 및 오디오와 관련된 설정을 관리하는 다양한 작업을 수행할 수 있습니다.

### <a name="call-properties"></a>통화 속성

다음과 같이 이 통화의 고유 ID를 가져옵니다.

```java
String callId = call.getId();
```

통화의 다른 참가자에 대해 알아보려면 `call` 인스턴스의 `remoteParticipant` 컬렉션을 검사합니다.

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

수신 전화가 걸려 올 때의 호출자 ID입니다.

```java
CommunicationIdentifier callerId = call.getCallerId();
```

다음과 같이 통화의 상태를 가져옵니다. 

```java
CallState callState = call.getState();
```

다음과 같은 통화의 현재 상태를 나타내는 문자열을 반환합니다.
* 'None' - 초기 통화 상태입니다.
* 'Connecting' - 전화를 걸었거나 수락되었을 때의 초기 전환 상태입니다.
* 'Ringing' - 발신 통화의 경우 원격 참가자의 전화 벨이 울리고 있다는 뜻입니다.
* 'EarlyMedia' - 전화가 연결되기 전에 알림이 재생되는 상태를 나타냅니다.
* 'Connected' - 전화가 연결되었습니다.
* 'LocalHold' - 로컬 참가자가 전화를 대기 상태로 설정했으며, 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
* 'RemoteHold' - 원격 참가자가 전화를 대기 상태로 설정했으며, 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
* 'Disconnecting' - 통화가 'Disconnected' 상태로 전환되기 전의 상태입니다.
* 'Disconnected' - 최종 통화 상태입니다.


통화가 종료된 이유를 알아보려면 `callEndReason` 속성을 검사합니다. 이 속성에는 다음과 같은 코드/하위 코드가 포함되어 있습니다. 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

현재 통화가 수신 통화인지 아니면 발신 통화인지 알아보려면 `callDirection` 속성을 검사합니다.

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.Incoming for incoming call
// callDirection == CallDirection.Outgoing for outgoing call
```

현재 마이크가 음소거되었는지 확인하려면 `muted` 속성을 검사합니다.

```java
boolean muted = call.getIsMicrophoneMuted();
```

현재 통화가 녹음되고 있는지 확인하려면 `isRecordingActive` 속성을 검사합니다.

```java
boolean recordinggActive = call.getIsRecordingActive();
```

활성 비디오 스트림을 검사하려면 `localVideoStreams` 컬렉션을 검사합니다.

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 엔드포인트를 음소거하거나 음소거를 해제하려면 다음과 같이 `mute` 및 `unmute` 비동기 API를 사용합니다.

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지

비디오를 시작하려면 `deviceManager` 개체의 `getCameraList` API를 사용하여 카메라를 열거해야 합니다. 그런 다음, 원하는 카메라를 전달하는 새 `LocalVideoStream` 인스턴스를 만들고, 이 인스턴스를 `startVideo` API에서 인수로 전달합니다.

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(currentLocalVideoStream);
startVideoFuture.get();
```

비디오 전송을 성공적으로 시작하면 호출 인스턴스의 `localVideoStreams` 컬렉션에 `LocalVideoStream` 인스턴스가 추가됩니다.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

로컬 비디오를 중지하려면 `LocalVideoStream` 컬렉션에서 사용할 수 있는 `localVideoStreams` 인스턴스를 전달합니다.

```java
call.stopVideo(currentLocalVideoStream).get();
```

`LocalVideoStream` 인스턴스에서 `switchSource`를 호출하여 비디오가 전송되는 동안 다른 카메라 디바이스로 전환할 수 있습니다.
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-participants-in-a-call"></a>통화 참가자 나열
`remoteParticipants` 컬렉션은 특정 통화의 원격 참가자 목록을 반환합니다.
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>원격 참가자 속성
특정 원격 참가자와 연결된 여러 가지 속성 및 컬렉션이 있습니다.

* 이 원격 참가자의 식별자를 가져옵니다.
ID는 'Identifier' 형식 중 하나입니다.
```java
CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
```

* 이 원격 참가자의 상태를 가져옵니다.
```java
ParticipantState state = remoteParticipant.getState();
```
상태는 다음 중 하나입니다.
* 'Idle' - 초기 상태입니다.
* 'EarlyMedia' - 참가자가 통화에 연결되기 전에 알림이 재생됩니다.
* 'Ringing' - 참가자의 전화벨이 울리고 있습니다.
* 'Connecting' - 참가자가 통화에 연결하는 동안의 전환 상태입니다.
* 'Connected' - 참가자가 전화에 연결되었습니다.
* 'Hold' - 참가자가 보류 중입니다.
* 'InLobby' - 참가자가 로비에서 참석 대기 중입니다. 현재는 Teams interop 시나리오에만 사용됩니다.
* 'Disconnected' - 최종 상태입니다. 참가자의 통화 연결이 끊겼습니다.


* 참가자가 통화를 종료한 이유를 알아보려면 `callEndReason` 속성을 검사합니다.
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* 이 원격 참가자가 음소거되었는지 확인하려면 `isMuted` 속성을 검사합니다.
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* 이 원격 참가자가 말하는 중인지 확인하려면 `isSpeaking` 속성을 검사합니다.
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* 특정 참가자가 이 통화에서 보내는 모든 비디오 스트림을 검사하려면 `videoStreams` 컬렉션을 확인합니다.
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>통화에 참가자 추가

통화에 참가자(사용자 또는 전화 번호)를 추가하려면 `addParticipant`를 호출합니다. 그러면 원격 참가자 인스턴스가 동기적으로 반환됩니다.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>통화에서 참가자 제거
통화에서 참가자(사용자 또는 전화 번호)를 제거하려면 `removeParticipant`를 호출합니다.
그러면 통화에서 참가자가 제거될 때 이를 비동기적으로 확인합니다.
참가자도 `remoteParticipants` 컬렉션에서 제거됩니다.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

## <a name="render-remote-participant-video-streams"></a>원격 참가자 비디오 스트림 렌더링
원격 참가자의 비디오 스트림과 화면 공유 스트림을 나열하려면 `videoStreams` 컬렉션을 검사합니다.
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
원격 참가자의 `RemoteVideoStream`을 렌더링하려면 `OnVideoStreamsUpdated` 이벤트를 구독해야 합니다.

이 이벤트 내에서 `isAvailable` 속성이 true로 변경되면 원격 참가자가 현재 스트림을 보내고 있다는 뜻입니다. 이 경우 새 `Renderer` 인스턴스를 만든 다음, 비동기 `createView` API를 사용하여 새 `RendererView`를 만들고 애플리케이션의 UI 내 아무 위치에나 `view.target`을 연결합니다.

원격 스트림의 가용성이 변경될 때마다 전체 렌더러, 즉, 특정 `RendererView`를 제거하거나 유지하도록 선택할 수 있지만, 이 경우 빈 비디오 프레임이 표시됩니다.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>원격 비디오 스트림 속성
원격 비디오 스트림에는 몇 가지 속성이 있습니다.

* `Id` - 원격 비디오 스트림의 ID
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` - 'Video' 또는 'ScreenSharing' 중 하나
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` - 원격 참가자 엔드포인트가 스트림을 능동적으로 전송하고 있는지 여부를 표시
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>렌더러 메서드 및 속성
API를 따르는 렌더러 개체

* 나중에 애플리케이션 UI에서 연결하여 원격 비디오 스트림을 렌더링할 수 있는 `RendererView` 인스턴스를 만듭니다.
```java
// Create a view for a video stream
renderer.createView()
```
* 렌더러 및 이 렌더러와 연결된 모든 `RendererView`를 삭제합니다. UI에서 연결된 모든 보기를 제거할 때 호출됩니다.
```java
renderer.dispose()
```

* `StreamSize` - 원격 비디오 스트림의 크기(너비/높이)
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>RendererView 메서드 및 속성
`RendererView`를 만들 때 이 보기에 적용되는 `scalingMode` 및 `mirrored` 속성을 지정할 수 있습니다. 크기 조정 모드는 '늘이기' | '자르기' | '맞춤'으로 설정할 수 있습니다. `mirrored`를 `true`로 설정하면 렌더링된 스트림이 상하로 대칭 이동됩니다.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

이렇게 만든 RendererView는 다음 코드 조각을 사용하여 애플리케이션 UI에 연결할 수 있습니다.
```java
layout.addView(rendererView);
```

나중에 RendererView 개체에서 ScalingMode.Stretch | ScalingMode.Crop | ScalingMode.Fit 중 하나를 인수로 사용하고 `updateScalingMode` API를 호출하여 크기 조정 모드를 업데이트할 수 있습니다.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>디바이스 관리

`DeviceManager`를 사용하여 오디오/비디오 스트림을 전송하는 호출에 사용 가능한 로컬 디바이스를 열거할 수 있습니다. 또한 네이티브 브라우저 API를 사용하여 사용자의 마이크 및 카메라에 액세스할 수 있는 권한을 요청할 수 있습니다.

`callClient.getDeviceManager()` 메서드를 호출하여 `deviceManager`에 액세스할 수 있습니다.
> [!WARNING]
> 현재는 DeviceManager에 대한 액세스 권한을 얻으려면 먼저 `callAgent` 개체를 인스턴스화해야 합니다.

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>로컬 디바이스 열거

로컬 디바이스에 액세스하려면 디바이스 관리자에서 열거 메서드를 사용합니다. 열거는 비동기 작업입니다.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>기본 마이크/스피커 설정

디바이스 관리자를 사용하여 호출을 시작할 때 사용할 기본 디바이스를 설정할 수 있습니다.
클라이언트 기본값을 설정하지 않으면 Communication Services는 OS 기본값으로 대체됩니다.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophones().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakers().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>로컬 카메라 미리 보기

`DeviceManager` 및 `Renderer`를 사용하여 로컬 카메라에서 스트림 렌더링을 시작할 수 있습니다. 이 스트림은 다른 참가자에게 전송되지 않는 로컬 미리 보기 피드입니다. 비동기 작업입니다.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>이벤트 모델
값이 변경될 때 알림을 받도록 대부분의 속성 및 컬렉션을 구독할 수 있습니다.

### <a name="properties"></a>속성
`property changed` 이벤트를 구독하는 방법은 다음과 같습니다.

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>컬렉션
`collection updated` 이벤트를 구독하는 방법은 다음과 같습니다.

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
