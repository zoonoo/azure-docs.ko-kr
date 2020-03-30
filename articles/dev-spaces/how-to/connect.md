---
title: 개발 컴퓨터를 AKS 클러스터에 연결(미리 보기)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Azure 개발자 공간을 사용하여 개발 컴퓨터를 AKS 클러스터에 연결하는 방법 알아보기
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235012"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>개발 컴퓨터를 AKS 클러스터에 연결(미리 보기)

Azure 개발자 공간을 사용하면 개발 컴퓨터에서 컨테이너유무에 관계없이 코드를 실행하고 디버깅할 수 있으며, 나머지 응용 프로그램 또는 서비스와 함께 Kubernetes 클러스터에 계속 연결할 수 있습니다. 개발 컴퓨터를 클러스터에 연결하면 Docker 또는 Kubernetes 구성을 만들지 않고도 응용 프로그램을 신속하게 개발하고 종단 간 테스트를 수행할 수 있습니다. 동일한 클러스터를 사용하는 다른 워크로드 나 사용자에게 영향을 주지 않고 AKS 클러스터에 연결할 수도 있습니다.

Azure 개발자 공간은 연결된 AKS 클러스터와 개발 컴퓨터 간의 트래픽을 리디렉션합니다. 이 트래픽 리디렉션을 사용하면 AKS 클러스터에서 실행중인 개발 컴퓨터 및 서비스의 코드가 동일한 AKS 클러스터에 있는 것처럼 통신할 수 있습니다. 코드가 개발 컴퓨터에서 실행되고 있으므로 해당 코드를 실행하고 디버깅하는 데 사용하는 개발 도구도 유연하게 사용할 수 있습니다. 또한 Azure 개발자 공간은 개발 컴퓨터의 AKS 클러스터의 포드에서 사용할 수 있는 환경 변수 및 탑재된 파일을 복제하는 방법을 제공합니다.

이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.

* Azure Dev Spaces를 Azure의 관리형 Kubernetes 클러스터에 설정합니다.
* 여러 마이크로서비스가 포함된 대규모 애플리케이션을 개발 공간에 배포합니다.
* Azure 개발자 공간을 사용하여 AKS 클러스터와 개발 컴퓨터에서 실행되는 코드 간의 트래픽을 리디렉션합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 가이드에서는 [Azure 개발자 공간 자전거 공유 샘플 응용 프로그램을](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) 사용하여 개발 컴퓨터를 AKS 클러스터에 연결하는 방법을 보여 줍니다. [Azure 개발자 공간 자전거 공유 샘플 응용 프로그램 README의](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) 지침에 따라 샘플 응용 프로그램을 실행합니다. 또는 AKS 클러스터에 자체 응용 프로그램이 있는 경우에도 아래 단계를 수행하고 사용자 고유의 서비스 및 창의 이름을 사용할 수 있습니다.

### <a name="limitations"></a>제한 사항

* 현재 UDP는 지원되지 않습니다.

### <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치][azure-cli]
* [Azure 개발자 공간][azds-vs-code] 확장이 설치되어 MacOS 또는 Windows 10에서 실행되는 시각적 스튜디오 [코드입니다.][vs-code]
* [Azure 개발자 공간 자전거 공유 샘플 응용 프로그램](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) 또는 AKS 클러스터에서 실행 중인 사용자 고유의 응용 프로그램입니다.

## <a name="connect-your-development-computer"></a>개발 컴퓨터 연결

비주얼 스튜디오 코드에서 *개발 공간/샘플/BikeSharingApp/Bikes를* 열고 Azure 개발자 공간 확장을 사용하여 개발 컴퓨터를 AKS 클러스터에 연결합니다.

Azure 개발자 공간 확장을 사용하려면 Visual Studio 코드에서 명령 팔레트를 열고 *[보기]* 다음 *명령 팔레트를*클릭합니다. 입력을 `Azure Dev Spaces: Redirect` 시작하고 를 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`클릭합니다. `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`

![명령](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>리디렉션 옵션 선택

실행하면 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`기존 Kubernetes 서비스를 선택하라는 메시지가 표시됩니다.

![서비스 선택](../media/how-to-connect/connect-choose-service.png)

이 옵션은 이 서비스에 대한 AKS 클러스터의 모든 트래픽을 개발 컴퓨터에서 실행 중인 응용 프로그램의 버전으로 리디렉션합니다. 이 서비스에 AKS 클러스터에서 실행 중인 여러 포드가 있는 경우 이 서비스의 모든 트래픽은 개발 컴퓨터로만 라우팅됩니다. 또한 Azure 개발자 공간은 응용 프로그램에서 AKS 클러스터로 모든 아웃바운드 트래픽을 라우팅합니다.

실행하면 `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`특정 창을 선택하라는 메시지가 표시됩니다.

![포드 선택](../media/how-to-connect/connect-choose-pod.png)

