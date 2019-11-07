---
title: Azure Data Lake Storage에서 멀티 프로토콜 액세스 Microsoft Docs
description: Azure Data Lake Storage Gen2에서 Blob Api를 사용 하는 응용 프로그램 및 Blob Api를 사용 합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f3e852d178a6720f8d567f4ecb042f0b83bcd56c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583161"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage에 대 한 다중 프로토콜 액세스

Blob Api는 이제 계층 구조가 있는 계정으로 작동 합니다. 이는 도구, 응용 프로그램 및 서비스 에코 시스템의 잠금을 해제 하 고 계층 네임 스페이스가 있는 계정에 몇 가지 Blob storage 기능을 제공 합니다.

최근 까지는 개체 저장소와 분석 저장소에 대 한 별도의 저장소 솔루션을 유지 해야 했을 수 있습니다. Azure Data Lake Storage Gen2는 에코 시스템 지원이 제한 되어 있기 때문입니다. 진단 로깅과 같은 Blob service 기능에 대 한 제한 된 액세스 권한도 있었습니다. 조각화 된 저장소 솔루션은 다양 한 시나리오를 수행 하기 위해 계정 간에 데이터를 이동 해야 하기 때문에 유지 관리가 어렵습니다. 더 이상이 작업을 수행할 필요가 없습니다.

Data Lake Storage에 대 한 다중 프로토콜 액세스를 통해 도구, 응용 프로그램 및 서비스 에코 시스템을 사용 하 여 데이터 작업을 수행할 수 있습니다. 또한 타사 도구 및 응용 프로그램도 포함 됩니다. 계층 구조 네임 스페이스를 수정할 필요 없이 계정으로 지정할 수 있습니다. Blob Api는 이제 계층 네임 스페이스가 있는 계정의 데이터에서 작동할 수 있으므로 이러한 응용 프로그램은 Blob Api를 호출 하는 경우에도 *그대로* 작동 합니다.

[진단 로깅](../common/storage-analytics-logging.md), [액세스 계층](storage-blob-storage-tiers.md)및 [blob 저장소 수명 주기 관리 정책과](storage-lifecycle-management-concepts.md) 같은 blob storage 기능은 이제 계층 구조가 있는 계정으로 작동 합니다. 따라서 이러한 중요 한 기능에 대 한 액세스를 잃지 않고 blob storage 계정에서 계층적 네임 스페이스를 사용 하도록 설정할 수 있습니다. 

> [!NOTE]
> Data Lake Storage에 대 한 다중 프로토콜 액세스는 일반적으로 사용할 수 있으며 모든 지역에서 사용할 수 있습니다. 다중 프로토콜 액세스에서 사용 하도록 설정 된 일부 Azure 서비스 또는 blob storage 기능은 미리 보기로 유지 됩니다. 자세한 내용은이 문서의 각 섹션에 있는 표를 참조 하세요. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data lake storage의 멀티 프로토콜 액세스 작동 방식

Blob Api 및 Data Lake Storage Gen2 Api는 계층적 네임 스페이스를 포함 하는 저장소 계정의 동일한 데이터에서 작동할 수 있습니다. Data Lake Storage Gen2는 계층 구조 네임 스페이스를 통해 Blob Api를 라우팅합니다. 따라서 첫 번째 클래스 디렉터리 작업 및 POSIX 규격 Acl (access control 목록)의 이점을 얻을 수 있습니다. 

![Data Lake Storage 개념에 대 한 다중 프로토콜 액세스](./media/data-lake-storage-interop/interop-concept.png) 

기존 도구와 Blob API를 사용 하는 응용 프로그램은 이러한 혜택을 자동으로 얻습니다. 개발자는이를 수정할 필요가 없습니다. Data Lake Storage Gen2는 도구 및 응용 프로그램에서 데이터에 액세스 하는 데 사용 하는 프로토콜에 관계 없이 디렉터리 및 파일 수준 Acl을 일관 되 게 적용 합니다. 

