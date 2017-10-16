---
title: "Azure 빠른 시작 - Azure CLI를 사용하여 저장소 계정 만들기 | Microsoft Docs"
description: "Azure CLI를 사용하여 새 저장소 계정을 만드는 방법을 간단히 알아봅니다."
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: b1fb2da4acf6e06219d790f2354cada4f1e34285
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Azure CLI를 사용하여 저장소 계정 만들기

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 빠른 시작에서는 Azure CLI를 사용하여 Azure Storage 계정을 만드는 방법을 설명합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 이 예제에서는 *eastus* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

`--location` 매개 변수에 어떤 지역을 지정할지 확실하지 않으면 [az account list-locations](/cli/azure/account#list) 명령을 사용하여 해당 구독에 대해 지원되는 지역을 검색할 수 있습니다.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>범용 표준 저장소 계정 만들기

다양한 사용 시나리오에 적합한 여러 가지 저장소 계정 형식이 있습니다. 각 형식은 하나 이상의 저장소 서비스(Blob, File, Table 또는 Queue)를 지원합니다. 다음 표에서는 사용 가능한 저장소 계정 형식을 설명합니다.

|**저장소 계정의 유형**|**범용 표준**|**범용 프리미엄**|**Blob Storage, 핫 및 쿨 액세스 계층**|
|-----|-----|-----|-----|
|**지원되는 서비스**| Blob, File, Table, Queue 서비스 | Blob 서비스 | Blob 서비스|
|**지원되는 Blob 유형**|블록 Blob, 페이지 Blob, 추가 Blob | 페이지 Blob | 블록 Blob 및 추가 Blob|

[az storage account create](/cli/azure/storage/account#create) 명령을 사용하여 범용 표준 저장소 계정을 만듭니다.

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 저장소 계정을 비롯하여 리소스 그룹의 어떠한 리소스도 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#delete) 명령으로 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 그룹 및 범용 표준 저장소 계정을 만들었습니다. 저장소 계정에서 데이터를 전송하는 방법을 알아보려면 계속해서 Blob 저장소 빠른 시작을 진행합니다.

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 Azure Blob Storage에서 개체 전송](../blobs/storage-quickstart-blobs-cli.md)