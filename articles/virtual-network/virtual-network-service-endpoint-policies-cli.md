---
title: Azure Storage에 대 한 데이터 반출 제한 Azure CLI
description: 이 문서에서는 Azure CLI를 사용 하 여 가상 네트워크 서비스 끝점 정책으로 리소스를 Azure Storage 하는 가상 네트워크 데이터를 제한 하 고 제한 하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: a080c3953c48227301052a0bb151c8cba96e515a
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94737390"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Azure CLI를 사용 하 여 가상 네트워크 서비스 끝점 정책으로 계정을 Azure Storage 하는 데이터 반출 관리

가상 네트워크 서비스 끝점 정책을 사용 하면 서비스 끝점을 통해 가상 네트워크 내에서 Azure Storage 계정에 대 한 액세스 제어를 적용할 수 있습니다. 이는 작업을 보호 하 고, 허용 되는 저장소 계정 및 데이터 반출이 허용 되는 위치를 관리 하는 데 중요 합니다.
이 문서에서는 다음 방법을 설명합니다.

* 가상 네트워크를 만들고 서브넷을 추가 합니다.
* Azure Storage에 대해 서비스 끝점을 사용 하도록 설정 합니다.
* 두 개의 Azure Storage 계정을 만들고 위에서 만든 서브넷에서 네트워크 액세스를 허용 합니다.
* 저장소 계정 중 하나에만 액세스를 허용 하는 서비스 끝점 정책을 만듭니다.
* 서브넷에 VM (가상 머신)을 배포 합니다.
* 서브넷에서 허용 된 저장소 계정에 대 한 액세스를 확인 합니다.
* 서브넷에서 허용 되지 않는 저장소 계정에 대 한 액세스가 거부 되었는지 확인 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.28 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들기 전에 가상 네트워크에 대한 리소스 그룹과 이 아티클에서 만든 다른 모든 리소스를 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

[az network vnet create](/cli/azure/network/vnet)를 사용하여 하나의 서브넷이 있는 가상 네트워크를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>서비스 엔드포인트 사용 

이 예제에서는 서브넷 *개인* 에 대해 *Microsoft. Storage* 에 대 한 서비스 끝점을 만듭니다. 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>서브넷에 대한 네트워크 액세스 제한

[az network nsg create](/cli/azure/network/nsg)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNsgPrivate* 라는 네트워크 보안 그룹을 만듭니다.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet)를 사용하여 네트워크 보안 그룹을 *프라이빗* 서브넷에 연결합니다. 다음 예제에서는 *myNsgPrivate* 네트워크 보안 그룹을 *프라이빗* 서브넷에 연결합니다.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

[az network nsg rule create](/cli/azure/network/nsg/rule)를 사용하여 보안 규칙을 만듭니다. 다음에 나오는 규칙은 Azure Storage 서비스에 지정된 공용 IP 주소에 대한 아웃바운드 액세스를 허용합니다. 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