## <a name="blob-storage-feature-support"></a>Blob storage 기능 지원

Data Lake Storage에 대 한 다중 프로토콜 액세스를 사용 하면 Data Lake Storage에서 더 많은 Blob Storage 기능을 사용할 수 있습니다. 이 표에는 Data Lake Storage에 대 한 다중 프로토콜 액세스에서 사용 하도록 설정 된 기능이 나열 되어 있습니다. 

이 표에 표시 되는 항목은 Blob 저장소 기능에 대 한 지원이 계속 확장 되기 때문에 시간이 지남에 따라 변경 됩니다. 

> [!NOTE]
> Data Lake Storage에 대 한 다중 프로토콜 액세스가 일반적으로 제공 되더라도 이러한 기능 중 일부에 대 한 지원은 미리 보기 상태로 유지 됩니다. 

|Blob storage 기능 | 지원 수준 |
|---|---|
|[쿨 액세스 계층](storage-blob-storage-tiers.md)|일반 공급|
|Blob Sdk |일반 공급|
|[PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |일반 공급|
|[CLI](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |일반 공급|
|[진단 로그](../common/storage-analytics-logging.md)| 미리 보기|
|[수명 주기 관리 정책](storage-lifecycle-management-concepts.md)| 미리 보기|
|[Azure Event Grid를 통한 알림](data-lake-storage-events.md)|미리 보기|
|[보관 액세스 계층](storage-blob-storage-tiers.md)| 미리 보기|
|[blobfuse](storage-how-to-mount-container-linux.md)|아직 지원 되지 않음|
|[변경할 수 없는 저장소](storage-blob-immutable-storage.md)|아직 지원 되지 않음|
|[스냅샷](storage-blob-snapshots.md)|아직 지원 되지 않음|
|[일시 삭제](storage-blob-soft-delete.md)|아직 지원 되지 않음|
|[정적 웹 사이트](storage-blob-static-website.md)|아직 지원 되지 않음|

Azure Data Lake Storage Gen2에 대 한 일반적인 알려진 문제 및 제한 사항에 대 한 자세한 내용은 [알려진 문제](data-lake-storage-known-issues.md)를 참조 하세요.

## <a name="azure-ecosystem-support"></a>Azure 에코 시스템 지원

Data Lake Storage에 대 한 다중 프로토콜 액세스를 사용 하 여 Data Lake Storage와 더 많은 Azure 서비스를 연결할 수도 있습니다. 이 표에는 Data Lake Storage에 대 한 다중 프로토콜 액세스에서 사용 하도록 설정 된 서비스가 나열 되어 있습니다. 

지원 되는 Blob storage 기능 목록과 마찬가지로이 표에 표시 되는 항목은 Azure 서비스에 대 한 지원이 계속 확장 되기 때문에 시간이 지남에 따라 변경 됩니다. 

> [!NOTE]
> Data Lake Storage에 대 한 다중 프로토콜 액세스를 일반적으로 사용할 수 있지만 이러한 서비스 중 일부에 대 한 지원은 미리 보기로 남아 있습니다. 

|Azure 서비스 | 지원 수준 |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|일반 공급|
|[Azure Event Hubs 캡처](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|일반 공급|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|일반 공급|
|[IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|일반 공급|
|[논리 앱](https://azure.microsoft.com/services/logic-apps/)|일반 공급|
|[Azure Search](https://docs.microsoft.com/azure/search/search-blob-storage-integration)|미리 보기|

Data Lake Storage Gen2에 대 한 Azure 에코 시스템 지원의 전체 목록은 [azure 서비스와 Azure Data Lake Storage 통합](data-lake-storage-integrate-with-azure-services.md)을 참조 하세요.

Azure Data Lake Storage Gen2에 대 한 일반적인 알려진 문제 및 제한 사항에 대 한 자세한 내용은 [알려진 문제](data-lake-storage-known-issues.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[알려진 문제](data-lake-storage-known-issues.md) 를 참조 하세요.




