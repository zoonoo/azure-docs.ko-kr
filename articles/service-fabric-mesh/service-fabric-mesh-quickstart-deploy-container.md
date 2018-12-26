---
title: 빠른 시작 - Azure Service Fabric Mesh에 Hello World 배포 | Microsoft Docs
description: 이 빠른 시작에서는 Azure Service Fabric Mesh에 Service Fabric Mesh 응용 프로그램을 배포하는 방법을 보여줍니다.
services: service-fabric-mesh
keywords: SEO champ와 상담하지 않고 키워드를 추가하거나 편집하지 마세요.
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 4be24b00c3ac4ffadf7eafdc7397f59113ec03b2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088366"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>빠른 시작: Service Fabric Mesh에 Hello World 배포

[Service Fabric Mesh](service-fabric-mesh-overview.md)를 통해 가상 머신을 프로비전 할 필요 없이 Azure에서 마이크로 서비스 응용 프로그램을 쉽게 만들고 관리할 수 있습니다. 이 빠른 시작에서는 Azure에 Hello World 애플리케이션을 만들고 인터넷에 노출합니다. 이 작업은 단일 명령으로 완료됩니다. 몇 분 안에 브라우저에 이 보기가 표시됩니다.

![브라우저의 Hello World 앱][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Azure 계정이 아직 없는 경우 시작하기 전에 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI 설정 
Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 빠른 시작을 완료할 수 있습니다. 다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
Azure에 로그인하고 구독을 선택합니다.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>리소스 그룹 만들기
애플리케이션을 배포할 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하고 이 단계를 건너뛸 수 있습니다. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>응용 프로그램 배포
`az mesh deployment create` 명령을 사용하여 리소스 그룹에 응용 프로그램을 만듭니다.  다음을 실행합니다.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

이전 명령은 [linux.json 템플릿](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json)을 사용하여 Linux 애플리케이션을 배포합니다. Windows 애플리케이션을 배포하려면 [windows.json 템플릿](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json)을 사용합니다. Windows 컨테이너 이미지가 Linux 컨테이너 이미지보다 크므로 배포하는 데 시간이 더 걸릴 수 있습니다.

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
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>애플리케이션 열기
애플리케이션이 성공적으로 배포되면 CLI 출력의 서비스 엔드포인트에 대한 공용 IP 주소를 복사합니다. 웹 브라우저에서 IP 주소를 엽니다. Azure Service Fabric Mesh 로고가 있는 웹 페이지가 표시됩니다.

## <a name="check-the-application-details"></a>애플리케이션 세부 정보 확인
`az mesh app show` 명령을 사용하여 응용 프로그램 상태를 확인할 수 있습니다. 이 명령은 수행할 수 있는 유용한 정보를 제공합니다.

이 빠른 시작에 대한 애플리케이션 이름은 `helloWorldApp`이며 애플리케이션에 대한 세부 정보를 수집하기 위해 다음 명령을 실행합니다.

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>애플리케이션 로그 보기

`az mesh code-package-log get` 명령을 사용하여 배포된 응용 프로그램에 대한 로그를 검사합니다.

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션을 삭제할 준비가 되었을 때 [az group delete][az-group-delete] 명령을 실행하여 포함된 리소스 그룹, 애플리케이션 및 네트워크 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

Service Fabric Mesh 애플리케이션을 만들고 배포하는 방법을 자세히 알아보려면 자습서를 계속 진행합니다.
> [!div class="nextstepaction"]
> [다중 서비스 웹 응용 프로그램 만들기 및 배포](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
