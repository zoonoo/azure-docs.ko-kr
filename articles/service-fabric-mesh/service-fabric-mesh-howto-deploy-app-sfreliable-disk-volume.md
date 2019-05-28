---
title: Azure Service Fabric 망상 조직 응용 프로그램에서 항상 사용 가능한 서비스 패브릭 신뢰할 수 있는 디스크 볼륨을 사용 하 여 | Microsoft Docs
description: Azure CLI를 사용하여 컨테이너 내부에서 볼륨에 기반한 Service Fabric Reliable Disk를 탑재하여 Azure Service Fabric Mesh 애플리케이션에 상태를 저장하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: ashishnegi
manager: raunakpandya
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: 9f760e7e693334475fb61ba9e5d44df019e78604
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147487"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Azure Service Fabric Mesh 애플리케이션에서 볼륨에 기반한 고가용성 Service Fabric Reliable Disk 탑재 
컨테이너 앱을 사용하여 상태를 유지하는 일반적인 방법은Azure File Storage와 같은 원격 스토리지 또는 Azure Cosmos DB와 같은 데이터베이스를 사용하는 것입니다. 이 경우 원격 저장소에 대한 상당한 읽기 및 쓰기 네트워크 지연이 발생합니다.

이 문서에서는 Service Fabric Mesh 애플리케이션의 컨테이너 내에서 볼륨을 탑재하여 고가용성 Service Fabric Reliable Disk에 상태를 저장하는 방법을 보여줍니다.
Service Fabric Reliable Disk는 고가용성 Service Fabric 클러스터 내에서 복제된 로컬 읽기 및 쓰기에 대한 볼륨을 제공합니다. 이렇게 하면 읽기에 대한 네트워크 호출을 제거하고 쓰기에 대한 네트워크 대기 시간이 줄어듭니다. 컨테이너가 다시 시작하거나 다른 노드로 이동할 경우 새 컨테이너 인스턴스가 기존 볼륨과 동일하게 표시됩니다. 따라서 효율적이고 가용성이 높습니다.

이 예제에서 카운터 애플리케이션에는 브라우저에 카운터 값을 표시하는 웹 페이지가 포함된 ASP.NET Core 서비스가 있습니다.

`counterService`는 주기적으로 파일에서 카운터 값을 읽고 이를 증분하여 파일에 다시 씁니다. 파일은 Service Fabric Reliable Disk에서 지원하는 볼륨에 탑재되는 폴더에 저장됩니다.

## <a name="prerequisites"></a>필수 조건

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 작업을 완료할 수 있습니다. 이 문서에서 Azure CLI를 사용하려면 `az --version`이 `azure-cli (2.0.43)` 이상을 반환하는지 확인합니다.  다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치 또는 업데이트합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure에 로그인하고 구독을 선택합니다.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

애플리케이션을 배포할 리소스 그룹을 만듭니다. 다음 명령은 미국 동부 위치에서 이름이 `myResourceGroup`인 리소스 그룹을 만듭니다. 아래 명령에서 리소스 그룹 이름을 변경하는 경우, 다음에 나오는 모든 명령에서 리소스 그룹 이름을 변경해야 합니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>템플릿 배포

다음 명령은 [counter.sfreliablevolume.linux.json 템플릿](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json)을 사용하여 Linux 애플리케이션을 배포합니다. Windows 애플리케이션을 배포하려면 [counter.sfreliablevolume.windows.json 템플릿](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json)을 사용합니다. 컨테이너 이미지가 크면 배포 시간이 더 오래 걸릴 수 있습니다.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

명령을 사용하여 배포 상태를 확인할 수도 있습니다.

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

`Microsoft.ServiceFabricMesh/gateways`와 리소스 형식 같은 게이트웨이 리소스의 이름을 확인합니다. 이 앱의 공용 IP 주소 가져오는 데 사용됩니다.

## <a name="open-the-application"></a>애플리케이션 열기

애플리케이션이 성공적으로 배포되면 앱에 대한 게이트웨이 리소스의 ipAddress를 가져옵니다. 위의 섹션에서 알게 게이트웨이 이름을 사용합니다.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

출력에 서비스 엔드포인트에 대한 공용 IP 주소인 `ipAddress` 속성이 있어야 합니다. 브라우저에서 속성을 엽니다. 카운터 값이 1초마다 업데이트되는 웹 페이지가 표시됩니다.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>애플리케이션에서 볼륨을 사용할 수 있는지 확인

애플리케이션은 `counter/counterService` 폴더 내 볼륨에서 `counter.txt`라는 이름의 파일을 만듭니다. 이 파일의 콘텐츠는 웹 페이지에 표시되는 카운터 값입니다.

## <a name="delete-the-resources"></a>리소스 삭제

Azure에서 더 이상 사용하지 않는 리소스는 자주 삭제합니다. 이 예제와 관련한 리소스를 삭제하려면 다음 명령을 통해 배포된 리소스 그룹을 삭제합니다(리소스 그룹과 관련한 모든 항목 삭제).

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)에서 Service Fabric Reliable Volume Disk 샘플 애플리케이션을 봅니다.
- Service Fabric 리소스 모델에 대한 자세한 내용은 [Service Fabric Mesh 리소스 모델](service-fabric-mesh-service-fabric-resources.md)을 참조하세요.
- Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.
