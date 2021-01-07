---
title: Azure HPC Cache 만들기
description: Azure HPC Cache 인스턴스를 만드는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 77bd5e3b7a258ef83e5de4ec645ea70578fb9dfb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657094"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC Cache 만들기

Azure Portal 또는 Azure CLI를 사용 하 여 캐시를 만듭니다.

![맨 아래에 만들기 단추가 있는, Azure Portal의 캐시 개요 스크린샷](media/hpc-cache-home-page.png)

아래 이미지를 클릭 하 여 캐시를 만들고 저장소 대상을 추가 하는 [비디오 데모](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) 를 시청 하세요.

[![비디오 미리 보기: Azure HPC 캐시: 설치 (비디오 페이지를 방문 하려면 클릭)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[포털](#tab/azure-portal)

## <a name="define-basic-details"></a>기본 세부 정보 정의

![Azure Portal의 프로젝트 세부 정보 페이지 스크린샷](media/hpc-cache-create-basics.png)

**프로젝트 세부 정보** 에서 캐시를 호스트할 구독 및 리소스 그룹을 선택합니다.

**서비스 세부 정보** 에서 캐시 이름과 다음 기타 특성을 설정합니다.

* 위치 - [지원되는 지역](hpc-cache-overview.md#region-availability) 중 하나를 선택합니다.
* 가상 네트워크 - 기존 가상 네트워크를 선택하거나 새 가상 네트워크를 만들 수 있습니다.
* 서브넷-64 개 이상의 IP 주소 (/24)를 사용 하 여 서브넷을 선택 하거나 만듭니다. 이 서브넷은이 Azure HPC 캐시 인스턴스에만 사용 해야 합니다.

## <a name="set-cache-capacity"></a>캐시 용량 설정
<!-- referenced from GUI - update aka.ms link if you change this header text -->

**캐시** 페이지에서 캐시의 용량을 설정해야 합니다. 여기에 설정된 값은 캐시가 보유할 수 있는 데이터의 양과 클라이언트 요청을 얼마나 빠르게 처리할 수 있는지를 결정됩니다.

용량은 캐시 비용에도 영향을 줍니다.

다음 두 값을 설정하여 용량을 선택합니다.

* 캐시의 최대 데이터 전송률(처리량)(GB/초)
* 캐시된 데이터에 할당되는 스토리지의 양(TB)

사용 가능한 처리량 값과 캐시 스토리지 크기 중 하나를 선택합니다.

실제 데이터 전송률은 워크로드, 네트워크 속도 및 스토리지 대상 유형에 따라 달라집니다. 선택한 값은 전체 캐시 시스템의 최대 처리량을 설정하지만 일부는 오버헤드 작업에 사용됩니다. 예를 들어 클라이언트가 캐시에 아직 저장 되지 않은 파일을 요청 하거나 파일이 오래 된 것으로 표시 된 경우 캐시는 해당 처리량 중 일부를 사용 하 여 백 엔드 저장소에서 가져옵니다.

Azure HPC Cache는 캐시 적중률을 최대화하기 위해 캐시되고 미리 로드되는 파일을 관리합니다. 캐시 콘텐츠는 지속적으로 평가 되며, 자주 액세스 하지 않을 때 파일은 장기 저장소로 이동 됩니다. 활성 작업 파일 집합을 편안 하 게 유지할 수 있는 캐시 저장소 크기와 메타 데이터 및 기타 오버 헤드를 위한 추가 공간을 선택 합니다.

![캐시 크기 페이지 스크린샷](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Azure Key Vault 암호화 사용 (선택 사항)

캐시가 고객이 관리 하는 암호화 키를 지 원하는 영역에 있는 경우 **디스크 암호화 키** 페이지가 **캐시** 및 **태그** 탭 사이에 표시 됩니다. 지역 지원에 대 한 자세한 내용은 지역 [가용성](hpc-cache-overview.md#region-availability) 을 참조 하세요.

캐시 저장소에 사용 되는 암호화 키를 관리 하려면 **디스크 암호화 키** 페이지에서 Azure Key Vault 정보를 제공 합니다. 키 자격 증명 모음은 캐시와 동일한 구독 및 동일한 지역에 있어야 합니다.

고객이 관리 하는 키가 필요 하지 않은 경우이 섹션을 건너뛸 수 있습니다. Azure는 기본적으로 Microsoft 관리 키를 사용 하 여 데이터를 암호화 합니다. 자세한 내용은 [Azure storage 암호화](../storage/common/storage-service-encryption.md) 를 참조 하세요.

> [!NOTE]
>
> * 캐시를 만든 후에는 Microsoft에서 관리 하는 키와 고객이 관리 하는 키를 변경할 수 없습니다.
> * 캐시를 만든 후에는 키 자격 증명 모음에 액세스할 수 있도록 권한을 부여 해야 합니다. 캐시의 **개요** 페이지에서 **암호화 사용** 단추를 클릭 하 여 암호화를 설정 합니다. 캐시를 만드는 데 90 분 내에이 단계를 수행 합니다.
> * 이 권한 부여 후에는 캐시 디스크가 생성 됩니다. 즉, 초기 캐시 생성 시간은 짧고, 액세스 권한을 부여한 후 10 분 이상 캐시를 사용할 준비가 되지 않습니다.

고객이 관리 하는 키 암호화 프로세스에 대 한 자세한 내용은 [AZURE HPC 캐시용 고객 관리 암호화 키 사용](customer-keys.md)을 참조 하세요.

!["customer 관리"가 선택 되 고 키 자격 증명 모음 필드가 표시 된 암호화 키 페이지의 스크린샷](media/create-encryption.png)

Customer **관리** 를 선택 하 여 고객 관리 키 암호화를 선택 합니다. 키 자격 증명 모음 사양 필드가 표시 됩니다. 사용할 Azure Key Vault을 선택 하 고이 캐시에 사용할 키 및 버전을 선택 합니다. 키는 2048 비트 RSA 키 여야 합니다. 이 페이지에서 새 주요 자격 증명 모음, 키 또는 키 버전을 만들 수 있습니다.

캐시를 만든 후에는 키 자격 증명 모음 서비스를 사용할 수 있도록 권한을 부여 해야 합니다. 자세한 내용은 [캐시에서 Azure Key Vault 암호화 권한 부여](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) 를 참조 하세요.

## <a name="add-resource-tags-optional"></a>리소스 태그 추가(선택 사항)

**태그** 페이지에서는 Azure HPC Cache 인스턴스에 [리소스 태그](../azure-resource-manager/management/tag-resources.md)를 추가할 수 있습니다.

## <a name="finish-creating-the-cache"></a>캐시 만들기 완료

새 캐시를 구성한 후 **검토 + 만들기** 탭을 클릭 합니다. 포털은 선택 항목의 유효성을 검사 하 고 선택 항목을 검토할 수 있습니다. 모든 선택 항목이 올바르면 **만들기** 를 클릭합니다.

캐시를 만드는 데 10분 정도 걸립니다. Azure Portal의 알림 패널에서 진행 상황을 추적할 수 있습니다.

![포털의 캐시 만들기 "배포 진행 중" 및 "알림" 페이지의 스크린샷](media/hpc-cache-deploy-status.png)

만들기가 완료되면 새 Azure HPC Cache 인스턴스에 대한 링크가 포함된 알림이 표시되고, 해당 캐시가 구독의 **리소스** 목록에 표시됩니다.

![Azure Portal의 Azure HPC Cache 인스턴스 스크린샷](media/hpc-cache-new-overview.png)

> [!NOTE]
> 캐시에서 고객이 관리 하는 암호화 키를 사용 하는 경우 배포 상태가 완료로 변경 되기 전에 캐시가 리소스 목록에 표시 될 수 있습니다. 캐시의 상태가 **키를 기다리고** 있는 즉시 키 자격 증명 모음을 사용 하도록 [권한을 부여할](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) 수 있습니다.

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Azure CLI를 사용 하 여 캐시 만들기

[AZURE HPC 캐시에 대 한 Azure CLI를 설정](./az-cli-prerequisites.md)합니다.

> [!NOTE]
> 현재 Azure CLI는 고객이 관리 하는 암호화 키를 사용 하 여 캐시를 만드는 것을 지원 하지 않습니다. Azure 포털을 사용합니다.

[Az hpc-cache create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) 명령을 사용 하 여 새 Azure hpc 캐시를 만듭니다.

다음 값을 제공 합니다.

* 캐시 리소스 그룹 이름
* 캐시 이름
* Azure 지역
* 다음 형식으로 서브넷 캐시:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  캐시 서브넷에는 최소 64 개의 IP 주소 (/24)가 필요 하며 다른 리소스를 저장할 수 없습니다.

* 캐시 용량입니다. Azure HPC 캐시의 최대 처리량을 설정 하는 두 값은 다음과 같습니다.

  * 캐시 크기 (GB)
  * 캐시 인프라에서 사용 되는 가상 컴퓨터의 SKU

  [az hpc-cache sku 목록](/cli/azure/ext/hpc-cache/hpc-cache/skus) 에는 사용할 수 있는 sku와 각각에 대 한 유효한 캐시 크기 옵션이 표시 됩니다. 캐시 크기 옵션의 범위는 3TB에서 48 TB 이지만 일부 값만 지원 됩니다.

  이 차트는이 문서가 준비 되는 시점 (7 월 2020 일)에 유효한 캐시 크기 및 SKU 조합을 보여 줍니다.

  | 캐시 크기 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072GB    | 예         | no          | no          |
  | 6144 GB    | 예         | 예         | no          |
  | 12288 GB   | 예         | 예         | 예         |
  | 24576 GB   | no          | 예         | 예         |
  | 49152 GB   | no          | no          | 예         |

  가격 책정, 처리량 및 워크플로에 적절 한 캐시 크기를 조정 하는 방법에 대 한 중요 한 정보는 포털 지침 탭의 **캐시 용량 설정** 섹션을 참조 하세요.

캐시 생성 예:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

캐시 생성은 몇 분 정도 걸립니다. 성공할 경우 create 명령은 다음과 같은 출력을 반환 합니다.

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

메시지에는 다음 항목을 포함 하 여 몇 가지 유용한 정보가 포함 되어 있습니다.

* 클라이언트 탑재 주소-캐시에 클라이언트를 연결할 준비가 되 면 이러한 IP 주소를 사용 합니다. 자세한 내용은 [AZURE HPC 캐시 탑재](hpc-cache-mount.md) 를 참조 하세요.
* 업그레이드 상태-소프트웨어 업데이트가 릴리스되면이 메시지가 변경 됩니다. [캐시 소프트웨어](hpc-cache-manage.md#upgrade-cache-software) 를 편리 하 게 수동으로 업그레이드할 수도 있고, 며칠 후에도 자동으로 적용 됩니다.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Az. HPCCache PowerShell 모듈은 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 프로덕션 워크로드에는 권장되지 않습니다. 일부 기능은 지원 되지 않거나 제한 된 기능을 가질 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="requirements"></a>요구 사항

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. Cloud Shell을 사용하도록 선택하는 경우 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요.

> [!IMPORTANT]
> **Az. HPCCache** PowerShell 모듈은 미리 보기 상태 이지만 cmdlet을 사용 하 여 별도로 설치 해야 합니다 `Install-Module` . 이 PowerShell 모듈은 일반적으로 사용할 수 있게 되 면 이후 Az PowerShell 모듈 릴리스에 포함 되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 캐시 만들기

> [!NOTE]
> 현재 Azure PowerShell에서는 고객이 관리 하는 암호화 키를 사용 하 여 캐시를 만드는 기능을 지원 하지 않습니다. Azure 포털을 사용합니다.

[AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) cmdlet을 사용 하 여 새 Azure HPC 캐시를 만듭니다.

다음 값을 제공합니다.

* 캐시 리소스 그룹 이름
* 캐시 이름
* Azure 지역
* 다음 형식으로 서브넷 캐시:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  캐시 서브넷에는 최소 64 개의 IP 주소 (/24)가 필요 하며 다른 리소스를 저장할 수 없습니다.

* 캐시 용량입니다. Azure HPC 캐시의 최대 처리량을 설정 하는 두 값은 다음과 같습니다.

  * 캐시 크기 (GB)
  * 캐시 인프라에서 사용 되는 가상 컴퓨터의 SKU

  [AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) 에는 사용 가능한 sku와 각 sku에 대 한 유효한 캐시 크기 옵션이 표시 됩니다. 캐시 크기 옵션의 범위는 3TB에서 48 TB 이지만 일부 값만 지원 됩니다.

  이 차트는이 문서가 준비 되는 시점 (7 월 2020 일)에 유효한 캐시 크기 및 SKU 조합을 보여 줍니다.

  | 캐시 크기 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072GB    | 예         | no          | no          |
  | 6144 GB    | 예         | 예         | no          |
  | 12288 GB   | 예         | 예         | 예         |
  | 24576 GB   | no          | 예         | 예         |
  | 49152 GB   | no          | no          | 예         |

  가격 책정, 처리량 및 워크플로에 적절 한 캐시 크기를 조정 하는 방법에 대 한 중요 한 정보는 포털 지침 탭의 **캐시 용량 설정** 섹션을 참조 하세요.

캐시 생성 예:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

캐시 생성은 몇 분 정도 걸립니다. 성공할 경우 create 명령은 다음 출력을 반환 합니다.

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

메시지에는 다음 항목을 포함 하 여 몇 가지 유용한 정보가 포함 되어 있습니다.

* 클라이언트 탑재 주소-캐시에 클라이언트를 연결할 준비가 되 면 이러한 IP 주소를 사용 합니다. 자세한 내용은 [AZURE HPC 캐시 탑재](hpc-cache-mount.md) 를 참조 하세요.
* 업그레이드 상태-소프트웨어 업데이트가 릴리스되면이 메시지가 변경 됩니다. [캐시 소프트웨어](hpc-cache-manage.md#upgrade-cache-software) 를 편리 하 게 수동으로 업그레이드 하거나 며칠 후에 자동으로 적용할 수 있습니다.

---

## <a name="next-steps"></a>다음 단계

**리소스** 목록에 캐시가 표시 되 면 다음 단계로 이동할 수 있습니다.

* [저장소 대상을 정의](hpc-cache-add-storage.md) 하 여 데이터 원본에 대 한 캐시 액세스를 제공 합니다.
* 고객이 관리 하는 암호화 키를 사용 하는 경우 캐시의 개요 페이지에서 [Azure Key Vault 암호화에 권한을 부여](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) 하 여 캐시 설정을 완료 해야 합니다. 저장소를 추가 하려면 먼저이 단계를 수행 해야 합니다. 자세한 내용은 [고객이 관리 하는 암호화 키 사용](customer-keys.md) 을 참조 하세요.