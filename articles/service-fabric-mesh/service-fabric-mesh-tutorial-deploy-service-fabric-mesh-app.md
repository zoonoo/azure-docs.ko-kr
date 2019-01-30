---
title: 자습서 - Service Fabric Mesh 애플리케이션 배포 | Microsoft Docs
description: Visual Studio를 사용하여 백 엔드 웹 서비스와 통신하는 ASP.NET Core 웹 사이트로 구성된 Azure Service Fabric Mesh 애플리케이션을 게시하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: e1f2991b2e006c97087c6288d3ed3c20d2927e8c
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413484"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>자습서: Service Fabric Mesh 애플리케이션 배포

이 자습서는 시리즈의 3부이며, Visual Studio에서 직접 Azure Service Fabric Mesh 웹 애플리케이션을 게시하는 방법을 보여 줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Visual Studio를 사용하여 Azure에 앱을 게시합니다.
> * 애플리케이션 배포 상태 확인
> * 현재 구독에 배포된 모든 애플리케이션 보기

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [Visual Studio에서 Service Fabric Mesh 앱 만들기](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [로컬 개발 클러스터에서 실행 중인 Service Fabric Mesh 앱 디버그](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Service Fabric Mesh 앱 배포
> * [Service Fabric Mesh 앱 업그레이드](service-fabric-mesh-tutorial-upgrade.md)
> * [Service Fabric Mesh 리소스 정리](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 아직 없으면 시작하기 전에 [무료 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

* Service Fabric 런타임, SDK, Docker 및 Visual Studio 2017 설치가 포함된 [개발 환경을 설정](service-fabric-mesh-howto-setup-developer-environment-sdk.md)했는지 확인합니다.

## <a name="download-the-to-do-sample-application"></a>할 일 애플리케이션 샘플 다운로드

[이 자습서 시리즈의 2부](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)에서 할 일 애플리케이션 샘플을 빌드하지 않은 경우 다운로드할 수 있습니다. 명령 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

애플리케이션은 `src\todolistapp` 디렉터리 아래에 있습니다.

## <a name="publish-to-azure"></a>Azure에 게시

Service Fabric Mesh 프로젝트를 Azure에 게시하려면 Visual Studio에서 **todolistapp**을 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.

그러면 **Service Fabric 애플리케이션 게시** 대화 상자가 표시됩니다.

![Visual Studio의 Service Fabric Mesh 게시 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Azure 계정과 구독을 선택합니다. **위치**를 선택합니다. 이 문서에서는 **미국 동부**를 사용합니다.

**리소스 그룹** 아래에서 **\<새 리소스 그룹 만들기...>** 를 선택합니다. 새 리소스 그룹을 만드는 대화 상자가 표시됩니다. 이 문서에서는 **미국 동부** 위치를 사용하고, 그룹 이름을 **sfmeshTutorial1RG**로 지정합니다(조직에서 여러 사용자가 동일한 구독을 사용하는 경우 고유한 그룹 이름을 선택함).  **만들기**를 눌러 리소스 그룹을 만들고, 게시 대화 상자로 돌아갑니다.

![Visual Studio의 Service Fabric Mesh 새 리소스 그룹 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

**Service Fabric 애플리케이션 게시** 대화 상자로 돌아간 다음, **Azure Container Registry**에서 **\<새 컨테이너 레지스트리 만들기...&gt;** 를 선택합니다. **컨테이너 레지스트리 만들기** 대화 상자에서 **컨테이너 레지스트리 이름**에 대해 고유한 이름을 사용합니다. **위치**를 지정합니다(이 자습서에서는 **미국 동부** 사용). 이전 단계에서 만든 **리소스 그룹**(예: **sfmeshTutorial1RG**)을 드롭다운에서 선택합니다. **SKU**를 **기본**으로 설정한 후 **만들기**를 눌러 개인 Azure 컨테이너 레지스트리를 만들고 게시 대화 상자로 돌아갑니다.

![Visual Studio Service Fabric Mesh 새 컨테이너 레지스트리 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

리소스 공급자가 구독에 등록되지 않았다는 오류가 표시되는 경우, 리소스 공급자를 등록할 수 있습니다. 먼저 리소스 공급자를 구독에 사용할 수 있는지 확인합니다.

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

컨테이너 레지스트리 공급자(`Microsoft.ContainerRegistry`)를 사용할 수 있는 경우 Powershell에서 등록합니다.

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

게시 대화 상자에서 **게시** 단추를 눌러 Service Fabric 애플리케이션을 Azure에 배포합니다.

처음으로 Azure에 게시하는 경우 Docker 이미지가 ACR(Azure Container Registry)에 게시되며, 이미지 크기에 따라 시간이 걸립니다. 이후에 동일한 프로젝트를 게시할 때는 더 빨리 게시됩니다. Visual Studio **출력** 창에서 **Service Fabric 도구** 창을 선택하여 배포 진행률을 모니터링할 수 있습니다. 배포가 완료되면 **Service Fabric 도구** 출력에 애플리케이션의 IP 주소와 포트가 URL 형식으로 표시됩니다.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

웹 브라우저를 열고, URL로 이동하여 Azure에서 실행되는 웹 사이트를 확인합니다.

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI 설정

나머지 단계에서는 Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용할 수 있습니다. 다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치합니다.

## <a name="check-application-deployment-status"></a>애플리케이션 배포 상태 확인

이 시점에서 애플리케이션이 배포되었습니다. `app show` 명령을 사용하여 상태를 확인할 수 있습니다. 

자습서 앱에 대한 애플리케이션 이름은 `todolistapp`입니다. 다음 명령을 사용하여 애플리케이션에 대한 세부 정보를 수집합니다.

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="get-the-ip-address-of-your-deployment"></a>배포의 IP 주소 가져오기

애플리케이션에 대한 IP 주소를 가져오려는 경우 다음 명령을 사용합니다.
  
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>현재 구독에 배포된 모든 애플리케이션 보기

“app list” 명령을 사용하여 구독에 배포한 애플리케이션 목록을 가져올 수 있습니다.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure에 앱 게시
> * 애플리케이션 배포 상태 확인
> * 현재 구독에 배포된 모든 애플리케이션 보기

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Service Fabric Mesh 앱 업그레이드](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
