---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5f7477671e7be4e6e8fd736ab94fa7e7b1e99a6e
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43184410"
---
ZRS(영역 중복 저장소)는 단일 지역에 있는 3개의 저장소 클러스터에서 데이터를 동기적으로 복제합니다. 각 저장소 클러스터는 다른 저장소 클러스터와 물리적으로 분리되어 있으며 자체 AZ(가용성 영역)에 상주합니다. 각 가용성 영역과 그 안에 포함된 ZRS 클러스터는 자율적으로 사용되며, 별도의 유틸리티와 네트워킹 기능을 갖추고 있습니다.

ZRS 계정에 데이터를 저장하면 영역을 사용할 수 없는 경우 데이터에 액세스하여 관리할 수 있습니다. ZRS는 뛰어난 성능과 짧은 대기 시간을 제공하며, [LRS(로컬 중복 저장소)](../articles/storage/common/storage-redundancy-lrs.md)와 동일한 [확장성 목표](../articles/storage/common/storage-scalability-targets.md)를 제공합니다.

가동 중단 또는 자연 재해로 인해 영역 데이터 센터를 사용할 수 없는 경우에도 강력한 일관성, 강력한 내구성 및 고가용성이 필요한 시나리오의 경우 ZRS를 사용하는 것이 좋습니다. ZRS는 지정된 연도 동안 99.9999999999% 이상의 내구성을 저장소 개체에 제공합니다(12 9의 경우).

가용성 영역에 대한 자세한 내용은 [가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)를 참조하세요.

