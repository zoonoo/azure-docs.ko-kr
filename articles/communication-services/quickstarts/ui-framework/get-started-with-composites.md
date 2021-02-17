---
title: Azure Communication Services UI 프레임워크 SDK 복합 구성 요소 시작
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 UI 프레임워크 복합 구성 요소를 시작하는 방법을 알아봅니다.
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 17a8369463a27acffc8bdc52c48e4ae0624f41cd
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539715"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>빠른 시작: UI 프레임워크 복합 구성 요소 시작

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

통신 환경을 애플리케이션에 빠르게 통합하기 위해 UI 프레임워크를 사용하여 Azure Communication Services를 시작합니다. 이 빠른 시작에서는 UI 프레임워크 복합 구성 요소를 애플리케이션에 통합하여 통신 환경을 구축하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(Node 12 권장)
- 활성 Communication Services 리소스 [Communication Services 리소스를 만듭니다](./../create-communication-resource.md).
- 통화 복합 구성 요소를 인스턴스화하기 위한 사용자 액세스 토큰. [사용자 액세스 토큰 만들기 및 관리](./../access-tokens.md) 방법에 대해 알아봅니다.

## <a name="setting-up"></a>설치

UI 프레임워크를 사용하려면 React 환경을 설정해야 합니다. 여기서는 이 작업을 수행합니다. 이미 React 앱이 있으면 이 섹션을 건너뛸 수 있습니다.

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

//Private Preview install tarball

npm install --save ./{path for tarball}

```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

### <a name="run-create-react-app"></a>React 앱 만들기 실행

다음을 실행하여 React 앱 만들기 설치를 테스트해 보겠습니다.

```console

npm run start 

```

## <a name="object-model"></a>개체 모델

Azure Communication Services UI 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 속성                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| GroupCall | 참가자 갤러리 및 컨트롤을 사용하여 그룹 통화 환경을 렌더링하는 복합 구성 요소입니다. |
| GroupChat | 채팅 스레드 및 입력을 사용하여 그룹 채팅 환경을 렌더링하는 복합 구성 요소입니다. |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>그룹 통화 및 그룹 채팅 복합 구성 요소 초기화

`my-app`의 `src` 폴더로 이동하여 `app.js` 파일을 찾습니다. 여기서는 그룹 채팅 및 통화를 위한 복합 구성 요소를 초기화하는 다음 코드를 배치합니다. 구축하는 통신 환경의 유형에 따라 사용할 항목을 선택할 수 있습니다. 필요한 경우 두 항목을 동시에 사용할 수 있습니다. 구성 요소를 초기화하려면 Azure Communication Services에서 검색한 액세스 토큰이 필요합니다. 액세스 토큰을 가져오는 방법에 대한 자세한 내용은 [사용자 액세스 토큰 만들기 및 관리](./../access-tokens.md)를 참조하세요.

> [!NOTE]
> 구성 요소는 액세스 토큰, 그룹 ID 또는 스레드 ID를 생성하지 않습니다. 이러한 요소는 이러한 ID를 생성하여 클라이언트 애플리케이션에 전달하는 적절한 단계를 수행하는 서비스에서 제공됩니다. 자세한 내용은 [클라이언트 서버 아키텍처](./../../concepts/client-and-server-architecture.md)를 참조하세요.
> 
> 예를 들면 다음과 같습니다. 그룹 채팅 복합 구성 요소에서는 초기화하는 데 사용되는 `token`에 연결된 `userId`가 제공되는 `threadId`에 이미 조인되어 있어야 한다고 예상합니다. 토큰이 스레드 ID에 조인되지 않은 경우 그룹 채팅 복합 구성 요소가 실패합니다. 채팅에 대한 자세한 내용은 [채팅 시작](./../chat/get-started.md)을 참조하세요.


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

```

## <a name="run-quickstart"></a>빠른 시작 실행

위의 코드를 실행하려면 다음 명령을 사용합니다.

```console

npm run start 

```

기능을 완전히 테스트하려면 통화 및 채팅 스레드에 조인하는 통화 및 채팅 기능이 있는 두 번째 클라이언트가 필요합니다. 잠재적 옵션으로 [통화 주인공 샘플](./../../samples/calling-hero-sample.md) 및 [채팅 주인공 샘플](./../../samples/chat-hero-sample.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [UI 프레임워크 기본 구성 요소 사용해 보기](./get-started-with-components.md)

자세한 내용은 다음 자료를 참조하세요.
- [UI 프레임워크 개요](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI 프레임워크 기능](./../../concepts/ui-framework/ui-sdk-features.md)
