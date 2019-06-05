---
title: Java 및 VS Code를 사용하여 여러 종속 서비스 실행
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
manager: yuvalm
ms.openlocfilehash: a5afd093e0f961d048681465850419c5e8712557
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800383"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Azure Dev Spaces로 다중 서비스 개발

이 자습서에서는 Azure Dev Spaces를 사용하여 다중 서비스 애플리케이션을 개발하는 방법과 Dev Spaces가 제공하는 추가적인 이점 몇 가지를 알아봅니다.

## <a name="call-a-service-running-in-a-separate-container"></a>별도의 컨테이너에서 실행되는 서비스 호출

이 섹션에서는 두 번째 서비스인 `mywebapi`를 만들고 `webfrontend`에서 이 서비스를 호출합니다. 각 서비스는 별도의 컨테이너에서 실행됩니다. 그런 다음, 두 컨테이너 모두에서 디버그합니다.

![여러 컨테이너](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>*mywebapi* 샘플 코드 다운로드
이제 GitHub 리포지토리에서 샘플 코드를 다운로드해 보겠습니다. https://github.com/Azure/dev-spaces로 이동하고 **복제 또는 다운로드**를 선택하여 GitHub 리포지토리를 다운로드합니다. 이 섹션에서 사용할 코드는 `samples/java/getting-started/mywebapi`에 있습니다.

### <a name="run-mywebapi"></a>*mywebapi* 실행
1. *별도의 VS Code 창*에서 `mywebapi` 폴더를 엽니다.
1. **명령 팔레트**(**보기 | 명령 팔레트** 메뉴를 사용하여)를 열고, 자동 완성을 사용하여 입력하고 이 명령을 선택합니다. `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`
1. F5 키를 누르고, 서비스가 빌드되고 배포될 때까지 기다립니다. 아래와 유사한 메시지가 디버그 콘솔에 나타나면 준비가 된 것입니다.

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. 엔드포인트 URL은 `http://localhost:<portnumber>`와 비슷합니다. **팁: VS Code 상태 표시줄에 클릭 가능한 URL이 표시됩니다.** 컨테이너가 로컬에서 실행되는 것처럼 보일 수도 있지만, 실제로는 Azure의 개발 환경에서 실행됩니다. localhost 주소를 사용하는 이유는 `mywebapi`에서 공용 엔드포인트를 정의하지 않았고 이로 인해 Kubernetes 인스턴스 내에서만 액세스할 수 있기 때문입니다. 편의상 로컬 컴퓨터에서 프라이빗 서비스와 쉽게 상호 작용할 수 있도록 Azure Dev Spaces는 Azure에서 실행되는 컨테이너에 대한 임시 SSH 터널을 만듭니다.
1. `mywebapi`에서 준비가 되면 브라우저를 localhost 주소로 엽니다.
1. 모든 단계가 성공적으로 완료되면 `mywebapi` 서비스의 응답이 표시될 수 있습니다.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend*에서 *mywebapi*로 요청
이제 `mywebapi`에 요청하는 코드를 `webfrontend`에 작성해 보겠습니다.
1. `webfrontend`에 대한 VS Code 창으로 전환합니다.
1. `package` 명령문 아래에 다음 `import` 명령문을 *추가*합니다.

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. greeting 메서드에 대한 코드를 *바꿉니다*.

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

위의 코드 예제는 `azds-route-as` 헤더를 수신 요청에서 발신 요청으로 전달합니다. 나중에 이를 통해 공동 작업 개발 팀에 어떻게 도움이 되는지 살펴보겠습니다.

### <a name="debug-across-multiple-services"></a>여러 서비스에서 디버깅
1. 이 시점에서 `mywebapi`는 디버거가 연결된 상태로 계속 실행되고 있습니다. 그렇지 않으면 `mywebapi` 프로젝트에서 F5 키를 누릅니다.
1. `webapi` 프로젝트의 `index()` 메서드에 중단점을 설정합니다.
1. `webfrontend` 프로젝트에서 GET 요청을 `mywebapi`로 보내기 바로 전에`try`로 시작하는 줄에 중단점을 설정합니다.
1. `webfrontend` 프로젝트에서 F5 키를 누릅니다(또는 현재 실행 중인 경우에 디버거를 다시 시작).
1. 웹앱을 호출하고, 두 서비스에서 코드를 단계별로 실행합니다.
1. 웹앱의 정보 페이지에는 두 서비스가 연결된 "Hello from webfrontend and Hello from mywebapi." 메시지가 표시됩니다.

### <a name="automatic-tracing-for-http-messages"></a>HTTP 메시지 자동 추적
*webfrontend*에는 *mywebapi*에 대한 HTTP 호출을 출력하는 특수 코드가 포함되어 있지 않지만 출력 창에 HTTP 추적 메시지가 표시되는 것을 눈치채셨을 것입니다.
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --ytv-> webfrontend:8080:
   GET /greeting?_=1544503627515 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --ve4-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-ve4-- mywebapi:
   HTTP/1.1 200
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-ytv-- webfrontend:8080:
   HTTP/1.1 200
   Hello from webfrontend and
   Hello from mywebapi
```
이는 Dev Spaces 계측에서 얻을 수 있는 "무료" 이점 중 하나입니다. HTTP 요청이 시스템을 통과할 때 이를 추적하여 개발 중에 복잡한 다중 서비스 호출을 더 쉽게 추적할 수 있게 해주는 구성 요소가 삽입되어 있습니다.

### <a name="well-done"></a>모두 완료되었습니다!
이제 각 컨테이너를 개별적으로 개발하고 배포할 수 있는 다중 컨테이너 애플리케이션이 있습니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Dev Spaces의 팀 개발에 대해 알아보기](team-development-java.md)
