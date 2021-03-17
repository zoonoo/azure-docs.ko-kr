---
title: 파일 포함
description: 포함 파일
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: dedea2a622cb0eece92bb8b57871c76daa05fb68
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495470"
---
## <a name="prerequisites"></a>사전 요구 사항
시작하기 전에 다음을 확인해야 합니다.

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요. 
- [Xcode](https://developer.apple.com/xcode/) 및 [Cocoapods](https://cocoapods.org/)를 설치합니다. Xcode를 사용하여 빠른 시작에 사용할 iOS 애플리케이션을 만들고, Cocoapods를 사용하여 종속성을 설치합니다.
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication 리소스 만들기](../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에서는 **리소스 엔드포인트를 기록** 해야 합니다.
- ACS 사용자를 **두 명** 만들고 해당 사용자에게 [사용자 액세스 토큰](../../access-tokens.md)을 발급합니다. 범위를 **채팅** 으로 설정하고 **토큰 문자열과 userId 문자열을 기록** 해 둡니다. 이 빠른 시작에서는 초기 참가자가 있는 스레드를 만든 다음, 두 번째 참가자를 스레드에 추가합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-ios-application"></a>새 iOS 애플리케이션 만들기

Xcode를 열고 `Create a new Xcode project`를 선택합니다.

다음 창에서 `iOS`를 플랫폼으로 선택하고 템플릿에 `App`을 선택합니다.

옵션을 선택할 때 프로젝트 이름에 `ChatQuickstart`를 입력합니다. 인터페이스에 `Storyboard`를, 수명 주기에 `UIKit App Delegate`를, 언어에 `Swift`를 선택합니다.

다음을 클릭하고 프로젝트를 만들려는 디렉터리를 선택합니다.

### <a name="install-the-libraries"></a>라이브러리 설치

Cocoapods를 사용하여 필요한 Communication Services 종속성을 설치합니다.

명령줄에서 `ChatQuickstart` iOS 프로젝트의 루트 디렉터리 안으로 이동합니다.

Podfile `pod init`를 만듭니다.

Podfile를 열고 `ChatQuickstart` 대상에 다음 종속성을 추가합니다.
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

종속성을 설치합니다. 그러면 Xcode 작업 영역 `pod install`도 생성됩니다.

**Pod 설치 실행 이후 새로 생성된 `.xcworkspace`를 선택하여 Xcode에서 프로젝트를 다시 엽니다.**

### <a name="setup-the-placeholders"></a>자리 표시자 설정

Xcode에서 작업 영역 `ChatQuickstart.xcworkspace`를 열고 `ViewController.swift`를 엽니다.

이 빠른 시작에서는 `viewController`에 코드를 추가하고 Xcode 콘솔에서 출력을 확인합니다. 이 빠른 시작은 iOS의 UI 빌드를 다루지 않습니다. 

`viewController.swift` 상단에서 `AzureCommunication` 및 `AzureCommunicatonChat` 라이브러리를 가져옵니다.

```
import AzureCommunication
import AzureCommunicationChat
```

`ViewController`의 `viewDidLoad()` 메서드에 다음 코드를 복사합니다.

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

세마포를 사용하여 코드를 데모용으로 동기화합니다. 다음 단계에서는 Azure Communication Services 채팅 라이브러리를 사용하여 자리 표시자를 샘플 코드로 바꿉니다.


### <a name="create-a-chat-client"></a>채팅 클라이언트 만들기

`<CREATE A CHAT CLIENT>` 주석을 다음 코드로 바꿉니다.

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
    let credential =
    try CommunicationTokenCredential(
        token: "<ACCESS_TOKEN>"
    )
    let options = AzureCommunicationChatClientOptions()

    let chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: options
    )
```

`<ACS_RESOURCE_ENDPOINT>`를 ACS 리소스의 엔드포인트로 바꿉니다.
`<ACCESS_TOKEN>`을 올바른 ACS 액세스 토큰으로 바꿉니다.

이 빠른 시작에서는 권장되는 사항이기는 하지만 채팅 애플리케이션에 대한 토큰을 관리하는 서비스 계층을 만드는 방법을 다루지 않습니다. [채팅 아키텍처](../../../concepts/chat/concepts.md)에 대한 자세한 내용은 다음 설명서를 참조하세요.

[사용자 액세스 토큰](../../access-tokens.md)에 대해 자세히 알아보세요.

## <a name="object-model"></a>개체 모델 
다음 클래스 및 인터페이스는 JavaScript용 Azure Communication Services 채팅 클라이언트 라이브러리의 주요 기능 중 일부를 처리합니다.

| 이름                                   | 설명                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatThreadClient | 이 클래스는 채팅 스레드 기능에 필요합니다. ChatClient를 통해 인스턴스를 확보하여 메시지 보내기/받기/업데이트/삭제, 사용자 추가/제거/받기, 입력 알림 보내기, 읽음 확인, 채팅 이벤트 구독에 사용할 수 있습니다. |

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

이제 `ChatClient`를 사용하여 초기 사용자로 새 스레드를 만듭니다.

`<CREATE A CHAT THREAD>` 주석을 다음 코드로 바꿉니다.

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

`<USER_ID>`를 올바른 Communication Services 사용자 ID로 바꿉니다.

계속하기 전에 여기서 세마포를 사용하여 완료 처리기를 기다립니다. 이후 단계에서 완료 처리기에 반환된 응답의 `threadId`를 사용합니다.

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기

이제 채팅 스레드를 만들었으므로 스레드 내에서 작업을 수행하기 위해 `ChatThreadClient`를 가져옵니다.

`<CREATE A CHAT THREAD CLIENT>` 주석을 다음 코드로 바꿉니다.

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`<SEND A MESSAGE>` 주석을 다음 코드로 바꿉니다.

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

먼저 콘텐츠와 보낸 사람 표시 이름을 포함하는 `SendChatMessageRequest`를 구성합니다(필요에 따라 기록 공유 시간을 포함할 수도 있음). 완료 처리기에 반환된 응답에는 전송된 메시지의 ID가 포함됩니다.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>채팅 스레드에 사용자를 참가자로 추가

`<ADD A USER>` 주석을 다음 코드로 바꿉니다.

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

`<USER_ID>`를 추가할 사용자의 ACS 사용자 ID로 바꿉니다.

스레드에 참가자를 추가하는 경우 완료를 반환한 응답에 오류가 포함될 수 있습니다. 이러한 오류는 특정 참가자를 추가하지 못했음을 나타냅니다.

## <a name="list-users-in-a-thread"></a>스레드에 사용자 나열

`<LIST USERS>` 주석을 다음 코드로 바꿉니다.

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>채팅 스레드에서 사용자 제거

`<REMOVE A USER>` 주석을 다음 코드로 바꿉니다.

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

`<USER ID>`를 제거할 참가자의 Communication Services 사용자 ID로 바꿉니다.

## <a name="run-the-code"></a>코드 실행

Xcode에서 실행 단추를 눌러 프로젝트를 빌드하고 실행합니다. 콘솔에서 코드의 출력과 ChatClient의 로거 출력을 볼 수 있습니다.


