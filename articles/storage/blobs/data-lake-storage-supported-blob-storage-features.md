---
title: Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능 | Microsoft Docs
description: Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b670ec3cee2ebd1cc98ea2e04fb2b0dfd90e4e1a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594218"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능

[진단 로깅](../common/storage-analytics-logging.md), [액세스 계층](storage-blob-storage-tiers.md)및 [Blob Storage 수명 주기 관리 정책과](storage-lifecycle-management-concepts.md) 같은 Blob Storage 기능은 이제 계층 구조가 있는 계정으로 작동 합니다. 따라서 이러한 기능에 대한 액세스를 잃지 않고 Blob 스토리지 계정에서 계층 구조 네임스페이스를 사용하도록 설정할 수 있습니다.

다음 표에는 Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능이 나열되어 있습니다. 이러한 표에 표시되는 항목은 이후 지원이 계속 확장됨에 따라 변경될 수 있습니다. 기능의 미리 보기 상태와 관련된 특정 문제에 대한 자세한 내용은 [알려진 문제](data-lake-storage-known-issues.md) 문서를 참조하세요.

## <a name="supported-blob-storage-features"></a>지원되는 Blob 스토리지 기능

다음 표에서는 각 Blob 저장소 기능이 Data Lake Storage Gen2에서 어떻게 지원 되는지 보여 줍니다. 표준 및 [프리미엄 성능](premium-tier-for-data-lake-storage.md) 계층에 대 한 열이 있습니다. 

|Blob Storage 기능 |Standard |Premium |관련 문서 |
|---------------|-------------------|---|
|핫 액세스 계층|일반 공급|지원되지 않음|[Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)|
|쿨 액세스 계층|일반 공급|지원되지 않음|[Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)|
|이벤트|일반 공급|일반 공급|[Blob Storage 이벤트에 응답](storage-blob-event-overview.md)|
|메트릭(클래식)|일반 공급|일반 공급|[Azure Storage 분석 메트릭(클래식)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Monitor의 메트릭|일반 공급|미리 보기|[Azure Monitor의 Azure Storage 메트릭](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob Storage PowerShell 명령|일반 공급|일반 공급|[빠른 시작: PowerShell을 사용하여 Blob 업로드, 다운로드 및 나열](storage-quickstart-blobs-powershell.md)|
|Blob Storage Azure CLI 명령|일반 공급|일반 공급|[빠른 시작: Azure CLI를 사용하여 Blob 생성, 다운로드 및 나열](storage-quickstart-blobs-cli.md)|
|Blob Storage API|일반 공급|일반 공급|[빠른 시작: .NET용 Azure Blob Storage 클라이언트 라이브러리 v12](storage-quickstart-blobs-dotnet.md)<br>[빠른 시작: Java v12 SDK로 Blob 관리](storage-quickstart-blobs-java.md)<br>[빠른 시작: Python v12 SDK로 Blob 관리](storage-quickstart-blobs-python.md)<br>[빠른 시작: Node.js에서 JavaScript v12 SDK를 사용하여 Blob 관리](storage-quickstart-blobs-nodejs.md)|
|진단 로그|일반 공급|미리 보기 |[Azure Storage 분석 로깅](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|보관 액세스 계층|일반 공급|지원되지 않음|[Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)|
|수명 주기 관리 정책 (계층화)|일반 공급|아직 지원되지 않음|[Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)|
|수명 주기 관리 정책 (blob 삭제)|일반 공급|일반 공급|[Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)|
|Azure Monitor 로그인|미리 보기 |미리 보기|[Azure Storage 모니터링](../common/monitor-storage.md)|
|스냅샷|미리 보기|미리 보기|[Blob 스냅숏](snapshots-overview.md)|
|정적 웹 사이트|미리 보기|미리 보기|[Azure Storage에서 정적 웹 사이트 호스팅](storage-blob-static-website.md)|
|변경 불가능한 스토리지|미리 보기|미리 보기|[비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](storage-blob-immutable-storage.md)|
|컨테이너 일시 삭제|미리 보기|미리 보기|[컨테이너에 대 한 일시 삭제 (미리 보기)](soft-delete-container-overview.md)|
|Blob 일시 삭제|아직 지원되지 않음|아직 지원되지 않음|[Blob에 대한 일시 삭제](storage-blob-soft-delete.md)|
|Blobfuse|일반 공급|일반 공급|[blobfuse를 사용하여 Blob Storage를 파일 시스템으로 탑재하는 방법](storage-how-to-mount-container-linux.md)|
|계정 장애 조치(failover)|아직 지원되지 않음|아직 지원되지 않음|[재해 복구 및 계정 장애 조치(failover)](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 컨테이너 ACL|지원되지 않음<div role="complementary" aria-labelledby="blob-container-ACL"><sup>1</sup></div>|지원되지 않음<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|이 표 아래의 관련 정보를 참조 하세요.|
|고객이 제공한 키|아직 지원되지 않음|아직 지원되지 않음|[Blob 저장소에 대 한 요청에 암호화 키 제공](encryption-customer-provided-keys.md)|
|사용자 지정 도메인|아직 지원되지 않음|아직 지원되지 않음|[Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](storage-custom-domain-name.md)|
|암호화 범위|아직 지원되지 않음|아직 지원되지 않음|[암호화 범위 만들기 및 관리 (미리 보기)](encryption-scope-manage.md)|
|변경 피드|아직 지원되지 않음|아직 지원되지 않음|[Azure Blob Storage의 변경 피드 지원](storage-blob-change-feed.md)|
|개체 복제|아직 지원되지 않음|아직 지원되지 않음|[블록 blob에 대 한 개체 복제 구성](object-replication-configure.md)|
|Blob 버전 관리|아직 지원되지 않음|아직 지원되지 않음|[Blob 버전 관리 설정 및 관리](versioning-enable.md)|

<div id="blob-container-ACL"><sup>1</sup> 컨테이너의 루트 폴더에 대 한 acl을 설정할 수 있지만 컨테이너 자체는 설정할 수 없습니다.</div><br>

<div id="preview-form"><sup>2</sup> Data Lake Storage Gen2를 사용 하 여 스냅숏, 변경할 수 없는 저장소 또는 정적 웹 사이트를 사용 하려면이 <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>양식을</a>완료 하 여 미리 보기에 등록 해야 합니다.  </div>

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)
- [Azure Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage에 대한 다중 프로토콜 액세스](data-lake-storage-multi-protocol-access.md)
