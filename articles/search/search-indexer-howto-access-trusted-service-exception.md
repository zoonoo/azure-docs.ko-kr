---
title: 신뢰할 수 있는 서비스 예외를 사용한 Azure Storage에 대한 인덱서 액세스
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 인덱서를 통해 Azure Storage에 안전하게 저장된 데이터에 대해 데이터 액세스를 사용하도록 설정합니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2021
ms.openlocfilehash: db614d99a3c25e6f782c0be6bea95ec82124b8e5
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111554843"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>신뢰할 수 있는 서비스 예외를 사용한 Azure Storage에 대한 인덱서 액세스(Azure Cognitive Search)

Azure Storage 계정의 데이터에 액세스하는 Azure Cognitive Search 서비스의 인덱서는 [신뢰할 수 있는 서비스 예외](../storage/common/storage-network-security.md#exceptions) 기능을 활용하여 데이터에 안전하게 액세스할 수 있습니다. 이 메커니즘은 [IP 방화벽 규칙을 사용한 인덱서 액세스](search-indexer-howto-access-ip-restricted.md) 권한을 부여할 수 없는 고객에게 스토리지 계정의 데이터에 액세스하기 위한 간단하고 안전한 무료 대체 방법을 제공합니다.

> [!NOTE]
> 신뢰할 수 있는 서비스 예외를 통한 스토리지 계정의 데이터 액세스 지원은 Azure Blob Storage 및 Azure Data Lake Gen2 스토리지로 제한됩니다. Azure Table Storage는 지원되지 않습니다.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>1단계: 관리 ID를 사용하여 연결 구성

[관리 ID를 사용하여 Azure Storage 계정에 대한 연결 설정](search-howto-managed-identities-storage.md)의 지침을 따릅니다. 작업이 완료되면 검색 서비스를 Azure Active Directory에 신뢰할 수 있는 서비스로 등록하고, Azure Storage에서 데이터 또는 정보에 액세스할 수 있는 특정 권한을 검색 ID에 부여합니다.

> [!NOTE]
> 지침은 Cognitive Search를 신뢰할 수 있는 서비스로 구성하기 위한 포털 접근 방식을 안내합니다. 코드에서 이 작업을 수행하려면 [REST API](/rest/api/searchmanagement/services/createorupdate), [Azure PowerShell](search-manage-powershell.md#create-a-service-with-a-system-assigned-managed-identity) 또는 [Azure CLI](search-manage-azure-cli.md#create-a-service-with-a-system-assigned-managed-identity)를 사용할 수 있습니다.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>2단계: 신뢰할 수 있는 Microsoft 서비스가 스토리지 계정에 액세스하도록 허용

Azure Portal에서 스토리지 계정의 **방화벽 및 가상 네트워크** 탭으로 이동합니다. **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용** 옵션이 선택되어 있는지 확인합니다. 이 옵션은 IP 방화벽 규칙으로 보호된 경우에도 스토리지 계정에 대한 적절한 역할 기반 액세스 권한(강력한 인증)을 가진 특정 검색 서비스 인스턴스만 스토리지 계정의 데이터에 액세스하도록 허용합니다.

![신뢰할 수 있는 서비스 예외](media\search-indexer-howto-secure-access\exception.png "신뢰할 수 있는 서비스 예외")

이제 인덱서는 계정이 IP 방화벽 규칙을 통해 보호된 경우에도 스토리지 계정의 데이터에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Storage 인덱서에 대한 자세한 정보:

- [Azure Blob 인덱서](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 인덱서](search-howto-index-azure-data-lake-storage.md)
- [Azure 테이블 인덱서](search-howto-indexing-azure-tables.md)