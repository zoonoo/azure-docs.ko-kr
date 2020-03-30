---
title: Blob 저장소에 대한 성능 및 확장성 검사 목록 - Azure 저장소
description: 고성능 응용 프로그램을 개발할 때 Blob 스토리지와 함께 사용하기 위한 입증된 사례의 체크리스트입니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e4103f8360f6fa80470b0f8002a61f8ac903bd8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255432"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Blob 스토리지에 대한 성능 및 확장성 검사 목록

Microsoft는 Blob 저장소를 통해 고성능 응용 프로그램을 개발하기 위한 여러 가지 입증된 방법을 개발했습니다. 이 검사 목록에서는 개발자가 성능을 최적화하기 위해 수행할 수 있는 주요 사례를 식별합니다. 애플리케이션을 설계하는 동안 프로세스 전체에서 이러한 사례를 명심하세요.

Azure Storage에는 용량, 트랜잭션 속도 및 대역폭에 대한 확장성 및 성능 목표가 있습니다. Azure Storage 확장성 목표에 대한 자세한 내용은 [표준 저장소 계정에 대한 확장성 및 성능 목표와](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) [Blob 저장소의 확장성 및 성능 목표를](scalability-targets.md)참조하십시오.

## <a name="checklist"></a>검사 목록

이 문서에서는 Blob 저장소 응용 프로그램을 개발하는 동안 수행할 수 있는 검사 목록으로 성능에 대한 입증된 방법을 정리합니다.

