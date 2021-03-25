---
ms.openlocfilehash: 3ec650f82b95cdfabc923e92325959c63f2846de
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110755"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>관리 되는 id를 통신 서비스 솔루션 (JS)에 추가

### <a name="install-the-sdk-packages"></a>SDK 패키지 설치

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-sdk-packages"></a>SDK 패키지 사용

`import`Azure id 및 Azure Storage sdk를 사용 하려면 다음 지시문을 코드에 추가 합니다.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

아래 예제에서는 [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential)를 사용 합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합 합니다.

개발 환경에 응용 프로그램을 등록 하 고 환경 변수를 설정 하려면 [관리 id를 사용 하 여 액세스 권한 부여](../managed-identity-from-cli.md) 를 참조 하세요.  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Id 만들기 및 관리 Id를 사용 하 여 토큰 발급

다음 코드 예제에서는 관리 되는 id를 사용 하 여 서비스 클라이언트 개체를 만든 다음 클라이언트를 사용 하 여 새 사용자에 대 한 토큰을 발급 하는 방법을 보여 줍니다.

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>관리 Id를 사용 하 여 SMS 보내기

다음 코드 예제에서는 관리 되는 id를 사용 하 여 서비스 클라이언트 개체를 만든 다음 클라이언트를 사용 하 여 SMS 메시지를 보내는 방법을 보여 줍니다.

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = { 
          from: fromNumber, 
          to: [toNumber], 
          message: message 
     };

     const response = await smsClient.send(
          sendRequest, 
          {} //Optional SendOptions
          );
}
```

