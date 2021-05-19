---
title: 스토리지 액세스 키 롤링 후 Media Services v3 업데이트 | Microsoft Docs
description: 이 문서에서는 스토리지 액세스 키 롤링 후에 Media Services v3를 업데이트하는 방법에 대한 지침을 제공합니다.
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
ms.openlocfilehash: 385e6109a80c8e1e455f98b5d02ca5762f8051c7
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281424"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>스토리지 액세스 키 롤링 후 Media Services v3 업데이트

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

새 AMS(Azure Media Services) 계정을 만들 때 Azure Storage 계정을 선택하라는 메시지가 표시됩니다.  Media Services 계정에 스토리지 계정을 둘 이상 추가할 수 있습니다. 이 문서에서는 스토리지 키를 회전하는 방법을 보여 줍니다. 또한 미디어 계정에 스토리지 계정을 추가하는 방법도 보여 줍니다.

이 문서에서 설명하는 작업을 완료하려면 [Azure Resource Manager API](/rest/api/media/operations/azure-media-services-rest-api-reference) 및 [PowerShell](/powershell/module/az.media)을 사용해야 합니다.  자세한 내용은 [PowerShell 및 Resource Manager로 Azure 리소스를 관리하는 방법](../../azure-resource-manager/management/manage-resource-groups-powershell.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>스토리지 액세스 키 생성

새 스토리지 계정이 만들어지면 Azure는 2개의 512비트 스토리지 액세스 키를 만들며, 이는 스토리지 계정 액세스를 인증하는 데 사용됩니다. 스토리지 연결을 더욱 안전하게 유지하려면 스토리지 액세스 키를 주기적으로 재생성하고 회전시킵니다. 2개의 액세스 키(기본 및 보조)는 다른 액세스 키를 다시 생성하는 동안 하나의 액세스 키를 사용하여 스토리지 계정에 대한 연결을 유지할 수 있도록 사용됩니다. 이 과정은 "액세스 키 롤링"이라고도 합니다.

Media Services는 제공되는 스토리지 키에 따라 달라집니다. 특히, 자산을 스트림 또는 다운로드하는 데 사용되는 로케이터는 지정된 스토리지 액세스 키에 따라 달라집니다. AMS 계정을 만들 때는 기본적으로 기본 스토리지 액세스 키에 종속됩니다. 그러나 사용자는 AMS의 스토리지 키를 업데이트할 수 있습니다. 다음 단계에 따라 사용할 키를 Media Services에 알려야 합니다.

>[!NOTE]
> 여러 스토리지 계정이 있는 경우 각각의 스토리지 계정마다 이 과정을 수행해야 합니다. 스토리지 키를 회전하는 순서는 고정되어 있지 않습니다. 보조 키를 먼저 회전시킨 다음 기본 키를 회전하거나 그 반대로 회전할 수 있습니다.
>
> 프로덕션 계정에서 단계를 실행하기 전에 사전 프로덕션 계정에서 테스트해야 합니다.
>

## <a name="steps-to-rotate-storage-keys"></a>스토리지 키를 회전하는 단계
 
 1. PowerShell cmdlet 또는 [Azure](https://portal.azure.com/) Portal을 통해 스토리지 계정 기본 키를 변경합니다.
 2. 적절한 매개 변수로 `Sync-AzMediaServiceStorageKeys` cmdlet을 호출하여 미디어 계정에서 스토리지 계정 키를 가져오도록 합니다
 
    다음 예제에서는 키를 스토리지 계정에 동기화하는 방법을 보여 줍니다.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. 1 시간 정도 기다립니다. 스트리밍 시나리오가 작동하는지 확인합니다.
 4. PowerShell cmdlet 또는 Azure Portal을 통해 스토리지 계정 보조 키를 변경합니다.
 5. 적절한 매개 변수로 `Sync-AzMediaServiceStorageKeys` PowerShell을 호출하여 미디어 계정이 새 스토리지 계정 키를 가져오도록 합니다.
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

[Media Services 계정에 여러 스토리지 계정 연결](storage-managing-multiple-storage-accounts-how-to.md) 문서에서는 AMS 계정에 스토리지 계정을 추가하는 방법을 보여 줍니다.
