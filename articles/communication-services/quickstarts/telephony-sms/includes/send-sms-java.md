---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: 76aae596c145c736ae75e65f7f72fdbdcead5919
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779058"
---
Communication Services Java SMS 클라이언트 라이브러리를 사용하여 SMS 메시지를 보내 Azure Communication Services를 시작하세요.

이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK(Java Development Kit)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).
- 활성 Communication Services 리소스 및 연결 문자열. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- SMS 지원 전화 번호입니다. [전화 번호를 가져옵니다](../get-phone-number.md).

### <a name="prerequisite-check"></a>필수 구성 요소 확인

- 터미널 또는 명령 창에서 `mvn -v`를 실행하여 maven이 설치되어 있는지 확인합니다.
- Communication Services 리소스와 연결된 전화 번호를 보려면 [Azure Portal](https://portal.azure.com/)에 로그인하고 Communication Services 리소스를 찾아 왼쪽 탐색 창에서 **전화 번호** 탭을 엽니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-java-application"></a>새 Java 애플리케이션 만들기

터미널 또는 명령 창을 열고 Java 애플리케이션을 만들 디렉터리로 이동합니다. 아래 명령을 실행하여 maven-archetype-quickstart 템플릿에서 Java 프로젝트를 생성합니다.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

'생성' 목표는 artifactId와 같은 이름으로 디렉터리를 만듭니다. 이 디렉터리 아래에서 **src/main/java** 디렉터리는 프로젝트 소스 코드를 포함하고, **src/test/java 디렉터리**는 테스트 원본을 포함하고, **pom.xml** 파일은 프로젝트의 프로젝트 개체 모델 또는 POM입니다.

### <a name="install-the-package"></a>패키지 설치

텍스트 편집기에서 **pom.xml** 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.2</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

**pom.xml**에 `azure-core-http-netty` 종속성을 추가합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

텍스트 편집기에서 **/src/main/java/com/communication/quickstart/App.java**를 열고, 가져오기 지시문을 추가하고, `System.out.println("Hello world!");` 문을 제거합니다.

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.CommunicationClientCredential;
import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 Java용 Azure Communication Services SMS 클라이언트 라이브러리의 주요 기능 중 일부를 처리합니다.

| 이름                                                             | 설명                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | 이 클래스는 SmsClient를 만듭니다. 엔드포인트, 자격 증명 및 http 클라이언트를 제공합니다. |
| SmsClient                    | 이 클래스는 모든 SMS 기능에 필요합니다. SMS 메시지를 보내는 데 사용합니다.                |
| SendSmsResponse               | 이 클래스는 SMS 서비스의 응답을 포함합니다.                                          |
| CommunicationClientCredential | 이 클래스는 서명 요청을 처리합니다.                                                            |
| PhoneNumber                   | 이 클래스에는 전화 번호 정보가 있습니다.

## <a name="authenticate-the-client"></a>클라이언트 인증

연결 문자열로 `SmsClient`를 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_ENDPOINT_STRING` 및 `COMMUNICATION_SERVICES_CREDENTIAL_STRING`이라는 환경 변수에서 리소스에 대한 엔드포인트와 자격 증명 문자열을 검색합니다(자격 증명은 Azure Portal의 `Key`임). [리소스의 연결 문자열을 관리](../../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

`main` 메서드에 다음 코드를 추가합니다.

```java
// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationClientCredential credential = new CommunicationClientCredential(accessKey);

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .httpClient(httpClient)
    .buildClient();
```

`com.azure.core.http.HttpClient` 인터페이스를 구현하는 사용자 지정 HTTP 클라이언트를 사용하여 클라이언트를 초기화할 수 있습니다. 위의 코드는 `azure-core`에서 제공하는 [Azure Core Netty HTTP 클라이언트](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true)의 사용 방법을 보여줍니다.

## <a name="send-an-sms-message"></a>SMS 메시지 보내기

sendMessage 메서드를 호출하여 SMS 메시지를 보냅니다. 다음 코드를 `main` 메서드의 끝에 추가합니다.

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

`<leased-phone-number>`를 Communication Services 리소스와 연결된 SMS 지원 전화 번호로 바꾸고, `<to-phone-number>`를 메시지를 보낼 전화 번호로 바꿔야 합니다.

`enableDeliveryReport` 매개 변수는 전달 보고를 구성하는 데 사용할 수 있는 선택적 매개 변수입니다. 이 기능은 SMS 메시지가 전달될 때 이벤트를 내보내려는 시나리오에 유용합니다. SMS 메시지에 대한 전달 보고를 구성하려면 [SMS 이벤트 처리](../handle-sms-events.md)를 참조하세요.

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>코드 실행

**pom.xml** 파일이 포함된 디렉터리로 이동하고 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

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
