---
title: '빠른 시작: Node.js 웹앱 만들기 - Linux'
description: 몇 분 안에 첫 번째 Node.js Hello World를 Azure App Service에 배포합니다.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 59a2c96987762e6b56cc7b453877cebe3124e443
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102109256"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. Visual Studio Code에서 Azure App Service에 배포

1. Visual Studio Code에서 애플리케이션 폴더를 엽니다.

    ```bash
    code .
    ```

1. **AZURE APP SERVICE** 탐색기에서 **Azure에 로그인...** 을 선택하고 지침을 따릅니다. 로그인하면 탐색기에 Azure 구독 이름이 표시됩니다.

    ![Azure에 로그인](../media/quickstart-nodejs/sign-in.png)
    <br>
    <details>
    <summary>Azure 로그인 문제 해결</summary>
    
    Azure에 로그인할 때 **"이름이 [구독 ID]인 구독을 찾을 수 없습니다"** 라는 오류가 표시되는 경우 백그라운드에서 프록시를 사용하고 있고 Azure API에 연결할 수 없기 때문일 수 있습니다. `export`를 사용하여 터미널의 프록시 정보를 사용하여 `HTTP_PROXY` 및 `HTTPS_PROXY` 환경 변수를 구성합니다.
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [문제 보고](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. **AZURE APP SERVICE** 탐색기에서 파란색 위쪽 화살표 아이콘을 선택하여 앱을 Azure에 배포합니다. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="선택한 파란색 화살표 아이콘을 표시하는 VS Code의 Azure App 서비스 스크린샷.":::

1. 현재 열려 있는 `nodejs-docs-hello-world` 디렉터리를 선택합니다.

1. 기본적으로 Linux의 App Service에 배포되는 **새 웹앱 만들기** 를 선택합니다.

1. 웹앱에 대해 전역적으로 고유한 <abbr title="앱 이름에 유효한 문자는 'a-z', '0-9' 및 '-'입니다.">name</abbr> 을 입력하고 **Enter** 키를 누릅니다. 

1. **Node.js 버전** 을 선택합니다. LTS가 추천됩니다.

    알림 채널에는 앱에 대해 만들어지는 Azure 리소스가 표시됩니다.

1. 대상 서버에서 `npm install`을 실행하도록 구성을 업데이트하라는 메시지가 표시되면 **예** 를 선택합니다. 그러면 앱이 배포됩니다.

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="[예] 단추를 선택한 상태에서 대상 서버의 구성을 업데이트하라는 프롬프트의 스크린샷.":::

1. 작업 영역을 업데이트하라는 메시지가 표시되면 **예** 를 선택합니다. 그래야 이후 배포에서 자동으로 동일한 App Service 웹앱이 대상으로 지정됩니다. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="[예] 단추를 선택한 상태에서 작업 영역을 업데이트하라는 프롬프트의 스크린샷.":::




1. 배포가 완료되면 프롬프트에서 **웹 사이트 찾아보기** 를 선택하여 새로 배포된 웹앱을 확인합니다.

<br/>
<details>
<summary><strong>문제 해결</strong></summary>

이러한 단계를 완료할 수 없는 경우 다음을 확인합니다.

* 애플리케이션이 PORT 환경 변수(`process.env.PORT`)에서 제공하는 포트를 수신 대기하고 있는지 확인하세요.

* **"이 디렉터리 또는 페이지를 볼 수 있는 권한을 가지고 있지 않습니다."** 오류가 표시되면 애플리케이션이 제대로 시작되지 못했을 수 있습니다. 로그 출력을 검토하고 오류를 찾아서 해결합니다. 

</details>

<br>

[문제 보고](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>


