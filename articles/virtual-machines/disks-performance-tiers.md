---
title: Azure managed disks의 성능 변경
description: 관리 디스크의 성능 계층 및 기존 managed disks의 성능 계층을 변경 하는 방법에 대해 알아봅니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7da500c3f18b7bf7057b0c5875bc9b39136a6483
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396589"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>관리 디스크의 성능 계층 (미리 보기)

Azure 디스크 저장소는 현재 기본 제공 버스트 기능을 제공 하 여 단기 예상치 못한 트래픽을 처리 하는 데 필요한 성능을 향상 시킬 수 있습니다. Premium Ssd는 실제 디스크 크기를 늘리지 않고 디스크 성능을 향상 시킬 수 있는 유연성을 제공 하므로 워크 로드 성능 요구를 충족 하 고 비용을 절감할 수 있습니다 .이 기능은 현재 미리 보기로 제공 됩니다. 이는 휴일 쇼핑, 성능 테스트 또는 교육 환경 실행과 같이 일관 되 게 높은 수준의 성능이 일시적으로 필요한 이벤트에 적합 합니다. 이러한 이벤트를 처리 하기 위해 필요에 따라 더 높은 성능 계층을 선택 하 고 추가 성능이 더 이상 필요 하지 않을 경우 원래 계층으로 돌아갈 수 있습니다.

## <a name="how-it-works"></a>작동 방식

디스크를 처음 배포 하거나 프로 비전 할 때 해당 디스크의 기준 성능 계층은 프로 비전 된 디스크 크기에 따라 설정 됩니다. 높은 수요를 충족 하기 위해 더 높은 성능 계층을 선택할 수 있으며, 더 이상 필요 하지 않은 경우 초기 기준 성능 계층으로 돌아갈 수 있습니다.

계층이 변경 됨에 따라 청구는 변경 됩니다. 예를 들어 P10 disk (128 GiB)를 프로 비전 하는 경우 기준 성능 계층은 P10 (500 IOPS 및 100 MB/s)로 설정 되 고 P10 요금으로 청구 됩니다. 디스크 크기를 늘리지 않고 P50 (7500 IOPS 및 250 MB/s)의 성능과 일치 하도록 계층을 업데이트할 수 있습니다 .이 기간 동안에는 P50 요금으로 요금이 청구 됩니다. 더 높은 성능이 더 이상 필요 하지 않으면 P10 계층으로 돌아갈 수 있으며 디스크는 P10 속도로 다시 청구 됩니다.

| 디스크 크기 | 기준 성능 계층 | 로 업그레이드할 수 있습니다. |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128GiB | P10 | P15, P20, P30, P40, P50 |
| 256GiB | P15 | P20, P30, P40, P50 |
| 512GiB | P20 | P30, P40, P50 |
| 1TiB | P30 | P40, P50 |
| 2TiB | P40 | P50 |
| 4TiB | P50 | 없음 |
| 8TiB | P60 |  P70, P80 |
| 16TiB | P70 | P80 |
| 32TiB | P80 | 없음 |

청구 정보 [는 관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조 하세요.

## <a name="restrictions"></a>제한

- 현재 premium Ssd 에서만 지원 됩니다.
- 계층을 변경 하기 전에 실행 중인 VM에서 디스크를 분리 해야 합니다.
- P60, P70 및 P80 성능 계층의 사용은 4096 GiB 이상 디스크로 제한 됩니다.
- 디스크 성능 계층은 24 시간 마다 한 번만 변경할 수 있습니다.

## <a name="regional-availability"></a>국가별 가용성

관리 디스크의 성능 계층 조정은 현재 다음 지역에서 premium Ssd에만 사용할 수 있습니다.

- 미국 중서부 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>계층을 기준 계층 보다 높은 빈 데이터 디스크를 만듭니다.

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Azure Marketplace 이미지에서 기준선 계층 보다 높은 계층의 OS 디스크 만들기

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>디스크의 계층 업데이트

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>디스크의 계층 표시

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>다음 단계

큰 성능 계층을 활용 하기 위해 디스크 크기를 조정 해야 하는 경우 주제에 대 한 문서를 참조 하세요.

- [Azure CLI를 사용하여 Linux VM에서 가상 하드 디스크 확장](linux/expand-disks.md)
- [Windows 가상 머신에 연결 된 관리 되는 디스크 확장](windows/expand-os-disk.md)
