---
title: Azure Communication Services UI 프레임워크 기본 구성 요소 시작
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 UI 프레임워크 기본 구성 요소를 시작하는 방법을 알아봅니다.
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6f4a8e8f26e88a73fc73c309ef336813282589f3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488179"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>빠른 시작: UI 프레임워크 기본 구성 요소 시작

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

UI 프레임워크를 사용하여 Azure Communication Services를 시작하면 통신 환경을 애플리케이션에 빠르게 통합할 수 있습니다. 이 빠른 시작에서는 UI 프레임워크 기본 구성 요소를 애플리케이션에 통합하여 통신 환경을 구축하는 방법에 대해 알아봅니다. 

UI 프레임워크 구성 요소는 기본 및 복합이라는 두 가지 버전으로 제공됩니다.

- **기본 구성 요소** 는 개별 통신 기능을 나타내며, 복잡한 통신 환경을 구축하는 데 사용할 수 있는 기본 구성 요소입니다. 
- **복합 구성 요소** 는 **기본 구성 요소** 를 빌딩 블록으로 사용하여 빌드된 일반적인 통신 시나리오를 위한 턴키 환경으로, 애플리케이션에 쉽게 통합되도록 패키지됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(Node 12 권장)
- 활성 Communication Services 리소스 [Communication Services 리소스를 만듭니다](./../create-communication-resource.md).
- 호출 클라이언트를 인스턴스화하기 위한 사용자 액세스 토큰입니다. [사용자 액세스 토큰 만들기 및 관리](./../access-tokens.md) 방법에 대해 알아봅니다.

## <a name="setting-up"></a>설치

UI 프레임워크를 사용하려면 React 환경을 설정해야 합니다. 다음 섹션에서 이 작업을 수행합니다. React 앱이 이미 있으면 이 섹션을 건너뛰어도 됩니다.

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
| CallingProvider| 호출을 인스턴스화하는 통화 공급자입니다. 추가 구성 요소를 추가하는 데 필요합니다.|
| ChatProvider | 채팅 스레드를 인스턴스화하는 채팅 공급자입니다. 추가 구성 요소를 추가하는 데 필요합니다.|
| MediaGallery | 호출 참가자와 원격 비디오 스트림을 표시하는 기본 구성 요소 |
| MediaControls | 호출을 제어하는 기본 구성 요소(음소거, 비디오, 공유 화면 포함) |
| ChatThread | 입력 표시기, 읽음 확인 등으로 채팅 스레드를 렌더링하는 기본 구성 요소 |
| SendBox | 조인된 스레드로 보낼 메시지를 사용자가 입력할 수 있는 기본 구성 요소|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Azure Communication Services 자격 증명을 사용하여 통화 및 채팅 공급자 초기화

`my-app`의 `src` 폴더로 이동하여 `app.js` 파일을 찾습니다. 여기서는 통화 및 채팅 공급자를 초기화하는 다음 코드를 배치합니다. 이러한 공급자는 통화 및 채팅 환경의 컨텍스트 유지 관리를 담당합니다. 구축하는 통신 환경의 유형에 따라 사용할 항목을 선택할 수 있습니다. 필요한 경우 두 가지를 동시에 사용할 수 있습니다. 구성 요소를 초기화하려면 Azure Communication Services에서 검색한 액세스 토큰이 필요합니다. 액세스 토큰을 얻는 방법에 대한 자세한 내용은 [액세스 토큰 만들기 및 관리](./../access-tokens.md)를 참조하세요.

> [!NOTE]
> 구성 요소는 액세스 토큰, 그룹 ID 또는 스레드 ID를 생성하지 않습니다. 이러한 요소는 적절한 단계를 거쳐 이러한 ID를 생성하고 클라이언트 애플리케이션에 전달하는 서비스에서 제공됩니다. 자세한 내용은 [클라이언트 서버 아키텍처](./../../concepts/client-and-server-architecture.md)를 참조하세요.
> 
> 예를 들면 다음과 같습니다. 채팅 공급자는 초기화에 사용되는 `token`에 연결된 `userId`가 제공된 `threadId`에 이미 조인되어 있다고 예상합니다. 토큰이 스레드 ID에 조인되어 있지 않으면 채팅 공급자는 실패합니다. 채팅에 대한 자세한 내용은 [채팅 시작](./../chat/get-started.md)을 참조하세요.

