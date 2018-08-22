---
title: Service Fabric Mesh 응용 프로그램의 컨테이너 내부에 Azure Files 기반 볼륨을 탑재하여 Azure Service Fabric Mesh 응용 프로그램에 상태 저장 | Microsoft Docs
description: Azure CLI를 사용하여 컨테이너 내부에 Azure Files 기반 볼륨을 탑재하여 Azure Service Fabric Mesh 응용 프로그램에 상태를 저장하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 3b350deff2883761af6a3a2b3c5c9ef22235bde0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038355"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Service Fabric Mesh 응용 프로그램의 컨테이너 내부에 Azure Files 기반 볼륨을 탑재하여 Azure Service Fabric Mesh 응용 프로그램에 상태 저장

이 문서에서는 Service Fabric Mesh 응용 프로그램의 컨테이너 내에 볼륨을 탑재하여 Azure Files에 상태를 저장하는 방법을 보여 줍니다. 이 예제에서 카운터 응용 프로그램에는 브라우저에 카운터 값을 표시하는 웹 페이지가 포함된 ASP.NET Core 서비스가 있습니다. 

`counterService`는 주기적으로 파일에서 카운터 값을 읽고 이를 증분하여 파일에 다시 씁니다. 파일은 Azure Files 공유에서 지원하는 볼륨에 탑재된 폴더에 저장됩니다.

## <a name="prerequisites"></a>필수 조건

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 작업을 완료할 수 있습니다. 이 문서에서 Azure CLI를 사용하려면 `az --version`이 `azure-cli (2.0.43)` 이상을 반환하는지 확인합니다.  다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치 또는 업데이트합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure에 로그인하고 구독을 선택합니다.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>파일 공유 만들기

이러한 [지침](/azure/storage/files/storage-how-to-create-file-share)에 따라 Azure 파일 공유를 만듭니다. 저장소 계정 이름, 저장소 계정 키 및 파일 공유 이름은 다음 명령에서 `<storageAccountName>`, `<storageAccountKey>` 및 `<fileShareName>`으로 참조됩니다. 이러한 값은 Azure Portal에서 사용할 수 있습니다.
* <storageAccountName> - **저장소 계정** 아래에서 파일 공유를 만들 때 사용한 저장소 계정 이름입니다.
* <storageAccountKey> - **저장소 계정**에서 저장소 계정을 선택한 다음, **액세스 키**를 선택하고 **key1** 아래의 값을 사용합니다.
* <fileShareName> - **저장소 계정**에서 저장소 계정을 선택한 다음, **파일**을 선택합니다. 사용할 이름은 방금 만든 파일 공유의 이름입니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

응용 프로그램을 배포할 리소스 그룹을 만듭니다. 다음 명령은 미국 동부 위치에서 이름이 `myResourceGroup`인 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>템플릿 배포

다음 명령을 사용하여 응용 프로그램 및 관련 리소스를 만들고 이전 [파일 공유 만들기](#create-a-file-share) 단계의 `storageAccountName`, `storageAccountKey` 및 `fileShareName` 값을 제공합니다.

템플릿의 `storageAccountKey` 매개 변수는 보안 문자열입니다. 배포 상태 및 `az mesh service show` 명령에는 표시되지 않습니다. 다음 명령에 올바르게 지정되었는지 확인합니다.

다음 명령은 [mesh_rp.linux.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json)을 사용하여 Linux 응용 프로그램을 배포합니다. Windows 응용 프로그램을 배포하려면 [mesh_rp.windows.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json)을 사용합니다. 컨테이너 이미지가 크면 배포 시간이 더 오래 걸릴 수 있습니다.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

몇 분 안에 명령에서 `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`를 반환합니다.

## <a name="open-the-application"></a>응용 프로그램 열기

배포 명령은 서비스 엔드포인트의 공용 IP 주소를 반환합니다. 응용 프로그램이 성공적으로 배포된 후 서비스 엔드포인트의 공용 IP 주소를 가져오고 브라우저에서 엽니다. 카운터 값이 1초마다 업데이트되는 웹 페이지가 표시됩니다.

이 응용 프로그램에 대한 네트워크 리소스 이름은 `counterAppNetwork`입니다. 다음 명령을 사용하여 설명, 위치, 리소스 그룹 등과 같은 앱 정보를 확인할 수 있습니다.

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>응용 프로그램에서 볼륨을 사용할 수 있는지 확인

응용 프로그램은 `counter/counterService` 폴더 내부의 파일 공유에 `counter.txt`라는 파일을 만듭니다. 이 파일의 콘텐츠는 웹 페이지에 표시되는 카운터 값입니다.

[Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)처럼 Azure Files 파일 공유를 검색할 수 있는 도구를 사용하여 파일을 다운로드할 수 있습니다.

## <a name="delete-the-resources"></a>리소스 삭제

Azure에서 더 이상 사용하지 않는 리소스는 자주 삭제합니다. 이 예제와 관련한 리소스를 삭제하려면 다음 명령을 통해 배포된 리소스 그룹을 삭제합니다(리소스 그룹과 관련한 모든 항목 삭제).

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)에서 Azure Files 볼륨 응용 프로그램 예제를 확인합니다.
- Service Fabric 리소스 모델에 대한 자세한 내용은 [Service Fabric Mesh 리소스 모델](service-fabric-mesh-service-fabric-resources.md)을 참조하세요.
- Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.