| 완료된 | Category | 디자인 고려 사항 |
| --- | --- | --- |
| &nbsp; |확장성 목표 |[애플리케이션에서 최대 스토리지 계정 수를 초과하여 사용하지 않도록 디자인할 수 있습니까?](#maximum-number-of-storage-accounts) |
| &nbsp; |확장성 목표 |[용량 및 트랜잭션 제한에 도달하지 않도록 방지하고 있습니까?](#capacity-and-transaction-targets) |
| &nbsp; |확장성 목표 |[많은 수의 클라이언트가 단일 Blob에 동시에 액세스하고 있습니까?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |확장성 목표 |[응용 프로그램이 단일 Blob에 대한 확장성 목표 내에 머물러 있습니까?](#bandwidth-and-operations-per-blob) |
| &nbsp; |분할 |[명명 규칙이 부하 분산 향상에 맞게 설계되었습니까?](#partitioning) |
| &nbsp; |네트워킹 |[클라이언트 쪽 디바이스에 필요한 성능을 달성할 수 있을 만큼 충분한 높은 대역폭과 짧은 대기 시간이 있습니까?](#throughput) |
| &nbsp; |네트워킹 |[클라이언트 쪽 디바이스에 고품질 네트워크 링크가 있습니까?](#link-quality) |
| &nbsp; |네트워킹 |[클라이언트 애플리케이션이 스토리지 계정과 동일한 지역에 있습니까?](#location) |
| &nbsp; |직접 클라이언트 액세스 |[SAS(공유 액세스 서명)와 CORS(원본 간 리소스 공유)를 사용하여 Azure Storage에 직접 액세스할 수 있습니까?](#sas-and-cors) |
| &nbsp; |캐싱 |[자주 액세스하고 거의 변경되지 않는 응용 프로그램 캐싱 데이터입니까?](#reading-data) |
| &nbsp; |캐싱 |[응용 프로그램이 클라이언트에서 업데이트를 캐싱한 다음 더 큰 집합으로 업로드하여 업데이트를 일괄 처리합니까?](#uploading-data-in-batches) |
| &nbsp; |.NET 구성 |[최적의 성능을 달성하기 위해 .NET Core 2.1 이상을 사용하고 있습니까?](#use-net-core) |
| &nbsp; |.NET 구성 |[클라이언트가 충분한 수의 동시 연결을 사용하도록 구성했습니까?](#increase-default-connection-limit) |
| &nbsp; |.NET 구성 |[.NET 애플리케이션의 경우 .NET에서 충분한 수의 스레드를 사용하도록 구성했습니까?](#increase-minimum-number-of-threads) |
| &nbsp; |병렬 처리 |[고객의 기능이 오버로드되지 않거나 확장성 목표에 도달하지 않도록 병렬 처리가 적절히 제한되도록 했습니까?](#unbounded-parallelism) |
| &nbsp; |도구 |[Microsoft에서 제공하는 클라이언트 라이브러리와 도구의 최신 버전을 사용하고 있습니까?](#client-libraries-and-tools) |
| &nbsp; |다시 시도 |[제한 오류 및 시간 제한에 대한 지수 백오프가 포함된 재시도 정책을 사용하고 있습니까?](#timeout-and-server-busy-errors) |
| &nbsp; |다시 시도 |[애플리케이션에서 다시 시도할 수 없는 오류 발생 시에는 작업을 다시 시도하지 않습니까?](#non-retryable-errors) |
| &nbsp; |Blob 복사 |[가장 효율적인 방법으로 Blob을 복사하고 있습니까?](#blob-copy-apis) |
| &nbsp; |Blob 복사 |[대량 복사 작업에 최신 버전의 AzCopy를 사용하고 있습니까?](#use-azcopy) |
| &nbsp; |Blob 복사 |[대량의 데이터를 가져오기 위해 Azure 데이터 박스 제품군을 사용하고 있습니까?](#use-azure-data-box) |
| &nbsp; |콘텐츠 배포 |[콘텐츠 배포를 위해 CDN을 사용합니까?](#content-distribution) |
| &nbsp; |메타데이터 사용 |[자주 사용되는 Blob 관련 메타데이터를 해당 메타데이터에 저장하고 있습니까?](#use-metadata) |
| &nbsp; |빠르게 업로드 |[Blob 하나를 빠르게 업로드하려는 경우 블록을 병렬로 업로드합니까?](#upload-one-large-blob-quickly) |
| &nbsp; |빠르게 업로드 |[Blob 여러 개를 빠르게 업로드하려는 경우 Blob를 병렬로 업로드합니까?](#upload-many-blobs-quickly) |
| &nbsp; |Blob 유형 |[해당하는 경우 페이지 Blob 또는 블록 Blob를 사용합니까?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>확장성 목표

애플리케이션이 확장성 목표에 도달하거나 목표를 초과하는 경우 트랜잭션 대기 시간이 길어지거나 제한이 증가할 수 있습니다. Azure Storage에서 애플리케이션을 제한하면 서비스에서 503(서버 작업 중) 또는 500(작업 시간 제한) 오류 코드를 반환하기 시작합니다. 확장성 목표의 한도 내에서 유지하여 이러한 오류를 방지하는 것은 애플리케이션의 성능을 향상시키는 데 중요한 부분입니다.

큐 서비스의 확장성 목표에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage)를 참조하세요.

### <a name="maximum-number-of-storage-accounts"></a>최대 스토리지 계정 수

특정 구독/지역 조합에 허용되는 최대 저장소 계정 수에 접근하는 경우 시나리오를 평가하고 다음 조건 중 어느 것이 적용되는지 여부를 결정합니다.

- 저장소 계정을 사용하여 관리되지 않는 디스크를 저장하고 해당 디스크를 가상 컴퓨터(VM)에 추가하고 있습니까? 이 시나리오에서는 관리되는 디스크를 사용하는 것이 좋습니다. 관리되는 디스크는 개별 저장소 계정을 만들고 관리할 필요 없이 자동으로 확장됩니다. 자세한 내용은 [Azure 관리 디스크 소개를 참조하십시오.](../../virtual-machines/windows/managed-disks-overview.md)
- 데이터 격리를 위해 고객당 하나의 저장소 계정을 사용하고 있습니까? 이 시나리오에서는 전체 저장소 계정 대신 각 고객에 대 한 Blob 컨테이너를 사용 하는 것이 좋습니다. 이제 Azure Storage를 사용하면 컨테이너별로 역할 기반 액세스 제어(RBAC) 역할을 할당할 수 있습니다. 자세한 내용은 [Azure Portal에서 RBAC로 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](../common/storage-auth-aad-rbac-portal.md)를 참조하세요.
- 여러 스토리지 계정을 사용하여 침투, 송신, 초당 I/O 작업(IOPS) 또는 용량을 늘리기 위해 샤드를 사용하고 있습니까? 이 시나리오에서는 가능한 경우 워크로드에 필요한 스토리지 계정 수를 줄이기 위해 스토리지 계정에 대한 제한을 늘리는 것이 좋습니다. 스토리지 계정에 대한 제한을 늘리도록 요청하려면 [Azure 지원](https://azure.microsoft.com/support/options/)에 문의하세요. 자세한 내용은 [더 크고 더 높은 규모의 스토리지 계정 발표](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)를 참조하세요.

### <a name="capacity-and-transaction-targets"></a>용량 및 트랜잭션 목표

애플리케이션이 특정 스토리지 계정의 확장성 목표에 도달한 경우 다음 방법 중 하나를 사용할 수 있습니다.  

- 응용 프로그램이 트랜잭션 대상에 도달하면 높은 트랜잭션 속도와 낮고 일관된 대기 시간에 최적화된 블록 Blob 저장소 계정을 사용하는 것이 좋습니다. 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.
- 애플리케이션이 확장성 목표에 도달하거나 목표를 초과한 원인이 되는 워크로드를 다시 고려합니다. 즉, 대역폭이나 용량을 더 적게 사용하거나 트랜잭션을 더 적게 수행하도록 해당 작업을 다시 디자인할 수 있는지를 파악합니다.
- 애플리케이션에서 확장성 목표 중 하나를 초과해야 하는 경우 여러 스토리지 계정을 만들어 애플리케이션 데이터를 해당 스토리지 계정에 분할합니다. 이러한 패턴을 사용하는 경우에는 향후 부하 분산을 위해 스토리지 계정을 더 추가할 수 있도록 애플리케이션을 디자인해야 합니다. 스토리지 계정 자체에는 저장된 데이터, 수행된 트랜잭션 또는 전송된 데이터와 관련된 사용량 이외의 비용은 없습니다.
- 애플리케이션의 대역폭 목표에 도달하는 경우 클라이언트 쪽의 데이터를 압축하여 Azure Storage에 보내는 데 필요한 대역폭을 줄이는 것이 좋습니다.
    데이터를 압축하면 대역폭이 절약되고 네트워크 성능이 향상될 수 있지만 성능에 부정적인 영향을 미칠 수도 있습니다. 클라이언트 쪽의 데이터 압축 및 압축 풀기에 대한 추가 처리 요구 사항으로 인해 성능에 미치는 영향을 평가합니다. 압축된 데이터를 저장하면 표준 도구를 사용하여 데이터를 보는 것이 더 어려울 수 있으므로 문제 해결이 더 어려워 질 수 있습니다.
- 애플리케이션에서 확장성 목표에 도달하는 경우 재시도에 대해 지수 백오프를 사용하고 있는지 확인합니다. 이 문서에서 설명하는 추천 사항을 구현하여 확장성 목표에 도달하지 않도록 하는 것이 가장 좋습니다. 그러나 지수 백오프를 재시도에 사용하면 애플리케이션에서 빠르게 다시 시도하지 못하므로 제한 성능이 저하될 수 있습니다. 자세한 내용은 [시간 제한 및 서버 작업 중 오류](#timeout-and-server-busy-errors) 섹션을 참조하세요.

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>단일 Blob에 동시에 액세스하는 여러 클라이언트

단일 Blob에 동시에 액세스하는 클라이언트가 많은 경우 Blob및 저장소당 계정 확장성 목표를 모두 고려해야 합니다. 단일 Blob에 액세스할 수 있는 클라이언트의 정확한 수는 Blob을 동시에 요청하는 클라이언트 수, Blob 크기 및 네트워크 조건과 같은 요인에 따라 달라집니다.

웹 사이트에서 제공되는 이미지 나 비디오와 같은 CDN을 통해 Blob을 배포 할 수 있는 경우 CDN을 사용할 수 있습니다. 자세한 내용은 [콘텐츠 배포](#content-distribution)섹션입니다.

데이터가 기밀인 과학 시뮬레이션과 같은 다른 시나리오에서는 두 가지 옵션이 있습니다. 첫 번째는 Blob이 시간 동안 액세스하고 동시에 액세스되도록 워크로드의 액세스를 엇갈리게 하는 것입니다. 또는 Blob을 여러 저장소 계정에 일시적으로 복사하여 Blob당 및 저장소 계정 전체의 총 IOPS를 늘릴 수 있습니다. 결과는 응용 프로그램의 동작에 따라 달라지므로 디자인 중에 동시성 패턴을 테스트해야 합니다.

### <a name="bandwidth-and-operations-per-blob"></a>Blob당 대역폭 및 작업

단일 Blob은 초당 최대 500개의 요청을 지원합니다. 동일한 Blob을 읽어야 하는 클라이언트가 여러 개 있고 이 제한을 초과할 수 있는 경우 블록 Blob 저장소 계정을 사용하는 것이 좋습니다. 블록 Blob 저장소 계정은 더 높은 요청 속도 또는 초당 I/O 작업(IOPS)을 제공합니다.

Azure CDN과 같은 CDN(콘텐츠 전송 네트워크)을 사용하여 Blob에 작업을 배포할 수도 있습니다. Azure CDN에 대한 자세한 내용은 [Azure CDN 개요를](../../cdn/cdn-overview.md)참조하십시오.  

## <a name="partitioning"></a>분할

Azure Storage가 Blob 데이터를 분할하는 방법을 이해하는 것은 성능 향상에 유용합니다. Azure Storage는 여러 파티션에 걸쳐 있는 데이터보다 단일 파티션에서 데이터를 더 빠르게 제공할 수 있습니다. Blob의 이름을 적절하게 지정하면 읽기 요청의 효율성을 향상시킬 수 있습니다.

Blob 저장소는 크기 조정 및 부하 분산을 위해 범위 기반 분할 체계를 사용합니다. 각 Blob에는 전체 Blob 이름(계정+컨테이너+Blob)으로 구성된 파티션 키가 있습니다. 파티션 키는 Blob 데이터를 범위로 분할하는 데 사용됩니다. 그런 다음 범위는 Blob 저장소 전체에서 부하 균형을 맞습니다.

범위 기반 분할은 어휘 순서를 사용하는 명명 규칙(예: *mypayroll,* *myperformance,* *myemployees*등) 또는 타임스탬프(log20160101, *log20160102,* *log20160102*등)가 동일한 파티션 서버에 공동 으로 배치될 가능성이 더 높다는 것을 의미합니다.*log20160101* 증가된 부하가 더 작은 범위로 분할될 때까지. 동일한 파티션 서버에서 Blob을 공동 배치하면 성능이 향상되므로 성능 향상의 중요한 부분에는 Blob을 가장 효과적으로 구성하는 방식으로 이름을 지정해야 합니다.

예를 들어 컨테이너 내 모든 Blob는 이러한 Blob의 부하가 파티션 범위의 추가 리밸런싱을 요구하기 전까지 단일 서버가 지원할 수 있습니다. 마찬가지로, 이름이 어휘 순서로 정렬된 가볍게 로드된 계정 그룹은 이러한 계정 중 하나 또는 모두에 로드될 때까지 단일 서버에서 여러 파티션 서버로 분할해야 할 때까지 제공될 수 있습니다.

각 부하 분산 작업은 작업 중 스토리지 호출의 대기 시간에 영향을 미칠 수 있습니다. 파티션에 대한 갑작스런 트래픽 버스트를 처리하는 서비스의 기능은 로드 밸런싱 작업이 시작되고 파티션 키 범위를 재조정할 때까지 단일 파티션 서버의 확장성에 의해 제한됩니다.

이러한 작업의 빈도를 줄이려면 몇 가지 모범 사례를 따르십시오.  

- 가능하면 표준 저장소 계정에는 4MiB보다 큰 Blob 또는 블록 크기를 사용하고 프리미엄 저장소 계정에는 256KiB를 초과합니다. Blob 또는 블록 크기가 클수록 처리량이 높은 블록 Blob이 자동으로 활성화됩니다. 처리량이 많은 블록 Blob은 파티션 이름 지정의 영향을 받지 않는 고성능 인제스트를 제공합니다.
- 거래처, 컨테이너, Blob, 테이블 및 큐에 사용하는 명명 규칙을 검사합니다. 필요에 가장 적합한 해시 함수를 사용하여 계정, 컨테이너 또는 Blob 이름을 세 자리 해시로 접두사하는 것이 좋습니다.
- 타임스탬프 또는 숫자 식별자를 사용하여 데이터를 구성하는 경우 추가 전용(또는 프레벤드 전용) 트래픽 패턴을 사용하지 않는지 확인합니다. 이러한 패턴은 범위 기반 분할 시스템에 적합하지 않습니다. 이러한 패턴으로 인해 모든 트래픽이 단일 파티션으로 이동하여 시스템이 효과적으로 로드 밸런싱되지 않도록 제한할 수 있습니다.

    예를 들어 *yyymmdd와*같은 타임스탬프가 있는 Blob을 사용하는 일별 작업이 있는 경우 해당 일별 작업에 대한 모든 트래픽은 단일 파티션 서버에서 제공하는 단일 Blob으로 이동됩니다. Blob당 제한 및 파티션당 제한이 요구 사항을 충족하는지 고려하고 필요한 경우 이 작업을 여러 Blob로 나누는 것이 좋습니다. 마찬가지로 테이블에 열렬 데이터를 저장하는 경우 모든 트래픽이 키 네임스페이스의 마지막 부분으로 이동될 수 있습니다. 숫자 ID를 사용하는 경우 ID를 세 자리 해시로 접두사로 지정합니다. 타임스탬프를 사용하는 경우 *ssyyymmdd와*같은 초 값으로 타임스탬프를 접두사합니다. 응용 프로그램이 정기적으로 목록 및 쿼리 작업을 수행하는 경우 쿼리 수를 제한하는 해시 함수를 선택합니다. 경우에 따라 임의 접두사로 충분할 수 있습니다.
  
- Azure 저장소에 사용되는 분할 구성표에 대한 자세한 내용은 [Azure 저장소: 강력한 일관성을 갖춘 가용성이 높은 클라우드 저장소 서비스를](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)참조하십시오.

## <a name="networking"></a>네트워킹

애플리케이션의 실제 네트워크 제약 조건은 성능에 큰 영향을 미칠 수 있습니다. 다음 섹션에서는 사용자에게 발생할 수 있는 몇 가지 제한 사항에 대해 설명합니다.  

### <a name="client-network-capability"></a>클라이언트 네트워크 기능

다음 섹션에서 설명한 대로 네트워크 링크의 대역폭과 품질은 애플리케이션 성능에서 중요한 역할을 합니다.

#### <a name="throughput"></a>처리량

대역폭의 경우에는 클라이언트 기능에 문제가 있는 경우가 많습니다. 대규모 Azure 인스턴스에는 용량이 더 많은 NIC가 포함되므로 머신 하나의 네트워크 한도를 높여야 하는 경우에는 더 큰 인스턴스나 더 많은 VM을 사용하는 것이 좋습니다. 온-프레미스 애플리케이션에서 Azure Storage에 액세스하는 경우에도 동일한 규칙이 적용됩니다. 즉, 클라이언트 디바이스의 네트워크 기능과 Azure Storage 위치에 대한 네트워크 연결을 파악하고, 필요한 경우 이를 향상시키거나 해당 기능 내에서 작동하도록 애플리케이션을 디자인해야 합니다.

#### <a name="link-quality"></a>링크 품질

네트워크 사용량과 마찬가지로 네트워크 조건으로 인해 오류와 패킷 손실이 발생하면 효과적인 처리량의 속도가 느려집니다.  WireShark 또는 NetMon을 사용하면 이 문제를 진단하는 데 도움이 될 수 있습니다.  

### <a name="location"></a>위치

모든 분산 환경에서는 클라이언트를 서버 근처에 배치하면 성능을 최대화할 수 있습니다. Azure Storage 액세스 시 대기 시간을 최소화하려는 경우에는 클라이언트를 같은 Azure 지역 내에 배치하는 것이 가장 좋습니다. 예를 들어 Azure 웹앱에서 Azure Storage를 사용하는 경우 웹앱과 스토리지를 모두 단일 하위 지역(예: 미국 서부 또는 동남 아시아) 내에 배치합니다. 리소스를 공동 배치하면 단일 지역 내의 대역폭 사용량이 무료이므로 대기 시간과 비용이 줄어듭니다.  

모바일 디바이스 앱 또는 온-프레미스 엔터프라이즈 서비스와 같이 클라이언트 애플리케이션이 Azure Storage에 액세스하지만 Azure 내에서 호스팅되지 않는 경우 스토리지 계정을 해당 클라이언트와 가까운 지역에 배치하면 대기 시간을 줄일 수 있습니다. 고객이 광범위하게 분산되어 있는 경우(예: 북아메리카 및 유럽 일부) 지역당 하나의 스토리지 계정을 사용하는 것이 좋습니다. 애플리케이션에서 개별 사용자 관련 데이터를 저장하며 스토리지 계정 간에 데이터를 복제하지 않아도 되는 경우 이 방식을 보다 쉽게 구현할 수 있습니다.

Blob 콘텐츠의 광범위한 배포를 위해 Azure CDN과 같은 콘텐츠 배달 네트워크를 사용합니다. Azure CDN에 대한 자세한 내용은 [Azure CDN](../../cdn/cdn-overview.md)을 참조하세요.  

## <a name="sas-and-cors"></a>SAS 및 CORS

Azure Storage의 데이터에 액세스할 수 있는 권한을 코드(예: 사용자의 웹 브라우저 또는 휴대폰 앱에서 실행되는 JavaScript)에 부여해야 한다고 가정합니다. 한 가지 방법은 프록시로 작동하는 서비스 애플리케이션을 구축하는 것입니다. 사용자의 디바이스는 서비스를 인증하여 Azure Storage 리소스에 대한 액세스 권한을 부여합니다. 이러한 방식을 사용하면 안전하지 않은 디바이스에서 스토리지 계정 키가 노출되는 상황을 방지할 수 있습니다. 그러나 이 방법은 사용자의 디바이스와 Azure Storage 간에 전송되는 모든 데이터가 서비스 애플리케이션을 통과해야 하므로 서비스 애플리케이션에 상당한 오버헤드가 발생합니다.

SAS(공유 액세스 서명)를 사용하여 서비스 애플리케이션을 Azure Storage의 프록시로 사용하지 않도록 방지할 수 있습니다. SAS를 사용하면 제한된 액세스 토큰을 사용하여 사용자의 디바이스에서 Azure Storage에 직접 요청할 수 있습니다. 예를 들어 사용자가 사진을 애플리케이션에 업로드하려는 경우 서비스 애플리케이션에서 SAS를 생성하여 사용자의 디바이스로 보낼 수 있습니다. SAS 토큰은 지정된 시간 간격 동안 Azure Storage 리소스에 쓸 수 있는 권한을 부여할 수 있으며, 그 후에는 SAS 토큰이 만료됩니다. SAS에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요.  

일반적으로 웹 브라우저는 한 도메인의 웹 사이트에서 호스팅하는 페이지의 JavaScript에서 다른 도메인에 대한 쓰기 작업과 같은 특정 작업을 수행할 수 없습니다. 동일한 원본 정책이라고 하는 이 정책은 한 페이지의 악성 스크립트에서 다른 웹 페이지의 데이터에 액세스하지 못하도록 방지합니다. 그러나 클라우드에서 솔루션을 구축하는 경우 동일한 원본 정책이 제한될 수 있습니다. CORS(원본 간 리소스 공유)는 대상 도메인이 원본 도메인에서 시작된 요청을 신뢰하는 브라우저와 통신할 수 있게 하는 브라우저 기능입니다.

예를 들어 Azure에서 실행되는 웹 애플리케이션에서 Azure Storage 계정에 리소스를 요청한다고 가정합니다. 웹 애플리케이션은 원본 도메인이고, 스토리지 계정은 대상 도메인입니다. Azure Storage에서 원본 도메인의 요청을 신뢰하는 웹 브라우저와 통신하도록 모든 Azure Storage 서비스에 대해 CORS를 구성할 수 있습니다. CORS에 대한 자세한 내용은 [Azure Storage에 대한 CORS(원본 간 리소스 공유) 지원](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)을 참조하세요.  
  
SAS 및 CORS는 모두 웹 애플리케이션에서 불필요한 로드를 방지하는 데 도움이 될 수 있습니다.  

## <a name="caching"></a>캐싱

캐싱은 성능에서 중요한 역할을 합니다. 다음 섹션에서는 캐싱 모범 사례에 대해 설명합니다.

### <a name="reading-data"></a>데이터 읽기

일반적으로 데이터를 한 번 읽는 것이 두 번 읽는 것이 좋습니다. 사용자에게 콘텐츠역할을 하기 위해 Azure Storage에서 50개의 MiB Blob을 검색한 웹 응용 프로그램의 예를 생각해 보십시오. 이상적으로 응용 프로그램은 Blob을 디스크에 로컬로 캐시한 다음 후속 사용자 요청에 대해 캐시된 버전을 검색하는 것이 좋습니다.

Blob이 캐시된 이후 수정되지 않은 경우 Blob을 검색하지 않는 한 가지 방법은 수정 시간에 대한 조건부 헤더로 GET 작업을 한정하는 것입니다. 마지막으로 수정된 시간이 Blob이 캐시된 시간 이후인 경우 Blob이 검색되고 다시 캐시됩니다. 그렇지 않으면 최적의 성능을 위해 캐시된 Blob이 검색됩니다.

또한 Blob을 검색한 후 짧은 기간 동안 변경되지 않은 상태로 유지된다고 가정하도록 응용 프로그램을 디자인할 수도 있습니다. 이 경우 응용 프로그램은 해당 간격 동안 Blob이 수정되었는지 여부를 확인할 필요가 없습니다.

구성 데이터, 조회 데이터 및 응용 프로그램에서 자주 사용하는 기타 데이터는 캐싱에 적합한 후보입니다.  

조건부 헤더 사용에 대한 자세한 내용은 [Blob 서비스 작업에 대한 조건부 헤더 지정을](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)참조하십시오.  

### <a name="uploading-data-in-batches"></a>일괄 처리로 데이터 업로드

일부 시나리오에서는 데이터를 로컬로 집계한 다음 각 데이터를 즉시 업로드하는 대신 주기적으로 일괄 적으로 업로드할 수 있습니다. 예를 들어 웹 응용 프로그램이 활동의 로그 파일을 유지한다고 가정합니다. 응용 프로그램은 테이블에 발생하는 모든 활동의 세부 정보를 업로드하거나(많은 저장소 작업이 필요함) 활동 세부 정보를 로컬 로그 파일에 저장한 다음 모든 활동 세부 정보를 Blob에 구분된 파일로 주기적으로 업로드할 수 있습니다. 각 로그 항목의 크기가 1KB인 경우 단일 트랜잭션에서 수천 개의 항목을 업로드할 수 있습니다. 단일 트랜잭션은 최대 64MiB 크기의 Blob 업로드를 지원합니다. 응용 프로그램 개발자는 클라이언트 장치 또는 업로드 실패의 가능성을 위해 설계해야 합니다. 단일 활동이 아닌 시간 간격으로 활동 데이터를 다운로드해야 하는 경우 Table 저장소를 통해 Blob 저장소를 사용하는 것이 좋습니다.

## <a name="net-configuration"></a>.NET 구성

이 섹션에서는 .NET Framework를 사용하는 경우 성능을 크게 개선하기 위해 사용할 수 있는 다양한 빠른 구성 설정을 소개합니다.  다른 언어를 사용하는 경우에는 선택한 언어에 비슷한 개념이 적용되는지를 확인하세요.  

### <a name="use-net-core"></a>.NET Core 사용

성능 향상을 활용하려면 .NET Core 2.1 이상을 사용하여 Azure Storage 애플리케이션을 개발합니다. 가능하면 .NET Core 3.x를 사용하는 것이 좋습니다.

.NET Core의 성능 향상에 대한 자세한 내용은 다음 블로그 게시물을 참조하세요.

- [.NET Core 3.0의 성능 향상](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [.NET Core 2.1의 성능 향상](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>기본 연결 제한 늘리기

.NET에서 다음 코드는 기본 연결 제한(일반적으로 클라이언트 환경에서 2개 또는 서버 환경에서 10개)을 100으로 늘입니다. 일반적으로 이 값을 애플리케이션에서 사용되는 대략적인 스레드 수로 설정해야 합니다. 연결을 열기 전에 연결 제한을 설정합니다.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

다른 프로그래밍 언어의 경우 연결 제한을 설정하는 방법을 결정하려면 설명서를 참조하십시오.  

자세한 내용은 블로그 게시물 [웹 서비스: 동시 연결](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)을 참조하십시오.  

### <a name="increase-minimum-number-of-threads"></a>최소 스레드 수 늘리기

비동기 작업에서 동기 호출을 함께 사용하는 경우 스레드 풀의 스레드 수를 늘릴 수 있습니다.

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

자세한 내용은 [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) 메서드를 참조하세요.  

## <a name="unbounded-parallelism"></a>무제한 병렬 처리

병렬 처리는 성능에 매우 유용할 수 있지만, 스레드 수 또는 병렬 처리 요청 수가 제한되지 않는 무제한 병렬 처리를 사용하는 데 주의해야 합니다. 데이터를 업로드하거나 다운로드하거나, 동일한 스토리지 계정의 여러 파티션에 액세스하거나, 동일한 파티션의 여러 항목에 액세스하기 위한 병렬 요청은 제한해야 합니다. 무제한 병렬 처리인 경우 애플리케이션에서 클라이언트 디바이스의 기능 또는 스토리지 계정의 확장성 목표를 초과하여 대기 시간과 제한이 길어질 수 있습니다.  

## <a name="client-libraries-and-tools"></a>클라이언트 라이브러리 및 도구

최상의 성능을 위해 항상 Microsoft에서 제공하는 최신 클라이언트 라이브러리와 도구를 사용합니다. Azure Storage 클라이언트 라이브러리는 다양한 언어로 사용할 수 있습니다. 또한 Azure Storage는 PowerShell 및 Azure CLI도 지원합니다. Microsoft는 성능을 고려하여 이러한 클라이언트 라이브러리와 도구를 적극적으로 개발하고, 최신 서비스 버전으로 해당 도구를 최신 상태로 유지하며, 이를 통해 검증된 여러 성능 사례를 내부적으로 처리하고 있습니다. 자세한 내용은 [Azure Storage 참조 설명서](/azure/storage/#reference)를 참조하세요.

## <a name="handle-service-errors"></a>서비스 오류 처리

서비스에서 요청을 처리할 수 없는 경우 Azure Storage는 오류를 반환합니다. 지정된 시나리오에서 Azure Storage가 반환할 수 있는 오류를 이해하면 성능을 최적화하는 데 도움이 됩니다.

### <a name="timeout-and-server-busy-errors"></a>시간 제한 및 서버 작업 중 오류

확장성 제한에 도달하면 Azure Storage에서 애플리케이션을 제한할 수 있습니다. Azure Storage에서 일시적인 조건으로 인해 요청을 처리하지 못하는 경우도 있습니다. 두 경우 모두 서비스에서 503(서버 작업 중) 또는 500(시간 제한) 오류를 반환할 수 있습니다. 이러한 오류는 서비스에서 처리량을 높이기 위해 데이터 파티션을 균형 있게 다시 분산시키는 경우에도 발생할 수 있습니다. 클라이언트 애플리케이션은 일반적으로 이러한 오류 중 하나가 발생되는 작업을 다시 시도해야 합니다. 그러나 애플리케이션에서 확장성 목표를 초과하여 Azure Storage에서 애플리케이션을 제한하는 경우 또는 서비스에서 다른 이유로 요청을 처리할 수 없었던 경우에도 적극적인 재시도로 인해 문제가 악화될 수 있습니다. 지수 백오프 재시도 정책을 사용하는 것이 좋으며, 클라이언트 라이브러리는 기본적으로 이 동작으로 설정됩니다. 예를 들어 애플리케이션은 작업을 2초, 4초, 10초, 30초 후에 다시 시도한 다음, 계속 실패하면 작업을 완전히 포기할 수 있습니다. 이렇게 하면 애플리케이션에서 제한으로 이어질 수 있는 동작을 악화시키는 대신 서비스에 대한 부하를 크게 줄입니다.  

연결 오류는 제한으로 인해 발생하는 것이 아니라 일시적이므로 즉시 다시 시도할 수 있습니다.  

### <a name="non-retryable-errors"></a>다시 시도할 수 없는 오류

클라이언트 라이브러리는 다시 시도할 수 있는 오류와 다시 시도할 수 없는 오류를 인식하여 재시도를 처리합니다. 그러나 Azure Storage REST API를 직접 호출하는 경우에는 다시 시도하지 않아야 하는 오류가 있습니다. 예를 들어 400(잘못된 요청) 오류는 클라이언트 애플리케이션에서 예상되지 않은 형식으로 인해 처리할 수 없는 요청을 보냈음을 나타냅니다. 이 요청을 다시 보내면 매번 동일한 응답이 발생하므로 다시 시도할 필요가 없습니다. Azure Storage REST API를 직접 호출하는 경우 잠재적 오류와 재시도 여부를 알고 있어야 합니다.

Azure Storage 오류 코드에 대한 자세한 내용은 [상태 및 오류 코드](/rest/api/storageservices/status-and-error-codes2)를 참조하세요.

## <a name="copying-and-moving-blobs"></a>Blob 복사 및 이동

Azure Storage는 저장소 계정, 저장소 계정 간, 온-프레미스 시스템과 클라우드 간에 Blob을 복사하고 이동하기 위한 여러 솔루션을 제공합니다. 이 섹션에서는 성능에 미치는 영향측면에서 이러한 옵션 중 일부에 대해 설명합니다. Blob 저장소에서 또는 Blob 저장소로 데이터를 효율적으로 전송하는 것에 대한 자세한 내용은 [데이터 전송을 위한 Azure 솔루션 선택을](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조하십시오.

### <a name="blob-copy-apis"></a>Blob 복사 API

저장소 계정 간에 Blob을 복사하려면 [URL에서 차단 하기](/rest/api/storageservices/put-block-from-url) 작업을 사용합니다. 이 작업은 모든 URL 원본에서 데이터를 블록 Blob에 동기적으로 복사합니다. `Put Block from URL` 이 작업을 사용하면 저장소 계정 간에 데이터를 마이그레이션할 때 필요한 대역폭을 크게 줄일 수 있습니다. 복사 작업은 서비스 쪽에서 수행되므로 데이터를 다운로드하고 다시 업로드할 필요가 없습니다.

동일한 저장소 계정 내에서 데이터를 복사하려면 [Blob 복사](/rest/api/storageservices/Copy-Blob) 작업을 사용합니다. 일반적으로 동일한 저장소 계정 내에서 데이터를 복사하는 것은 신속하게 완료됩니다.  

### <a name="use-azcopy"></a>AzCopy 사용

AzCopy 명령줄 유틸리티는 Blob을 저장소 계정으로, 저장소 계정으로, 및 저장소 계정간에 대량으로 전송할 수 있는 간단하고 효율적인 옵션입니다. AzCopy는 이 시나리오에 최적화되어 있으며 높은 전송 속도를 달성할 수 있습니다. AzCopy 버전 10은 `Put Block From URL` 이 작업을 사용하여 저장소 계정 간에 Blob 데이터를 복사합니다. 자세한 내용은 [AzCopy v10을 사용하여 데이터 복사 또는 Azure 저장소로 이동을](/azure/storage/common/storage-use-azcopy-v10)참조하세요.  

### <a name="use-azure-data-box"></a>Azure 데이터 상자 사용

대량의 데이터를 Blob 저장소로 가져오면 Azure Data Box 제품군을 사용하여 오프라인 전송을 고려해 보세요. Microsoft에서 제공하는 데이터 박스 장치는 시간, 네트워크 가용성 또는 비용으로 인해 제한된 경우 많은 양의 데이터를 Azure로 이동하는 데 적합합니다. 자세한 내용은 Azure [데이터 박스 설명서를](/azure/databox/)참조하십시오.

## <a name="content-distribution"></a>콘텐츠 배포

응용 프로그램이 동일한 지역 또는 여러 지역에 있는 여러 사용자(예: 웹 사이트의 홈 페이지에 사용되는 제품 데모 비디오)에게 동일한 콘텐츠를 제공해야 하는 경우가 있습니다. 이 시나리오에서는 Azure CDN과 같은 CDN(콘텐츠 배달 네트워크)을 사용하여 Blob 콘텐츠를 지리적으로 배포합니다. 단일 하위 지역에 있으며 짧은 대기 시간 내에 다른 하위 지역으로 콘텐츠를 배달할 수 없는 Azure Storage 계정과는 달리 Azure CDN은 전 세계 여러 데이터 센터의 서버를 사용합니다. 또한 CDN은 일반적으로 단일 스토리지 계정보다 더 높은 송신 제한을 지원합니다.  

Azure CDN에 대한 자세한 내용은 [Azure CDN](../../cdn/cdn-overview.md)을 참조하세요.

## <a name="use-metadata"></a>메타데이터 사용

Blob 서비스는 Blob 속성 또는 메타데이터를 포함할 수 있는 HEAD 요청을 지원합니다. 예를 들어 응용 프로그램에 사진에서 Exif(exchangable 이미지 형식) 데이터가 필요한 경우 사진을 검색하여 추출할 수 있습니다. 대역폭을 절약하고 성능을 향상시키기 위해 응용 프로그램은 응용 프로그램이 사진을 업로드할 때 Blob의 메타데이터에 Exif 데이터를 저장할 수 있습니다. 그런 다음 HEAD 요청만 사용하여 메타데이터에서 Exif 데이터를 검색할 수 있습니다. Blob의 전체 내용이 아닌 메타데이터만 검색하면 상당한 대역폭이 절약되고 Exif 데이터를 추출하는 데 필요한 처리 시간이 줄어듭니다. Blob당 8KiB의 메타데이터를 저장할 수 있습니다.  

## <a name="upload-blobs-quickly"></a>Blob을 빠르게 업로드

Blob을 빠르게 업로드하려면 먼저 하나의 Blob을 업로드할지 또는 여러 개 업로드할지 결정합니다. 아래 지침을 참고하여 시나리오에 따라 사용할 적절한 방법을 결정합니다.  

### <a name="upload-one-large-blob-quickly"></a>하나의 큰 Blob을 빠르게 업로드

단일 큰 Blob을 빠르게 업로드하려면 클라이언트 응용 프로그램이 블록 이나 페이지를 병렬로 업로드할 수 있습니다. Azure Storage 클라이언트 라이브러리는 병렬로 업로드를 지원합니다. 예를 들어 다음 속성을 사용하여 .NET 또는 Java에서 허용되는 동시 요청 수를 지정할 수 있습니다. 지원되는 다른 언어에 대한 클라이언트 라이브러리는 유사한 옵션을 제공합니다.

- .NET의 경우 [BlobRequestOptions.병렬 작업스레드카운트](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) 속성을 설정합니다.
- 자바/안 드 로이드에 대 한 [호출 BlobRequestOptions.setConcurrentRequestCount (최종 정수 동시 요청 카운트)](/java/api/com.microsoft.azure.storage.blob._blob_request_options.setconcurrentrequestcount) 메서드.

### <a name="upload-many-blobs-quickly"></a>많은 Blob을 빠르게 업로드

Blob 여러 개를 빠르게 업로드하려면 Blob을 병렬로 업로드합니다. 병렬로 업로드하는 것은 저장소 서비스의 여러 파티션에 업로드를 분산하기 때문에 병렬 블록 업로드를 사용하여 한 번에 단일 Blob을 업로드하는 것보다 빠릅니다. 이 시나리오에서는 기본적으로 업로드를 병렬로 수행하는 AzCopy를 사용하는 것이 좋습니다. 자세한 내용은 [AzCopy 로 시작하기](../common/storage-use-azcopy-v10.md)를 참조하십시오.  

## <a name="choose-the-correct-type-of-blob"></a>올바른 유형의 Blob 선택

Azure Storage는 블록 Blob, 부백 Blob 및 페이지 Blob을 지원합니다. 지정된 사용 시나리오에 대해 선택하는 Blob 유형은 솔루션의 성능과 확장성에 영향을 줍니다.

블록 Blob은 대량의 데이터를 효율적으로 업로드하려는 경우에 적합합니다. 예를 들어 사진이나 비디오를 Blob 저장소에 업로드하는 클라이언트 응용 프로그램은 블록 Blob을 대상으로 합니다.

부속 Blob은 블록으로 구성된다는 점에서 블록 Blob과 유사합니다. 추가 Blob을 수정하면 블록이 Blob의 끝에만 추가됩니다. 추가 Blob은 응용 프로그램이 기존 Blob에 데이터를 추가해야 하는 경우 로깅과 같은 시나리오에 유용합니다.

응용 프로그램에서 데이터에 대한 임의 쓰기를 수행해야 하는 경우 페이지 Blob이 적합합니다. 예를 들어 Azure 가상 시스템 디스크는 페이지 Blob로 저장됩니다. 자세한 내용은 [블록 Blob 이해, Blob 및 페이지 Blob 추가를](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)참조하십시오.  

## <a name="next-steps"></a>다음 단계

- [Blob 스토리지의 확장성 및 성능 목표](scalability-targets.md)
- [표준 스토리지 계정의 확장성 및 성능 목표](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [상태 및 오류 코드](/rest/api/storageservices/Status-and-Error-Codes2)
