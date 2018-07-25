---
title: Service Fabric Mesh 응용 프로그램의 컨테이너 내부에 Azure Files 기반 볼륨을 탑재하여 상태 저장 | Microsoft Docs
description: Azure CLI를 사용하여 Service Fabric Mesh 응용 프로그램의 컨테이너 내부에 Azure Files 기반 볼륨을 탑재하여 상태를 저장하는 방법을 알아봅니다.
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
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090635"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Service Fabric Mesh 응용 프로그램에 Azure Files 기반 볼륨을 탑재하여 상태 저장

이 문서에서는 Service Fabric Mesh 응용 프로그램의 컨테이너 내에 볼륨을 탑재하여 Azure Files에 상태를 저장하는 방법을 보여 줍니다. 이 예제에서 카운터 응용 프로그램에는 브라우저에 카운터 값을 표시하는 웹 페이지가 포함된 ASP.NET Core 서비스가 있습니다. 

`counterService`는 주기적으로 파일에서 카운터 값을 읽고 이를 증분하여 파일에 다시 씁니다. 파일은 Azure Files 공유에서 지원하는 볼륨에 탑재된 폴더에 저장됩니다. 

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI 설정 
Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 작업을 완료할 수 있습니다. 다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
Azure에 로그인하고 구독을 선택합니다.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>파일 공유 만들기 
이러한 [지침](/azure/storage/files/storage-how-to-create-file-share)에 따라 Azure 파일 공유를 만듭니다. 저장소 계정 이름, 저장소 계정 키 및 파일 공유 이름은 다음 명령에서 `<storageAccountName>`, `<storageAccountKey>` 및 `<fileShareName>`으로 참조됩니다.

## <a name="create-resource-group"></a>리소스 그룹 만들기
응용 프로그램을 배포할 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하고 이 단계를 건너뛸 수 있습니다. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>템플릿 배포

다음 명령을 사용하여 응용 프로그램 및 관련 리소스를 만들고 이전 단계의 `storageAccountName`, `storageAccountKey` 및 `fileShareName` 값을 제공합니다.

템플릿의 `storageAccountKey` 매개 변수는 `securestring`입니다. 배포 상태 및 `az mesh service show` 명령에는 표시되지 않습니다. 다음 명령에 올바르게 지정되었는지 확인합니다.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

이전 명령은 [mesh_rp.linux.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json)을 사용하여 Linux 응용 프로그램을 배포합니다. Windows 응용 프로그램을 배포하려면 [mesh_rp.windows.json 템플릿](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json)을 사용합니다. Windows 컨테이너 이미지가 Linux 컨테이너 이미지보다 크므로 배포하는 데 시간이 더 걸릴 수 있습니다.

몇 분 안에 명령에서 다음 내용이 반환됩니다.

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>응용 프로그램 열기
응용 프로그램이 성공적으로 배포된 후 서비스 끝점의 공용 IP 주소를 가져오고 브라우저에서 엽니다. 카운터 값이 1초마다 업데이트되는 웹 페이지가 표시됩니다.

배포 명령은 서비스 끝점의 공용 IP 주소를 반환합니다. 필요한 경우, 네트워크 리소스를 쿼리하여 서비스 끝점의 공용 IP 주소를 찾을 수도 있습니다. 
 
이 응용 프로그램의 네트워크 리소스 이름은 `counterAppNetwork`입니다. 다음 명령을 사용하여 이 정보를 페치합니다. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>응용 프로그램에서 볼륨을 사용할 수 있는지 확인
응용 프로그램은 `counter/counterService` 폴더 내부의 파일 공유에 `counter.txt`라는 파일을 만듭니다. 이 파일의 콘텐츠는 웹 페이지에 표시되는 카운터 값입니다.

Azure Files 파일 공유를 검색할 수 있는 도구를 사용하여 파일을 다운로드할 수 있습니다. [Microsoft Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)는 이러한 도구의 예입니다.

## <a name="delete-the-resources"></a>리소스 삭제

미리 보기 프로그램에 할당된 제한된 리소스를 절약하려면 리소스를 자주 삭제합니다. 이 예제와 관련된 리소스를 삭제하려면 배포된 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>다음 단계

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)에서 Azure Files 볼륨 응용 프로그램 예제를 확인합니다.
- Service Fabric 리소스 모델에 대한 자세한 내용은 [Service Fabric Mesh 리소스 모델](service-fabric-mesh-service-fabric-resources.md)을 참조하세요.
- Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.
