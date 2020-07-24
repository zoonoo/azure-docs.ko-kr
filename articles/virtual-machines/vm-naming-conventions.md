---
title: Azure VM 크기 명명 규칙
description: Azure VM 크기에 사용 되는 명명 규칙을 설명 합니다.
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 2059c6f374e4cd5c2518e2fc0ac0da5858b99825
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131721"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Azure 가상 머신 크기 명명 규칙

이 페이지에서는 Azure Vm에 사용 되는 명명 규칙을 간략하게 설명 합니다. Vm은 이러한 명명 규칙을 사용 하 여 다양 한 기능 및 사양을 나타냅니다.

## <a name="naming-convention-explanation"></a>명명 규칙 설명

**[제품군]**  +  **[하위 제품군 *]**  +  **[vCPUs 개수]**  +  **[추가 기능]**  +  **[액셀러레이터 키 유형 *]**  +  **[버전]**

|값 | 설명|
|---|---|
| 표준, 기본 또는 실험적 | "Standard"는 모든 GA VM 크기에 할당 되는 기본값입니다. | 
| 패밀리 | VM 제품군 시리즈를 나타냅니다.| 
| * 하위 제품군 | 특수 VM differentiations 사용 됩니다.|
| vCPUs 개수| VM의 vCPUs 수를 나타냅니다. |
| 추가 기능 | 하나 이상의 소문자가 다음과 같은 추가 기능을 나타냅니다. <br> a = AMD 기반 프로세서 <br> d = 디스크 (로컬 임시 디스크 있음); 최신 Azure Vm의 경우 [Ddv4 및 Ddsv4 시리즈](./ddv4-ddsv4-series.md) 를 참조 하세요. <br> h = 최대 절전 모드 가능 <br> i = 격리 됨 <br> l = 메모리가 부족 합니다. <br> m = 메모리 집약적 <br> t = 작은 메모리 <br> r = RDMA <br> s = Premium Storage 가능한 [울트라 SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) 를 포함 하 여 사용할 수 있습니다. 참고:의 특성이 없는 몇 가지 최신 크기는 여전히 Premium Storage (예: M128, M64 등)를 지원할 수 있습니다.<br> |
| * 액셀러레이터 키 유형 | 특수/GPU Sku의 하드웨어 가속기 유형을 나타냅니다. Q3 2020에서 시작 된 새 특수/GPU Sku에만 이름에 하드웨어 가속기가 포함 됩니다. |
| 버전 | VM 제품군 시리즈의 버전을 나타냅니다. |

## <a name="example-breakdown"></a>분석 예제

**[제품군]**  +  **[하위 제품군 *]**  +  **[vCPUs 개수]**  +  **[추가 기능]**  +  **[액셀러레이터 키 유형 *]**  +  **[버전]**

### <a name="example-1-m416ms_v2"></a>예 1: M416ms_v2

|값 | 설명|
|---|---|
| 패밀리 | M | 
| vCPUs 개수 | 416 |
| 추가 기능 | m = 메모리 집약적 <br> s = Premium Storage 가능 |
| 버전 | v2 |

### <a name="example-2-nv16as_v4"></a>예 2: NV16as_v4

|값 | 설명|
|---|---|
| 패밀리 | N | 
| 하위 제품군 | V |
| vCPUs 개수 | 16 |
| 추가 기능 | a = AMD 기반 프로세서 <br> s = Premium Storage 가능 |
| 버전 | v4 |

### <a name="example-3-nc4as_t4_v3"></a>예 3: NC4as_T4_v3

|값 | 설명|
|---|---|
| 패밀리 | N | 
| 하위 제품군 | C |
| vCPUs 개수 | 4 |
| 추가 기능 | a = AMD 기반 프로세서 <br> s = Premium Storage 가능 |
| 액셀러레이터 키 유형 | T4 |
| 버전 | v3 |

## <a name="next-steps"></a>다음 단계

Azure에서 사용 가능한 [VM 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 에 대해 자세히 알아보세요. 