각 네트워크 보안 그룹에는 몇 가지 [기본 보안 규칙이](security-overview.md#default-security-rules)포함 되어 있습니다. 다음 규칙은 모든 공용 IP 주소에 대 한 아웃 바운드 액세스를 허용 하는 기본 보안 규칙을 재정의 합니다. `destination-address-prefix "Internet"`옵션은 모든 공용 IP 주소에 대 한 아웃 바운드 액세스를 거부 합니다. 우선 순위가 더 높은 이전 규칙이 이 규칙을 재정의하여 Azure Storage의 공용 IP 주소에 대한 액세스를 허용합니다.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

다음 규칙은 어디에서 나 서브넷에 대 한 SSH 트래픽 인바운드를 허용 합니다. 이 규칙은 인터넷의 모든 인바운드 트래픽을 거부하는 기본 보안 규칙을 재정의합니다. 이후 단계에서 연결을 테스트할 수 있도록 서브넷에 SSH를 사용할 수 있습니다.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Azure Storage 계정에 대 한 네트워크 액세스 제한

이 섹션에서는 서비스 끝점을 통해 가상 네트워크의 지정 된 서브넷에서 Azure Storage 계정에 대 한 네트워크 액세스를 제한 하는 단계를 나열 합니다.

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

[Az storage account create](/cli/azure/storage/account)를 사용 하 여 두 개의 Azure storage 계정을 만듭니다.

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

저장소 계정을 만든 후에는 [az storage account show-connection 문자열](/cli/azure/storage/account)을 사용 하 여 저장소 계정에 대 한 연결 문자열을 변수로 검색 합니다. 이 연결 문자열은 이후 단계에서 파일 공유를 만드는 데 사용됩니다.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>변수의 내용을 확인하고 이후 단계에서 사용되므로 출력에 반환된 **AccountKey** 값을 적어둡니다.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>스토리지 계정에 파일 공유 만들기

[az storage share create](/cli/azure/storage/share)를 사용하여 스토리지 계정에서 파일 공유를 만듭니다. 이후 단계에서 네트워크 액세스를 확인하기 위해 이 파일 공유가 탑재됩니다.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>저장소 계정에 대 한 모든 네트워크 액세스 거부

기본적으로 스토리지 계정은 네트워크에 있는 클라이언트의 네트워크 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 [az storage account update](/cli/azure/storage/account)를 사용하여 기본 작업을 *거부* 로 변경합니다. 네트워크 액세스가 거부되면 네트워크에서 스토리지 계정에 액세스할 수 없습니다.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>가상 네트워크 서브넷에서 네트워크 액세스 사용

[az storage account network-rule add](/cli/azure/storage/account/network-rule)를 사용하여 *프라이빗* 서브넷에서 스토리지 계정에 대한 네트워크 액세스를 허용합니다.

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>올바른 저장소 계정에 대 한 액세스를 허용 하는 정책 적용

Azure 서비스 끝점 정책은 Azure Storage에만 사용할 수 있습니다. 따라서이 예제 설치에서는이 서브넷의 *Microsoft 저장소* 에 대 한 서비스 끝점을 사용 하도록 설정 합니다.

서비스 끝점 정책은 서비스 끝점에 적용 됩니다. 서비스 끝점 정책을 만들어 시작 합니다. 그런 다음이 서브넷에 대해 승인 될 Azure Storage 계정을 위해이 정책에 정책 정의를 만듭니다.

서비스 엔드포인트 정책 만들기

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

허용 되는 저장소 계정에 대 한 리소스 URI를 변수에 저장 합니다. 아래 명령을 실행 하기 전에을 *\<your-subscription-id>* 구독 ID의 실제 값으로 바꿉니다.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

위의 Azure Storage 계정을 서비스 끝점 정책에 허용 하는 정책 정의를 만들고 & 추가 합니다.

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

그리고 가상 네트워크 서브넷을 업데이트 하 여 이전 단계에서 만든 서비스 끝점 정책에 연결 합니다.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Azure Storage 계정에 대 한 액세스 제한 확인

### <a name="create-the-virtual-machine"></a>가상 머신 만들기

저장소 계정에 대 한 네트워크 액세스를 테스트 하려면 서브넷에 VM을 배포 합니다.

[Az vm create](/cli/azure/vm)를 사용 하 여 *개인* 서브넷에서 vm을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 해당 명령이 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

VM을 만드는 데 몇 분이 걸립니다. 만든 후에는 반환된 출력에서 **publicIpAddress** 를 적어둡니다. 이 주소는 이후 단계에서 인터넷을 통해 VM에 액세스하는 데 사용됩니다.

### <a name="confirm-access-to-storage-account"></a>스토리지 계정에 대한 액세스 확인

*myVmPrivate* VM으로 SSH를 수행합니다. *\<publicIpAddress>* 를 *Myvmprivate* VM의 공용 IP 주소로 바꿉니다.

```bash 
ssh <publicIpAddress>
```

탑재 지점에 대한 폴더를 만듭니다.

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

만든 디렉터리에 Azure 파일 공유를 탑재합니다. 아래 명령을 실행 하기 전에 *\<storage-account-key>* **$saConnectionString 1** 에서 *AccountKey* 값으로 대체 합니다.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

`user@myVmPrivate:~$` 프롬프트가 수신됩니다. Azure 파일 공유가 */mnt/MyAzureFileShare* 에 탑재되었습니다.

### <a name="confirm-access-is-denied-to-storage-account"></a>스토리지 계정에 대한 액세스가 거부되는지 확인

동일한 VM *Myvmprivate* 에서 탑재 지점에 대 한 디렉터리를 만듭니다.

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

저장소 계정 *notallowedstorageacc* 에서 만든 디렉터리에 Azure 파일 공유를 탑재 하려고 합니다. 이 문서에서는 최신 버전의 Ubuntu를 배포했다고 가정합니다. 이전 버전의 Ubuntu를 사용하는 경우 [Linux에 탑재](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에서 파일 공유 탑재에 대한 추가 지침을 참조하세요. 

아래 명령을 실행 하기 전에 *\<storage-account-key>* **$saConnectionString 2** 의 *AccountKey* 값으로 대체 합니다.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

`mount error(13): Permission denied`이 저장소 계정은 서브넷에 적용 한 서비스 끝점 정책의 허용 목록에 없기 때문에 액세스가 거부 되 고 오류가 수신 됩니다. 

*myVmPublic* VM에 대한 SSH 세션을 종료합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure)를 사용하여 리소스 그룹 및 그룹에 포함된 모든 리소스를 제거합니다.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 가상 네트워크 서비스 끝점에 대 한 서비스 끝점 정책을 Azure Storage에 적용 했습니다. 가상 네트워크 서브넷에서 특정 저장소 계정 (즉, 다른 사용자가 거부 함)에 대 한 제한 된 네트워크 액세스와 Azure Storage 계정을 만들었습니다. 서비스 끝점 정책에 대해 자세히 알아보려면 [서비스 끝점 정책 개요](virtual-network-service-endpoint-policies-overview.md)를 참조 하세요.
