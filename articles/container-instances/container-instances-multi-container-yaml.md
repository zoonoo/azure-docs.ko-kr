---
title: Azure CLI 및 YAML을 사용하여 Azure Container Instances에 다중 컨테이너 그룹 배포
description: Azure CLI 및 YAML 파일을 사용하여 Azure Container Instances에서 여러 컨테이너가 있는 컨테이너 그룹을 배포하는 방법을 알아봅니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/17/2018
ms.author: danlep
ms.openlocfilehash: ffc9cf24e686924878a752b5d9df31160328ef0a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854714"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>YAML을 사용하여 다중 컨테이너 컨테이너 그룹 배포

Azure Container Instances에서는 [컨테이너 그룹](container-instances-container-groups.md)을 사용하여 여러 컨테이너를 단일 호스트에 배포하는 것을 지원합니다. 로깅, 모니터링 또는 서비스에 두 번째로 연결된 프로세스가 필요한 기타 구성용으로 애플리케이션 사이드카를 빌드할 때 다중 컨테이너 컨테이너 그룹을 사용하면 유용합니다.

Azure CLI를 사용하여 다중 컨테이너 그룹을 배포하는 두 가지 방법이 있습니다.

* YAML 파일 배포(이 문서)
* [Resource Manager 템플릿 배포](container-instances-multi-container-group.md)

YAML 형식이 좀 더 간결하기 때문에 배포에 컨테이너 인스턴스*만* 있는 경우 YAML 파일을 사용하여 배포하는 것이 좋습니다. 컨테이너 인스턴스 배포 시 추가 Azure 서비스 리소스(예: Azure 파일 공유)를 배포해야 하는 경우 Resource Manager 템플릿 배포를 사용하는 것이 좋습니다.

> [!NOTE]
> 현재 다중 컨테이너 그룹은 Linux 컨테이너에 제한됩니다. 모든 기능을 Windows 컨테이너에서 제공하려고 합니다. 그 동안 [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md)에서 현재 플랫폼의 차이점을 찾을 수 있습니다.

## <a name="configure-the-yaml-file"></a>YAML 파일 구성

Azure CLI에서 [az container create][az-container-create] 명령을 사용하여 다중 컨테이너 컨테이너 그룹을 배포하려면 YAML 파일에서 컨테이너 그룹 구성을 지정한 다음, YAML 파일을 매개 변수로 명령에 전달해야 합니다.

먼저 다음 YAML을 **deploy-aci.yaml**이라는 새 파일에 복사합니다.

이 YAML 파일은 두 개의 컨테이너, 하나의 공용 IP 주소, 두 개의 노출된 포트가 있는 “myContainerGroup”이라는 컨테이너 그룹을 정의합니다. 그룹의 첫 번째 컨테이너는 인터넷 연결 웹 애플리케이션을 실행합니다. 두 번째 컨테이너인 사이드카는 컨테이너 그룹의 로컬 네트워크를 통해 첫 번째 컨테이너에서 실행되는 웹 애플리케이션에 주기적으로 HTTP 요청을 보냅니다.

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="deploy-the-container-group"></a>컨테이너 그룹 배포

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az container create][az-container-create] 명령을 사용하여 컨테이너 그룹을 배포하고, YAML 파일을 인수로 전달합니다.

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다.

## <a name="view-deployment-state"></a>배포 상태 확인

배포 상태를 확인하려면 [az container show][az-container-show] 명령을 사용합니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

실행 중인 애플리케이션을 보려면 사용하는 브라우저에서 공용 IP 주소로 이동합니다. 예를 들어 이 예제 출력의 IP는 `52.168.26.124`입니다.

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>로그 보기

[az container logs][az-container-logs] 명령을 사용하여 컨테이너의 로그 출력을 확인합니다. `--container-name` 인수는 로그를 가져올 컨테이너를 지정합니다. 이 예제에서는 첫 번째 컨테이너를 지정합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

출력:

```console
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

사이드카 컨테이너의 로그를 확인하려면 두 번째 컨테이너 이름을 지정하여 같은 명령을 실행합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

출력:

```console
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

위 출력에 나와 있는 것처럼, 사이드카는 그룹 로컬 네트워크를 통해 주 웹 애플리케이션에 대한 HTTP 요청을 주기적으로 수행하여 해당 애플리케이션이 실행되고 있는지를 확인합니다. 200 OK 이외의 HTTP 응답 코드가 수신된 경우에는 이 사이드카 예제를 확장하여 경고를 트리거할 수 있습니다.

## <a name="deploy-from-private-registry"></a>개인 레지스트리에서 배포

개인 컨테이너 이미지 레지스트리를 사용하려면 환경에 맞게 수정된 값이 포함된 다음 YAML을 포함해야 합니다.

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

예를 들어 다음 YAML은 "myregistry"라는 개인 Azure Container Registry에서 이미지를 끌어온 단일 컨테이너를 사용하여 컨테이너 그룹을 배포합니다.

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>YAML로 컨테이너 그룹 내보내기

[az container export][az-container-export] Azure CLI 명령을 사용하여 기존 컨테이너 그룹의 구성을 YAML 파일로 내보낼 수 있습니다.

이 내보내기는 컨테이너 그룹의 구성을 유지하는 데 유용하며, 컨테이너 그룹 구성을 "코드로 구성"에 대한 버전 제어에 저장할 수 있습니다. 또는 YAML에서 새 구성을 개발할 때 내보낸 파일을 시작점으로 사용할 수 있습니다.

[az container export][az-container-export] 명령을 실행하여 이전에 만든 컨테이너 그룹의 구성을 내보낼 수 있습니다.

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

명령이 성공하면 아무 출력도 표시되지 않지만, 파일의 콘텐츠를 보면 결과를 알 수 있습니다. 예를 들어 `head`가 붙은 처음 몇 줄은 다음과 같습니다.

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 다중 컨테이너 Azure Container Instances를 배포하기 위해 수행해야 하는 단계에 대해 설명했습니다. 개인 Azure 컨테이너 레지스트리가 포함된 종합적 Azure Container Instances 환경에 대한 내용은 Azure Container Instances 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
