---
ms.openlocfilehash: b552629c23991880a2f9cfc6f9e96376daecc1a0
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103439164"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>통신 서비스 솔루션에 관리 되는 id 추가 (.NET)

### <a name="install-the-client-library-packages"></a>클라이언트 라이브러리 패키지 설치

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0-beta.5
dotnet add package Azure.Communication.Sms  --version 1.0.0-beta.4
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>클라이언트 라이브러리 패키지 사용

`using`Azure id 및 Azure Storage 클라이언트 라이브러리를 사용 하는 다음 지시문을 코드에 추가 합니다.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

아래 예제에서는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)를 사용 합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합 합니다.

`AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` 및 `AZURE_TENANT_ID` 환경 변수는 개체를 만드는 데 필요 `DefaultAzureCredential` 합니다. 개발 환경에서 등록 된 응용 프로그램을 만들고 환경 변수를 설정 하려면 [관리 id를 사용 하 여 액세스 권한 부여](../managed-identity-from-cli.md)를 참조 하세요.

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Id 만들기 및 관리 Id를 사용 하 여 토큰 발급

다음 코드 예제에서는 Azure Active Directory 토큰을 사용 하 여 서비스 클라이언트 개체를 만드는 방법을 보여 줍니다.

그런 다음 클라이언트를 사용 하 여 새 사용자에 대 한 토큰을 발급 합니다.

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>관리 Id를 사용 하 여 SMS 보내기

다음 코드 예제에서는 관리 되는 id를 사용 하 여 SMS 서비스 클라이언트 개체를 만든 다음 클라이언트를 사용 하 여 SMS 메시지를 보내는 방법을 보여 줍니다.

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

