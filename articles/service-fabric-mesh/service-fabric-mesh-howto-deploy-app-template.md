---
title: 템플릿을 사용하여 Azure Service Fabric Mesh에 앱 배포 | Microsoft Docs
description: Azure CLI를 사용하여 템플릿에서 Service Fabric Mesh에 .NET Core 응용 프로그램을 배포하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 356e8019f9a4a64cb1c1c113d0f44990aa4e0f95
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038558"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>템플릿을 사용하여 Service Fabric Mesh에 Service Fabric Mesh 응용 프로그램 배포
이 문서에서는 템플릿을 사용하여 Service Fabric Mesh에 .NET Core 응용 프로그램을 배포하는 방법을 보여 줍니다. 완료하면 투표 결과를 클러스터의 백 엔드 서비스에 저장하는 ASP.NET Core 웹 프런트 엔드가 있는 투표 응용 프로그램이 생깁니다. 프런트 엔드는 DNS를 사용하여 백 엔드 서비스의 주소를 확인합니다.

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI 설정 
Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 작업을 완료할 수 있습니다. 다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
Azure에 로그인하고 구독을 선택합니다.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>리소스 그룹 만들기
응용 프로그램을 배포할 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하고 이 단계를 건너뛸 수 있습니다. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>응용 프로그램 배포
`deployment create` 명령을 사용하여 리소스 그룹에 응용 프로그램을 만듭니다.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

이전 명령은 [mesh_rp.windows.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json)을 사용하여 Windows 응용 프로그램을 배포합니다. Linux 응용 프로그램을 배포하려면 [mesh_rp.linux.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json)을 사용합니다. Windows 컨테이너 이미지가 Linux 컨테이너 이미지보다 크므로 배포하는 데 시간이 더 걸릴 수 있습니다.

몇 분 안에 명령에서 다음 내용이 반환됩니다.

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>응용 프로그램 열기
응용 프로그램이 성공적으로 배포된 후 서비스 끝점의 공용 IP 주소를 가져오고 브라우저에서 엽니다. 다음 웹 페이지가 표시됩니다. 

![투표 응용 프로그램](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

이제 응용 프로그램에 투표 옵션을 추가하고 응용 프로그램에 투표하거나, 투표 옵션을 삭제할 수 있습니다.

배포 명령은 서비스 끝점의 공용 IP 주소를 반환합니다. 필요한 경우, 네트워크 리소스를 쿼리하여 서비스 끝점의 공용 IP 주소를 찾을 수도 있습니다. 

이 응용 프로그램의 네트워크 리소스 이름은 `VotingAppNetwork`입니다. 다음 명령을 사용하여 이 정보를 페치합니다. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>응용 프로그램 세부 정보 확인
`app show` 명령을 사용하여 응용 프로그램 상태를 확인할 수 있습니다. 배포된 응용 프로그램의 응용 프로그램 이름은 “VotingApp”이므로 세부 정보를 페치합니다. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>배포된 응용 프로그램 나열
“app list” 명령을 사용하여 구독에 배포한 응용 프로그램 목록을 가져올 수 있습니다. 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>리소스 정리
응용 프로그램이 더 이상 필요하지 않고 관련 리소스인 경우, 해당 리소스를 포함하는 리소스 그룹을 삭제합니다. 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>다음 단계
- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)에서 투표 응용 프로그램 예제를 확인합니다.
- Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.


