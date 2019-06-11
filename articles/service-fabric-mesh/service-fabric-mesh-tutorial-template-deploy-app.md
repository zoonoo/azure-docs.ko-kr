---
title: 자습서 - Azure Service Fabric Mesh에 앱 배포 | Microsoft Docs
description: 이 자습서에서는 템플릿을 사용하여 Service Fabric Mesh에 애플리케이션을 배포하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: ce063d8a256cbf2507e19d459aafe13150eccce7
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306944"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>자습서: 템플릿을 사용하여 Service Fabric Mesh에 애플리케이션 배포

이 자습서는 시리즈의 1부입니다. 여기서는 템플릿을 사용하여 Azure Service Fabric Mesh 응용 프로그램을 배포하는 방법을 알아봅니다.  애플리케이션은 ASP.NET 웹 프런트 엔드 서비스와 ASP.NET Core Web API 백 엔드 서비스(Docker Hub에서 제공됨)로 구성되어 있습니다.  Docker Hub에서 두 컨테이너 이미지를 끌어온 다음 프라이빗 레지스트리로 푸시합니다. 그런 다음, 애플리케이션용 Azure RM 템플릿을 만들고 컨테이너 레지스트리에서 Service Fabric Mesh로 애플리케이션을 배포합니다. 이 작업을 완료하면 Service Fabric Mesh에서 실행되는 간단한 할 일 목록 응용 프로그램이 완성됩니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 프라이빗 Azure Container Registry 인스턴스 만들기
> * 레지스트리로 컨테이너 이미지 푸시
> * RM 템플릿 및 parameters 파일 만들기
> * Service Fabric Mesh에 응용 프로그램 배포

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 템플릿을 사용하여 Service Fabric Mesh에 응용 프로그램 배포
> * [Service Fabric Mesh에서 실행되는 응용 프로그램에서 서비스 확장](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Service Fabric Mesh에서 실행 중인 응용 프로그램 업그레이드](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [응용 프로그램 제거](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 아직 없으면 시작하기 전에 [무료 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

* [Docker 설치](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [로컬에 Azure CLI 및 Service Fabric Mesh CLI를 설치합니다](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

Service Fabric Mesh 응용 프로그램의 서비스와 연결된 컨테이너 이미지는 컨테이너 레지스트리에 저장해야 합니다.  이 자습서에서는 프라이빗 ACR(Azure Container Registry) 인스턴스를 사용합니다. 

아래 단계에 따라 ACR 인스턴스를 만듭니다.  ACR 인스턴스가 이미 설치되어 있는 경우 다음 작업으로 건너뛰면 됩니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

Azure에 로그인하고 활성 구독을 설정합니다.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 명령을 사용하여 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>컨테이너 레지스트리 만들기

`az acr create` 명령을 사용하여 ACR 인스턴스를 만듭니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자를 포함해야 합니다. 다음 예제에서는 이름으로 *myContainerRegistry*를 사용합니다. 레지스트리 이름이 사용 중이라는 오류가 표시되면 다른 이름을 선택합니다.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

레지스트리를 만들면 다음과 같은 출력이 표시됩니다.

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>Azure Container Registry로 이미지 푸시

이 자습서에서는 To Do List 샘플 애플리케이션을 예제로 사용합니다.  [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) 및 [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) 서비스용 컨테이너 이미지는 Docker Hub에서 확인할 수 있습니다. Visual Studio에서 애플리케이션을 빌드하는 방법에 대한 정보는 [Service Fabric Mesh 웹앱 빌드](service-fabric-mesh-tutorial-create-dotnetcore.md)를 참조하세요. Service Fabric Mesh는 Windows 또는 Linux Docker 컨테이너를 실행할 수 있습니다.  Linux 컨테이너를 사용 중이라면 Docker에서 **Linux 컨테이너로 전환**을 선택합니다.  Windows 컨테이너를 사용 중이라면 Docker에서 **Windows 컨테이너로 전환**을 선택합니다.

ACR 인스턴스로 이미지를 푸시하려면 컨테이너 이미지가 있어야 합니다. 로컬 컨테이너 이미지가 아직 없으면 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Docker Hub에서 [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) 및 [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) 이미지를 끌어옵니다.

Windows 이미지를 끌어옵니다.

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

레지스트리에 이미지를 푸시하기 전에 ACR 로그인 서버의 정규화된 이름을 사용하여 태그를 지정해야 합니다.

다음 명령을 실행하여 ACR 인스턴스의 전체 로그인 서버 이름을 가져옵니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

이제 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 명령을 사용하여 Docker 이미지에 태그를 지정합니다. 다음 예제에서는 seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 및 seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 이미지에 태그를 지정합니다. 다른 이미지를 사용하는 경우 다음 명령에서 이미지 이름을 해당 이미지 이름으로 대체하세요.

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Azure Container Registry에 로그인합니다.

```azurecli
az acr login -n myContainerRegistry
```

[docker push](https://docs.docker.com/engine/reference/commandline/push/) 명령을 사용하여 ACR 인스턴스로 이미지를 푸시합니다.

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>컨테이너 이미지 나열

ACR 인스턴스의 리포지토리를 확인하려면 다음 명령을 실행합니다.

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

다음 예제에서는 리포지토리의 태그를 나열합니다 **.**

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

이전 출력에서는 프라이빗 컨테이너 레지스트리에 `azure-mesh-todo-service:1.0-nanoserver-1709`이 있는지 확인합니다.  `azure-mesh-todo-webfrontend:1.0-nanoserver-1709`가 있는지도 확인합니다.

## <a name="retrieve-credentials-for-the-registry"></a>레지스트리의 자격 증명 검색

> [!IMPORTANT]
> 프로덕션 시나리오에서는 ACR 인스턴스에서 관리 사용자를 활성화하지 않는 것이 좋습니다. 이 예제에서는 편의상 관리 사용자를 활성화하는 것입니다. 프로덕션 시나리오에서는 사용자 및 시스템 인증에 모두 [서비스 주체](https://docs.microsoft.com/azure/container-registry/container-registry-auth-service-principal)를 사용해야 합니다.

템플릿을 사용하여 만든 레지스트리에서 컨테이너 인스턴스를 배포하려면 배포 중에 레지스트리 자격 증명을 제공해야 합니다. 먼저 다음 명령을 사용하여 레지스트리에서 관리 사용자를 활성화합니다.

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

그런 후에 다음 명령을 사용하여 레지스트리 로그인 서버 이름, 사용자 이름 및 암호를 가져옵니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

반환된 ACR 로그인 서버 이름, 사용자 이름 및 암호 값은 다음 섹션에서 RM 템플릿 및 parameters 파일을 만들 때 사용합니다.

## <a name="download-and-explore-the-template-and-parameters-files"></a>템플릿 및 parameters 파일 다운로드/탐색

Service Fabric Mesh 애플리케이션은 Azure RM(Resource Manager) 템플릿을 사용하여 배포하고 관리할 수 있는 Azure 리소스입니다. Azure 솔루션 배포 및 관리와 관련된 개념이 익숙하지 않은 경우 [Azure Resource Manager 개요](/azure/azure-resource-manager/resource-group-overview) 및 [RM 템플릿의 구조와 구문 이해](/azure/azure-resource-manager/resource-group-authoring-templates)를 참조하세요.

이 자습서에서는 To Do List 샘플을 예제로 사용합니다.  새 템플릿 및 parameters 파일을 작성하는 대신 [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) and [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) 파일을 다운로드하세요.

### <a name="parameters"></a>매개 변수
응용 프로그램을 배포한 후에 템플릿의 값이 변경될 것으로 예상되거나 배포별로 값을 변경하는 옵션을 포함하려는 경우(다른 배포에 이 템플릿을 다시 사용하려는 경우) 가장 좋은 방법은 값을 매개 변수화하는 것입니다. 이렇게 하려면 배포 템플릿 맨 위에 “parameters” 섹션을 만들고 여기서 매개 변수 이름과 속성을 지정합니다. 그러면 나중에 배포 템플릿에서 해당 이름과 속성이 참조됩니다. 각 매개 변수 정의는 *type*, *defaultValue* 및 *description*이 있는 선택적 *metadata* 섹션을 포함합니다.

parameters 섹션은 배포 템플릿 맨 위의 *resources* 섹션 바로 앞에 정의됩니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

[mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)에서 선언되는 매개 변수는 location, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount, serviceReplicaCount입니다.  각 매개 변수의 설명은 배포 템플릿 파일에서 확인할 수 있습니다.

이러한 매개 변수는 [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) 파일에서 사용됩니다.  별도의 parameters 파일을 사용하는 경우 배포 템플릿 자체를 업데이트하지 않고도 배포 간에 매개 변수 값을 변경할 수 있습니다.

### <a name="overview-of-the-application-and-services"></a>애플리케이션 및 서비스 개요

서비스는 템플릿에서 애플리케이션 리소스의 속성으로 지정됩니다.  애플리케이션은 템플릿에서 리소스로 선언되는 프라이빗 네트워크에 배포됩니다.  서비스는 볼륨을 사용하여 데이터를 유지할 수 있습니다. 이러한 볼륨은 템플릿에서 리소스로 선언됩니다.  각 서비스에 대해 OS 유형, 코드 패키지, 복제본 수 및 네트워크가 서비스 속성으로 지정됩니다.  각 코드 패키지에 대해 컨테이너 이미지, 엔드포인트, 메모리 및 CPU 리소스와 이미지 리포지토리 자격 증명을 지정합니다. 여러 서비스가 포함된 Service Fabric Mesh 애플리케이션용 템플릿은 대략적으로 다음과 같습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 20001
                    }
                  ],
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

To Do List 애플리케이션의 구체적인 정보는 [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) 파일을 참조하세요.

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Service Fabric Mesh에 애플리케이션 배포
다음 명령을 사용하여 애플리케이션 및 관련 리소스를 만들고 이전 [레지스트리의 자격 증명 검색](#retrieve-credentials-for-the-registry) 단계의 자격 증명을 제공합니다.

parameters 파일에서 다음 매개 변수 값을 업데이트합니다.

|매개 변수|값|
|---|---|
|location|응용 프로그램을 배포할 지역입니다.  예를 들면 “eastus”입니다.|
|registryPassword|이전에 [레지스트리의 자격 증명 검색](#retrieve-credentials-for-the-registry)에서 가져온 암호입니다. 템플릿의 이 매개 변수는 보안 문자열이므로 배포 상태 또는 `az mesh service show` 명령에 표시되지 않습니다.|
|registryUserName|[레지스트리의 자격 증명 검색](#retrieve-credentials-for-the-registry)에서 가져온 사용자 이름입니다.|
|registryServer|[레지스트리의 자격 증명 검색](#retrieve-credentials-for-the-registry)에서 가져온 레지스트리 서버 이름입니다.|
|frontEndImage|프런트 엔드 서비스용 컨테이너 이미지입니다.  예: `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709`|
|serviceImage|백 엔드 서비스용 컨테이너 이미지입니다.  예: `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709`|

애플리케이션을 배포하려면 다음 명령을 실행합니다.

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

이 명령은 아래 표시되는 JSON 코드 조각을 생성합니다. JSON 출력의 ```outputs``` 섹션에서 ```publicIPAddress``` 속성을 복사합니다.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

이 정보는 ARM 템플릿의 ```outputs``` 섹션에서 제공됩니다. 아래와 같이 이 섹션에서는 공용 IP 주소를 가져올 게이트웨이 리소스를 참조합니다. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>응용 프로그램 열기

응용 프로그램이 정상적으로 배포되고 나면 서비스 엔드포인트의 공용 IP 주소를 가져옵니다. 배포 명령은 서비스 엔드포인트의 공용 IP 주소를 반환합니다. 필요한 경우, 네트워크 리소스를 쿼리하여 서비스 엔드포인트의 공용 IP 주소를 찾을 수도 있습니다. 이 응용 프로그램의 네트워크 리소스 이름은 `todolistappNetwork`입니다. 다음 명령을 사용하여 이 정보를 페치합니다. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

웹 브라우저에서 IP 주소로 이동합니다.

## <a name="check-application-status"></a>애플리케이션 상태 확인

app show 명령을 사용하여 애플리케이션 상태를 확인할 수 있습니다. 배포된 응용 프로그램의 응용 프로그램 이름은 “todolistapp”이므로 다음과 같이 해당 정보를 가져옵니다.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

`az mesh code-package-log get` 명령을 사용하여 배포된 애플리케이션에 대한 로그를 검사합니다.
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 프라이빗 컨테이너 레지스트리 만들기
> * 레지스트리로 컨테이너 이미지 푸시
> * 템플릿 및 parameters 파일 만들기
> * Service Fabric Mesh에 응용 프로그램 배포

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Service Fabric Mesh에서 실행되는 응용 프로그램 크기 조정](service-fabric-mesh-tutorial-template-scale-services.md)
