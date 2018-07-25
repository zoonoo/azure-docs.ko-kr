---
title: Azure Service Fabric Mesh 응용 프로그램에서 서비스 확장 | Microsoft Docs
description: Azure CLI를 사용하여 Service Fabric Mesh에서 실행되는 응용 프로그램 내에서 서비스를 개별적으로 확장하는 방법을 알아봅니다.
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
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a4260fd808643971036ad87c01bd2fdec299ccc6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089746"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Service Fabric Mesh에서 실행되는 응용 프로그램 내에서 서비스 확장

이 문서에서는 응용 프로그램 내에서 개별적으로 마이크로 서비스를 확장하는 방법을 보여 줍니다. 이 예제에서 시각적 개체 응용 프로그램은 두 개의 마이크로 서비스 `web` 및 `worker`로 구성됩니다. 

`web` 서비스는 브라우저에 삼각형을 표시하는 웹 페이지가 있는 ASP.NET Core 응용 프로그램입니다. 브라우저에는 `worker` 서비스의 각 인스턴스에 하나의 삼각형이 표시됩니다. 

`worker` 서비스는 공간의 미리 정의된 간격으로 삼각형을 이동하고 삼각형 위치를 `web` 서비스에 보냅니다. DNS를 사용하여 `web` 서비스의 주소를 확인합니다.

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

## <a name="deploy-the-application-with-one-worker-service"></a>한 작업자 서비스를 사용하여 응용 프로그램 배포
`deployment create` 명령을 사용하여 리소스 그룹에 응용 프로그램을 만듭니다.

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
이전 명령은 [mesh_rp.base.linux.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json)을 사용하여 Linux 응용 프로그램을 배포합니다. Windows 응용 프로그램을 배포하려면 [mesh_rp.base.windows.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json)을 사용합니다. Windows 컨테이너 이미지가 Linux 컨테이너 이미지보다 크므로 배포하는 데 시간이 더 걸릴 수 있습니다.

몇 분 안에 명령에서 다음 내용이 반환됩니다.

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>응용 프로그램 열기
응용 프로그램이 성공적으로 배포된 후 서비스 끝점의 공용 IP 주소를 가져오고 브라우저에서 엽니다. 하나의 삼각형이 공간을 이동하면서 웹 페이지가 표시됩니다.

배포 명령은 서비스 끝점의 공용 IP 주소를 반환합니다. 필요한 경우, 네트워크 리소스를 쿼리하여 서비스 끝점의 공용 IP 주소를 찾을 수도 있습니다. 
 
이 응용 프로그램의 네트워크 리소스 이름은 `visualObjectsNetwork`입니다. 다음 명령을 사용하여 이 정보를 페치합니다. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>`worker` 서비스 확장

다음 명령을 사용하여 `worker` 서비스를 세 개의 인스턴스로 확장합니다. 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
이전 명령은 [mesh_rp.scaleout.linux.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json)을 사용하여 Linux를 배포합니다. Windows 응용 프로그램을 배포하려면 [mesh_rp.scaleout.windows.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json)을 사용합니다. Windows 컨테이너 이미지가 Linux 컨테이너 이미지보다 크므로 배포하는 데 시간이 더 걸릴 수 있습니다.

응용 프로그램이 성공적으로 배포된 후 브라우저에는 세 개의 삼각형이 공간을 이동하는 웹 페이지가 표시됩니다.

## <a name="delete-the-resources"></a>리소스 삭제

미리 보기 프로그램에 할당된 제한된 리소스를 절약하려면 리소스를 자주 삭제합니다. 이 예제와 관련된 리소스를 삭제하려면 배포된 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>다음 단계
- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects)에서 시각적 개체 응용 프로그램 예제를 확인합니다.
- Service Fabric 리소스 모델에 대한 자세한 내용은 [Service Fabric Mesh 리소스 모델](service-fabric-mesh-service-fabric-resources.md)을 참조하세요.
- Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.