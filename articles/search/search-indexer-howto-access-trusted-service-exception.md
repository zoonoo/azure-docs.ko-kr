---
title: 신뢰할 수 있는 서비스 예외를 사용 하 여 Azure Storage에 대 한 인덱서 액세스
titleSuffix: Azure Cognitive Search
description: Azure Storage에서 안전 하 게 저장 된 데이터에 대 한 Azure Cognitive Search의 인덱서에서 데이터 액세스를 사용 하도록 설정 합니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101378"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>신뢰할 수 있는 서비스 예외를 사용 하 여 Azure Storage에 대 한 인덱서 액세스 (Azure Cognitive Search)

Azure Storage 계정의 데이터에 액세스 하는 Azure Cognitive Search 서비스의 인덱서는 신뢰할 수 있는 [서비스 예외](../storage/common/storage-network-security.md#exceptions) 기능을 사용 하 여 데이터에 안전 하 게 액세스할 수 있습니다. 이 메커니즘은 [IP 방화벽 규칙을 사용 하 여 인덱서 액세스](search-indexer-howto-access-ip-restricted.md) 를 부여할 수 없는 고객에 게 저장소 계정의 데이터에 액세스 하기 위한 간단 하 고 안전한 무료 대체 방법을 제공 합니다.

> [!NOTE]
> 신뢰할 수 있는 서비스 예외를 통해 저장소 계정의 데이터에 액세스 하는 기능은 Azure Blob storage 및 Azure Data Lake Gen2 저장소로 제한 됩니다. Azure Table storage는 지원 되지 않습니다.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>1 단계: 관리 되는 id를 사용 하 여 연결 구성

[관리 id를 사용 하 여 Azure Storage 계정에 대 한 연결 설정](search-howto-managed-identities-storage.md)의 지침을 따릅니다. 작업이 완료 되 면 검색 서비스를 신뢰할 수 있는 서비스로 Azure Active Directory 등록 하 고, 데이터 또는 정보에 액세스 하는 검색 id에 대 한 특정 권한을 제공 하는 Azure Storage에서 권한을 부여 받습니다.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>2 단계: 신뢰할 수 있는 Microsoft 서비스에서 저장소 계정에 액세스 하도록 허용

Azure Portal에서 저장소 계정의 **방화벽 및 가상 네트워크** 탭으로 이동 합니다. **신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스할 수 있도록 허용** 옵션이 선택 되어 있는지 확인 합니다. 이 옵션은 저장소 계정 (강력한 인증)에 대 한 적절 한 역할 기반 액세스 권한을 가진 특정 검색 서비스 인스턴스만 허용 하 여 IP 방화벽 규칙에 의해 보안이 유지 되는 경우에만 저장소 계정의 데이터에 액세스 합니다.

![신뢰할 수 있는 서비스 예외](media\search-indexer-howto-secure-access\exception.png "신뢰할 수 있는 서비스 예외")

이제 인덱서는 IP 방화벽 규칙을 통해 계정을 보호 하는 경우에도 저장소 계정의 데이터에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Storage 인덱서에 대한 자세한 정보:

- [Azure Blob 인덱서](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 인덱서](search-howto-index-azure-data-lake-storage.md)
- [Azure 테이블 인덱서](search-howto-indexing-azure-tables.md)