Fluent UI 테마를 사용하여 애플리케이션의 모양과 느낌을 개선하겠습니다.

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

초기화되면 이 공급자를 통해 UI 프레임워크 기본 구성 요소 및 추가 레이아웃 논리를 사용하여 자체 레이아웃을 빌드할 수 있습니다. 공급자는 모든 기본 논리를 초기화하고, 서로 다른 구성 요소를 적절하게 연결합니다. 다음으로, UI 프레임워크에서 제공하는 다양한 기본 구성 요소를 사용하여 통신 환경을 구축합니다. 이러한 구성 요소의 레이아웃을 사용자 지정하고 함께 렌더링할 다른 사용자 지정 구성 요소를 추가할 수 있습니다. 

## <a name="build-ui-framework-calling-component-experiences"></a>UI 프레임워크 통화 구성 요소 환경 빌드

통화에는 `MediaGallery` 및 `MediaControls` 구성 요소를 사용합니다. 이에 대한 자세한 내용은 [UI 프레임워크 기능](./../../concepts/ui-framework/ui-sdk-features.md)을 참조하세요. 시작하려면 `src` 폴더에서 `CallingComponents.js`라는 새 파일을 만듭니다. 여기서는 기본 구성 요소를 보관할 함수 구성 요소를 초기화한 다음, `app.js`에서 가져옵니다. 구성 요소 주위에 레이아웃 및 스타일을 더 추가할 수 있습니다. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

이 파일의 아래쪽에서, 매핑 함수 `MapToCallingSetupProps`를 사용하여 통화 UI 구성 요소를 기본 상태에 연결하기 위해 UI 프레임워크에서 `connectFuncsToContext` 메서드를 사용하여 통화 구성 요소를 내보냈습니다. 이 메서드는 props가 채워진 구성 요소를 생성하고, 이를 사용하여 상태를 확인하고 호출을 조인합니다. `isCallInitialized` 속성을 사용하여 `CallAgent`가 준비되었는지 확인한 다음, `joinCall` 메서드를 사용하여 조인합니다. UI 프레임워크는 데이터가 구성 요소로 푸시되는 방식을 개발자가 제어하려는 시나리오에 사용할 사용자 지정 매핑 함수를 지원합니다.

## <a name="build-ui-framework-chat-component-experiences"></a>UI 프레임워크 채팅 구성 요소 환경 빌드

채팅에는 `ChatThread` 및 `SendBox` 구성 요소를 사용합니다. 이러한 구성 요소에 대한 자세한 내용은 [UI 프레임워크 기능](./../../concepts/ui-framework/ui-sdk-features.md)을 참조하세요. 시작하려면 `src` 폴더에서 `ChatComponents.js`라는 새 파일을 만듭니다. 여기서는 기본 구성 요소를 보관할 함수 구성 요소를 초기화한 다음, `app.js`에서 가져옵니다.

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>기본 애플리케이션에 통화 및 채팅 구성 요소 추가

`app.js` 파일로 돌아가서 아래와 같이 `CallingProvider` 및 `ChatProvider`에 구성 요소를 추가합니다.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

```

## <a name="run-quickstart"></a>빠른 시작 실행

위의 코드를 실행하려면 다음 명령을 사용합니다.

```console

npm run start   

```

기능을 완전히 테스트하려면 통화 및 채팅 스레드에 조인하기 위한 통화 및 채팅 기능이 있는 두 번째 클라이언트가 필요합니다. 잠재적 옵션으로 [통화 주인공 샘플](./../../samples/calling-hero-sample.md) 및 [채팅 주인공 샘플](./../../samples/chat-hero-sample.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [UI 프레임워크 복합 구성 요소 사용해 보기](./get-started-with-composites.md)

자세한 내용은 다음 자료를 참조하세요.
- [UI 프레임워크 개요](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI 프레임워크 기능](./../../concepts/ui-framework/ui-sdk-features.md)