## <a name="support-coverage-and-regional-availability"></a>지원 범위 및 국가별 가용성
ZRS는 현재 표준 [GPv2(범용 v2)](../articles/storage/common/storage-account-options.md#general-purpose-v2-accounts) 계정 유형을 지원합니다. ZRS는 블록 Blob, 디스크가 아닌 페이지 Blob, 파일, 테이블 및 큐에 사용할 수 있습니다. 또한 모든 [저장소 분석](../articles/storage/common/storage-analytics.md) 로그와 [Storage 메트릭](../articles/storage/common/storage-enable-and-view-metrics.md)에도 사용할 수 있습니다.

ZRS를 일반적으로 사용할 수 있는 지역은 다음과 같습니다.

- 미국 동부
- 미국 동부 2
- 미국 서부 2
- 미국 중부
- 북유럽
- 서유럽
- 프랑스 중부
- 동남아시아

Microsoft는 추가 Azure 지역에서 ZRS를 사용할 수 있도록 계속 지원하고 있습니다. 새 지역에 대한 정보는 [Azure 서비스 업데이트](https://azure.microsoft.com/updates/) 페이지에서 정기적으로 확인하세요.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>영역을 사용할 수 없게 되면 어떻게 되나요?

영역을 사용할 수 없게 되더라도 데이터의 복원력은 유지됩니다. 지수 백오프를 사용하는 다시 시도 정책을 구현하는 것과 같은 일시적인 오류 처리에 대한 사례를 계속 따르는 것이 좋습니다. 영역을 사용할 수 없는 경우 Azure에서 DNS 재지정과 같은 네트워킹 업데이트를 수행합니다. 완료되기 전에 데이터에 액세스하는 경우 이러한 업데이트는 응용 프로그램에 영향을 줄 수 있습니다.

ZRS는 여러 영역에 영구적인 영향을 주는 지역 재해로부터 데이터를 보호하지 못할 수 있습니다. 대신, 데이터를 일시적으로 사용할 수 없게 되면 ZRS에서 해당 데이터에 대한 복원력을 제공합니다. 지역 재해로부터 보호하려면 GRS(지역 중복 저장소)를 사용하는 것이 좋습니다. GRS에 대한 자세한 내용은 [GRS(지역 중복 저장소): Azure Storage에 대한 지역 간 복제](../articles/storage/common/storage-redundancy-grs.md)를 참조하세요.

## <a name="converting-to-zrs-replication"></a>ZRS 복제로 변환
현재 Azure Portal 또는 저장소 리소스 공급자 API를 사용하여 LRS, GRS 및 RA-GRS 간에 마이그레이션하는 경우 계정의 중복성 형식을 변경할 수 있습니다. 그러나 ZRS를 사용하면 단일 저장소 스탬프에서 한 지역 내의 여러 스탬프로의 물리적 데이터 이동이 필요하므로 마이그레이션이 간단하지 않습니다. 

ZRS 간의 마이그레이션에는 두 가지 기본 옵션이 있습니다. 기존 계정에서 새 ZRS 계정으로 데이터를 수동으로 복사하거나 이동할 수 있습니다. 실시간 마이그레이션을 요청할 수도 있습니다. 실시간 마이그레이션이 완료될 때까지 보장할 수 없으므로 수동 마이그레이션을 수행하는 것이 좋습니다. 수동 마이그레이션 경로는 실시간 마이그레이션보다 유연성이 뛰어나며 마이그레이션 시기를 제어할 수 있습니다.

수동 마이그레이션을 수행하려면 다음과 같은 다양한 옵션을 사용할 수 있습니다.
- AzCopy, 저장소 SDK, 신뢰할 수 있는 타사 도구 등과 같은 기존 도구를 사용합니다.
- Hadoop 또는 HDInsight에 익숙한 경우 원본 및 대상(ZRS) 계정을 클러스터에 연결하고, DistCp와 같은 도구를 사용하여 데이터 복사 프로세스를 대규모로 병렬 처리할 수 있습니다
- 저장소 SDK의 한 가지 특징을 활용하여 사용자 고유의 도구를 빌드합니다.

그러나 수동으로 마이그레이션하면 일부 응용 프로그램이 가동 중지되고, 결국에는 이를 받아들일 수 없는 경우 Microsoft에서 실시간 마이그레이션 옵션을 제공합니다. 실시간 마이그레이션은 원본 및 대상 저장소 스탬프간에 데이터를 마이그레이션하는 동안 기존 저장소 계정을 계속 사용할 수 있는 현재 위치 마이그레이션입니다. 마이그레이션하는 동안에도 일반적으로 수행하는 것과 동일한 수준의 내구성 및 가용성 SLA가 유지됩니다.

그러나 실시간 마이그레이션에는 특정 제한 사항이 있습니다. 이러한 제한 사항은 아래에 나열되어 있습니다.

- Microsoft는 실시간 마이그레이션 요청을 신속하게 처리하지만 마이그레이션이 완료되는 시기는 보장하지 않습니다. 특정 시간까지 데이터가 ZRS에 있어야 하는 경우 수동 마이그레이션을 수행해야 합니다. 일반적으로 계정에 데이터가 많을수록 해당 데이터를 마이그레이션하는 데 걸리는 시간이 더 오래 걸립니다. 
- LRS 또는 GRS 복제를 사용하는 계정에서만 실시간 마이그레이션을 수행할 수 있습니다. 계정에서 RA-GRS를 사용하는 경우 계속하기 전에 먼저 이러한 복제 유형 중 하나로 마이그레이션해야 합니다. 이 중간 단계는 RA-GRS에서 제공하는 보조 읽기 전용 엔드포인트가 마이그레이션 전에 제거되도록 합니다.
- 계정에 데이터가 있어야 합니다.
- 지역 내 마이그레이션만 지원됩니다. 데이터를 원본 계정과 다른 지역에 있는 ZRS 계정으로 마이그레이션하려면 수동 마이그레이션을 수행해야 합니다.
- 표준 저장소 계정 유형만 지원됩니다. 프리미엄 저장소 계정에서 마이그레이션할 수 없습니다.

실시간 마이그레이션 요청은 Azure 지원 포털을 통해 이동합니다. 포털에서 ZRS로 변환하려는 저장소 계정을 선택합니다.
1. **새 지원 요청**을 클릭합니다.
2. 기본 사항을 확인합니다. **다음**을 클릭합니다. 
3. **문제** 섹션에서 
    - 심각도를 있는 그대로 유지합니다.
    - 문제 유형 = **데이터 마이그레이션**
    - 범주 = **지역 내에서 ZRS로 마이그레이션**
    - 제목 = **ZRS 계정 마이그레이션**(또는 다른 설명적인 이름)
    - 세부 정보 = ______ 지역의 [LRS, GRS]에서 ZRS로 마이그레이션하고 싶습니다. 
4. **다음**을 클릭합니다.
5. [연락처 정보] 블레이드에서 연락처 정보가 올바른지 확인합니다.
6. **제출**을 클릭합니다.

그러면 지원 담당자가 연락할 것입니다. 해당 담당자는 필요로 하는 도움을 제공할 수 있습니다. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS 클래식: 블록 Blob 중복성을 위한 레거시 옵션
> [!NOTE]
> ZRS 클래식 계정은 사용이 중단될 예정이며 2021년 3월 31일에 마이그레이션해야 합니다. 사용이 중단되기 전에 ZRS 클래식 고객에게 자세한 내용이 전송될 예정입니다. Microsoft는 앞으로 ZRS 클래식에서 ZRS로의 자동 마이그레이션 프로세스를 제공할 예정입니다.

>[!NOTE]
> 한 지역에서 ZRS가 [일반 공급](#support-coverage-and-regional-availability)되는 경우 ZRS 클래식 계정은 해당 지역의 포털에서 더 이상 만들 수 없게 됩니다. 그러나 ZRS 클래식이 더 이상 사용되지 않을 때까지는 Microsoft PowerShell 및 Azure CLI와 같은 다른 방법을 통해 하나의 ZRS 클래식 계정을 만들 수 있습니다.

ZRS 클래식은 1~2개의 지역 내의 데이터 센터에서 데이터를 비동기적으로 복제합니다. 복제본은 Microsoft가 보조 지역에 장애 조치를 시작하지 않는 한 사용할 수 없습니다. ZRS 클래식은 [GPv1(범용 V1)](../articles/storage/common/storage-account-options.md#general-purpose-v1-accounts) 저장소 계정의 **블록 Blob**에서만 사용할 수 있습니다. ZRS 클래식 계정은 LRS 또는 GRS 간에 변환할 수 없으며 메트릭 또는 로깅 기능이 없습니다.

ZRS 클래식 계정은 LRS, GRS 또는 RA-GRS 계정으로 변환하거나 이러한 계정을 ZRS 클래식 계정으로 변환할 수 없습니다. 또한 ZRS 클래식 계정은 메트릭이나 로깅을 지원하지 않습니다.

ZRS 계정 데이터를 LRS, ZRS 클래식, GRS 또는 RA-GRS 계정 간에 수동으로 마이그레이션하려면 AzCopy, Azure Storage 탐색기, Azure PowerShell 또는 Azure CLI를 사용합니다. Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 마이그레이션 솔루션을 빌드할 수도 있습니다.
