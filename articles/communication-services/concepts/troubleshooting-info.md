---
title: Azure Communication Services의 문제 해결
description: Communication Services 솔루션 문제를 해결하는 데 필요한 정보를 수집하는 방법을 알아봅니다.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ff3e7fee87661fb89ba930b7368bd54e71ad57bf
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357626"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Azure Communication Services의 문제 해결

이 문서는 Communication Services 솔루션에서 발생할 수 있는 문제를 해결하는 데 도움이 됩니다. SMS 문제를 해결하는 경우 [Event Grid를 통해 전달 보고를 사용](../quickstarts/telephony-sms/handle-sms-events.md)하여 SMS 전달 세부 정보를 캡처할 수 있습니다.

## <a name="getting-help"></a>도움말 보기

개발자는 Communication Services [GitHub 리포지토리](https://github.com/Azure/communication)에서 질문을 제출하고, 기능을 제안하고, 문제를 보고하는 것이 좋습니다. 다른 포럼은 다음과 같습니다.

* [Microsoft Q&A](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

Azure 구독 [지원 계획](https://azure.microsoft.com/support/plans/)에 따라 [Azure Portal](https://azure.microsoft.com/support/create-ticket/)을 통해 지원 티켓을 직접 제출할 수 있습니다.

특정 유형의 문제를 해결하는 데 도움이 되도록 다음 정보 중 하나를 입력하라는 메시지가 표시될 수 있습니다.

* **MS-CV ID**: 이 ID는 호출 및 메시지 문제를 해결하는 데 사용됩니다. 
* **호출 ID**: 이 ID는 Communication Services 호출을 식별하는 데 사용됩니다.
* **SMS 메시지 ID**: 이 ID는 SMS 메시지를 식별하는 데 사용됩니다.
* **통화 로그**: 이러한 로그에는 통화 및 네트워크 문제를 해결하는 데 사용할 수 있는 자세한 정보가 포함되어 있습니다.


## <a name="access-your-ms-cv-id"></a>MS CV ID에 액세스

MS-CV ID는 클라이언트 라이브러리를 초기화할 때 `clientOptions` 개체 인스턴스에서 진단을 구성하여 액세스할 수 있습니다. 채팅, 관리 및 VoIP 호출을 비롯한 모든 Azure 클라이언트 라이브러리에 대해 진단을 구성할 수 있습니다.

### <a name="client-options-example"></a>클라이언트 옵션 예제

다음 코드 조각에서는 진단 구성을 보여줍니다. 진단이 활성화된 클라이언트 라이브러리를 사용하는 경우 진단 세부 정보가 구성된 이벤트 수신기로 내보내집니다.

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>호출 ID에 액세스

호출 문제와 관련된 Azure Portal을 통해 지원 요청을 작성하는 경우 참조하는 호출의 ID를 제공하라는 메시지가 표시될 수 있습니다. 호출 클라이언트 라이브러리를 통해 액세스할 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>SMS 메시지 ID에 액세스

SMS 문제의 경우 응답 개체에서 메시지 ID를 수집할 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>통화 로그 활성화 및 액세스




# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 코드를 사용하여 JavaScript 클라이언트 라이브러리를 통해 로그를 콘솔에 출력하도록 `AzureLogger`를 구성할 수 있습니다.

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

iOS용으로 개발할 때 로그는 `.blog` 파일에 저장됩니다. 로그는 암호화되어 있으므로 직접 볼 수 없습니다.

Xcode를 열어 액세스할 수 있습니다. Windows > 디바이스 및 시뮬레이터 > 디바이스로 이동합니다. 디바이스를 선택합니다. 설치된 앱에서 애플리케이션을 선택하고 "컨테이너 다운로드"를 클릭합니다. 

그러면 `xcappdata` 파일이 제공됩니다. 이 파일을 마우스 오른쪽 단추로 클릭하고 “패키지 내용 표시”를 선택합니다. 그러면 Azure 지원 요청에 첨부할 수 있는 `.blog` 파일이 표시됩니다.

# <a name="android"></a>[Android](#tab/android)

Android용으로 개발할 때 로그는 `.blog` 파일에 저장됩니다. 로그는 암호화되어 있으므로 직접 볼 수 없습니다.

Android Studio의 경우 시뮬레이터와 디바이스 모두에서 보기 > 도구 창 > 디바이스 파일 탐색기를 선택하여 디바이스 파일 탐색기로 이동합니다. `.blog` 파일은 `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`와 같이 표시되는 애플리케이션의 디렉터리 내에 위치합니다. 이 파일을 지원 요청에 첨부할 수 있습니다. 
   

---


## <a name="related-information"></a>관련 정보
- [로그 및 진단](logging-and-diagnostics.md)
- [메트릭](metrics.md)
