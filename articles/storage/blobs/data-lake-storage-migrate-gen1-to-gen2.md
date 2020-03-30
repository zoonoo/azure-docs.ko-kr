---
title: Azure 데이터 레이크 스토리지를 Gen1에서 Gen2로 마이그레이션
description: Azure 데이터 레이크 저장소를 Gen1에서 Gen2로 마이그레이션합니다.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: fb982324b66c5ac0d2db00eb906ed850827bc72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533286"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Azure 데이터 레이크 스토리지를 Gen1에서 Gen2로 마이그레이션

데이터 레이크 스토리지 Gen1에서 데이터 레이크 스토리지 Gen2로 데이터, 워크로드 및 애플리케이션을 마이그레이션할 수 있습니다.

Azure Data Lake Storage Gen2는 [Azure Blob 저장소를](storage-blobs-introduction.md) 기반으로 구축되었으며 빅 데이터 분석 전용 기능 집합을 제공합니다. [Data Lake Storage Gen2는](https://azure.microsoft.com/services/storage/data-lake-storage/) Azure [Data Lake Storage Gen1의](https://docs.microsoft.com/azure/data-lake-store/index)기능(예: 파일 시스템 의미 체계, 디렉터리 및 파일 수준 보안 및 확장)을 저렴한 비용의 계층형 저장소, [Azure Blob 저장소의](storage-blobs-introduction.md)고가용성/재해 복구 기능과 결합합니다.

> [!NOTE]
> 더 쉽게 읽을 수 있도록 이 문서에서는 *Gen1이라는* 용어를 사용하여 Azure Data Lake Storage Gen1을 참조하고 *Gen2라는* 용어는 Azure Data Lake Storage Gen2를 참조합니다.

## <a name="recommended-approach"></a>권장 접근 방식

Gen2로 마이그레이션하려면 다음 방법을 권장합니다.

:heavy_check_mark: 1단계: 준비 태세 평가

:heavy_check_mark: 2단계: 마이그레이션 준비

:heavy_check_mark: 3단계: 데이터 및 애플리케이션 워크로드 마이그레이션

:heavy_check_mark: 4단계: Gen1에서 Gen2로 의 절단

> [!NOTE]
> Gen1 및 Gen2는 서로 다른 서비스이며, 현재 업그레이드 경험이 없으며 의도적인 마이그레이션 노력이 필요합니다. 

### <a name="step-1-assess-readiness"></a>1단계: 준비 성 평가

1. [데이터 레이크 스토리지 Gen2 오퍼링에](https://azure.microsoft.com/services/storage/data-lake-storage/)대해 자세히 알아보십시오. 이점, 비용 및 일반 아키텍처입니다. 

2. Gen1의 기능을 Gen2의 [기능과 비교합니다.](#gen1-gen2-feature-comparison) 

3. [알려진 문제](data-lake-storage-known-issues.md) 목록을 검토하여 기능의 차이를 평가합니다.

4. Gen2는 [진단 로깅,](../common/storage-analytics-logging.md)액세스 [계층](storage-blob-storage-tiers.md)및 [Blob 저장소 수명 주기 관리 정책과](storage-lifecycle-management-concepts.md)같은 Blob 저장소 기능을 지원합니다. 이러한 기능을 사용하는 데 관심이 있다면 [현재 지원 수준을](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)검토합니다.

5. [Azure 에코시스템의](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) 현재 상태를 검토하여 Gen2가 솔루션에 의존하는 모든 서비스를 지원하는지 확인합니다.

### <a name="step-2-prepare-to-migrate"></a>2단계: 마이그레이션 준비

1. 마이그레이션할 데이터 집합을 식별합니다.

   이 기회를 통해 더 이상 사용하지 않는 데이터 집합을 정리합니다. 한 번에 모든 데이터를 마이그레이션하려는 경우가 아니면 단계적으로 마이그레이션할 수 있는 논리적 데이터 그룹을 식별합니다.
   
2. 마이그레이션이 비즈니스에 미치는 영향을 확인합니다.

   예를 들어 마이그레이션이 진행되는 동안 가동 중지 시간을 감당할 수 있는지 여부를 고려합니다. 이러한 고려 사항은 적합한 마이그레이션 패턴을 식별하고 가장 적합한 도구를 선택하는 데 도움이 될 수 있습니다.

3. 마이그레이션 계획을 만듭니다. 

   이러한 [마이그레이션 패턴을](#migration-patterns)사용하는 것이 좋습니다. 이러한 패턴 중 하나를 선택하거나, 함께 결합하거나, 사용자 지정 패턴을 디자인할 수 있습니다.

### <a name="step-3-migrate-data-workloads-and-applications"></a>3단계: 데이터, 워크로드 및 애플리케이션 마이그레이션

원하는 패턴을 사용하여 데이터, 워크로드 및 응용 프로그램을 마이그레이션합니다. 시나리오를 점진적으로 검사하는 것이 좋습니다.

1. [저장소 계정을 만들고](data-lake-storage-quickstart-create-account.md) 계층적 네임스페이스 기능을 사용하도록 설정합니다. 

2. 데이터 마이그레이션 

3. 워크로드에서 Gen2 끝점을 가리키도록 [서비스를](data-lake-storage-integrate-with-azure-services.md) 구성합니다. 
   
4. Gen2 API를 사용하도록 응용 프로그램을 업데이트합니다. [.NET,](data-lake-storage-directory-file-acl-dotnet.md) [자바,](data-lake-storage-directory-file-acl-java.md) [파이썬,](data-lake-storage-directory-file-acl-python.md) [자바 스크립트](data-lake-storage-directory-file-acl-javascript.md) 및 [REST에](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)대한 가이드를 참조하십시오. 
   
5. 데이터 레이크 저장소 Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md)및 [Azure CLI 명령을](data-lake-storage-directory-file-acl-cli.md)사용 하 여 스크립트를 업데이트 합니다.
   
6. 코드 파일 또는 Databricks 노트북, 아파치 Hive HQL 파일 또는 워크로드의 일부로 사용되는 다른 파일에 문자열이 `adl://` 포함된 URI 참조를 검색합니다. 이러한 참조를 새 저장소 계정의 [Gen2 형식의 URI로](data-lake-storage-introduction-abfs-uri.md) 바꿉니다. 예를 들어 Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`가 될 수 있습니다. 

7. [RBAC(역할 기반 액세스 제어) 역할,](../common/storage-auth-aad-rbac-portal.md)파일 및 [폴더 수준 보안,](data-lake-storage-access-control.md)Azure 저장소 방화벽 및 가상 네트워크를 포함하도록 계정의 보안을 [구성합니다.](../common/storage-network-security.md)

### <a name="step-4-cutover-from-gen1-to-gen2"></a>4단계: Gen1에서 Gen2로 의 절단

응용 프로그램과 워크로드가 Gen2에서 안정적이라고 확신하면 Gen2를 사용하여 비즈니스 시나리오를 충족할 수 있습니다. Gen1에서 실행 중인 나머지 파이프라인을 끄고 Gen1 계정을 해제합니다. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 대 Gen2 기능

이 표는 Gen1의 기능을 Gen2의 기능과 비교합니다.

|영역 |1세대   |2세대 |
|---|---|---|
|데이터 구성|[계층 구조 네임스페이스](data-lake-storage-namespace.md)<br>파일 및 폴더 지원|[계층 구조 네임스페이스](data-lake-storage-namespace.md)<br>컨테이너, 파일 및 폴더 지원 |
|지리적 중복| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS,](../common/storage-redundancy.md#locally-redundant-storage) [ZRS,](../common/storage-redundancy.md#zone-redundant-storage) [GRS,](../common/storage-redundancy.md#geo-redundant-storage) [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|인증|[AAD 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)<br>[서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)<br>[서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)<br>[공유 액세스 키](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|권한 부여|관리 - [RBAC](../../role-based-access-control/overview.md)<br>데이터 – [ACL](data-lake-storage-access-control.md)|관리 – [RBAC](../../role-based-access-control/overview.md)<br>데이터 - [ACL,](data-lake-storage-access-control.md) [RBAC](../../role-based-access-control/overview.md) |
|암호화 – 미사용 데이터|서버 측 – [Microsoft 관리](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 키 또는 고객 관리 키 [포함](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|서버 측 – [Microsoft 관리](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 키 또는 고객 관리 키 [포함](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|VNET 지원|[VNET 통합](../../data-lake-store/data-lake-store-network-security.md)|[서비스 끝점,](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) [비공개 끝점(공개 미리 보기)](../common/storage-private-endpoints.md)|
|개발자 환경|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET,](../../data-lake-store/data-lake-store-data-operations-net-sdk.md) [자바,](../../data-lake-store/data-lake-store-get-started-java-sdk.md) [파이썬,](../../data-lake-store/data-lake-store-data-operations-python.md) [파워쉘,](../../data-lake-store/data-lake-store-get-started-powershell.md) [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|[REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET,](data-lake-storage-directory-file-acl-dotnet.md) [자바,](data-lake-storage-directory-file-acl-java.md) [파이썬,](data-lake-storage-directory-file-acl-python.md) [자바 스크립트,](data-lake-storage-directory-file-acl-javascript.md) [파워 쉘](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md) (공개 미리보기)|
|진단 로그|클래식 로그<br>[Azure 모니터 통합](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[클래식 로그(공개](../common/storage-analytics-logging.md) 미리 보기)<br>Azure 모니터 통합 – 타임라인 TBD|
|에코시스템|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure 데이터 브릭 (3.1 이상)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW,](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store) [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure 데이터 브릭 (5.1 이상)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW,](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md) [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Gen 1 ~ Gen2 패턴

마이그레이션 패턴을 선택한 다음 필요에 따라 해당 패턴을 수정합니다.

|||
|---|---|
|**리프트 및 시프트**|가장 간단한 패턴입니다. 데이터 파이프라인이 가동 중지 시간을 감당할 수 있는 경우에 이상적입니다.|
|**증분 복사본**|리프트 *및 시프트와*유사하지만 가동 중지 시간이 적습니다. 복사하는 데 시간이 오래 걸리는 대용량 데이터에 이상적입니다.|
|**이중 파이프라인**|가동 중지 시간을 감당할 수 없는 파이프라인에 이상적입니다.|
|**양방향 동기화**|이중 *파이프라인과*유사하지만 보다 복잡한 파이프라인에 적합한 보다 단계적인 접근 방식을 사용할 수 있습니다.|

각 패턴을 자세히 살펴보겠습니다.
 
### <a name="lift-and-shift-pattern"></a>리프트 및 변속 패턴

가장 간단한 패턴입니다.

1. Gen1에 대한 모든 쓰기를 중지합니다.

2. 데이터를 Gen1에서 Gen2로 이동합니다. Azure [데이터 팩터리.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) ACL은 데이터와 함께 복사합니다.

3. 인제스트 작업 및 워크로드를 Gen2로 가리킵니다.

4. 폐기 Gen1.

> [!div class="mx-imgBorder"]
> ![리프트 및 시프트 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>리프트 및 변속 패턴 사용에 대한 고려 사항

:heavy_check_mark: 모든 워크로드에 대해 Gen1에서 Gen2로 동시에 컷오버합니다.

:heavy_check_mark: 마이그레이션 및 컷오버 기간 동안 가동 중지 시간을 예상합니다.

:heavy_check_mark: 가동 중지 시간을 감당할 수 있는 파이프라인에 이상적이며 모든 앱을 한 번에 업그레이드할 수 있습니다.

### <a name="incremental-copy-pattern"></a>증분 복사 패턴

1. 1세대에서 Gen2로 데이터를 이동하기 시작합니다. Azure [데이터 팩터리.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) ACL은 데이터와 함께 복사합니다.

2. Gen1에서 새 데이터를 점진적으로 복사합니다.

3. 모든 데이터가 복사되면 Gen1에 대한 모든 쓰기를 중지하고 Gen2에 대한 워크로드를 가리킵니다.

4. 폐기 Gen1.

> [!div class="mx-imgBorder"]
> ![증분 복사 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>증분 복사 패턴을 사용하기 위한 고려 사항:

:heavy_check_mark: 모든 워크로드에 대해 Gen1에서 Gen2로 동시에 컷오버합니다.

:heavy_check_mark: 컷오버 기간 동안에만 가동 중지 시간을 예상합니다.

:heavy_check_mark: 모든 앱이 한 번에 업그레이드되었지만 데이터 복사본에 더 많은 시간이 필요한 파이프라인에 이상적입니다.

### <a name="dual-pipeline-pattern"></a>이중 파이프라인 패턴

1. 데이터를 Gen1에서 Gen2로 이동합니다. Azure [데이터 팩터리.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) ACL은 데이터와 함께 복사합니다.

2. Gen1과 Gen2 모두에 새 데이터를 수집합니다.

3. 워크로드를 Gen2로 가리킵니다.

4. Gen1에 대한 모든 쓰기를 중지한 다음 Gen1을 해제합니다.

> [!div class="mx-imgBorder"]
> ![이중 파이프라인 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>이중 파이프라인 패턴을 사용하기 위한 고려 사항:

:heavy_check_mark: Gen1 및 Gen2 파이프라인이 나란히 실행됩니다.

:heavy_check_mark: 가동 중지 시간 제로를 지원합니다.

:heavy_check_mark: 워크로드와 애플리케이션이 가동 중지 시간을 감당할 수 없는 상황에서 이상적이며 두 저장소 계정에 모두 통합할 수 있습니다.

### <a name="bi-directional-sync-pattern"></a>양방향 동기화 패턴

1. Gen1과 Gen2 간에 양방향 복제를 설정합니다. [완디스코를 추천한다.](https://docs.wandisco.com/bigdata/wdfusion/adls/) 기존 데이터에 대한 복구 기능을 제공합니다.

3. 모든 이동이 완료되면 Gen1에 대한 모든 쓰기를 중지하고 양방향 복제를 끕니다.

4. 폐기 Gen1.

> [!div class="mx-imgBorder"]
> ![양방향 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>양방향 동기화 패턴을 사용하기 위한 고려 사항:

:heavy_check_mark: 단계별 접근 방식이 더 합리적일 수 있는 많은 수의 파이프라인과 종속성을 포함하는 복잡한 시나리오에 적합합니다.  

:heavy_check_mark: 마이그레이션 노력은 높지만 Gen1 및 Gen2에 대한 나란히 지원을 제공합니다.

## <a name="next-steps"></a>다음 단계

- 저장소 계정에 대한 보안 설정의 다양한 부분에 대해 알아봅니다. [Azure 저장소 보안 가이드를](../common/storage-security-guide.md)참조하십시오.
- 데이터 레이크 스토어의 성능을 최적화합니다. [성능에 대한 Azure 데이터 레이크 스토리지 Gen2 최적화](data-lake-storage-performance-tuning-guidance.md) 를 참조하십시오.
- 데이터 레이크 스토어 관리를 위한 모범 사례를 검토합니다. [Azure 데이터 레이크 스토리지 Gen2 사용에 대한 모범 사례](data-lake-storage-best-practices.md) 를 참조하십시오.

