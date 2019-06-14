---
title: 풀의 VM 크기 선택 - Azure Batch | Microsoft Docs
description: Azure Batch 풀에서 계산 노드에 사용할 수 있는 VM 크기를 선택하는 방법을 설명합니다.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 033e0865f23034b94e3133e0ba5890eca4e746ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080882"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch 풀의 계산 노드에 대한 VM 크기 선택

Azure Batch 풀에 대한 노드 크기를 선택하면 Azure에서 사용할 수 있는 거의 모든 VM 크기 중에서 선택할 수 있습니다. Azure는 다양한 작업에 맞게 다양한 크기의 Linux 및 Windows VM을 제공합니다.

VM 크기를 선택하는 데는 몇 가지 예외 및 제한 사항이 있습니다.

* 일부 VM 시리즈 또는 VM 크기 일괄 처리에서 지원 되지 않습니다.
* 일부 VM 크기는 제한되어 있으므로 할당하기 전에 특별히 사용하도록 설정해야 합니다.

## <a name="supported-vm-series-and-sizes"></a>지원 되는 VM 시리즈 및 크기

### <a name="pools-in-virtual-machine-configuration"></a>Virtual Machine 구성의 풀

가상 컴퓨터 구성의 batch 풀 거의 모든 VM 크기를 지원 합니다. ([Linux](../virtual-machines/linux/sizes.md)하십시오 [Windows](../virtual-machines/windows/sizes.md)). 지원 되는 크기 및 제한 사항에 자세히 알아보려면 다음 표를 참조 하십시오.

어떤 프로 모션 하거나 지원에 대 한 미리 보기 VM 크기 나열 되지 않은 보장 되지 않습니다.

| VM 시리즈  | 지원되는 크기 | Batch 계정의 풀 할당 모드<sup>1</sup> |
|------------|---------|-----------------|
| 기본 A 시리즈 | 모든 규모 *제외한* Basic_A0 (A0) | 모두 |
| A 시리즈 | 모든 규모 *제외한* Standard_A0 | 모두 |
| Av2 시리즈 | 모든 크기 | 모두 |
| B 시리즈 | 없음 | 사용할 수 없음 |
| DC 시리즈 | 없음 | 사용할 수 없음 |
| Dsv2 시리즈, Dv2 | 모든 크기 | 모두 |
| Dv3, Dsv3 시리즈 | 모든 크기 | 모두 |
| [메모리 액세스에 최적화 크기](../virtual-machines/linux/sizes-memory.md) | 없음 | 사용할 수 없음 |
| Fsv2 시리즈 | 모든 크기 | 모두 |
| H 시리즈 | 모든 크기 | 모두 |
| HB 시리즈 | 모든 크기 | 사용자 구독 모드 |
| HC 시리즈 | 모든 크기 | 사용자 구독 모드 |
| Ls 시리즈 | 모든 크기 | 모두 |
| Lsv2 시리즈 | 없음 | 사용할 수 없음 |
| M 시리즈 | Standard_M64ms (낮은 우선 순위만), Standard_M128s (낮은 우선 순위만) | 모두 |  
| NCv2-series<sup>2</sup> | 모든 크기 | 모두 |
| NCv3-series<sup>2</sup> | 모든 크기 | 모두 |
| ND-series<sup>2</sup> | 모든 크기 | 모두 |
| NDv2 시리즈 | 모든 크기 | 사용자 구독 모드 |
| NV 시리즈 | 모든 크기 | 모두 |
| NVv3 시리즈 | 없음 | 사용할 수 없음 |
| SAP HANA | 없음 | 사용할 수 없음 |

<sup>1</sup> 몇 가지 새로운 VM 시리즈는 처음에 부분적으로 지원 합니다. 이러한 VM 시리즈를 사용 하 여 Batch 계정에서 할당할 수는 **풀 할당 모드가** 로 설정 **사용자 구독**합니다. 참조 [Batch 관리 계정](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) 일괄 처리 계정 구성에 대 한 자세한 내용은 합니다. 참조 [할당량 및 제한](batch-quota-limit.md) 부분적으로 지원 되는 VM 시리즈에 대 한 이러한 할당량을 요청 하는 방법에 알아보려면 **사용자 구독** Batch 계정.  

<sup>2</sup> 이러한 VM 크기는 가상 컴퓨터 구성에서 Batch 풀에서 할당 될 수 있지만 특정 요청 해야 합니다 [할당량 증가](batch-quota-limit.md#increase-a-quota)합니다.

### <a name="pools-in-cloud-service-configuration"></a>Cloud Service 구성의 풀

Cloud Service 구성의 Batch 풀은 다음을 **제외한** 모든 [Cloud Services에 적합한 VM 크기](../cloud-services/cloud-services-sizes-specs.md)를 지원합니다.

| VM 시리즈  | 지원되지 않는 크기 |
|------------|-------------------|
| A 시리즈   | 아주 작음       |
| Av2 시리즈 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>크기 고려 사항

* **애플리케이션 요구 사항** - 노드에서 실행할 애플리케이션의 특성 및 요구 사항을 고려합니다. 애플리케이션이 다중 스레드되었는지 여부 및 애플리케이션에서 소비하는 메모리 양과 같은 측면은 가장 적절하고 비용 효과적인 노드 크기를 결정하는 데 도움을 줄 수 있습니다. 다중 인스턴스 [MPI 작업](batch-mpi.md) 또는 CUDA 애플리케이션의 경우, 각각 특수화된 [HPC](../virtual-machines/linux/sizes-hpc.md) 또는 [GPU 가능](../virtual-machines/linux/sizes-gpu.md) VM 크기를 고려합니다. [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](batch-pool-compute-intensive-sizes.md)을 참조하세요.

* **노드당 태스크 수** - 한 번에 하나의 태스크가 하나의 노드에서 실행된다고 가정하여 노드 크기를 선택하는 것이 일반적입니다. 하지만 작업 실행 중에 계산 노드에서 여러 태스크(따라서 여러 애플리케이션 인스턴스)를 [병렬로 실행하는](batch-parallel-node-tasks.md) 것이 유리할 수도 있습니다. 이 경우 병렬 태스크 실행에 대해 증가되는 수요를 수용할 수 있도록 멀티 코어 노드 크기를 선택하는 것이 일반적입니다.

* **다른 태스크에 대한 로드 수준** - 풀의 모든 노드는 동일한 크기입니다. 상이한 시스템 요구 사항 및/또는 부하 수준으로 애플리케이션을 실행하려면 별도의 풀을 사용하는 것이 좋습니다.

* **지역 가용성** -VM 시리즈 또는 크기 못할 Batch 계정을 만든 지역에서 합니다. 크기를 사용할 수 있는지를 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

* **할당량** - 배치 계정의 [코어 할당량](batch-quota-limit.md#resource-quotas)은 Batch 풀에 추가할 수 있는 지정된 크기의 노드 수를 제한할 수 있습니다. 할당량 증가를 요청하려면 [이 문서](batch-quota-limit.md#increase-a-quota)를 참조하세요. 

* **풀 구성** - 일반적으로 Cloud Service 구성에 비해 Virtual Machine 구성에서 풀을 만들 때 더 많은 VM 크기 옵션을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)을 참조하세요.
* 계산 집약적 VM 크기를 사용하는 방법에 대한 자세한 내용은 [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](batch-pool-compute-intensive-sizes.md)을 참조하세요.
