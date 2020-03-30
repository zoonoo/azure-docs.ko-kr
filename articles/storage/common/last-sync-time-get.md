---
title: 저장소 계정의 마지막 동기화 시간 속성을 확인합니다.
titleSuffix: Azure Storage
description: 지리적으로 복제된 저장소 계정의 **마지막 동기화 시간** 속성을 확인하는 방법을 알아봅니다. **마지막 동기화 시간** 속성은 기본 지역의 모든 쓰기가 보조 영역에 성공적으로 기록된 마지막 시간을 나타냅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165489"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>저장소 계정의 마지막 동기화 시간 속성을 확인합니다.

저장소 계정을 구성할 때 데이터가 기본 리전에서 수백 마일 떨어진 보조 지역으로 복사되도록 지정할 수 있습니다. 지역 복제는 자연 재해와 같은 기본 지역에서 심각한 중단이 발생할 경우 데이터에 대한 내구성을 제공합니다. 보조 영역에 대한 읽기 액세스를 추가로 사용하도록 설정하면 기본 지역을 사용할 수 없게 된 경우 데이터를 읽기 작업에 계속 사용할 수 있습니다. 기본 영역이 응답하지 않는 경우 보조 지역에서 읽기로 원활하게 전환하도록 응용 프로그램을 디자인할 수 있습니다.

지리적 중복 저장소(GRS) 및 지리 영역 중복 저장소(GZRS) (미리 보기) 모두 데이터를 보조 영역에 비동기적으로 복제합니다. 보조 영역에 대한 읽기 액세스를 위해 읽기 액세스 지리적 중복 저장소(RA-GRS) 또는 읽기 액세스 지리적 영역 중복 저장소(RA-GZRS)를 사용하도록 설정합니다. Azure Storage에서 제공하는 다양한 중복 옵션에 대한 자세한 내용은 [Azure 저장소 중복을](storage-redundancy.md)참조하십시오.

이 문서에서는 기본 영역과 보조 지역 간의 불일치를 평가할 수 있도록 저장소 계정에 대한 **마지막 동기화 시간** 속성을 확인하는 방법에 대해 설명합니다.

## <a name="about-the-last-sync-time-property"></a>마지막 동기화 시간 속성 정보

지역 복제는 비동기이므로 중단이 발생했을 때 기본 영역에 기록된 데이터가 아직 보조 지역에 기록되지 않았을 수 있습니다. **마지막 동기화 시간** 속성은 기본 지역의 데이터가 보조 영역에 성공적으로 기록된 마지막 시간을 나타냅니다. 마지막 동기화 시간 전에 기본 영역에 대한 모든 쓰기를 보조 위치에서 읽을 수 있습니다. 마지막 동기화 시간 속성 이후에 기본 영역에 대한 쓰기는 아직 읽기에 사용할 수 없거나 제공되지 않을 수 있습니다.

**마지막 동기화 시간** 속성은 GMT 날짜/시간 값입니다.

## <a name="get-the-last-sync-time-property"></a>마지막 동기화 시간 속성 받기

PowerShell 또는 Azure CLI를 사용하여 마지막 **동기화 시간** 속성의 값을 검색할 수 있습니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 저장소 계정에 대한 마지막 동기화 시간을 얻으려면 지역 복제 통계 가져오는 것을 지원하는 Azure Storage 미리 보기 모듈을 설치합니다. 예를 들어:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

그런 다음 저장소 계정의 **GeoReplicationStats.LastSyncTime** 속성을 확인합니다. 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 저장소 계정에 대한 마지막 동기화 시간을 얻으려면 저장소 계정의 **geoReplicationStats.lastSyncTime** 속성을 확인하십시오. 매개 `--expand` 변수를 사용하여 **geoReplicationStats**에 중첩된 속성에 대한 값을 반환합니다. 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>참조

- [Azure 스토리지 중복성](storage-redundancy.md)
- [저장소 계정의 중복 옵션 변경](redundancy-migration.md)
- [읽기 액세스 지리적 중복 스토리지를 사용하여 가용성이 높은 응용 프로그램 설계](storage-designing-ha-apps-with-ragrs.md)