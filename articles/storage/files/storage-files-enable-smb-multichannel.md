---
title: SMB 다중 채널 사용
description: Azure premium 파일 공유에서 SMB 다중 채널을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2f867fa6d4b7e1d864a85106b5d957a53d38eb76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732544"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>FileStorage 계정에서 SMB 다중 채널 사용 (미리 보기) 

Azure FileStorage 계정은 SMB 다중 채널 (미리 보기)을 지원 하므로 premium 파일 공유에 여러 네트워크 연결을 설정 하 여 SMB 2.x 클라이언트의 성능을 향상 시킵니다. 이 문서에서는 기존 저장소 계정에서 SMB 다중 채널을 사용 하도록 설정 하는 단계별 지침을 제공 합니다. Azure Files SMB 다중 채널에 대 한 자세한 내용은 SMB 다중 채널 성능을 참조 하세요.

## <a name="limitations"></a>제한 사항

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>필수 구성 요소

- [FileStorage 계정을 만듭니다](./storage-how-to-create-file-share.md).
- Azure PowerShell 모듈을 사용 하려는 경우 [3.0.1-preview 버전의 모듈을 설치](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview)합니다.

## <a name="getting-started"></a>시작

PowerShell 창을 열고 Azure 구독에 로그인 합니다. 여기에서 다음 명령을 사용 하 여 SMB 다중 채널 미리 보기에 등록할 수 있습니다.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> 등록 하는 데 최대 한 시간이 걸릴 수 있습니다.

### <a name="verify-that-feature-registration-is-complete"></a>기능 등록이 완료 되었는지 확인

저장소 계정에서이 기능을 사용 하도록 설정 하는 데 최대 한 시간이 걸릴 수 있기 때문에 다음 명령을 사용 하 여 구독에 대해 등록 되었는지 확인 하는 것이 좋습니다.

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>SMB 다중 채널 사용 
FileStorage 계정을 만든 후에는 지침에 따라 저장소 계정에 대 한 SMB 다중 채널 설정을 업데이트할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
1. Azure Portal에 로그인 하 여 SMB 다중 채널을 구성 하려는 FileStorage 저장소 계정으로 이동 합니다.
1. 파일 **서비스** 에서 **파일 공유** 를 선택한 다음 **파일 공유 설정** 을 선택 합니다.
1. **SMB 다중 채널** 을 **설정 (** 해제 하려면 **해제** ) 하 고 **저장** 을 선택 합니다.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Smb 다중 채널이 설정 된 저장소 계정의 스크린샷":::

**파일 공유 설정** 에서 SMB 다중 채널 옵션이 표시 되지 않거나 구성을 업데이트 하는 동안 설정 오류를 업데이트 하지 못한 경우 구독이 등록 되어 있고 계정이 지원 되는 계정 유형 및 복제를 사용 하 여 지원 되는 [지역](#regional-availability) 중 하나에 있는지 확인 하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell 모듈을 사용 하 여 SMB 다중 채널을 사용 하도록 설정 하려면 [3.0.1-preview 버전](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) 의 모듈을 설치 해야 합니다.

`$resourceGroupName` `$storageAccountName` 이러한 PowerShell 명령을 실행 하기 전에 및 변수를 리소스 그룹 및 저장소 계정으로 설정 합니다.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI는 아직 SMB 다중 채널 구성을 지원 하지 않습니다. 저장소 계정에서 SMB 다중 채널을 구성 하는 방법에 대해서는 포털 지침을 참조 하세요.

---

> [!NOTE]
> SMB 다중 채널 구성 설정에 대 한 모든 변경 내용은 저장소 계정의 모든 파일 공유에 적용 됩니다. 그러나 변경 내용을 적용 하려면 클라이언트에서 공유를 다시 탑재 해야 합니다.


## <a name="next-steps"></a>다음 단계 

- SMB 다중 채널을 활용 하도록 [파일 공유](storage-how-to-use-files-windows.md) 를 다시 탑재 합니다.
- [SMB 다중 채널과 관련 하 여 발생 한 문제를 해결](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings)합니다.
- 향상 된 기능에 대해 자세히 알아보려면 [SMB 다중 채널 성능](storage-files-smb-multichannel-performance.md) 을 참조 하세요.
 - Windows SMB 다중 채널 기능에 대해 자세히 알아보려면 [Smb 관리과](/azure-stack/hci/manage/manage-smb-multichannel)를 참조 하세요.