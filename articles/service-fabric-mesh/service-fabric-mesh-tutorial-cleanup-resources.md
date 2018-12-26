---
title: 자습서 - Azure Service Fabric Mesh 리소스 정리 | Microsoft Docs
description: 더 이상 사용하지 않는 리소스 요금이 부과되지 않도록 Azure Service Fabric Mesh 리소스를 제거하는 방법을 알아봅니다.
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
ms.openlocfilehash: fb7a444c54a57e7f2c38d941eb99f2fea7eebcef
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993360"
---
# <a name="tutorial-remove-azure-resources"></a>자습서: Azure 리소스 제거

이 자습서는 시리즈의 5부이며 요금이 부과되지 않도록 앱 및 리소스를 삭제하는 방법을 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 리소스 요금이 부과되지 않도록 앱에서 사용하는 리소스를 정리합니다.

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [Visual Studio에서 Service Fabric Mesh 앱 만들기](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [로컬 개발 클러스터에서 실행 중인 Service Fabric Mesh 앱 디버그](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh 앱 배포](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh 앱 업그레이드](service-fabric-mesh-tutorial-upgrade.md)
> * Service Fabric Mesh 리소스 정리

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* 할 일 앱을 배포하지 않은 경우 [Service Fabric Mesh 웹 응용 프로그램 게시](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)의 지침을 따르세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 마지막 부분입니다. 만든 리소스를 더 이상 사용하지 않는 경우 리소스 요금이 부과되지 않도록 리소스를 삭제하세요. Mesh는 초당 요금이 부과되는 서버리스 서비스이기 때문에 리소스를 삭제하는 것이 특히 중요합니다. Mesh 가격 책정에 대한 자세한 내용은 https://aka.ms/sfmeshpricing에서 확인할 수 있습니다.

Azure의 편리한 점 중 하나는 특정 리소스 그룹에 연결된 리소스를 만들 때 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 모든 리소스가 삭제된다는 것입니다. 따라서 리소스를 하나씩 삭제할 필요가 없습니다.

할 일 앱을 만들기 위한 새 리소스 그룹을 만들었으니, 이 리소스 그룹을 안전하게 삭제할 수 있습니다. 그러면 해당 그룹과 연결된 리소스가 모두 삭제됩니다.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

또는 [포털에서](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources) **sfmeshTutorial1RG** 리소스 그룹을 삭제해도 됩니다. 

## <a name="next-steps"></a>다음 단계

이제 Azure에 대한 Service Fabric Mesh 애플리케이션 게시가 완료되었으므로 다음을 시도해 보세요.

* 서비스 간 통신의 다른 예제를 보려면 [투표 앱 샘플](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)을 탐색하세요.
* Service Fabric 리소스 모델에 대한 자세한 내용은 [Service Fabric Mesh 리소스 모델](service-fabric-mesh-service-fabric-resources.md)을 참조하세요.
* Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.
* [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)에 대해 알아봅니다.