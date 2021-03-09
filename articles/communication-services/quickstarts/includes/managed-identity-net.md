---
ms.openlocfilehash: 11b10817959a390b4ea0215d72f97513a6b23345
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486594"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>통신 서비스 솔루션에 관리 되는 id 추가 (.NET)

### <a name="install-the-client-library-packages"></a>클라이언트 라이브러리 패키지 설치

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>클라이언트 라이브러리 패키지 사용

`using`Azure id 및 Azure Storage 클라이언트 라이브러리를 사용 하는 다음 지시문을 코드에 추가 합니다.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

아래 예제에서는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)를 사용 합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합 합니다.

`AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` 및 `AZURE_TENANT_ID` 환경 변수는 개체를 만드는 데 필요 `DefaultAzureCredential` 합니다. 개발 환경에서 등록 된 응용 프로그램을 만들고 환경 변수를 설정 하려면 [관리 id를 사용 하 여 액세스 권한 부여](../managed-identity-from-cli.md)를 참조 하세요.

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Id 만들기 및 관리 Id를 사용 하 여 토큰 발급

다음 코드 예제에서는 Azure Active Directory 토큰을 사용 하 여 서비스 클라이언트 개체를 만드는 방법을 보여 줍니다.

그런 다음 클라이언트를 사용 하 여 새 사용자에 대 한 토큰을 발급 합니다.

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>관리 Id를 사용 하 여 SMS 보내기

다음 코드 예제에서는 관리 되는 id를 사용 하 여 SMS 서비스 클라이언트 개체를 만든 다음 클라이언트를 사용 하 여 SMS 메시지를 보내는 방법을 보여 줍니다.

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

