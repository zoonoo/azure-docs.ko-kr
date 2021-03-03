---
ms.openlocfilehash: c5d83cc709c334e15a1c13e64ba17ef24835fed0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657658"
---
## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>통신 서비스 솔루션에 관리 id 추가 (Java)

### <a name="install-the-client-library-packages"></a>클라이언트 라이브러리 패키지 설치
pom.xml 파일에서 종속성 그룹에 다음 종속성 요소를 추가 합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-client-library-packages"></a>클라이언트 라이브러리 패키지 사용

`import`Azure id 및 Azure 통신 클라이언트 라이브러리를 사용 하는 다음 지시문을 코드에 추가 합니다.

```java
import com.azure.identity.*;
import com.azure.communication.sms.*;
import com.azure.communication.identity.*;
import com.azure.communication.common.*;
```

아래 예제에서는 [DefaultAzureCredential](/java/api/azure.identity.defaultazurecredential)를 사용 합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합 합니다.

`AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` 및 `AZURE_TENANT_ID` 환경 변수는 개체를 만드는 데 필요 `DefaultAzureCredential` 합니다. 개발 환경에서 등록 된 응용 프로그램을 만들고 환경 변수를 설정 하려면 [관리 id를 사용 하 여 액세스 권한 부여](../managed-identity-from-cli.md)를 참조 하세요.

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Id 만들기 및 관리 Id를 사용 하 여 토큰 발급

다음 코드 예제에서는 관리 되는 id를 사용 하 여 서비스 클라이언트 개체를 만드는 방법을 보여 줍니다.
그런 다음 클라이언트를 사용 하 여 새 사용자에 대 한 토큰을 발급 합니다.

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          AccessToken userToken = communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
          return userToken;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>관리 Id를 사용 하 여 SMS 보내기

다음 코드 예제에서는 관리 되는 id를 사용 하 여 서비스 클라이언트 개체를 만든 다음 클라이언트를 사용 하 여 SMS 메시지를 보내는 방법을 보여 줍니다.

```java
     public SendSmsResponse sendSms() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          // Send the message and check the response for a message id
          SendSmsResponse response = smsClient.sendMessage(
               new PhoneNumberIdentifier("<leased-phone-number>"),
               to,
               "your message",
               options /* Optional */
          );
          return response;
    }
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증에 대한 자세한 정보](../concepts/authentication.md)

다음을 수행할 수도 있습니다.

- [Azure 역할 기반 access control에 대 한 자세한 정보](../../../../articles/role-based-access-control/index.yml)
- [Java 용 Azure identity library에 대 한 자세한 정보](/java/api/overview/azure/identity-readme)
- [사용자 액세스 토큰 만들기](../../quickstarts/access-tokens.md)
- [SMS 메시지 보내기](../../quickstarts/telephony-sms/send.md)
- [SMS에 대한 자세한 정보](../../concepts/telephony-sms/concepts.md)
