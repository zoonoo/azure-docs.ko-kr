---
title: 연결 Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Azure Dev Spaces 연결을 사용 하는 방법 알아보기
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, 연결
ms.openlocfilehash: 2e1984b838e961239e0533179c34eccb118abd8e
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065874"
---
# <a name="azure-dev-spaces-connect-preview"></a>Azure Dev Spaces 연결 (미리 보기)

Azure Dev Spaces 연결을 사용 하면 응용 프로그램의 나머지 부분을 사용 하 여 Kubernetes 클러스터에 연결 하면서도 개발 컴퓨터에서 컨테이너를 사용 하거나 사용 하지 않고 코드를 실행 하 고 디버그할 수 있습니다. 개발 컴퓨터를 클러스터에 연결 하면 빠르게 응용 프로그램을 개발 하 고 Docker 또는 Kubernetes 구성을 만들지 않고도 종단 간 테스트를 수행할 수 있습니다. 동일한 클러스터를 사용할 수 있는 다른 작업 또는 사용자에 게 영향을 주지 않고 AKS 클러스터에 연결할 수도 있습니다.

Azure Dev Spaces 연결은 연결 된 AKS 클러스터와 개발 컴퓨터 간의 트래픽을 리디렉션합니다. 이러한 트래픽 리디렉션을 통해 AKS 클러스터에서 실행 되는 서비스와 개발 컴퓨터의 코드가 동일한 AKS 클러스터에 있는 것 처럼 통신할 수 있습니다. 코드는 개발 컴퓨터에서 실행 되므로 해당 코드를 실행 하 고 디버그 하는 데 사용 하는 개발 도구를 유연 하 게 사용할 수도 있습니다. Azure Dev Spaces Connect는 개발 컴퓨터의 AKS 클러스터에서 pod에 사용할 수 있는 환경 변수 및 탑재 된 파일을 복제 하는 방법도 제공 합니다.

이 가이드에서는 다음을 수행하는 방법을 알아봅니다.

