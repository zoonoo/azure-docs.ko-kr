---
title: 신뢰할 수 있는 서비스 예외를 통해 저장소에 대 한 액세스 허용
titleSuffix: Azure Cognitive Search
description: 저장소 계정에서 안전 하 게 데이터에 액세스 하는 신뢰할 수 있는 서비스 예외를 설정 하는 방법을 설명 하는 방법 가이드입니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 30fc71e6f59766a759cdb8e4e503123623f48bd9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320475"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>신뢰할 수 있는 서비스 예외를 통해 안전 하 게 저장소 계정에서 데이터 액세스

저장소 계정의 데이터에 액세스 하는 인덱서는 신뢰할 수 있는 [서비스 예외](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) 기능을 사용 하 여 데이터에 안전 하 게 액세스할 수 있습니다. 이 메커니즘은 저장소 계정의 데이터에 액세스 하는 데 사용할 수 있는 간단 하 고 안전 하며 무료 대안이 [IP 방화벽 규칙을 통해 인덱서 액세스](search-indexer-howto-access-ip-restricted.md) 를 부여할 수 없는 고객을 제공 합니다.

> [!NOTE]
> 신뢰할 수 있는 서비스 예외를 통해 저장소 계정의 데이터에 대 한 액세스 지원은 Azure Blob storage 및 Azure Data Lake Gen2 저장소로 제한 됩니다. Azure table storage는 지원 되지 않습니다.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>1 단계: id를 통해 저장소 계정에 대 한 연결 구성

[관리 id 액세스 가이드](search-howto-managed-identities-storage.md) 에 설명 된 세부 정보에 따라 검색 서비스의 관리 id를 통해 저장소 계정에 액세스 하는 인덱서를 구성 합니다.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>2 단계: 신뢰할 수 있는 Microsoft 서비스에서 저장소 계정에 액세스 하도록 허용

Azure Portal에서 저장소 계정의 "방화벽 및 가상 네트워크" 탭으로 이동 합니다. "신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스할 수 있도록 허용" 옵션이 선택 되어 있는지 확인 합니다. 이 옵션은 저장소 계정 (강력한 인증)에 대 한 적절 한 역할 기반 액세스 권한을 가진 특정 검색 서비스 인스턴스만 허용 하 여 IP 방화벽 규칙에 의해 보안이 유지 되는 경우에만 저장소 계정의 데이터에 액세스 합니다.

![신뢰할 수 있는 서비스 예외](media\search-indexer-howto-secure-access\exception.png "신뢰할 수 있는 서비스 예외")

이제 인덱서는 IP 방화벽 규칙을 통해 계정을 보호 하는 경우에도 저장소 계정의 데이터에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Storage 인덱서에 대한 자세한 정보:

- [Azure Blob 인덱서](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 인덱서](search-howto-index-azure-data-lake-storage.md)
- [Azure 테이블 인덱서](search-howto-indexing-azure-tables.md)
