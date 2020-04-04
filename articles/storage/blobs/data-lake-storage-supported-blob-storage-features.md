---
title: Azure 데이터 레이크 스토리지 Gen2에서 사용할 수 있는 Blob 저장소 기능 | 마이크로 소프트 문서
description: Azure 데이터 레이크 스토리지 Gen2에서 사용할 수 있는 Blob 저장소 기능에 대해 알아보기
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b270ce3cd15dbd1e8dd53bd60376a87d6e08f75c
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637208"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure 데이터 레이크 스토리지 Gen2에서 사용할 수 있는 Blob 저장소 기능

[진단 로깅,](../common/storage-analytics-logging.md) [액세스 계층](storage-blob-storage-tiers.md)및 [Blob Storage 수명 주기 관리 정책과](storage-lifecycle-management-concepts.md) 같은 Blob Storage 기능은 이제 계층적 네임스페이스가 있는 계정에서 작동합니다. 따라서 이러한 기능에 대한 액세스 권한을 잃지 않고 Blob 저장소 계정에 계층적 네임스페이스를 활성화할 수 있습니다.

이 표에는 Azure Data Lake 저장소 Gen2에서 사용할 수 있는 Blob 저장소 기능이 나열되어 있습니다. 이러한 테이블에 나타나는 항목은 지원이 계속 확장됨에 따라 시간이 지남에 따라 변경됩니다.

## <a name="supported-blob-storage-features"></a>지원되는 Blob 스토리지 기능

> [!NOTE]
> 지원 수준은 데이터 레이크 스토리지 Gen2에서 이 기능을 지원하는 방법을 나타냅니다.

|Blob 스토리지 기능 |지원 수준 |관련 문서 |
|---------------|-------------------|---|
|핫 액세스 계층|일반 공급|[Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)|
|쿨 액세스 계층|일반 공급|[Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)|
|이벤트|일반 공급|[Blob Storage 이벤트에 대응](storage-blob-event-overview.md)|
|메트릭(클래식)|일반 공급|[Azure 저장소 분석 메트릭(클래식)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Monitor의 메트릭|일반 공급|[Azure Monitor의 Azure Storage 메트릭](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 스토리지 PowerShell 명령|일반 공급|[빠른 시작: PowerShell을 사용 하 고 Blob업로드, 다운로드 및 목록](storage-quickstart-blobs-powershell.md)|
|Blob 저장소 Azure CLI 명령|일반 공급|[빠른 시작: Azure CLI를 사용하여 Blob 만들기, 다운로드 및 목록 작성](storage-quickstart-blobs-cli.md)|
|Blob Storage API|일반 공급|[빠른 시작: .NET에 대 한 Azure Blob 저장소 클라이언트 라이브러리 v12](storage-quickstart-blobs-dotnet.md)<br>[빠른 시작: Java v12 SDK로 Blob 관리](storage-quickstart-blobs-java.md)<br>[빠른 시작 : 파이썬 v12 SDK로 Blob 관리](storage-quickstart-blobs-python.md)<br>[빠른 시작: Node.js에서 자바 스크립트 v12 SDK로 Blob관리](storage-quickstart-blobs-nodejs.md)|
|아카이브 액세스 계층|미리 보기|[Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)|
|수명 주기 관리 정책|미리 보기|[Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)|
|진단 로그|일반 공급|[Azure Storage 분석 로깅](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|변경 피드|아직 지원되지 않음|[Azure Blob 저장소에서 피드 지원 변경](storage-blob-change-feed.md)|
|계정 장애 조치|아직 지원되지 않음|[재해 복구 및 계정 장애 조치](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 컨테이너 ACL|아직 지원되지 않음|[컨테이너 ACL 설정](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|사용자 지정 도메인|아직 지원되지 않음|[Azure Blob 저장소 끝점에 사용자 지정 도메인 매핑](storage-custom-domain-name.md)|
|변경할 수 없는 스토리지|아직 지원되지 않음|[변경할 수 없는 저장소로 비즈니스에 중요한 Blob 데이터 저장](storage-blob-immutable-storage.md)|
|스냅샷|아직 지원되지 않음|[.NET에서 Blob 스냅샷 생성 및 관리](storage-blob-snapshots.md)|
|일시 삭제|아직 지원되지 않음|[Azure Storage Blob에 대한 일시 삭제](storage-blob-soft-delete.md)|
|정적 웹 사이트|아직 지원되지 않음|[Azure Storage에서 정적 웹 사이트 호스팅](storage-blob-static-website.md)|
|Azure 모니터에 로그인|아직 지원되지 않음|아직 사용할 수 없음|
|프리미엄 블록 Blob|아직 지원되지 않음|[블록블블스토리지 계정 만들기](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)
- [Azure 데이터 레이크 저장소 Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)
- [Azure 데이터 레이크 스토리지 Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage에 대한 다중 프로토콜 액세스](data-lake-storage-multi-protocol-access.md)