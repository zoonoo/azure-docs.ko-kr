---
title: Gen1에서 Gen2로 Azure Data Lake Storage 마이그레이션
description: Gen1에서 Gen2로 Azure Data Lake Storage을 마이그레이션합니다.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: aa4881aef9f3a9ba5d19fb0b768f13a1eb372296
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131428"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Gen1에서 Gen2로 Azure Data Lake Storage 마이그레이션

데이터, 워크 로드 및 응용 프로그램을 Data Lake Storage Gen1에서 Data Lake Storage Gen2로 마이그레이션할 수 있습니다.

Azure Data Lake Storage Gen2는 [Azure Blob Storage](storage-blobs-introduction.md) 를 기반으로 하며 빅 데이터 분석 전용 기능 집합을 제공 합니다. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) 는 파일 시스템 의미 체계, 디렉터리 및 파일 수준 보안과 같은 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)의 기능을 결합 하 고 [Azure Blob Storage](storage-blobs-introduction.md)의 저렴 한 비용, 계층화 된 저장소, 고가용성/재해 복구 기능으로 확장 합니다.

> [!NOTE]
> 쉽게 읽을 수 있도록이 문서에서는 *Gen1* 이라는 용어를 사용 하 여 Azure Data Lake Storage Gen1를 참조 하 고 *Gen2* 라는 용어를 사용 하 여 Azure Data Lake Storage Gen2을 참조 합니다.

## <a name="recommended-approach"></a>권장 접근 방식

Gen2로 마이그레이션하려면 다음 방법을 사용 하는 것이 좋습니다.

: heavy_check_mark: 1 단계: 준비 상태 평가

: heavy_check_mark: 2 단계: 마이그레이션 준비

: heavy_check_mark: 3 단계: 데이터 및 응용 프로그램 워크 로드 마이그레이션

: heavy_check_mark: 4 단계: Gen1에서 Gen2로

> [!NOTE]
> Gen1와 Gen2는 서로 다른 서비스 이며, 현재 위치의 업그레이드 환경과 의도적인 마이그레이션 노력이 필요 하지 않습니다. 

### <a name="step-1-assess-readiness"></a>1 단계: 준비 상태 평가

