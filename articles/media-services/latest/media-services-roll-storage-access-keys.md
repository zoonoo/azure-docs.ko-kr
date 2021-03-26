---
title: 저장소 액세스 키 롤링 후 v3 Media Services 업데이트 | Microsoft Docs
description: 이 문서에서는 저장소 액세스 키 롤링 후에 v3 Media Services를 업데이트 하는 방법에 대 한 지침을 제공 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 76ba1b848b033c5a26e81be18bef23a2b4715a7e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572478"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>저장소 액세스 키 롤링 후 v3 업데이트 Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

새 Azure Media Services (AMS) 계정을 만들 때 Azure Storage 계정을 선택 하 라는 메시지가 표시 됩니다.  둘 이상의 저장소 계정을 Media Services 계정에 추가할 수 있습니다. 이 문서에서는 스토리지 키를 회전하는 방법을 보여 줍니다. 또한 미디어 계정에 스토리지 계정을 추가하는 방법도 보여 줍니다.

이 문서에 설명 된 작업을 완료 하려면 [Azure Resource Manager api](/rest/api/media/operations/azure-media-services-rest-api-reference) 및 [PowerShell](/powershell/module/az.media)을 사용 해야 합니다.  자세한 내용은 [PowerShell을 사용 하 여 Azure 리소스를 관리 하는 방법 및 리소스 관리자](../../azure-resource-manager/management/manage-resource-groups-powershell.md)을 참조 하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>저장소 액세스 키 생성

새 저장소 계정이 만들어지면 Azure는 저장소 계정에 대 한 액세스를 인증 하는 데 사용 되는 2 512 비트 저장소 액세스 키를 생성 합니다. 저장소 연결을 더욱 안전 하 게 유지 하려면 저장소 액세스 키를 주기적으로 다시 생성 하 고 회전 합니다. 다른 액세스 키를 다시 생성 하는 동안 하나의 액세스 키를 사용 하 여 저장소 계정에 대 한 연결을 유지할 수 있도록 두 개의 액세스 키 (기본 및 보조)가 제공 됩니다. 이 과정은 "액세스 키 롤링"이라고도 합니다.

Media Services는 제공되는 스토리지 키에 따라 달라집니다. 특히, 자산을 스트림 또는 다운로드하는 데 사용되는 로케이터는 지정된 스토리지 액세스 키에 따라 달라집니다. AMS 계정이 만들어질 때 기본 저장소 액세스 키에 대 한 종속성이 기본적으로 사용 됩니다. 그러나 사용자는 AMS의 저장소 키를 업데이트할 수 있습니다. 다음 단계를 수행 하 여 사용할 키를 Media Services 알 수 있어야 합니다.

>[!NOTE]
> 여러 스토리지 계정이 있는 경우 각각의 스토리지 계정마다 이 과정을 수행해야 합니다. 스토리지 키를 회전하는 순서는 고정되어 있지 않습니다. 보조 키를 먼저 회전시킨 다음 기본 키를 회전하거나 그 반대로 회전할 수 있습니다.
>
> 프로덕션 계정에 대 한 단계를 실행 하기 전에 사전 프로덕션 계정에서 테스트를 수행 해야 합니다.
>

## <a name="steps-to-rotate-storage-keys"></a>스토리지 키를 회전하는 단계
 
 1. PowerShell cmdlet 또는 [Azure](https://portal.azure.com/) portal을 통해 저장소 계정 기본 키를 변경 합니다.
 2. `Sync-AzMediaServiceStorageKeys`적절 한 매개 변수를 사용 하 여 cmdlet을 호출 하 여 미디어 계정을 강제로 저장소 계정 키를 선택 합니다.
 
    다음 예제에서는 키를 스토리지 계정에 동기화하는 방법을 보여 줍니다.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. 1 시간 정도 기다립니다. 스트리밍 시나리오가 작동하는지 확인합니다.
 4. PowerShell cmdlet 또는 Azure Portal을 통해 저장소 계정 보조 키를 변경 합니다.
 5. `Sync-AzMediaServiceStorageKeys`적절 한 매개 변수를 사용 하 여 PowerShell을 호출 하 여 미디어 계정을 강제로 새 저장소 계정 키를 선택 합니다.
 6. 1 시간 정도 기다립니다. 스트리밍 시나리오가 작동하는지 확인합니다.
 
### <a name="a-powershell-cmdlet-example"></a>PowerShell cmdlet 예제

다음 예제에서는 스토리지 계정을 가져와서 AMS 계정과 동기화하는 방법을 보여 줍니다.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>AMS 계정에 스토리지 계정을 추가하는 단계

[Media Services 계정에 여러 스토리지 계정 연결](media-services-managing-multiple-storage-accounts.md) 문서에서는 AMS 계정에 스토리지 계정을 추가하는 방법을 보여 줍니다.
