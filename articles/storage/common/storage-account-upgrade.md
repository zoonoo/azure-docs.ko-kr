---
title: 범용 v2 저장소 계정으로 업그레이드 - Azure Storage | Microsoft Docs
description: 범용 v2 저장소 계정으로 업그레이드.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 6e77c4836531a7efd0b52b9a411ac40ff6a613fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224497"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>범용 v2 저장소 계정으로 업그레이드

범용 v2 저장소 계정은 최신 Azure Storage 기능을 지원하고 모든 범용 v1의 기능 및 Blob Storage 계정을 통합합니다. 범용 v2 계정은 대부분의 저장소 시나리오에 권장됩니다. 범용 v2 계정은 업계 경쟁력 있는 트랜잭션 가격 뿐만 아니라 Azure Storage에 대해서도 가장 낮은 기가바이트당 용량 가격을 제공합니다.

범용 v1 또는 Blob Storage 계정에서 범용 v2 저장소 계정으로 간단히 업그레이드할 수 있습니다. Azure Portal, PowerShell 또는 Azure CLI를 사용하여 업그레이드할 수 있습니다. 계정 업그레이드는 취소할 수 없으며 요금이 청구될 수 있습니다.

## <a name="upgrade-using-the-azure-portal"></a>Azure Portal을 사용하여 업그레이드

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 저장소 계정으로 이동합니다.
3. **설정** 섹션에서 **구성**을 클릭합니다.
4. **계정 종류** 아래에서 **업그레이드**를 클릭합니다.
5. **업그레이드 확인**에서 계정 이름을 입력합니다. 
6. 블레이드 하단에서 **업그레이드**를 클릭합니다.

## <a name="upgrade-with-powershell"></a>Powershell로 업그레이드

PowerShell을 사용하여 범용 v1 계정을 업그레이드 v2 계정으로 업그레이드하려면 먼저 최신 버전의 **AzureRm.Storage** 모듈을 사용하도록 PowerShell을 업데이트합니다. PowerShell 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 참조하세요. 

다음으로, 리소스 그룹의 이름 및 저장소 계정을 대신하여 계정을 업그레이드하도록 다음 명령을 호출합니다.

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Azure CLI를 사용하여 업그레이드

Azure CLI를 사용하여 범용 v1 계정을 범용 v2 계정으로 업그레이드하려면 먼저 최신 버전의 Azure CLI를 설치합니다. CLI 설치에 대한 자세한 내용은 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요. 

다음으로, 리소스 그룹의 이름 및 저장소 계정을 대신하여 계정을 업그레이드하도록 다음 명령을 호출합니다.

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Blob 데이터에 대한 액세스 계층을 지정합니다.

범용 v2 계정은 모든 Azure Storage 서비스 및 데이터 개체를 지원하지만 액세스 계층은 Blob Storage의 블록 blob에 대해서만 사용할 수 있습니다. 범용 v2 저장소 계정으로 업그레이드할 때 blob 데이터에 대한 액세스 계층을 지정할 수 있습니다. 

액세스 계층을 사용하면 예상된 사용량 패턴에 따라 가장 비용 효율적인 저장소를 선택할 수 있습니다. 블록 blob을 핫, 쿨 또는 보관 계층에 저장할 수 있습니다. 액세스 계층에 대한 자세한 내용은 [Azure Blob Storage: 핫, 쿨 및 보관 저장소 계층](../blobs/storage-blob-storage-tiers.md)을 참조하세요.

기본적으로 새 저장소 계정은 핫 액세스 계층에 만들어지고 범용 v1 저장소 계정은 핫 액세스 계층으로 업그레이드됩니다. 업그레이드 후에 데이터를 사용하는 액세스 계층을 탐색하는 경우 사용자의 시나리오를 고려합니다. 범용 v2 계정으로 마이그레이션하기 위한 두 가지 일반적인 사용자 시나리오가 있습니다.

* 기존 범용 v1 저장소 계정이 있고, blob 데이터에 대한 올바른 저장소 계정을 사용하여 범용 v2 저장소 계정에 대한 변경을 평가하려고 합니다.
* 범용 v2 저장소 계정을 사용하도록 결정했거나 이미 저장소 계정이 하나 있고 blob 데이터에 대해 핫 또는 쿨 저장소 계층을 사용해야 하는지 여부를 평가하려고 합니다.

두 경우에 첫 번째 우선 순위는 범용 v2 저장소 계정에 저장된 데이터를 저장, 액세스 및 운영하는 비용을 산정하고 현재 비용과 비교하는 것입니다.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>현재 사용 패턴에 대한 예상 비용

특정 계층의 범용 v2 저장소 계정에 blob 데이터를 저장하고 액세스하는 비용을 예상하기 위해 기존 사용 패턴을 평가하거나 예상된 사용 패턴을 계산해야 합니다. 일반적으로 다음을 파악해야 합니다.

* 다음을 포함한 Blob Storage 사용량(기가바이트 단위):
    - 저장소 계정에 저장되는 데이터의 양
    - 월간 데이터 볼륨 변경 추이, 새 데이터가 기존 데이터를 계속 대체하는지 여부
* 다음을 포함한 Blob Storage 데이터에 대한 기본 액세스 패턴:
    - 읽어오는 데이터의 양과 저장소 계정에 기록되는 데이터의 양 
    - 저장소 계정의 데이터에서 발생하는 읽기 작업 대 쓰기 작업의 수

요구 사항에 가장 적합한 액세스 계층을 결정하려면 현재 blob 데이터가 사용하는 용량 및 해당 데이터가 사용 중인 용량을 판단하는 것이 유용할 수 있습니다. 

마이그레이션 전에 저장소 계정에 대한 사용 현황 데이터를 수집하려면 [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)를 사용하여 저장소 계정을 모니터링할 수 있습니다. Azure Monitor는 로깅을 수행하고 Azure Storage 등의 Azure 서비스에 대한 메트릭 데이터를 제공합니다. 

저장소 계정의 blob에 대한 사용량 데이터를 모니터링하려면 용량 메트릭을 Azure Monitor에서 사용하도록 설정합니다. 용량 메트릭은 사용자 계정의 blob이 매일 사용하는 저장소의 용량에 대한 데이터를 기록합니다. 용량 메트릭을 사용하여 저장소 계정에 데이터를 저장하는 비용을 예상할 수 있습니다. 각 저장소 유형에 대한 Blob Storage 용량의 가격 책정 방식을 알아보려면 [블록 blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

Blob Storage에 대한 데이터 액세스 패턴을 모니터링하려면 Azure Monitor에서 트랜잭션 메트릭을 활성화합니다. 각 호출 빈도를 예측하기 위해 다른 Azure Storage 작업을 필터링할 수 있습니다. 블록 및 추가 blob에 대한 다양한 트랜잭션 유형의 가격 책정 방식을 알아보려면 [블록 blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.  

Azure Monitor에서 메트릭을 수집하는 방법에 대 한 자세한 내용은 [Azure Monitor의 Azure Storage 메트릭](storage-metrics-in-azure-monitor.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [저장소 계정을 만드는](storage-quickstart-create-account.md)
- [Azure Storage 계정 관리](storage-account-manage.md)