이 옵션은 특정 포드에 연결됩니다. 이 옵션은 트래픽을 보내거나 받지 않는 포드와 상호 작용하고 종료된 포드를 복제하는 데 유용합니다. 포드가 트래픽을 보내고 받는 경우 이 옵션은 유사한 방식으로 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` 행동하며 선택한 포드의 서비스와 관련된 모든 포드에 대해 AKS 클러스터의 모든 트래픽을 리디렉션합니다.

을 실행하는 `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`경우 기존 포드 또는 서비스를 선택하라는 메시지가 표시되지 않습니다. 이 옵션은 개발 컴퓨터에서 실행중인 응용 프로그램에서 모든 아웃바운드 트래픽을 AKS 클러스터로 리디렉션합니다.

이 예제에서는 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` 자전거 서비스를 선택하고 *선택합니다.*

### <a name="select-a-connection-mode"></a>연결 모드 선택

리디렉션 옵션을 선택하면 *연결 변경* 또는 *복제* 모드를 선택하라는 메시지가 표시됩니다.

![바꾸기 또는 복제](../media/how-to-connect/connect-replace-clone.png)

*바꾸기* 옵션은 AKS 클러스터의 현재 포드 또는 서비스를 대체하고 해당 서비스의 모든 트래픽을 개발 컴퓨터로 리디렉션합니다. 이 옵션은 리디렉션하는 서비스와 상호 작용하는 AKS 클러스터의 다른 서비스에 지장을 주어 개발 컴퓨터에서 응용 프로그램을 시작할 때까지 작동하지 않을 수 있습니다. *복제* 옵션을 사용하면 기존 자식 개발 공간을 선택하거나 포드 또는 서비스에 대한 트래픽을 개발 컴퓨터로 리디렉션하기 위한 새 자식 개발 공간을 만들 수 있습니다. 이 옵션을 사용하면 해당 자식 개발 공간에 대한 트래픽만 개발 컴퓨터로 리디렉션되므로 격리상태에서 작업하고 다른 서비스를 중단하지 않아도 됩니다. *복제* 옵션을 사용하려면 AKS 클러스터에 Azure 개발자 공간이 활성화되어 있어야 합니다.

이 예제에서는 *[바꾸기]*

> [!NOTE]
> 기존 서비스의 포드에 컨테이너가 여러 개 있는 경우 응용 프로그램의 컨테이너를 선택하라는 메시지가 표시됩니다.

### <a name="select-a-port-for-your-application"></a>응용 프로그램에 대한 포트 선택

연결 모드를 선택하면 로컬 응용 프로그램을 TCP 포트로 입력하라는 메시지가 표시됩니다. 응용 프로그램이 여러 포트를 여는 경우 쉼표(예: *80,81)로*구분합니다. 응용 프로그램이 네트워크 요청을 수락하지 않는 경우 *0을*입력합니다. 이 예제에서는 *3000을*입력합니다.

