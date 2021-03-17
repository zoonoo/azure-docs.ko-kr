---
title: UI 프레임워크 구성 요소 만들기
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 UI 프레임워크와 호환되는 사용자 지정 구성 요소를 빌드하는 방법을 알아봅니다.
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 093fcfd95d291d959ed49cc39a227a99f14a0383
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488247"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>빠른 시작: 사용자 고유의 UI 프레임워크 구성 요소 만들기

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

통신 환경을 애플리케이션에 빠르게 통합하기 위해 UI 프레임워크를 사용하여 Azure Communication Services를 시작합니다.

이 빠른 시작에서는 UI 프레임워크에서 제공하는 미리 정의된 상태 인터페이스를 사용하여 사용자 고유의 구성 요소를 만드는 방법에 대해 알아봅니다. 이 방법은 더 많은 사용자 지정이 필요하고 환경에 맞는 고유한 디자인 자산을 사용하려는 개발자에게 적합합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(Node 12 권장)
- 활성 Communication Services 리소스 [Communication Services 리소스를 만듭니다](./../create-communication-resource.md).
- 호출 클라이언트를 인스턴스화하기 위한 사용자 액세스 토큰입니다. [사용자 액세스 토큰 만들기 및 관리](./../access-tokens.md) 방법에 대해 알아봅니다.

UI 프레임워크를 사용하려면 React 환경을 설정해야 합니다. 여기서는 이 작업을 수행합니다. React 앱이 이미 있으면 이 섹션을 건너뛰어도 됩니다.

### <a name="set-up-react-app"></a>React 앱 설정

이 빠른 시작에서는 create-react-app 템플릿을 사용합니다. 자세한 내용은 다음을 참조하세요. [React 시작](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

이 프로세스가 완료되면 `my-app` 폴더 내에 전체 애플리케이션이 있어야 합니다. 이 빠른 시작에서는 `src` 폴더 내의 파일을 수정합니다.

### <a name="install-the-package"></a>패키지 설치

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services 통화 클라이언트 라이브러리를 설치합니다. 제공된 tarball(프라이빗 미리 보기)을 my-app 디렉터리로 이동합니다.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

### <a name="run-create-react-app"></a>Create React App 실행

다음을 실행하여 Create React App 설치를 테스트해 보겠습니다.

```console

npm run start   

```

## <a name="object-model"></a>개체 모델

Azure Communication Services UI 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 속성                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| 공급자| 개발자가 기본 Fluent UI 구성 요소를 수정할 수 있도록 하는 Fluent UI 공급자입니다.|
| CallingProvider| 통화를 인스턴스화하는 통화 공급자입니다. 기본 구성 요소를 추가하는 데 필요합니다.|
| ChatProvider | 채팅 스레드를 인스턴스화하는 채팅 공급자입니다. 기본 구성 요소를 추가하는 데 필요합니다.|
| connectFuncsToContext | 매퍼를 사용하여 UI 프레임워크 구성 요소를 기본 공급자와 연결하는 메서드입니다. |
| MapToChatMessageProps | 채팅 메시지 props를 구성 요소에 제공하는 채팅 메시지 데이터 계층 매퍼입니다. |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Azure Communication Services 자격 증명을 사용하여 채팅 공급자 초기화

이 빠른 시작에서는 채팅을 예로 사용합니다. 통화에 대한 자세한 내용은 [기본 구성 요소 빠른 시작](./get-started-with-components.md) 및 [복합 구성 요소 빠른 시작](./get-started-with-composites.md)을 참조하세요.

`my-app`의 `src` 폴더로 이동하여 `app.js` 파일을 찾습니다. 여기서는 채팅 공급자를 초기화하는 다음 코드를 배치합니다. 이 공급자는 통화 및 채팅 환경의 컨텍스트를 유지 관리합니다. 구성 요소를 초기화하려면 Azure Communication Services에서 검색한 액세스 토큰이 필요합니다. 액세스 토큰을 가져오는 방법에 대한 자세한 내용은 [액세스 토큰 만들기 및 관리](./../access-tokens.md)를 참조하세요.

UI 프레임워크 구성 요소는 나머지 서비스에 대해 동일한 일반 아키텍처를 따릅니다. 구성 요소는 액세스 토큰, 그룹 ID 또는 스레드 ID를 생성하지 않습니다. 이러한 요소는 이러한 ID를 생성하여 클라이언트 애플리케이션에 전달하는 적절한 단계를 수행하는 서비스에서 제공됩니다. 자세한 내용은 [클라이언트 서버 아키텍처](./../../concepts/client-and-server-architecture.md)를 참조하세요.

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

초기화되면 이 공급자를 통해 UI 프레임워크 구성 요소 및 추가 레이아웃 논리를 사용하여 사용자 고유의 레이아웃을 빌드할 수 있습니다. 공급자는 모든 기본 논리를 초기화하고, 서로 다른 구성 요소를 모두 적절하게 연결합니다. 다음으로 UI 프레임워크 매퍼를 사용하여 채팅 공급자에 연결하는 사용자 지정 구성 요소를 만듭니다.


## <a name="create-a-custom-component-using-mappers"></a>매퍼를 사용하여 사용자 지정 구성 요소 만들기

먼저 구성 요소를 만들 `SimpleChatThread.js`라는 새 파일을 만듭니다. 그런 다음, 필요한 UI 프레임워크 구성 요소를 가져옵니다. 여기서는 기본 제공 html을 사용하고, 간단한 채팅 스레드에 대해 완전한 사용자 지정 구성 요소를 만드는 데 반응합니다. `connectFuncsToContext` 메서드를 사용하는 경우 `MapToChatMessageProps` 매퍼를 사용하여 props를 `SimpleChatThread` 사용자 지정 구성 요소에 매핑합니다. 이러한 props를 통해 보내고 받는 채팅 메시지에 액세스하여 간단한 스레드에 채울 수 있습니다.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>애플리케이션에 사용자 지정 구성 요소 추가

이제 사용자 지정 구성 요소가 준비되었으므로 이를 가져와서 레이아웃에 추가합니다.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>빠른 시작 실행

위의 코드를 실행하려면 다음 명령을 사용합니다.

```console

npm run start   

```

기능을 완전히 테스트하려면 간단한 채팅 스레드에서 받는 메시지를 보낼 수 있는 채팅 기능이 있는 두 번째 클라이언트가 필요합니다. 잠재적 옵션으로 [통화 주인공 샘플](./../../samples/calling-hero-sample.md) 및 [채팅 주인공 샘플](./../../samples/chat-hero-sample.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [UI 프레임워크 복합 구성 요소 사용해 보기](./get-started-with-composites.md)

자세한 내용은 다음 자료를 참조하세요.
- [UI 프레임워크 개요](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI 프레임워크 기능](./../../concepts/ui-framework/ui-sdk-features.md)
- [UI 프레임워크 기본 구성 요소 빠른 시작](./get-started-with-components.md)
