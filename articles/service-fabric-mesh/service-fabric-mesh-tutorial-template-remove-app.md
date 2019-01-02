---
title: 자습서 - Azure Service Fabric Mesh에서 실행 중인 앱 제거 | Microsoft Docs
description: 이 자습서에서는 Service Fabric Mesh에서 실행 중인 애플리케이션을 제거하고 리소스를 삭제하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 9b72ca2a166e805d960eab47835f449e0f5b991e
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889517"
---
# <a name="tutorial-remove-an-application-and-resources"></a>자습서: 애플리케이션 및 리소스 제거

이 자습서는 시리즈의 4부입니다. [이전에 Service Fabric Mesh에 배포된](service-fabric-mesh-tutorial-template-deploy-app.md) 실행 중인 응용 프로그램을 제거하는 방법을 알아보겠습니다. 

시리즈 4부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Service Fabric Mesh에서 실행 중인 앱 삭제
> * 애플리케이션 리소스 삭제

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [템플릿을 사용하여 Service Fabric Mesh에 응용 프로그램 배포](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Service Fabric Mesh에서 실행 중인 응용 프로그램 확장](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Service Fabric Mesh에서 실행 중인 응용 프로그램 업그레이드](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * 애플리케이션 제거

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 아직 없으면 시작하기 전에 [무료 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

* [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md)을 열거나 [Azure CLI 및 Service Fabric Mesh CLI를 로컬에 설치](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)합니다.

## <a name="delete-the-resource-group-and-all-the-resources"></a>리소스 그룹 및 모든 리소스 삭제

더 이상 필요하지 않은 경우 만든 리소스를 모두 삭제합니다. 이전에는 Azure Container Registry 인스턴스 및 Service Fabric Mesh 애플리케이션 리소스를 호스트하는 [새로운 리소스 그룹을 생성](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry)했습니다.  이 리소스 그룹은 삭제할 수 있으며, 그러면 연결된 모든 리소스도 삭제됩니다.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>리소스 개별 삭제
또한 ACR 인스턴스, Service Fabric Mesh 애플리케이션 및 네트워크 리소스를 개별적으로 삭제할 수도 있습니다.

ACR 인스턴스를 삭제하려면:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Service Fabric Mesh 애플리케이션을 삭제하려면:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

네트워크를 삭제하려면:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Service Fabric Mesh에서 실행 중인 앱 삭제
> * 애플리케이션 리소스 삭제