- Azure Dev Spaces를 Azure의 관리형 Kubernetes 클러스터에 설정합니다.
- 여러 마이크로서비스가 포함된 대규모 애플리케이션을 개발 공간에 배포합니다.
- Azure Dev Spaces Connect를 사용 하 여 AKS 클러스터와 개발 컴퓨터에서 실행 되는 코드 간에 트래픽을 리디렉션합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 가이드에서는 [Azure Dev Spaces 자전거 공유 샘플 응용 프로그램](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) 을 사용 하 여 Azure Dev Spaces 연결을 보여 줍니다. AKS 클러스터에 응용 프로그램을 대신 사용 하려는 경우 [여기](#use-azure-dev-spaces-connect)에서 시작할 수 있습니다.

### <a name="limitations"></a>제한 사항

* UDP는 현재 Azure Dev Spaces 연결에서 지원 되지 않습니다.

### <a name="prerequisites"></a>전제 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치][azure-cli]
* [Helm 2.13 이상 설치][helm-installed]
* MacOS 또는 Windows 10에서 [Azure Dev Spaces][azds-vs-code] 확장을 설치 하 고 실행 하는 [Visual Studio Code][vs-code] .

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기

[지원되는 지역][supported-regions]에서 AKS 클러스터를 만들어야 합니다. 아래 명령은 *MyResourceGroup*이라는 리소스 그룹과 *MyAKS*라는 AKS 클러스터를 만듭니다.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정

`use-dev-spaces` 명령을 사용하여 AKS 클러스터에서 Dev Spaces를 사용하도록 설정하고 프롬프트의 지시를 따릅니다. 아래 명령은 *MyResourceGroup* 그룹의 *MyAKS* 클러스터에서 Dev Spaces를 사용하도록 설정하고 *dev*라는 개발 공간을 만듭니다.

> [!NOTE]
> `use-dev-spaces` 명령은 Azure Dev Spaces CLI가 아직 설치되지 않은 경우에도 설치합니다. Azure Cloud Shell에는 Azure Dev Spaces CLI를 설치할 수 없습니다.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>샘플 애플리케이션 코드 받기

이 문서에서는 [Azure Dev Spaces 자전거 공유 샘플 애플리케이션](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)을 사용하여 Azure Dev Spaces 사용하는 방법을 보여 줍니다.

GitHub에서 애플리케이션을 복제하고 해당 디렉터리로 이동합니다.

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>*dev*에 대한 HostSuffix 검색

`azds show-context` 명령을 사용하여 *dev*에 대한 HostSuffix를 표시합니다.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>HostSuffix를 사용하여 Helm 차트 업데이트

[charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml)을 열고, 모든 `<REPLACE_ME_WITH_HOST_SUFFIX>` 인스턴스를 앞에서 검색한 HostSuffix 값으로 바꿉니다. 변경 내용을 저장하고 파일을 닫습니다.

## <a name="run-the-sample-application-in-kubernetes"></a>Kubernetes에서 샘플 애플리케이션 실행

Kubernetes에서 샘플 애플리케이션을 실행하는 명령은 기존 프로세스의 일부이며 Azure Dev Spaces 도구에 종속되지 않습니다. 이 경우 Helm은 이 샘플 애플리케이션을 실행하는 데 사용되는 도구이지만, 클러스터 내에서 네임스페이스의 전체 애플리케이션을 실행하는 경우 다른 도구를 사용할 수 있습니다. Helm 명령은 앞에서 만든 *dev*라는 개발 공간을 대상으로 하지만 이 개발 공간도 Kubernetes 네임스페이스입니다. 따라서 개발 공간은 다른 네임스페이스와 동일한 다른 도구에서 대상이 될 수 있습니다.

응용 프로그램을 배포 하는 데 사용 되는 도구에 관계 없이 클러스터에서 응용 프로그램을 실행 한 후 개발에 Azure Dev Spaces를 사용할 수 있습니다.

### <a name="use-helm-to-install-the-sample-application"></a>예제 응용 프로그램을 설치 하는 데 투구 사용

`helm init` 및 `helm install` 명령을 사용하여 클러스터에 샘플 애플리케이션을 설정하고 설치합니다.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```

> [!Note]
> **RBAC 사용 클러스터를 사용하는 경우**에는 [Tiller에 대한 서비스 계정](https://helm.sh/docs/using_helm/#role-based-access-control)을 구성해야 합니다. 그렇지 않으면, `helm` 명령이 실패합니다.

`helm install` 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 명령 출력은 완료되었을 때 클러스터에 배포된 모든 서비스의 상태를 보여 줍니다.

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

### <a name="navigate-to-your-sample-application"></a>샘플 응용 프로그램으로 이동

샘플 애플리케이션이 클러스터에 설치되고 클러스터에서 Dev Spaces를 사용하도록 설정되었으므로 `azds list-uris` 명령을 사용하여 현재 선택된 *dev*에 샘플 애플리케이션에 대한 URL을 표시합니다.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

`azds list-uris` 명령에서 공용 URL을 열어 *bikesharingweb* 서비스로 이동합니다. 위의 예제에서 *bikesharingweb* 서비스에 대한 공용 URL은 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`입니다. 사용자로 *Aurelia Briggs (고객)* 를 선택 하 고 임대에 대 한 자전거를 선택 합니다. 자전거에 대 한 자리 표시자 이미지가 표시 되는지 확인 합니다.

## <a name="use-azure-dev-spaces-connect"></a>Azure Dev Spaces 연결 사용

Visual Studio Code에서 *dev/samples/BikeSharingApp/Bikes* 를 열고 Azure Dev Spaces 연결 확장을 사용 하 여 개발 컴퓨터를 AKS 클러스터에 연결 합니다.

Azure Dev Spaces 연결 확장을 사용 하려면 *보기* , *명령 팔레트*를 차례로 클릭 하 여 Visual Studio Code 명령 팔레트를 엽니다. `Azure Dev Spaces: Redirect` 입력을 시작 하 고 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`또는 `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`를 클릭 합니다.

![연결 명령](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>리디렉션 옵션 선택

`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`를 실행 하는 경우 기존 Kubernetes 서비스를 선택 하 라는 메시지가 표시 됩니다.

![연결 선택 서비스](../media/how-to-connect/connect-choose-service.png)

이 옵션은이 서비스에 대 한 AKS 클러스터의 모든 트래픽을 개발 컴퓨터에서 실행 중인 응용 프로그램의 버전으로 리디렉션합니다. 이 서비스의 AKS 클러스터에서 여러 pod를 실행 하는 경우이 서비스에 대 한 모든 트래픽은 개발 컴퓨터에만 라우팅됩니다. 또한 연결 Azure Dev Spaces는 응용 프로그램의 모든 아웃 바운드 트래픽을 AKS 클러스터로 다시 라우팅합니다.

`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`를 실행 하는 경우 특정 pod를 선택 하 라는 메시지가 표시 됩니다.

![연결 선택 Pod](../media/how-to-connect/connect-choose-pod.png)

이 옵션은 특정 pod에 연결 합니다. 이 옵션은 트래픽을 전송 하거나 수신 하지 않고 종료 된 pod을 복제 하지 않는 pod와 상호 작용 하는 데 유용 합니다. Pod가 트래픽을 보내고 받는 경우이 옵션은 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` 하는 것과 비슷한 방식으로 동작 하며, 선택한 pod의 서비스와 관련 된 모든 pod에 대해 AKS 클러스터의 모든 트래픽을 리디렉션합니다.

`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`를 실행 하는 경우 기존 pod 또는 서비스를 선택 하 라는 메시지가 표시 되지 않습니다. 이 옵션은 개발 컴퓨터에서 실행 되는 응용 프로그램의 모든 아웃 바운드 트래픽을 AKS 클러스터로 리디렉션합니다.

이 예에서는 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`를 선택 하 고 *자전거* 서비스를 선택 합니다.

### <a name="select-a-connection-mode"></a>연결 모드 선택

리디렉션 옵션을 선택한 후에는 *바꾸기* 또는 *복제* 연결 모드를 선택 하 라는 메시지가 표시 됩니다.

![연결 바꾸기 또는 복제](../media/how-to-connect/connect-replace-clone.png)

*Replace* 옵션은 AKS 클러스터의 현재 pod 또는 서비스를 대체 하 고 해당 서비스에 대 한 모든 트래픽을 개발 컴퓨터로 리디렉션합니다. 이 옵션은 개발 컴퓨터에서 응용 프로그램을 시작할 때까지 리디렉션하는 서비스와 상호 작용 하는 AKS 클러스터의 다른 서비스에 방해가 될 수 있습니다. *복제* 옵션을 사용 하면 기존 자식 개발 공간을 선택 하거나 pod 나 서비스에 대 한 트래픽을 개발 컴퓨터로 리디렉션하는 새 자식 개발 공간을 만들 수 있습니다. 이 옵션을 사용 하면 해당 자식 개발 공간으로의 트래픽만 개발 컴퓨터로 리디렉션되도록 다른 서비스를 중단 하지 않고 격리로 작업할 수 있습니다. *복제* 옵션을 사용 하려면 AKS 클러스터가 Azure Dev Spaces 사용 하도록 설정 되어 있어야 합니다.

이 예에서는 *바꾸기*를 선택 합니다.

> [!NOTE]
> 기존 서비스의 pod에 여러 컨테이너가 있는 경우 응용 프로그램의 컨테이너를 선택 하 라는 메시지도 표시 됩니다.

### <a name="select-a-port-for-your-application"></a>응용 프로그램에 대 한 포트 선택

연결 모드를 선택한 후에는 로컬 응용 프로그램의 TCP 포트를 입력 하 라는 메시지가 표시 됩니다. 응용 프로그램에서 여러 포트를 여는 경우 쉼표로 구분 합니다 (예: *80, 81*). 응용 프로그램에서 네트워크 요청을 허용 하지 않는 경우 *0*을 입력 합니다. 이 예제에서는 *8080*를 입력 합니다.

![연결 선택 포트](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>연결 되어 있는지 확인

응용 프로그램의 TCP 포트를 선택한 후 Azure Dev Spaces Connect는 AKS 클러스터에 대 한 연결을 설정 합니다. Azure Dev Spaces 연결은 에이전트를 AKS 클러스터에 삽입 하 여 AKS 클러스터와 개발 컴퓨터 간에 트래픽을 리디렉션합니다. 이 연결을 설정 하는 데 몇 분 정도 걸릴 수 있습니다. 또한 연결 Azure Dev Spaces는 개발 컴퓨터에서 *호스트* 파일을 수정 하기 위해 관리자 액세스를 요청 합니다.

> [!IMPORTANT]
> 연결 Azure Dev Spaces AKS 클러스터에 연결 되 면 개발 컴퓨터에서 서비스를 시작할 때까지 AKS 클러스터의 다른 서비스가 올바르게 작동 하지 않을 수 있습니다. 또한 서비스에 개발 컴퓨터에서 사용할 수 없는 종속성이 있는 경우 응용 프로그램을 수정 하거나 [추가 구성을](#additional-configuration) 제공 해야 할 수 있습니다.

연결 Azure Dev Spaces AKS 클러스터에 대 한 연결을 설정한 후에 *AZDS connect-Bikes* 라는 터미널 창이 열립니다. 이 터미널 창에는 AKS 클러스터에서 구성 된 모든 환경 변수 및 DNS 항목이 있습니다. 이 터미널 창에서 실행 하거나 Visual Studio Code 디버거를 사용 하 여 실행 하는 모든 코드는 AKS 클러스터에 연결 됩니다.

![터미널 연결](../media/how-to-connect/connect-terminal.png)

또한 Azure Dev Spaces 연결을 사용 하면 *Dev Spaces* 에서 모든 출력을 연결 하는 창이 만들어집니다.

![연결 출력](../media/how-to-connect/connect-output.png)

연결 Azure Dev Spaces에는 연결 상태를 보여 주는 상태 표시줄 항목도 있습니다.

![연결 상태](../media/how-to-connect/connect-status.png)

상태 표시줄에 Dev Spaces가 표시 되는지 확인 합니다 *. 로컬 포트 8080에서 dev/bikes에 연결*됩니다.

### <a name="configure-your-application-on-your-development-machine"></a>개발 컴퓨터에서 응용 프로그램 구성

*AZDS 연결-자전거* 터미널 창을 열고 `npm install`를 실행 합니다.

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

[서버 .js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L352) 를 열고 352 줄에 업데이트 *포트* 를 *8080*:

```javascript
var port = 8080;
var server = null;
```

*디버그* 를 클릭 하 고 *구성 열기*를 클릭 합니다. 그러면 `.vscode/launch.json` 파일이 만들어집니다. 해당 파일의 내용을 다음으로 바꿉니다.

```json
{
    "configurations": [
        ...
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

[Package. json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) 을 열고 디버깅 스크립트를 추가 합니다.

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>개발 컴퓨터에서 응용 프로그램 시작

왼쪽의 *디버그* 아이콘을 클릭 하 고 맨 위에 있는 *NPM를 통해* 시작 옆의 시작 단추를 클릭 합니다.

![NPM를 통해 시작](../media/how-to-connect/launch-npm.png)

응용 프로그램이 시작 되 고 연결을 Azure Dev Spaces AKS 클러스터와 개발 컴퓨터 간의 트래픽이 리디렉션됩니다. *디버그 콘솔*아래와 비슷한 메시지가 표시 됩니다.

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 8080
```

Azure Dev Spaces 연결 상태 표시줄을 클릭 하 고 응용 프로그램의 공용 URL을 선택 하 여 *bikesharingweb* 서비스로 이동 합니다. 이전에 실행 한 `azds list-uris` 명령에서 공용 URL을 찾을 수도 있습니다. 클러스터에서 Azure Dev Spaces를 사용 하지 않는 경우 사용 중인 네임 스페이스의 응용 프로그램에 대 한 IP 또는 URL을 사용 합니다. 위의 예제에서 *bikesharingweb* 서비스에 대한 공용 URL은 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`입니다. 사용자로 *Aurelia Briggs (고객)* 를 선택 하 고 임대에 대 한 자전거를 선택 합니다.

### <a name="set-a-break-point"></a>중단점 설정

[Node.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) 를 열고 줄 233의 아무 곳 이나 클릭 하 여 커서를 놓습니다. *F9* 또는 *디버그* 를 클릭 한 다음 *중단점 설정/해제*를 클릭 하 여 중단점을 설정 합니다.

공용 URL을 열어 *bikesharingweb* 서비스로 이동 합니다. 사용자로 *Aurelia Briggs (고객)* 를 선택 하 고 임대에 대 한 자전거를 선택 합니다. 자전거에 대 한 이미지는 로드 되지 않습니다. Visual Studio Code로 돌아가서 233 줄이 강조 표시 됩니다. 설정 하는 중단점에서 233 줄의 서비스를 일시 중지 했습니다. 서비스를 다시 시작하려면 *F5* 키를 누르거나 *디버그*, *계속*을 차례로 클릭합니다. 브라우저로 돌아가서 자전거에 대 한 자리 표시자 이미지가 표시 되는지 확인 합니다.

`server.js`의 233 줄에 커서를 놓고 *F9*를 눌러 중단점을 제거 합니다.

### <a name="update-your-application"></a>애플리케이션 업데이트

`server.js`을 편집 하 여 232 및 233 줄을 제거 합니다.

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

변경 내용을 저장 하 고 *디버그* 를 클릭 한 다음 *디버깅을 다시 시작*합니다. 브라우저를 새로 고치고 자전거에 대 한 자리 표시자 이미지가 더 이상 표시 되지 않는지 확인 합니다.

*디버그*와 *디버깅 중지*를 차례로 클릭하여 디버거를 중지합니다. Azure Dev Spaces 연결 상태 표시줄을 클릭 하 여 AKS 클러스터에서 연결을 끊습니다.

## <a name="additional-configuration"></a>추가 구성

연결 Azure Dev Spaces는 추가 구성 없이 라우팅 트래픽을 처리 하 고 환경 변수를 복제할 수 있습니다. AKS 클러스터의 컨테이너에 탑재 된 파일 (예: ConfigMap 파일)을 다운로드 해야 하는 경우 해당 파일을 개발 컴퓨터에 다운로드 하는 `azds-local.env`를 만들 수 있습니다.

`azds-local.env`예제는 다음과 같습니다.

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>다음 단계

끌어오기 요청이 리포지토리의 주 분기에 병합 되기 전에 Azure Dev Spaces 및 GitHub 작업을 사용 하 여 끌어오기 요청에서 직접 AKS의 변경 내용을 테스트 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes Service & GitHub 작업][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download