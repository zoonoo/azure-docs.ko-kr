---
title: "Azure Container Instances - 다중 컨테이너 그룹 | Azure 문서"
description: "Azure Container Instances - 다중 컨테이너 그룹"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 94744e8138d40dd777c0c004472804e3af6c0b1e
ms.contentlocale: ko-kr
ms.lasthandoff: 07/28/2017

---

# <a name="deploy-a-container-group"></a>컨테이너 그룹 배포

Azure Container Instances에서는 *컨테이너 그룹*을 사용하여 여러 컨테이너를 단일 호스트에 배포할 수 있습니다. 로깅, 모니터링 또는 서비스에 두 번째 연결된 프로세스가 필요한 기타 구성용으로 응용 프로그램 사이드카를 빌드할 때 이러한 기능을 사용하면 유용합니다. 

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 간단한 다중 컨테이너 사이드카 구성을 실행하는 과정을 안내합니다.

## <a name="configure-the-template"></a>템플릿 구성

`azuredeploy.json`이라는 파일을 만들고 다음 json을 해당 파일에 복사합니다. 

이 샘플에서는 두 개의 컨테이너와 공용 IP 주소 하나가 포함된 컨테이너 그룹을 정의합니다. 그룹의 첫 번째 컨테이너는 인터넷 연결 응용 프로그램을 실행합니다. 두 번째 컨테이너인 사이드카는 그룹 로컬 네트워크를 통해 주 웹 응용 프로그램에 대한 HTTP 요청을 수행합니다. 

200 OK 이외의 HTTP 응답 코드가 수신된 경우에는 이 사이드카 예제를 확장하여 경고를 트리거할 수 있습니다. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",    
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
    "resources": [
      {
        "name": "myContainerGroup",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2017-08-01-preview",
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
              }
            ]
          }
        }
      }
    ],
    "outputs": {
      "containerIPv4Address": {
        "type": "string",
        "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
      }
    }
  }
```

개인 컨테이너 이미지 레지스트리를 사용하려면 다음과 같은 형식의 개체를 json 문서에 추가합니다.

```json
"imageRegistryCredentials": [
    {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
    }
]
```

## <a name="deploy-the-template"></a>템플릿 배포

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

[az group deployment create](/cli/azure/group/deployment#create) 명령을 사용하여 템플릿을 배포합니다.

```azurecli-interactive
az group deployment create --name myContainerGroup --resource-group myResourceGroup --template-file azuredeploy.json
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다. 

## <a name="view-deployment-state"></a>배포 상태 확인

배포 상태를 확인하려면 `az container show` 명령을 사용합니다. 그러면 응용 프로그램이 액세스할 수 있는 프로비전된 공용 IP 주소가 반환됩니다.

```azurecli-interactive
az container show --name myContainerGroup --resource-group myResourceGroup -o table
```

출력:

```azurecli
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGrou2  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>로그 보기   

`az container logs` 명령을 사용하여 컨테이너의 로그 출력을 확인합니다. `--container-name` 인수는 로그를 가져올 컨테이너를 지정합니다. 이 예제에서는 첫 번째 컨테이너를 지정합니다. 

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-app --resource-group myResourceGroup
```

출력:

```bash
istening on port 80
::1 - - [27/Jul/2017:17:35:29 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:32 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:35 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:38 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

사이드카 컨테이너의 로그를 확인하려면 두 번째 컨테이너 이름을 지정하여 같은 명령을 실행합니다.

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-sidecar --resource-group myResourceGroup
```

출력:

```bash
Every 3.0s: curl -I http://localhost                                                                                                                       Mon Jul 17 11:27:36 2017

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 1663
Content-Type: text/html; charset=utf-8
Last-Modified: Sun, 16 Jul 2017 02:08:22 GMT
Date: Mon, 17 Jul 2017 18:27:36 GMT
```

위 출력에 나와 있는 것처럼, 사이드카는 그룹 로컬 네트워크를 통해 주 웹 응용 프로그램에 대한 HTTP 요청을 주기적으로 수행하여 해당 응용 프로그램이 실행되고 있는지를 확인합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다중 컨테이너 Azure Container Instances를 배포하기 위해 수행해야 하는 단계에 대해 설명했습니다. 종단 간 Azure Container Instances 환경을 확인하려면 Azure Container Instances 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서]: ./container-instances-tutorial-prepare-app.md

