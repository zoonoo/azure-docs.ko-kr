---
title: 빠른 시작 - Azure CLI를 사용하여 Azure Media Services 계정 만들기 | Microsoft Docs
description: Azure Media Services 계정을 만들려면 빠른 시작의 단계를 수행합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: de54571308b737b9160a39ee4ba5d4b2d9f15775
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376536"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>빠른 시작: Azure Media Services 계정을 만듭니다.

개발자 또는 미디어 콘텐츠 제작자이든, Azure에서 미디어 콘텐츠를 저장, 암호화, 인코딩, 관리 및 스트리밍하려면 Media Services 계정을 만들어야 합니다. 미디어 서비스 계정을 만들 때는 Azure Storage 계정 리소스의 ID를 제공해야 합니다. 지정된 저장소 계정은 Media Services 계정에 연결됩니다. 이 저장소 계정 리소스는 미디어 서비스 계정과 동일한 지역에 있어야 합니다.  

이 빠른 시작은 Azure CLI를 사용하여 새로운 Azure Media Services 계정을 만드는 단계를 설명합니다.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Azure에 로그인

다음 단계와 같이[Azure Portal](http://portal.azure.com)에 로그인하고 **CloudShell**을 시작하여 CLI명령을 실행합니다.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="set-the-azure-subscription"></a>Azure 구독 설정

다음 명령에서 Media Services 계정에 사용할 Azure 구독 ID를 제공합니다. [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)으로 이동하면 액세스 권한이 있는 구독 목록을 볼 수 있습니다.

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Azure 리소스 그룹 만들기

다음 명령으로 저장소 및 미디어 서비스 계정이 있는 리소스 그룹을 만듭니다. *myresourcegroup* 자리 표시자를 리소스 그룹에 사용할 이름으로 바꿉니다.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

미디어 서비스 계정을 만들 때는 Azure Storage 계정 리소스의 ID를 제공해야 합니다. 지정된 저장소 계정은 Media Services 계정에 연결됩니다. 

**기본** 저장소 계정은 하나 있어야 하며 Media Services 계정과 연결된 **보조** 저장소 계정은 여러 개 사용할 수 있습니다. Media Services는 **범용 v2** 또는 **범용 v1** 계정을 지원합니다. Blob Storage 계정은 **기본**으로 허용되지 않습니다. 저장소 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../../storage/common/storage-account-overview.md)를 참조하세요. 

다음 명령은 미디어 서비스 계정(기본)과 연결될 저장소 계정을 만듭니다. 아래 스크립트에서 *storageaccountforams* 자리 표시자를 대체합니다. 'account_name'의 길이는 24 미만이어야 합니다.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Azure Media Services 계정 만들기

아래에서 새로운 미디어 서비스 계정을 만드는 Azure CLI 명령을 찾을 수 있습니다. 다음 강조 표시된 값을 바꾸기만 하면 됩니다.

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 Media Services 계정을 비롯하여 리소스 그룹의 어떠한 리소스도 더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다.

**CloudShell**에서 다음 명령을 실행합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [파일 스트리밍](stream-files-dotnet-quickstart.md)