1. [Data Lake Storage Gen2 제공](https://azure.microsoft.com/services/storage/data-lake-storage/)에 대해 알아봅니다. 이는 이점, 비용 및 일반 아키텍처입니다. 

2. Gen1의 기능을 Gen2의 [기능과 비교](#gen1-gen2-feature-comparison) 합니다. 

3. [알려진 문제](data-lake-storage-known-issues.md) 목록을 검토 하 여 기능에 대 한 모든 차이를 평가 합니다.

4. Gen2은 [진단 로깅](../common/storage-analytics-logging.md), [액세스 계층](storage-blob-storage-tiers.md)및 [blob 저장소 수명 주기 관리 정책과](storage-lifecycle-management-concepts.md)같은 blob 저장소 기능을 지원 합니다. 이러한 기능을 사용 하는 것에 관심이 있는 경우 [현재 지원 수준](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)을 검토 하세요.

5. [Azure 에코 시스템 지원](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) 의 현재 상태를 검토 하 여 Gen2가 솔루션에 의존 하는 모든 서비스를 지원 하는지 확인 합니다.

### <a name="step-2-prepare-to-migrate"></a>2 단계: 마이그레이션 준비

1. 마이그레이션할 데이터 집합을 식별 합니다.

   더 이상 사용 하지 않는 데이터 집합을 정리 하려면이 기회를 사용 합니다. 데이터를 한 번에 모두 마이그레이션하지 않으려는 경우이 시간을 사용 하 여 단계별로 마이그레이션할 수 있는 데이터의 논리적 그룹을 식별 합니다.
   
2. 마이그레이션이 비즈니스에 미치는 영향을 확인 합니다.

   예를 들어 마이그레이션이 이루어지는 동안 가동 중지 시간을 감당할 수 있는지 여부를 고려 합니다. 이러한 고려 사항은 적절 한 마이그레이션 패턴을 식별 하 고 가장 적합 한 도구를 선택 하는 데 도움이 될 수 있습니다.

3. 마이그레이션 계획을 만듭니다. 

   이러한 [마이그레이션 패턴](#migration-patterns)을 권장 합니다. 이러한 패턴 중 하나를 선택 하거나 함께 결합 하거나 사용자 지정 패턴을 디자인할 수 있습니다.

### <a name="step-3-migrate-data-workloads-and-applications"></a>3 단계: 데이터, 워크 로드 및 응용 프로그램 마이그레이션

원하는 패턴을 사용 하 여 데이터, 워크 로드 및 응용 프로그램을 마이그레이션합니다. 시나리오를 증분 방식으로 확인 하는 것이 좋습니다.

1. [저장소 계정을 만들고](data-lake-storage-quickstart-create-account.md) 계층 구조 네임 스페이스 기능을 사용 하도록 설정 합니다. 

2. 데이터를 마이그레이션합니다. 

3. Gen2 끝점을 가리키도록 [작업에서 서비스](data-lake-storage-integrate-with-azure-services.md) 를 구성 합니다. 
   
4. Gen2 Api를 사용 하도록 응용 프로그램을 업데이트 합니다. [.Net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) 및 [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)에 대 한 가이드를 참조 하세요. 
   
5. Data Lake Storage Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md)및 [Azure CLI 명령을](data-lake-storage-directory-file-acl-cli.md)사용 하도록 스크립트를 업데이트 합니다.
   
6. 코드 파일 또는 Databricks 노트북의 문자열 `adl://` 을 포함 하는 URI 참조를 검색 하거나, 작업의 일부로 사용 되는 노트북 Apache Hive HQL 파일 또는 다른 파일을 검색 합니다. 이러한 참조를 새 저장소 계정의 [Gen2 형식 URI](data-lake-storage-introduction-abfs-uri.md) 로 바꿉니다. 예: Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` 가 될 `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`수 있습니다. 

7. 계정에 대 한 보안을 구성 하 여 [RBAC (역할 기반 액세스 제어) 역할](../common/storage-auth-aad-rbac-portal.md), [파일 및 폴더 수준 보안](data-lake-storage-access-control.md), [Azure Storage 방화벽 및 가상 네트워크](../common/storage-network-security.md)를 포함 합니다.

### <a name="step-4-cutover-from-gen1-to-gen2"></a>4 단계: Gen1에서 Gen2로의 가공선

응용 프로그램 및 워크 로드가 Gen2에서 안정적 이라고 확신 하는 경우 Gen2를 사용 하 여 비즈니스 시나리오를 충족할 수 있습니다. Gen1에서 실행 되는 나머지 파이프라인을 끄고 Gen1 계정을 서비스 해제 합니다. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 vs Gen2 기능

이 표에서는 Gen1의 기능을 Gen2와 비교 합니다.

|영역 |1세대   |2세대 |
|---|---|---|
|데이터 구성|[계층 구조 네임스페이스](data-lake-storage-namespace.md)<br>파일 및 폴더 지원|[계층 구조 네임스페이스](data-lake-storage-namespace.md)<br>컨테이너, 파일 및 폴더 지원 |
|지리적 중복| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|인증|[AAD 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)<br>[서비스 사용자](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)<br>[서비스 사용자](../../active-directory/develop/app-objects-and-service-principals.md)<br>[공유 액세스 키](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|권한 부여|관리- [RBAC](../../role-based-access-control/overview.md)<br>데이터- [acl](data-lake-storage-access-control.md)|관리 – [RBAC](../../role-based-access-control/overview.md)<br>데이터- [acl](data-lake-storage-access-control.md), [RBAC](../../role-based-access-control/overview.md) |
|암호화 – 미사용 데이터|서버 쪽 – [Microsoft 관리](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 또는 [고객 관리](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 키 사용|서버 쪽 – [Microsoft 관리](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 또는 [고객 관리](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 키 사용|
|VNET 지원|[VNET 통합](../../data-lake-store/data-lake-store-network-security.md)|[서비스 끝점](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [개인 끝점](../common/storage-private-endpoints.md)|
|개발자 환경|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.net](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|일반적으로 사용 가능한 [REST](/rest/api/storageservices/data-lake-storage-gen2), [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>공개 미리 보기- [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|리소스 로그|클래식 로그<br>[Azure Monitor 통합](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[클래식 로그](../common/storage-analytics-logging.md) -일반적으로 사용 가능<br>Azure monitor 통합 – 타임 라인 TBD|
|에코시스템|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3.1 이상)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5.1 이상)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Gen1 Gen2 패턴

마이그레이션 패턴을 선택 하 고 필요에 따라 해당 패턴을 수정 합니다.

|||
|---|---|
|**리프트 앤 시프트**|가장 간단한 패턴입니다. 데이터 파이프라인이 가동 중지 시간을 감당할 수 있는 경우 이상적입니다.|
|**증분 복사**|리프트 앤 *시프트*와 비슷하지만 가동 중지 시간이 줄어듭니다. 복사 하는 데 시간이 오래 걸리는 대량의 데이터에 적합 합니다.|
|**이중 파이프라인**|가동 중지 시간을 감당할 수 없는 파이프라인에 이상적입니다.|
|**양방향 동기화**|*이중 파이프라인과*유사 하지만 좀 더 복잡 한 파이프라인에 적합 한 보다 단계별 접근 방식이 있습니다.|

각 패턴에 대해 좀 더 자세히 살펴보겠습니다.
 
### <a name="lift-and-shift-pattern"></a>리프트 및 시프트 패턴

이는 가장 간단한 패턴입니다.

1. Gen1에 대 한 모든 쓰기를 중지 합니다.

2. Gen1에서 Gen2로 데이터를 이동 합니다. [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)하는 것이 좋습니다. 데이터를 사용 하 여 Acl을 복사 합니다.

3. Point 수집 작업 및 워크 로드를 Gen2 합니다.

4. Gen1 서비스를 해제 합니다.

> [!div class="mx-imgBorder"]
> ![리프트 및 시프트 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>리프트 및 시프트 패턴 사용에 대 한 고려 사항

: heavy_check_mark: 모든 워크 로드에 대해 동시에 Gen1에서 Gen2로 건너뜁니다.

: heavy_check_mark: 마이그레이션하는 동안 가동 중지 시간이 발생 하 고 기간을 초과 합니다.

: heavy_check_mark: 가동 중지 시간을 감당할 수 있는 파이프라인에 이상적 이며 모든 앱을 한 번에 업그레이드할 수 있습니다.

### <a name="incremental-copy-pattern"></a>증분 복사 패턴

1. Gen1에서 Gen2로 데이터 이동을 시작 합니다. [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)하는 것이 좋습니다. 데이터를 사용 하 여 Acl을 복사 합니다.

2. Gen1에서 새 데이터를 증분 복사 합니다.

3. 모든 데이터를 복사한 후 Gen1에 대 한 모든 쓰기를 중지 하 고 워크 로드를 Gen2로 가리킵니다.

4. Gen1 서비스를 해제 합니다.

> [!div class="mx-imgBorder"]
> ![증분 복사 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>증분 복사 패턴 사용에 대 한 고려 사항:

: heavy_check_mark: 모든 워크 로드에 대해 동시에 Gen1에서 Gen2로 건너뜁니다.

: heavy_check_mark: 기간을 초과 하는 동안에만 중단 시간을 예측 합니다.

: heavy_check_mark: 모든 앱을 한 번에 업그레이드 하지만 데이터 복사에 시간이 더 필요한 파이프라인에 이상적입니다.

### <a name="dual-pipeline-pattern"></a>이중 파이프라인 패턴

1. Gen1에서 Gen2로 데이터를 이동 합니다. [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)하는 것이 좋습니다. 데이터를 사용 하 여 Acl을 복사 합니다.

2. Gen1 및 Gen2 둘 다에 새 데이터를 수집 합니다.

3. Gen2에 대 한 작업을 가리킵니다.

4. Gen1에 대 한 모든 쓰기를 중지 한 다음 Gen1를 해제 합니다.

> [!div class="mx-imgBorder"]
> ![이중 파이프라인 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>이중 파이프라인 패턴 사용에 대 한 고려 사항:

: heavy_check_mark: Gen1 및 Gen2 파이프라인이 side-by-side 방식으로 실행 됩니다.

: heavy_check_mark: 0 가동 중지 시간을 지원 합니다.

: heavy_check_mark: 워크 로드 및 응용 프로그램에서 가동 중지 시간을 감당할 수 없고 두 저장소 계정에 모두 수집할 수 있는 경우에 적합 합니다.

### <a name="bi-directional-sync-pattern"></a>양방향 동기화 패턴

1. Gen1와 Gen2 간에 양방향 복제를 설정 합니다. [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)을 권장 합니다. 기존 데이터에 대 한 복구 기능을 제공 합니다.

3. 모든 이동이 완료 되 면 Gen1에 대 한 모든 쓰기를 중지 하 고 양방향 복제를 해제 합니다.

4. Gen1 서비스를 해제 합니다.

> [!div class="mx-imgBorder"]
> ![양방향 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>양방향 동기화 패턴 사용에 대 한 고려 사항:

: heavy_check_mark: 단계별 접근 방식이 더 적합할 수 있는 많은 수의 파이프라인 및 종속성을 포함 하는 복잡 한 시나리오에 적합 합니다.  

: heavy_check_mark: 마이그레이션의 노력은 우수 하지만 Gen1 및 Gen2에 대 한 side-by-side 지원을 제공 합니다.

## <a name="next-steps"></a>다음 단계

- 저장소 계정에 대 한 보안 설정의 다양 한 부분에 대해 알아봅니다. [Azure Storage 보안 가이드](../common/storage-security-guide.md)를 참조 하세요.
- Data Lake Store에 대 한 성능을 최적화 합니다. [성능 최적화 Azure Data Lake Storage Gen2를](data-lake-storage-performance-tuning-guidance.md) 참조 하세요.
- Data Lake Store 관리에 대 한 모범 사례를 검토 합니다. [Azure Data Lake Storage Gen2 사용에 대 한 모범 사례를](data-lake-storage-best-practices.md) 참조 하세요.

