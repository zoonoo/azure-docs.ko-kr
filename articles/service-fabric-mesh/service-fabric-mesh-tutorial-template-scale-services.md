---
title: 자습서 - Azure Service Fabric Mesh에서 실행 중인 앱 크기 조정 | Microsoft Docs
description: 이 자습서에서는 Service Fabric Mesh에서 실행 중인 애플리케이션에서 서비스 크기를 조정하는 방법을 알아봅니다.
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
ms.openlocfilehash: 31b1e7a777c65a270b7b08673fcd73a5ab63f11f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337768"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>자습서: Service Fabric Mesh에서 실행 중인 애플리케이션 크기 조정

이 자습서는 시리즈의 2부입니다. [이전에 Service Fabric Mesh에 배포된](service-fabric-mesh-tutorial-template-deploy-app.md) 애플리케이션의 서비스 인스턴스 수를 수동으로 조정하는 방법을 알아봅니다. 조정이 완료되면 3개 인스턴스를 실행하는 프런트 엔드 서비스와 2개 인스턴스를 실행하는 데이터 서비스가 생깁니다.

시리즈 2부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 서비스 인스턴스 수를 원하는 대로 구성
> * 업그레이드 수행

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [템플릿을 사용하여 Service Fabric Mesh에 애플리케이션 배포](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Service Fabric Mesh에서 실행 중인 애플리케이션 크기 조정
> * [Service Fabric Mesh에서 실행되는 애플리케이션 업그레이드](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [응용 프로그램 제거](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 아직 없으면 시작하기 전에 [무료 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

* [로컬에 Azure CLI 및 Service Fabric Mesh CLI를 설치합니다](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="manually-scale-your-services-in-or-out"></a>수동으로 서비스 확대 또는 축소

Service Fabric Mesh에 애플리케이션을 배포하여 얻을 수 있는 주요 이점 중 하나는 서비스를 쉽게 확장 또는 축소하는 기능입니다. 이 기능을 사용하여 서비스에서 다양한 크기의 부하를 처리하거나 가용성을 향상할 수 있습니다.

이 자습서에서는 [이전에 배포되어](service-fabric-mesh-tutorial-template-deploy-app.md) 현재 실행 중인 할 일 목록 샘플을 예제로 사용합니다. 애플리케이션에 두 개의 서비스가 있습니다. WebFrontEnd 및 ToDoService 각 서비스는 처음에 복제본 1개로 배포되었습니다.  WebFrontEnd 서비스에 대해 실행 중인 복제본 수를 보려면 다음을 실행합니다.

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

ToDoService 서비스에 대해 실행 중인 복제본 수를 보려면 다음을 실행합니다.

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

애플리케이션 리소스에 대한 배포 템플릿에서, 각 서비스에는 서비스 배포 횟수를 설정하는 데 사용할 수 있는 *replicaCount* 속성이 있습니다. 애플리케이션은 함께 배포되고 관리되는 여러 서비스로 구성될 수 있으며, 각 서비스에는 고유한 *replicaCount* 수가 있습니다. 서비스 복제본의 수를 조정하려면 배포 템플릿 또는 매개 변수 파일에서 조정하려는 서비스의 *replicaCount* 값을 수정합니다.  그런 후 애플리케이션을 업그레이드합니다.

### <a name="modify-the-deployment-template-parameters"></a>배포 템플릿 매개 변수 수정

애플리케이션을 배포한 후에 템플릿의 값이 변경될 것으로 예상되거나 배포별로 값을 변경하는 옵션을 포함하려는 경우(다른 배포에 이 템플릿을 다시 사용하려는 경우) 가장 좋은 방법은 값을 매개 변수화하는 것입니다.

앞에서 [mesh_rp.windows.json 배포 템플릿](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) 및 [mesh_rp.windows.parameter.json 매개 변수](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) 파일을 사용하여 애플리케이션을 배포했습니다.

로컬로 [mesh_rp.windows.parameter.json 매개 변수](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) 파일을 열고 *frontEndReplicaCount* 값을 3으로, *serviceReplicaCount* 값을 2로 설정합니다.

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

변경 내용을 매개 변수 파일에 저장합니다.  *frontEndReplicaCount* 및 *serviceReplicaCount* 매개 변수는 [mesh_rp.windows.json 배포 템플릿](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)의 *매개 변수* 섹션에서 선언됩니다.

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

WebFrontEnd 서비스 *replicaCount* 속성은 *frontEndReplicaCount* 매개 변수를 참조하고 ToDoService 서비스 *replicaCount* 속성은 *serviceReplicaCount* 매개 변수를 참조합니다.

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

템플릿을 수정한 후에는 애플리케이션을 업그레이드합니다.

### <a name="upgrade-your-application"></a>애플리케이션 업그레이드

애플리케이션이 실행되는 동안 템플릿 및 업데이트된 매개 변수 파일을 다시 배포하여 애플리케이션을 업그레이드할 수 있습니다.

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

그러면 애플리케이션 롤링 업그레이드가 시작되고 몇 분 후 서비스 인스턴스 수가 증가하는 것을 볼 수 있습니다.  WebFrontEnd 서비스에 대해 실행 중인 복제본 수를 보려면 다음을 실행합니다.

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

ToDoService 서비스에 대해 실행 중인 복제본 수를 보려면 다음을 실행합니다.

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 서비스 인스턴스 수를 원하는 대로 구성
> * 업그레이드 수행

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Service Fabric Mesh에서 실행되는 애플리케이션 업그레이드](service-fabric-mesh-tutorial-template-upgrade-app.md)
