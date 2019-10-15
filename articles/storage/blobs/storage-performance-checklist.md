---
title: Blob 저장소에 대 한 성능 및 확장성 검사 목록-Azure Storage
description: 고성능 응용 프로그램을 개발 하는 데 Blob storage와 함께 사용 하기 위한 검증 된 관행의 검사 목록입니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 56bb5a1ac3c4003eca6ebe8392fc5b97f36a3317
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311140"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Blob 저장소에 대 한 성능 및 확장성 검사 목록

Microsoft는 Blob storage를 사용 하 여 고성능 응용 프로그램을 개발 하기 위한 다양 한 검증 사례를 개발 했습니다. 이 검사 목록에서는 개발자가 성능을 최적화 하기 위해 수행할 수 있는 주요 사례를 식별 합니다. 응용 프로그램을 디자인 하는 동안 프로세스 전체에서 이러한 사례를 염두에 두어야 합니다.

Azure Storage에는 용량, 트랜잭션 속도 및 대역폭에 대 한 확장성 및 성능 목표가 있습니다. Azure Storage 확장성 목표에 대 한 자세한 내용은 [저장소 계정에 대 한 확장성 및 성능 목표 Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조 하세요.

## <a name="checklist"></a>검사 목록

이 문서에서는 성능에 대 한 검증 된 사례를 Blob storage 응용 프로그램을 개발 하는 동안 수행할 수 있는 검사 목록으로 구성 합니다.

| 완료된 | 범주 | 디자인 고려 사항 |
| --- | --- | --- |
| &nbsp; |확장성 목표 |[최대 저장소 계정 수를 사용 하지 않도록 응용 프로그램을 디자인할 수 있나요?](#maximum-number-of-storage-accounts) |
| &nbsp; |확장성 목표 |[용량 및 트랜잭션 제한에 도달 하는 것을 방지 하 고 있나요?](#capacity-and-transaction-targets) |
| &nbsp; |확장성 목표 |[많은 수의 클라이언트가 동시에 단일 blob에 액세스 하나요?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |확장성 목표 |[응용 프로그램이 단일 blob에 대 한 확장성 목표 내에서 유지 되나요?](#bandwidth-and-operations-per-blob) |
| &nbsp; |분할 |[명명 규칙이 부하 분산 향상에 맞게 설계되었습니까?](#partitioning) |
| &nbsp; |네트워킹 |[필요한 성능을 얻기 위해 클라이언트 쪽 장치에 충분 한 대역폭과 짧은 대기 시간이 있나요?](#throughput) |
| &nbsp; |네트워킹 |[클라이언트 쪽 장치에 고품질 네트워크 링크가 있나요?](#link-quality) |
| &nbsp; |네트워킹 |[클라이언트 응용 프로그램이 저장소 계정과 동일한 지역에 있나요?](#location) |
| &nbsp; |직접 클라이언트 액세스 |[SAS (공유 액세스 서명) 및 CORS (원본 간 리소스 공유)를 사용 하 여 Azure Storage에 대 한 직접 액세스를 사용 하 고 있습니까?](#sas-and-cors) |
| &nbsp; |캐싱 |[응용 프로그램이 자주 액세스 되 고 거의 변경 되지 않는 데이터를 캐싱 하나요?](#reading-data) |
| &nbsp; |캐싱 |[응용 프로그램이 업데이트를 클라이언트에 캐시 한 다음 더 큰 집합으로 업로드 하 여 응용 프로그램을 일괄 처리 하나요?](#uploading-data-in-batches) |
| &nbsp; |.NET 구성 |[최적의 성능을 위해 .NET Core 2.1 이상을 사용 하 고 있습니까?](#use-net-core) |
| &nbsp; |.NET 구성 |[클라이언트가 충분한 수의 동시 연결을 사용하도록 구성했습니까?](#increase-default-connection-limit) |
| &nbsp; |.NET 구성 |[.NET 응용 프로그램의 경우 충분 한 수의 스레드를 사용 하도록 .NET을 구성 했습니까?](#increase-minimum-number-of-threads) |
| &nbsp; |병렬 처리 |[클라이언트의 기능 또는 확장성 목표에 대 한 접근 방식을 오버 로드 하지 않도록 병렬 처리를 적절 하 게 제한 해야 하나요?](#unbounded-parallelism) |
| &nbsp; |도구 |[Microsoft에서 제공 하는 최신 버전의 클라이언트 라이브러리 및 도구를 사용 하 고 있나요?](#client-libraries-and-tools) |
| &nbsp; |다시 시도 |[제한 오류 및 시간 제한에 대해 지 수 백오프가 포함 된 재시도 정책을 사용 하나요?](#timeout-and-server-busy-errors) |
| &nbsp; |다시 시도 |[애플리케이션에서 다시 시도할 수 없는 오류 발생 시에는 작업을 다시 시도하지 않습니까?](#non-retryable-errors) |
| &nbsp; |Blob 복사 |[가장 효율적인 방식으로 blob을 복사 하 고 있나요?](#blob-copy-apis) |
| &nbsp; |Blob 복사 |[대량 복사 작업에 최신 버전의 AzCopy를 사용 하 고 있나요?](#use-azcopy) |
| &nbsp; |Blob 복사 |[Azure Data Box 패밀리를 사용 하 여 대량의 데이터를 가져올 수 있나요?](#use-azure-data-box) |
| &nbsp; |콘텐츠 배포 |[콘텐츠 배포를 위해 CDN을 사용합니까?](#content-distribution) |
| &nbsp; |메타 데이터 사용 |[자주 사용되는 Blob 관련 메타데이터를 해당 메타데이터에 저장하고 있습니까?](#use-metadata) |
| &nbsp; |신속 하 게 업로드 |[Blob 하나를 빠르게 업로드하려는 경우 블록을 병렬로 업로드합니까?](#upload-one-large-blob-quickly) |
| &nbsp; |신속 하 게 업로드 |[Blob 여러 개를 빠르게 업로드하려는 경우 Blob를 병렬로 업로드합니까?](#upload-many-blobs-quickly) |
| &nbsp; |Blob 유형 |[해당하는 경우 페이지 Blob 또는 블록 Blob를 사용합니까?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>확장성 목표

애플리케이션이 확장성 목표에 도달하거나 목표를 초과하는 경우 트랜잭션 대기 시간이 길어지거나 제한이 증가할 수 있습니다. 응용 프로그램을 제한 Azure Storage 경우 서비스는 503 (서버 사용 중) 또는 500 (작업 시간 제한) 오류 코드를 반환 하기 시작 합니다. 확장성 목표의 한도 내에서 이러한 오류를 방지 하는 것은 응용 프로그램의 성능을 향상 시키는 데 중요 한 부분입니다.

큐 서비스의 확장성 목표에 대 한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets)를 참조 하세요.

### <a name="maximum-number-of-storage-accounts"></a>최대 저장소 계정 수

특정 구독/지역 조합에 허용 되는 최대 저장소 계정 수에 도달 하는 경우 시나리오를 평가 하 고 다음 조건이 적용 되는지 확인 합니다.

- 저장소 계정을 사용 하 여 관리 되지 않는 디스크를 저장 하 고 해당 디스크를 Vm (가상 컴퓨터)에 추가 하나요? 이 시나리오에서는 Microsoft에서 관리 디스크를 사용 하는 것이 좋습니다. 관리 디스크는 개별 저장소 계정을 만들고 관리할 필요 없이 자동으로 확장 됩니다. 자세한 내용은 [Azure managed Disks 소개](../../virtual-machines/windows/managed-disks-overview.md) 를 참조 하세요.
- 데이터 격리를 위해 고객 당 하나의 저장소 계정을 사용 하 고 있나요? 이 시나리오에서는 전체 저장소 계정 대신 각 고객에 대해 blob 컨테이너를 사용 하는 것이 좋습니다. 이제 Azure Storage를 사용 하 여 컨테이너 별로 RBAC (역할 기반 액세스 제어) 역할을 할당할 수 있습니다. 자세한 내용은 [Azure Portal에서 Azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 데이터 큐](../common/storage-auth-aad-rbac-portal.md)에 추가를 참조 하세요.
- 여러 저장소 계정을 분할 하 여 수신, 송신, IOPS (초당 i/o 작업 수) 또는 용량을 늘릴 수 있나요? 이 시나리오에서는 가능한 경우 워크 로드에 필요한 저장소 계정의 수를 줄이기 위해 표준 저장소 계정에 대해 증가 된 제한을 활용 하는 것이 좋습니다. 저장소 계정에 대 한 증가 한도를 요청 하려면 [Azure 지원](https://azure.microsoft.com/support/options/) 에 문의 하세요. 자세한 내용은 더 [큰 규모의 저장소 계정 발표](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)를 참조 하세요.

### <a name="capacity-and-transaction-targets"></a>용량 및 트랜잭션 대상

애플리케이션이 특정 스토리지 계정의 확장성 목표에 도달한 경우 다음 방법 중 하나를 사용할 수 있습니다.  

- 응용 프로그램이 트랜잭션 대상에 도달 하는 경우 높은 트랜잭션 속도 및 낮은 대기 시간에 최적화 된 블록 blob storage 계정을 사용 하는 것이 좋습니다. 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.
- 애플리케이션이 확장성 목표에 도달하거나 목표를 초과한 원인이 되는 워크로드를 다시 고려합니다. 즉, 대역폭이나 용량을 더 적게 사용하거나 트랜잭션을 더 적게 수행하도록 해당 작업을 다시 디자인할 수 있는지를 파악합니다.
- 응용 프로그램이 확장성 목표 중 하나를 초과 해야 하는 경우 여러 저장소 계정을 만들고 이러한 여러 저장소 계정에서 응용 프로그램 데이터를 분할 합니다. 이러한 패턴을 사용하는 경우에는 향후 부하 분산을 위해 스토리지 계정을 더 추가할 수 있도록 애플리케이션을 디자인해야 합니다. 저장소 계정 자체는 저장 된 데이터, 트랜잭션 작성 또는 전송 데이터를 기준으로 사용 하는 것 외에는 비용이 없습니다.
- 응용 프로그램이 대역폭 목표에 근접 하는 경우 데이터를 Azure Storage 전송 하는 데 필요한 대역폭을 줄이기 위해 클라이언트 쪽에서 데이터를 압축 하는 것이 좋습니다.
    데이터를 압축 하 여 대역폭을 절약 하 고 네트워크 성능을 향상 시킬 수 있지만 성능에 부정적인 영향을 미칠 수도 있습니다. 클라이언트 쪽에서 데이터 압축 및 압축 풀기에 대 한 추가 처리 요구 사항의 성능 영향을 평가 합니다. 압축 된 데이터를 저장 하면 표준 도구를 사용 하 여 데이터를 보는 것이 더 어려울 수 있으므로 문제를 더 어려울 수 있습니다.
- 응용 프로그램이 확장성 목표에 도달 하는 경우 다시 시도에 지 수 백오프를 사용 하 고 있는지 확인 합니다. 이 문서에 설명 된 권장 사항을 구현 하 여 확장성 목표에 도달 하지 않도록 하는 것이 가장 좋습니다. 그러나 재시도에 지 수 백오프를 사용 하면 응용 프로그램을 신속 하 게 다시 시도 하지 않으므로 조정 성능이 저하 될 수 있습니다. 자세한 내용은 [시간 제한 및 서버 사용 중 오류](#timeout-and-server-busy-errors)섹션을 참조 하세요.

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>단일 blob에 동시에 액세스 하는 여러 클라이언트

단일 blob에 동시에 액세스 하는 클라이언트 수가 많은 경우 blob 및 저장소 계정 확장성 목표를 모두 고려해 야 합니다. 단일 blob에 액세스할 수 있는 정확한 클라이언트 수는 blob을 동시에 요청 하는 클라이언트 수, blob 크기 및 네트워크 상태와 같은 요소에 따라 달라 집니다.

웹 사이트에서 제공 하는 이미지 또는 비디오와 같은 CDN을 통해 blob을 배포할 수 있는 경우 CDN을 사용할 수 있습니다. 자세한 내용은 [콘텐츠 배포](#content-distribution)섹션을 참조 하세요.

데이터의 기밀을 유지 하는 과학적 시뮬레이션 등의 다른 시나리오에서는 두 가지 옵션을 사용할 수 있습니다. 첫 번째 방법은 일정 기간 동안 blob에 액세스 하 고 동시에 액세스 하는 작업의 액세스를 지연 하는 것입니다. 또는 blob을 여러 저장소 계정에 임시로 복사 하 여 blob 당 총 IOPS 및 저장소 계정 간에 총 IOPS를 늘릴 수 있습니다. 결과는 응용 프로그램의 동작에 따라 달라 지므로 디자인 중에 동시성 패턴을 테스트 해야 합니다.

### <a name="bandwidth-and-operations-per-blob"></a>Blob 당 대역폭 및 작업

단일 blob는 초당 최대 500 요청을 지원 합니다. 동일한 blob을 읽어야 하는 여러 클라이언트가 있고이 제한을 초과할 수 있는 경우 블록 blob storage 계정을 사용 하는 것이 좋습니다. 블록 blob 저장소 계정은 더 높은 요청 속도가 나 IOPS (초당 i/o 작업 수)를 제공 합니다.

Azure CDN와 같은 CDN (content delivery network)을 사용 하 여 blob에 대 한 작업을 배포할 수도 있습니다. Azure CDN에 대 한 자세한 내용은 [Azure CDN 개요](../../cdn/cdn-overview.md)를 참조 하세요.  

## <a name="partitioning"></a>분할

성능을 향상 시키기 위해 blob 데이터를 Azure Storage 분할 하는 방법을 이해 하는 것이 유용 합니다. Azure Storage은 여러 파티션에 걸쳐 있는 데이터 보다 신속 하 게 단일 파티션에 데이터를 제공할 수 있습니다. Blob의 이름을 적절 하 게 지정 하면 읽기 요청의 효율성을 향상 시킬 수 있습니다.

Blob storage는 크기 조정 및 부하 분산을 위해 범위 기반 파티션 구성표를 사용 합니다. 각 blob에는 전체 blob 이름 (계정 + 컨테이너 + blob)으로 구성 된 파티션 키가 있습니다. 파티션 키는 blob 데이터를 범위로 분할 하는 데 사용 됩니다. 그러면 범위는 Blob storage에서 부하가 분산 됩니다.

범위 기반 분할은 어휘 순서 (예: *mypayroll*, *myperformance*, *myperformance*등) 또는 타임 스탬프 (*log20160101*, *log20160102*, log20160102)를 사용 하는 명명 규칙을 의미 합니다.등) 파티션이 같은 파티션 서버에 배치 될 가능성이 높습니다. 부하가 증가 될 때까지 더 작은 범위로 분할 되어야 합니다. 동일한 파티션 서버에 blob을 배치 하면 성능이 향상 되므로 성능 향상의 중요 한 부분은 blob의 이름을 가장 효과적으로 구성 하는 방식으로 blob의 이름을 지정 하는 것입니다.

예를 들어 컨테이너 내 모든 Blob는 이러한 Blob의 부하가 파티션 범위의 추가 리밸런싱을 요구하기 전까지 단일 서버가 지원할 수 있습니다. 마찬가지로, 이러한 계정 중 하나 또는 모두에 대 한 부하가 여러 파티션 서버 간에 분할 되어야 하는 경우에만 단일 서버에서 사전순으로 정렬 된 이름으로 적은 로드 된 계정 그룹을 제공할 수 있습니다.

각 부하 분산 작업은 작업 중 스토리지 호출의 대기 시간에 영향을 미칠 수 있습니다. 파티션에 대 한 대량 트래픽 버스트를 처리 하는 서비스는 부하 분산 작업이 시작 되 고 파티션 키 범위를 다시 분산 시킬 때까지 단일 파티션 서버의 확장성에 의해 제한 됩니다.

이러한 작업의 빈도를 줄이려면 몇 가지 모범 사례를 따르십시오.  

- 가능 하면 standard storage 계정에는 4gb 보다 큰 blob 또는 블록 크기를 사용 하 고 premium storage 계정에는 256 KiB 이상 사용 합니다. Blob 또는 블록 크기가 크면 처리량이 높은 블록 blob이 자동으로 활성화 됩니다. 처리량이 높은 블록 blob은 파티션 명명의 영향을 받지 않는 고성능 수집을 제공 합니다.
- 계정, 컨테이너, blob, 테이블 및 큐에 사용할 명명 규칙을 검사 합니다. 사용자의 요구에 가장 적합 한 해싱 함수를 사용 하 여 세 자리 해시를 사용 하 여 계정, 컨테이너 또는 blob 이름을 접두사로 사용 하는 것이 좋습니다.
- 타임 스탬프 또는 숫자 식별자를 사용 하 여 데이터를 구성 하는 경우에는 추가 전용 또는 앞 으로만 사용 되는 트래픽 패턴을 사용 하지 않아야 합니다. 이러한 패턴은 범위 기반 분할 시스템에 적합 하지 않습니다. 이러한 패턴은 모든 트래픽이 단일 파티션으로 이동 하 고 시스템을 효과적으로 부하 분산 하는 것을 제한 합니다.

    예를 들어 *yyyymmdd*와 같이 타임 스탬프를 사용 하 여 blob을 사용 하는 일상적인 작업이 있는 경우 해당 일별 작업의 모든 트래픽은 단일 파티션 서버에서 제공 하는 단일 blob으로 전달 됩니다. Blob 당 제한과 파티션당 한도가 요구 사항을 충족 하는지 여부를 고려 하 고 필요한 경우이 작업을 여러 blob으로 분리 하는 것이 좋습니다. 마찬가지로 테이블에 시계열 데이터를 저장 하는 경우 모든 트래픽이 키 네임 스페이스의 마지막 부분으로 전달 될 수 있습니다. 숫자 Id를 사용 하는 경우 3 자리 해시를 사용 하 여 ID에 접두사를 붙입니다. 타임 스탬프를 사용 하는 경우 타임 스탬프에 초 값 (예: *ssyyyymmdd*)을 접두사로 사용 합니다. 응용 프로그램에서 정기적으로 나열 및 쿼리 작업을 수행 하는 경우 쿼리 수를 제한 하는 해싱 함수를 선택 합니다. 경우에 따라 임의의 접두사 만으로도 충분할 수 있습니다.
  
- Azure Storage에서 사용 되는 파티션 구성표에 대 한 자세한 내용은 [Azure Storage를 참조 하세요. 강력한 일관성과 함께 항상 사용 가능한 클라우드 스토리지 서비스](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf) 문서를 참조하세요.

## <a name="networking"></a>네트워킹

응용 프로그램의 실제 네트워크 제약 조건은 성능에 상당한 영향을 미칠 수 있습니다. 다음 섹션에서는 사용자에 게 발생할 수 있는 몇 가지 제한 사항에 대해 설명 합니다.  

### <a name="client-network-capability"></a>클라이언트 네트워크 기능

네트워크 연결의 대역폭 및 품질은 다음 섹션에 설명 된 대로 응용 프로그램 성능에서 중요 한 역할을 합니다.

#### <a name="throughput"></a>처리량

대역폭의 경우에는 클라이언트 기능에 문제가 있는 경우가 많습니다. 대규모 Azure 인스턴스에는 용량이 더 많은 NIC가 포함되므로 머신 하나의 네트워크 한도를 높여야 하는 경우에는 더 큰 인스턴스나 더 많은 VM을 사용하는 것이 좋습니다. 온-프레미스 응용 프로그램에서 Azure Storage에 액세스 하는 경우에도 동일한 규칙이 적용 됩니다. 클라이언트 장치의 네트워크 기능 및 Azure Storage 위치에 대 한 네트워크 연결을 이해 하 고 필요한 경우이를 개선 하거나 디자인 응용 프로그램의 기능 내에서 작업 합니다.

#### <a name="link-quality"></a>링크 품질

네트워크를 사용 하는 경우와 마찬가지로 네트워크 조건으로 인해 오류가 발생 하 고 패킷 손실이 발생 하면 효과적인 처리량이 저하 됩니다.  WireShark 또는 NetMon을 사용하면 이 문제를 진단하는 데 도움이 될 수 있습니다.  

### <a name="location"></a>위치

모든 분산 환경에서는 클라이언트를 서버 근처에 배치하면 성능을 최대화할 수 있습니다. Azure Storage 액세스 시 대기 시간을 최소화하려는 경우에는 클라이언트를 같은 Azure 지역 내에 배치하는 것이 가장 좋습니다. 예를 들어 Azure Storage를 사용 하는 Azure 웹 앱이 있는 경우 미국 서 부 또는 동남 아시아와 같은 단일 지역 내에서 둘 모두를 찾습니다. 단일 지역 내에서 대역폭 사용량은 무료 이므로 리소스를 공동 배치 하면 대기 시간과 비용이 줄어듭니다.  

클라이언트 응용 프로그램이 모바일 장치 앱 또는 온-프레미스 엔터프라이즈 서비스와 같이 Azure 내에서 호스트 되지 않는 Azure Storage 액세스 하는 경우 해당 클라이언트 근처의 지역에서 저장소 계정을 찾으면 대기 시간을 줄일 수 있습니다. 클라이언트가 광범위 하 게 분산 된 경우 (예: 일부 북아메리카 및 유럽의 일부), 지역별 저장소 계정 하나를 사용 하는 것이 좋습니다. 애플리케이션에서 개별 사용자 관련 데이터를 저장하며 스토리지 계정 간에 데이터를 복제하지 않아도 되는 경우 이 방식을 보다 쉽게 구현할 수 있습니다.

Blob 콘텐츠를 광범위 하 게 배포 하려면 Azure CDN와 같은 콘텐츠 제공 네트워크를 사용 합니다. Azure CDN에 대한 자세한 내용은 [Azure CDN](../../cdn/cdn-overview.md)을 참조하세요.  

## <a name="sas-and-cors"></a>SAS 및 CORS

사용자의 웹 브라우저나 휴대폰 앱에서 실행 되는 JavaScript와 같은 코드에 Azure Storage의 데이터에 액세스할 수 있는 권한을 부여 해야 한다고 가정 합니다. 한 가지 방법은 프록시 역할을 하는 서비스 응용 프로그램을 빌드하는 것입니다. 사용자 장치는 서비스를 인증 하 고,이 서비스는 Azure Storage 리소스에 대 한 액세스 권한을 부여 합니다. 이러한 방식을 사용하면 안전하지 않은 디바이스에서 스토리지 계정 키가 노출되는 상황을 방지할 수 있습니다. 그러나 사용자의 장치와 Azure Storage 간에 전송 되는 모든 데이터는 서비스 응용 프로그램을 통과 해야 하기 때문에이 방법은 서비스 응용 프로그램에 상당한 오버 헤드를 발생 시킵니다.

SAS (공유 액세스 서명)를 사용 하 여 Azure Storage에 대 한 프록시로 서비스 응용 프로그램을 사용 하지 않을 수 있습니다. SAS를 사용 하 여 사용자의 장치에서 제한 된 액세스 토큰을 사용 하 여 Azure Storage에 직접 요청 하도록 설정할 수 있습니다. 예를 들어 사용자가 응용 프로그램에 사진을 업로드 하려는 경우 서비스 응용 프로그램은 SAS를 생성 하 고 사용자의 장치에 보낼 수 있습니다. Sas 토큰은 지정 된 시간 간격 동안 Azure Storage 리소스에 대 한 쓰기 권한을 부여할 수 있으며, 그 후에는 SAS 토큰이 만료 됩니다. SAS에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요.  

일반적으로 웹 브라우저는 특정 도메인의 웹 사이트에서 호스트 되는 페이지의 JavaScript를 다른 도메인에 대 한 쓰기 작업과 같은 특정 작업을 수행 하는 것을 허용 하지 않습니다. 동일한 원본 정책 이라고 하는이 정책은 한 페이지의 악성 스크립트가 다른 웹 페이지의 데이터에 액세스 하는 것을 방지 합니다. 그러나 클라우드에서 솔루션을 빌드할 때 동일한 원본 정책은 제한이 있을 수 있습니다. CORS (원본 간 리소스 공유)는 대상 도메인이 원본 도메인에서 발생 하는 요청을 신뢰 하는 브라우저와 통신할 수 있도록 하는 브라우저 기능입니다.

예를 들어 Azure에서 실행 중인 웹 응용 프로그램이 Azure Storage 계정에 리소스를 요청 하는 경우를 가정해 보겠습니다. 웹 응용 프로그램은 원본 도메인 이며 저장소 계정은 대상 도메인입니다. Azure Storage에서 원본 도메인의 요청을 신뢰 하는 웹 브라우저와 통신 하는 Azure Storage 서비스에 대해 CORS를 구성할 수 있습니다. CORS에 대 한 자세한 내용은 [Azure Storage에 대 한 cors (크로스-원본 자원 공유) 지원](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)을 참조 하세요.  
  
SAS와 CORS 모두 웹 응용 프로그램에서 불필요 한 로드를 방지 하는 데 도움이 될 수 있습니다.  

## <a name="caching"></a>캐싱

캐싱은 성능에서 중요 한 역할을 합니다. 다음 섹션에서는 캐싱 모범 사례에 대해 설명 합니다.

### <a name="reading-data"></a>데이터 읽기

일반적으로 데이터를 한 번 읽으면 데이터를 두 번 읽는 것이 좋습니다. Azure Storage에서 50 MiB blob을 검색 하 여 사용자에 게 콘텐츠를 제공 하는 웹 응용 프로그램의 예를 살펴보겠습니다. 응용 프로그램은 blob을 로컬로 디스크에 캐시 한 다음 이후 사용자 요청에 대 한 캐시 된 버전을 검색 하는 것이 가장 좋습니다.

Blob이 캐시 된 후 수정 되지 않은 경우 blob 검색을 방지 하는 한 가지 방법은 수정 시간에 대 한 조건부 헤더를 사용 하 여 GET 작업을 정규화 하는 것입니다. 마지막으로 수정한 시간이 blob이 캐시 된 시간 이후 이면 blob이 검색 되 고 다시 캐시 됩니다. 그렇지 않은 경우 최적의 성능을 위해 캐시 된 blob가 검색 됩니다.

응용 프로그램을 검색 한 후에는 짧은 기간 동안 변경 되지 않은 상태로 유지 되도록 응용 프로그램을 디자인할 수도 있습니다. 이 경우 응용 프로그램은 해당 간격 동안 blob이 수정 되었는지 여부를 확인할 필요가 없습니다.

응용 프로그램에서 자주 사용 하는 구성 데이터, 조회 데이터 및 기타 데이터는 캐싱이 적합 합니다.  

조건부 헤더를 사용 하는 방법에 대 한 자세한 내용은 [Blob service 작업에 대 한 조건부 헤더 지정](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)을 참조 하세요.  

### <a name="uploading-data-in-batches"></a>일괄 처리로 데이터 업로드

일부 시나리오에서는 데이터를 로컬로 집계 한 다음 데이터의 각 부분을 즉시 업로드 하는 대신 일괄 처리로 정기적으로 업로드할 수 있습니다. 예를 들어 웹 응용 프로그램에서 작업의 로그 파일을 유지 한다고 가정 합니다. 응용 프로그램은 테이블에 발생 하는 모든 작업 (많은 저장소 작업 필요)의 세부 정보를 업로드 하거나, 로컬 로그 파일에 작업 세부 정보를 저장 한 다음 모든 작업 세부 정보를 분리 된 파일로 blob에 정기적으로 업로드할 수 있습니다. 각 로그 항목의 크기가 1kb 이면 단일 트랜잭션으로 수천 개의 항목을 업로드할 수 있습니다. 단일 트랜잭션은 최대 64 MiB 크기의 blob 업로드를 지원 합니다. 응용 프로그램 개발자는 클라이언트 장치 또는 업로드 실패의 가능성을 디자인 해야 합니다. 활동 데이터를 단일 활동이 아닌 시간 간격으로 다운로드 해야 하는 경우 테이블 저장소 보다 Blob storage를 사용 하는 것이 좋습니다.

## <a name="net-configuration"></a>.NET 구성

이 섹션에서는 .NET Framework를 사용하는 경우 성능을 크게 개선하기 위해 사용할 수 있는 다양한 빠른 구성 설정을 소개합니다.  다른 언어를 사용하는 경우에는 선택한 언어에 비슷한 개념이 적용되는지를 확인하세요.  

### <a name="use-net-core"></a>.NET Core 사용

.NET Core 2.1 이상을 사용 하 여 Azure Storage 응용 프로그램을 개발 하 여 성능 향상을 활용 하세요. 가능 하면 .NET Core 3.x를 사용 하는 것이 좋습니다.

.NET Core의 성능 향상에 대 한 자세한 내용은 다음 블로그 게시물을 참조 하세요.

- [.NET Core 3.0의 성능 향상](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [.NET Core 2.1의 성능 향상](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>기본 연결 제한 늘리기

.NET에서 다음 코드는 기본 연결 제한 (일반적으로 클라이언트 환경에서 2 개 또는 서버 환경의 10 개)을 100로 늘립니다. 일반적으로 이 값을 애플리케이션에서 사용되는 대략적인 스레드 수로 설정해야 합니다. 연결을 열기 전에 연결 제한을 설정 합니다.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

다른 프로그래밍 언어의 경우 연결 제한을 설정 하는 방법을 확인 하려면 설명서를 참조 하세요.  

자세한 내용은 [웹 서비스: 동시 연결](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/) 블로그 게시물을 참조하세요.  

### <a name="increase-minimum-number-of-threads"></a>최소 스레드 수 늘리기

비동기 작업과 함께 동기 호출을 사용 하는 경우 스레드 풀의 스레드 수를 늘려야 할 수 있습니다.

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

자세한 내용은 [ThreadPool. SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) 메서드를 참조 하세요.  

## <a name="unbounded-parallelism"></a>바인딩되지 않은 병렬 처리

병렬 처리는 성능에 매우 유용할 수 있지만, 제한 없는 병렬 처리를 사용 하는 경우에는 스레드 수 또는 병렬 요청에 대해 적용 되는 제한이 없습니다. 데이터를 업로드 또는 다운로드 하거나, 동일한 저장소 계정의 여러 파티션에 액세스 하거나, 동일한 파티션의 여러 항목에 액세스 하려면 병렬 요청을 제한 해야 합니다. 병렬 처리를 사용할 수 없는 경우 응용 프로그램에서 클라이언트 장치의 기능 또는 저장소 계정의 확장성 목표를 초과 하 여 대기 시간이 길어지고 제한이 길어질 수 있습니다.  

## <a name="client-libraries-and-tools"></a>클라이언트 라이브러리 및 도구

최상의 성능을 위해 항상 Microsoft에서 제공 하는 최신 클라이언트 라이브러리 및 도구를 사용 합니다. Azure Storage 클라이언트 라이브러리는 다양 한 언어로 사용할 수 있습니다. Azure Storage은 PowerShell 및 Azure CLI도 지원 합니다. Microsoft는 이러한 클라이언트 라이브러리와 도구를 성능에 맞게 적극적으로 개발 하 고 최신 서비스 버전으로 최신 상태를 유지 하며,이를 통해 내부적으로 검증 된 많은 성능 사례를 처리 합니다. 자세한 내용은 [Azure Storage 참조 설명서](/azure/storage/#reference)를 참조 하세요.

## <a name="handle-service-errors"></a>처리 서비스 오류

서비스에서 요청을 처리할 수 없는 경우 Azure Storage에서 오류를 반환 합니다. 지정 된 시나리오에서 Azure Storage에서 반환 될 수 있는 오류를 이해 하면 성능을 최적화 하는 데 도움이 됩니다.

### <a name="timeout-and-server-busy-errors"></a>시간 제한 및 서버 사용 중 오류

확장성 한도에 도달 하는 경우 응용 프로그램을 제한할 수 Azure Storage. 일부 경우에는 일시적인 조건으로 인해 요청을 처리 하지 못할 수 Azure Storage. 두 경우 모두 서비스는 503 (서버 사용 중) 또는 500 (시간 제한) 오류를 반환할 수 있습니다. 이러한 오류는 서비스에서 더 높은 처리량을 위해 데이터 파티션을 균형 있게 분산 하는 경우에도 발생할 수 있습니다. 클라이언트 응용 프로그램은 일반적으로 이러한 오류 중 하나를 발생 시키는 작업을 다시 시도 해야 합니다. 그러나 Azure Storage에서 확장성 목표를 초과 하 여 응용 프로그램을 제한 하는 경우 또는 서비스에서 다른 이유로 요청을 처리할 수 없는 경우에도 적극적인 재시도로 인해 문제가 악화 될 수 있습니다. 지 수 백오프 다시 시도 정책을 사용 하는 것이 좋지만 클라이언트 라이브러리는이 동작을 기본적으로 사용 합니다. 예를 들어 애플리케이션은 작업을 2초, 4초, 10초, 30초 후에 다시 시도한 다음, 계속 실패하면 작업을 완전히 포기할 수 있습니다. 이러한 방식으로 응용 프로그램은 조정으로 이어질 수 있는 동작을 악화 하는 대신 서비스에 대 한 부하를 크게 줄입니다.  

연결 오류는 제한으로 인해 발생하는 것이 아니라 일시적이므로 즉시 다시 시도할 수 있습니다.  

### <a name="non-retryable-errors"></a>다시 시도할 때 발생 하지 않는 오류

클라이언트 라이브러리는 다시 시도할 수 있는 오류 및 수행할 수 없는 오류를 인식 하 여 재시도를 처리 합니다. 그러나 Azure Storage REST API 직접 호출 하는 경우에는 몇 가지 오류가 발생 합니다. 예를 들어 400 (잘못 된 요청) 오류는 클라이언트 응용 프로그램이 예상한 형식이 아니기 때문에 처리할 수 없는 요청을 보낸 것을 나타냅니다. 이 요청을 다시 보내면 매번 동일한 응답이 발생 하므로 다시 시도 하는 시점이 없습니다. Azure Storage REST API을 직접 호출 하는 경우 잠재적인 오류 및 재시도 해야 하는지 여부를 알고 있어야 합니다.

Azure Storage 오류 코드에 대 한 자세한 내용은 [상태 및 오류 코드](/rest/api/storageservices/status-and-error-codes2)를 참조 하세요.

## <a name="copying-and-moving-blobs"></a>Blob 복사 및 이동

Azure Storage는 저장소 계정, 저장소 계정 간, 온-프레미스 시스템 및 클라우드 간에 blob을 복사 하 고 이동 하는 여러 가지 솔루션을 제공 합니다. 이 섹션에서는 성능에 미치는 영향을 기준으로 이러한 옵션 중 일부에 대해 설명 합니다. Blob 저장소로 또는 Blob 저장소에서 데이터를 효율적으로 전송 하는 방법에 대 한 자세한 내용은 [데이터 전송을 위한 Azure 솔루션 선택](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조 하세요.

### <a name="blob-copy-apis"></a>Blob 복사 Api

저장소 계정 간에 blob을 복사 하려면 [URL에서 블록 배치](/rest/api/storageservices/put-block-from-url) 작업을 사용 합니다. 이 작업을 수행 하면 모든 URL 원본에서 블록 blob으로 데이터를 동기적으로 복사 합니다. @No__t-0 연산을 사용 하면 저장소 계정 간에 데이터를 마이그레이션할 때 필요한 대역폭을 상당히 줄일 수 있습니다. 복사 작업은 서비스 쪽에서 수행 되기 때문에 데이터를 다운로드 하 고 다시 업로드할 필요가 없습니다.

동일한 저장소 계정 내에서 데이터를 복사 하려면 [Blob 복사](/rest/api/storageservices/Copy-Blob) 작업을 사용 합니다. 일반적으로 동일한 저장소 계정 내에서 데이터를 복사 하는 것이 신속 하 게 완료 됩니다.  

### <a name="use-azcopy"></a>AzCopy 사용

AzCopy 명령줄 유틸리티는 저장소 계정 간에 blob을 대량으로 대량 전송할 수 있는 간단 하 고 효율적인 옵션입니다. AzCopy는이 시나리오에 맞게 최적화 되었으며 높은 전송 속도를 달성할 수 있습니다. AzCopy 버전 10은 `Put Block From URL` 연산을 사용 하 여 저장소 계정 간에 blob 데이터를 복사 합니다. 자세한 내용은 [AzCopy v10를 사용 하 여 Azure Storage에 데이터 복사 또는 이동](/azure/storage/common/storage-use-azcopy-v10)을 참조 하세요.  

### <a name="use-azure-data-box"></a>Azure Data Box 사용

대용량의 데이터를 Blob 저장소로 가져오려면 오프 라인 전송에 Azure Data Box 패밀리를 사용 하는 것이 좋습니다. Microsoft에서 제공 하는 Data Box 장치는 시간, 네트워크 가용성 또는 비용을 기준으로 제한 될 때 Azure로 많은 양의 데이터를 이동 하는 데 적합 합니다. 자세한 내용은 [Azure DataBox 설명서](/azure/databox/)를 참조 하세요.

## <a name="content-distribution"></a>콘텐츠 배포

경우에 따라 응용 프로그램은 동일한 또는 여러 지역에 있는 여러 사용자 (예: 웹 사이트의 홈 페이지에서 사용 되는 제품 데모 비디오)에 동일한 콘텐츠를 제공 해야 합니다. 이 시나리오에서는 Azure CDN와 같은 CDN (Content Delivery Network)을 사용 하 여 blob 콘텐츠를 지리적으로 분산 합니다. 단일 하위 지역에 있으며 짧은 대기 시간 내에 다른 하위 지역으로 콘텐츠를 배달할 수 없는 Azure Storage 계정과는 달리 Azure CDN은 전 세계 여러 데이터 센터의 서버를 사용합니다. 또한 CDN은 일반적으로 단일 스토리지 계정보다 더 높은 송신 제한을 지원합니다.  

Azure CDN에 대한 자세한 내용은 [Azure CDN](../../cdn/cdn-overview.md)을 참조하세요.

## <a name="use-metadata"></a>메타 데이터 사용

Blob service는 Blob 속성 또는 메타 데이터를 포함할 수 있는 HEAD 요청을 지원 합니다. 예를 들어 응용 프로그램에 사진에서 Exif (exchangable image format) 데이터가 필요한 경우 사진을 검색 하 고 압축을 풀 수 있습니다. 응용 프로그램은 대역폭을 절약 하 고 성능을 향상 시키기 위해 응용 프로그램에서 사진을 업로드할 때 응용 프로그램에서 Exif 데이터를 blob의 메타 데이터에 저장할 수 있습니다. 그런 다음 HEAD 요청만 사용 하 여 메타 데이터에서 Exif 데이터를 검색할 수 있습니다. 메타 데이터만 검색 하 고 blob의 전체 콘텐츠가 아닌 경우에는 상당한 대역폭이 절약 되며 Exif 데이터를 추출 하는 데 필요한 처리 시간이 줄어듭니다. KiB 8 개의 메타 데이터는 blob 당 저장할 수 있습니다.  

## <a name="upload-blobs-quickly"></a>신속 하 게 blob 업로드

Blob을 신속 하 게 업로드 하려면 먼저 하나의 blob을 업로드할 지 여부를 결정 합니다. 아래 지침을 참고하여 시나리오에 따라 사용할 적절한 방법을 결정합니다.  

### <a name="upload-one-large-blob-quickly"></a>신속 하 게 하나의 large blob 업로드

단일 blob을 신속 하 게 업로드 하기 위해 클라이언트 응용 프로그램은 블록 또는 페이지를 병렬로 업로드할 수 있으며, 개별 blob 및 저장소 계정에 대 한 확장성 목표를 염두에 둘 수 있습니다. Azure Storage 클라이언트 라이브러리는 병렬로 업로드를 지원 합니다. 예를 들어, 다음 속성을 사용 하 여 .NET 또는 Java에서 허용 되는 동시 요청 수를 지정할 수 있습니다. 지원 되는 다른 언어의 클라이언트 라이브러리는 비슷한 옵션을 제공 합니다.

- .NET의 경우 [Blobrequestoptions. 대해 paralleloperationthreadcount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) 속성을 설정 합니다.
- Java/Android의 경우 [Blobrequestoptions (Final Integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob._blob_request_options.setconcurrentrequestcount) 메서드를 호출 합니다.

### <a name="upload-many-blobs-quickly"></a>많은 blob을 신속 하 게 업로드

Blob 여러 개를 빠르게 업로드하려면 Blob을 병렬로 업로드합니다. 병렬 업로드는 저장소 서비스의 여러 파티션에 업로드를 분산 하므로 병렬 블록 업로드를 사용 하 여 한 번에 단일 blob을 업로드 하는 것 보다 빠릅니다. AzCopy는 기본적으로 동시에 업로드를 수행 하며이 시나리오에 권장 됩니다. 자세한 내용은 [AzCopy 시작](../common/storage-use-azcopy-v10.md)을 참조 하세요.  

## <a name="choose-the-correct-type-of-blob"></a>올바른 blob 유형 선택

Azure Storage는 블록 blob, 추가 blob 및 페이지 blob을 지원 합니다. 지정된 사용 시나리오에 대해 선택하는 Blob 유형은 솔루션의 성능과 확장성에 영향을 줍니다.

블록 blob은 많은 양의 데이터를 효율적으로 업로드 하려는 경우에 적합 합니다. 예를 들어 사진 또는 비디오를 Blob 저장소에 업로드 하는 클라이언트 응용 프로그램은 블록 blob을 대상으로 합니다.

추가 blob은 블록으로 구성 된다는 점에서 블록 blob과 비슷합니다. 추가 blob을 수정 하면 블록은 blob의 끝에만 추가 됩니다. 추가 blob은 응용 프로그램에서 기존 blob에 데이터를 추가 해야 하는 경우 로깅 등의 시나리오에 유용 합니다.

페이지 blob는 응용 프로그램에서 데이터에 대 한 임의 쓰기를 수행 해야 하는 경우에 적합 합니다. 예를 들어 Azure 가상 머신 디스크는 페이지 blob으로 저장 됩니다. 자세한 내용은 [블록 blob, 추가 blob 및 페이지 Blob 이해](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)를 참조 하세요.  

## <a name="next-steps"></a>다음 단계

- [스토리지 계정의 Azure Storage 확장성 및 성능 목표](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [큐 저장소에 대 한 성능 및 확장성 검사 목록](../queues/storage-performance-checklist.md)
- [테이블 저장소에 대 한 성능 및 확장성 검사 목록](../tables/storage-performance-checklist.md)
- [상태 및 오류 코드](/rest/api/storageservices/Status-and-Error-Codes2)
