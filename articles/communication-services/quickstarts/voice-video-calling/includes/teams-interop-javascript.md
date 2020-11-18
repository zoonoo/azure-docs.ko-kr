---
title: 빠른 시작 - Teams 모임 참가
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0559a9b763d273579373bb57922606f14ff6e6db
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574307"
---
## <a name="prerequisites"></a>사전 요구 사항

- 작동하는 [Communication Services 호출 앱](../getting-started-with-calling.md)
- [Teams 배포](https://docs.microsoft.com/deployoffice/teams-install)

## <a name="enable-teams-interoperability"></a>Teams 상호 운용성 사용

Teams 상호 운용성 기능은 현재 프라이빗 미리 보기에 있습니다. 이 기능을 Communication Services 리소스에 사용하도록 설정하려면 다음 항목이 포함된 이메일을 [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com)으로 보내주세요.

1. Communication Services 리소스가 포함된 Azure 구독의 구독 ID
2. Teams 테넌트 ID. 이를 가져오는 가장 쉬운 방법은 [Team에 대한 링크를 가져와서 공유](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team)하는 것입니다.

이 기능을 사용하려면 두 엔터티를 소유하는 조직의 구성원이어야 합니다.

## <a name="add-the-teams-ui-controls"></a>Teams UI 컨트롤 추가

새 텍스트 상자와 단추를 HTML 내에 추가합니다. 텍스트 상자는 Teams 모임 컨텍스트를 입력하는 데 사용되며, 단추는 지정된 모임에 참가하는 데 사용됩니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Teams UI 컨트롤 사용

이제 **Teams 모임 참가** 단추를 제공된 Teams 모임에 참가하는 코드에 바인딩할 수 있습니다.

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>모임 컨텍스트 가져오기

Teams 컨텍스트는 Graph API를 사용하여 검색할 수 있습니다. 자세한 내용은 [Graph 설명서](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?view=graph-rest-beta&tabs=http)에서 설명하고 있습니다.

또한 모임 초대 자체의 **모임 참가** URL에서 필요한 모임 정보를 가져올 수 있습니다.

## <a name="run-the-code"></a>코드 실행

webpack 사용자는 `webpack-dev-server`를 사용하여 앱을 빌드하고 실행할 수 있습니다. 다음 명령을 실행하여 로컬 웹 서버에서 애플리케이션 호스트를 번들로 묶습니다.

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

브라우저를 열고 http://localhost:8080/로 이동합니다. 다음이 표시되어야 합니다.

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="완성된 JavaScript 애플리케이션의 스크린샷":::

Teams 컨텍스트를 텍스트 상자에 삽입하고, *Teams 모임 참가* 를 눌러 Communication Services 애플리케이션 내에서 Teams 모임에 참가합니다.

