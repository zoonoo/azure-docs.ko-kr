---
title: 검색 인덱싱을 위한 Azure SQL Managed Instance 연결
titleSuffix: Azure Cognitive Search
description: 퍼블릭 엔드포인트를 통해 Azure Cognitive Search 인덱서에서 SQL Managed Instance로 연결할 수 있도록 합니다.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e8625724f67caac99ae799674f9db9399e11ad8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89294257"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Azure Cognitive Search 인덱서와 SQL Managed Instance 간 연결 구성

[인덱서를 사용하여 Azure Cognitive Search에 Azure SQL Database 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)에 설명된 것처럼 Azure Cognitive Search에서는 퍼블릭 엔드포인트를 통해 **SQL Managed Instance** 에 대한 인덱서를 만들 수 있습니다.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>퍼블릭 엔드포인트를 사용하여 Azure SQL Managed Instance 만들기
**퍼블릭 엔드포인트 사용** 옵션을 선택하여 SQL Managed Instance를 만듭니다.

   ![공용 엔드포인트 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "공용 엔드포인트 사용")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Azure SQL Managed Instance 퍼블릭 엔드포인트 사용
**보안** > **가상 네트워크** > **퍼블릭 엔드포인트** > **사용** 에서 기존 SQL Managed Instance의 퍼블릭 엔드포인트를 사용하도록 설정할 수도 있습니다.

   ![관리형 인스턴스 VNET을 통해 퍼블릭 엔드포인트 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "공용 엔드포인트 사용")

## <a name="verify-nsg-rules"></a>NSG 규칙 확인
네트워크 보안 그룹에 Azure 서비스의 연결을 허용하는 올바른 **인바운드 보안 규칙** 이 있는지 확인합니다.

   ![NSG 인바운드 보안 규칙](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 인바운드 보안 규칙")

> [!NOTE]
> 인덱서에서는 데이터를 읽기 위해 여전히 퍼블릭 엔드포인트를 사용하여 SQL Managed Instance를 구성해야 합니다.
> 그러나 현재 규칙(`public_endpoint_inbound`)을 다음 두 개의 규칙으로 바꿔 퍼블릭 엔드포인트에 대한 인바운드 액세스를 제한할 수 있습니다.
>
> * `AzureCognitiveSearch` [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)에서 인바운드 액세스 허용(“SOURCE” = `AzureCognitiveSearch`, “NAME” = `cognitive_search_inbound`)
>
> * 정규화된 도메인 이름(예: `<your-search-service-name>.search.windows.net`)을 ping하여 가져올 수 있는 Search Service의 IP 주소에서 인바운드 액세스 허용 (“SOURCE” = `IP address`, “NAME” = `search_service_inbound`)
>
> 두 규칙에서 각각 “PORT” = `3342`, “PROTOCOL” = `TCP`, “DESTINATION” = `Any`, “ACTION” = `Allow`를 설정합니다.

## <a name="get-public-endpoint-connection-string"></a>퍼블릭 엔드포인트 연결 문자열 가져오기
**퍼블릭 엔드포인트**(포트 1433이 아닌 포트 3342)의 연결 문자열을 사용해야 합니다.

   ![퍼블릭 엔드포인트 연결 문자열](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "퍼블릭 엔드포인트 연결 문자열")

## <a name="next-steps"></a>다음 단계
구성이 완료되었으므로 이제 포털 또는 REST API를 사용하여 SQL Managed Instance를 Azure Cognitive Search 인덱서의 데이터 원본으로 지정할 수 있습니다. 자세한 내용은 [인덱서를 사용하여 Azure Cognitive Search에 Azure SQL Database 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)을 참조하세요.