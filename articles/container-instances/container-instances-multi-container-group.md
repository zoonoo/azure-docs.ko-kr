---
title: Azure Container Instances에 다중 컨테이너 그룹 배포
description: Azure Container Instances에서 여러 컨테이너가 있는 컨테이너 그룹을 배포하는 방법을 알아봅니다.
services: container-instances
author: iainfoulds
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6d337c9ed23ac9af884f4113b046a8e9756fd441
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097107"
---
# <a name="deploy-a-container-group"></a>컨테이너 그룹 배포

Azure Container Instances에서는 [컨테이너 그룹](container-instances-container-groups.md)을 사용하여 여러 컨테이너를 단일 호스트에 배포하도록 지원합니다. 로깅, 모니터링 또는 서비스에 두 번째 연결된 프로세스가 필요한 기타 구성용으로 응용 프로그램 사이드카를 빌드할 때 이러한 기능을 사용하면 유용합니다.

Azure CLI를 사용하여 다중 컨테이너 그룹을 배포하는 두 가지 방법이 있습니다.

* Resource Manager 템플릿 배포(이 문서)
* [YAML 파일 배포](container-instances-multi-container-yaml.md)

컨테이너 인스턴스 배포 시 추가 Azure 서비스 리소스(예: Azure 파일 공유)를 배포해야 하는 경우 Resource Manager 템플릿을 사용하여 배포하는 것이 좋습니다. YAML 형식이 좀 더 간결하기 때문에 배포에 컨테이너 인스턴스*만* 있는 경우 YAML 파일을 사용하여 배포하는 것이 좋습니다.

> [!NOTE]
> 현재 다중 컨테이너 그룹은 Linux 컨테이너에 제한됩니다. 모든 기능을 Windows 컨테이너에서 제공하려고 합니다. 그 동안 [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md)에서 현재 플랫폼의 차이점을 찾을 수 있습니다.

## <a name="configure-the-template"></a>템플릿 구성

본 문서의 이 섹션에서는 Azure Resource Manager 템플릿을 배포하여 간단한 다중 컨테이너 사이드카 구성을 실행하는 과정을 설명합니다.

먼저 `azuredeploy.json` 파일을 만든 후, 다음 JSON을 이 파일에 복사합니다.

이 Resource Manager 템플릿은 두 개의 컨테이너, 하나의 공용 IP 주소, 두 개의 노출된 포트가 포함된 컨테이너 그룹을 정의합니다. 그룹의 첫 번째 컨테이너는 인터넷 연결 응용 프로그램을 실행합니다. 두 번째 컨테이너인 사이드카는 그룹 로컬 네트워크를 통해 주 웹 응용 프로그램에 대한 HTTP 요청을 수행합니다.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-04-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

개인 컨테이너 이미지 레지스트리를 사용하려면 다음과 같은 형식의 개체를 JSON 문서에 추가합니다. 이 구성의 예제 구성은 [ACI Resource Manager 템플릿 참조][template-reference] 설명서를 참조하세요.

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>템플릿 배포

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az group deployment create][az-group-deployment-create] 명령을 사용하여 템플릿을 배포합니다.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다.

## <a name="view-deployment-state"></a>배포 상태 확인

배포 상태를 확인하려면 [az container show][az-container-show] 명령을 사용합니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

실행 중인 응용 프로그램을 보려면 사용하는 브라우저에서 공용 IP 주소로 이동합니다. 예를 들어 이 예제 출력의 IP는 `52.168.26.124`입니다.

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>로그 보기

[az container logs][az-container-logs] 명령을 사용하여 컨테이너의 로그 출력을 확인합니다. `--container-name` 인수는 로그를 가져올 컨테이너를 지정합니다. 이 예제에서는 첫 번째 컨테이너를 지정합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

출력

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

사이드카 컨테이너의 로그를 확인하려면 두 번째 컨테이너 이름을 지정하여 같은 명령을 실행합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

출력

```bash
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

위 출력에 나와 있는 것처럼, 사이드카는 그룹 로컬 네트워크를 통해 주 웹 응용 프로그램에 대한 HTTP 요청을 주기적으로 수행하여 해당 응용 프로그램이 실행되고 있는지를 확인합니다. 200 OK 이외의 HTTP 응답 코드가 수신된 경우에는 이 사이드카 예제를 확장하여 경고를 트리거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다중 컨테이너 Azure Container Instances를 배포하기 위해 수행해야 하는 단계에 대해 설명했습니다. 종단 간 Azure Container Instances 환경을 확인하려면 Azure Container Instances 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