![선택 포트 연결](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>연결되어 있는지 확인

응용 프로그램의 TCP 포트를 선택하면 Azure 개발자 공간에서 AKS 클러스터에 대한 연결을 설정합니다. Azure 개발자 공간은 AKS 클러스터에 에이전트를 삽입하여 AKS 클러스터와 개발 컴퓨터 간의 트래픽을 리디렉션합니다. 이 연결을 설정하는 데 몇 분 정도 걸릴 수 있습니다. 또한 Azure 개발자 공간은 개발 컴퓨터에서 *호스트* 파일을 수정하기 위해 관리자 액세스를 요청합니다.

> [!IMPORTANT]
> Azure 개발자 공백이 AKS 클러스터에 대한 연결을 설정하면 *연결 바꾸기* 모드를 선택하면 개발 컴퓨터에서 서비스를 시작할 때까지 AKS 클러스터의 다른 서비스가 제대로 작동하지 않을 수 있습니다. 대신 *복제* 연결 모드를 선택하여 리디렉션을 위한 자식 개발 공간을 만들고 부모 공간에 대한 중단을 방지할 수 있습니다. 또한 서비스에 개발 컴퓨터에서 사용할 수 없는 종속성이 있는 경우 응용 프로그램을 수정하거나 [추가 구성을](#additional-configuration) 제공해야 할 수 있습니다.

Azure 개발자 공간은 AKS 클러스터에 대한 연결을 설정한 후 *AZDS Connect - 자전거라는* 터미널 창을 엽니다. 이 터미널 창에는 AKS 클러스터에서 구성된 모든 환경 변수 및 DNS 항목이 있습니다. 이 터미널 창에서 실행하거나 Visual Studio 코드 디버거를 사용하는 모든 코드가 AKS 클러스터에 연결됩니다.

![터미널](../media/how-to-connect/connect-terminal.png)

또한 Azure 개발자 공간은 모든 출력과 함께 *개발자 공간 연결이라는* 창을 만듭니다.

![출력](../media/how-to-connect/connect-output.png)

Azure 개발자 공간에는 연결 상태를 보여 줄 상태 표시줄 항목도 있습니다.

![상태](../media/how-to-connect/connect-status.png)

상태 *표시줄에 개발자 공백: 로컬 포트 3000의 개발자/자전거에 연결*되어 있는지 확인 합니다.

### <a name="configure-your-application-on-your-development-computer"></a>개발 컴퓨터에서 응용 프로그램 구성

*AZDS 연결 열기 - 자전거* 터미널 `npm install`창 및 실행 :

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

*디버그를* 클릭한 다음 *구성을 엽니다.* 환경을 선택하라는 메시지가 표시되면 *Node.js*를 선택합니다. 이렇게 하면 `.vscode/launch.json` 파일이 만들어집니다. 해당 파일의 내용을 다음 내용으로 바꿉입니다.

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

[package.json을](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) 열고 디버깅 스크립트를 추가합니다.

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>개발 컴퓨터에서 응용 프로그램 시작

왼쪽에있는 *디버그* 아이콘을 클릭하고 *상단에있는 NPM을 통해 실행* 옆에시작 버튼을 클릭합니다.

![NPM을 통한 출시](../media/how-to-connect/launch-npm.png)

응용 프로그램이 시작되고 Azure 개발자 공간은 AKS 클러스터와 개발 컴퓨터 간의 트래픽을 리디렉션합니다. *디버그 콘솔에서*아래와 유사한 메시지가 표시됩니다.

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Azure 개발자 공간 상태 표시줄을 클릭하고 응용 프로그램의 공용 URL을 선택하여 *bikesharingweb* 서비스로 이동합니다. 이전에 실행한 명령에서 공용 `azds list-uris` URL을 찾을 수도 있습니다. 클러스터에서 Azure 개발자 공간을 사용하지 않는 경우 사용 중인 네임스페이스에 대한 응용 프로그램의 IP 또는 URL을 사용합니다. 위의 예제에서 *bikesharingweb* 서비스에 대한 공용 URL은 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`입니다. *사용자로 Aurelia Briggs(고객)를* 선택한 다음 대여할 자전거를 선택합니다.

### <a name="set-a-break-point"></a>중단점 설정

[server.js를](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) 열고 233줄의 어딘가를 클릭하여 커서를 넣습니다. *F9을* 누르거나 *디버그를* 클릭한 다음 *중단점을 전환하여 중단점을*설정합니다.

공용 URL을 열어 *자전거 공유 웹* 서비스로 이동합니다. *사용자로 Aurelia Briggs(고객)를* 선택한 다음 대여할 자전거를 선택합니다. 자전거 이미지가 로드되지 않습니다. 시각적 스튜디오 코드로 돌아가서 233 줄을 관찰합니다. 설정한 중단점이 233줄에서 서비스를 일시 중지했습니다. 서비스를 다시 시작하려면 *F5* 키를 누르거나 *디버그*, *계속*을 차례로 클릭합니다. 브라우저로 돌아가서 자전거의 자리 표시자 이미지가 표시되는지 확인합니다.

233호선에 `server.js` 커서를 놓고 *F9를*누르면 중단점을 제거합니다.

### <a name="update-your-application"></a>애플리케이션 업데이트

줄 `server.js` 232 및 233을 제거하려면 편집합니다.

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

이제 섹션은 다음과 같습니다.

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

변경 내용을 저장하고 *디버그를* 클릭한 다음 *디버깅을 다시 시작합니다.* 브라우저를 새로 고치고 자전거의 자리 표시자 이미지가 더 이상 표시되지 않는지 확인합니다.

*디버그*와 *디버깅 중지*를 차례로 클릭하여 디버거를 중지합니다. Azure 개발자 공간 상태 표시줄을 클릭하여 AKS 클러스터에서 연결을 끊습니다.

## <a name="additional-configuration"></a>추가 구성

Azure 개발자 공간은 추가 구성 없이 라우팅 트래픽 및 복제 환경 변수를 처리할 수 있습니다. ConfigMap 파일과 같이 AKS 클러스터의 컨테이너에 탑재된 파일을 다운로드해야 하는 경우 해당 `azds-local.env` 파일을 개발 컴퓨터에 다운로드할 수 있는 A를 만들 수 있습니다.

다음은 예입니다. `azds-local.env`

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>로깅 및 진단 사용

개발 컴퓨터를 AKS 클러스터에 연결한 후 로깅 출력이 *개발 공간 연결* 창에 기록됩니다.

![출력](../media/how-to-connect/connect-output.png)

Azure 개발자 공백 상태 표시줄을 클릭하고 *진단 정보 표시를 선택합니다.* 이 명령은 로깅 출력에서 현재 환경 변수 및 DNS 전체를 인쇄합니다.

![진단 기능이 있는 출력](../media/how-to-connect/connect-output-diagnostics.png)

또한 [개발 컴퓨터의 *TEMP* 디렉터리에서][azds-tmp-dir] `Azure Dev Spaces` 진단 로그를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 개발자 공간 및 GitHub 작업을 사용하여 끌어오기 요청이 리포지토리의 기본 분기에 병합되기 전에 AKS에서 직접 끌어오기 요청의 변경 내용을 테스트하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [GitHub 작업 & Azure Kubernetes 서비스][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download