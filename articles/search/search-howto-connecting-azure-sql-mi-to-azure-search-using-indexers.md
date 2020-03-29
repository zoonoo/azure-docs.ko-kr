---
title: 검색 인덱싱을 위한 Azure SQL 관리 인스턴스 연결
titleSuffix: Azure Cognitive Search
description: 공용 끝점을 사용하도록 설정하여 Azure Cognitive Search의 인덱서에서 SQL 관리 형 인스턴스에 대한 연결을 허용합니다.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964892"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Azure 인지 검색 인덱서에서 SQL 관리 인스턴스로의 연결 구성

[인덱서를 사용하여 Azure SQL 데이터베이스에 Azure 인지 검색에 연결하는](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)데 언급했듯이 공용 끝점을 통해 Azure 인지 검색에서 **SQL 관리형 인스턴스에** 대한 인덱서를 만드는 것이 지원됩니다.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>공용 끝점을 사용하여 Azure SQL 관리 인스턴스 만들기
**공용 끝점 활성화** 옵션을 선택한 SQL 관리 인스턴스를 만듭니다.

   ![공용 엔드포인트 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "공용 엔드포인트 사용")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Azure SQL 관리 인스턴스 공용 끝점 사용
**보안** > **가상 네트워크** > 공용 끝점 사용에서 기존 SQL 관리 인스턴스에서**공용 끝점을** > 활성화할 수도**있습니다.**

   ![공용 엔드포인트 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "공용 엔드포인트 사용")

## <a name="verify-nsg-rules"></a>NSG 규칙 확인
네트워크 보안 그룹에 Azure 서비스의 연결을 허용하는 올바른 **인바운드 보안 규칙이** 있는지 확인합니다.

   ![NSG 인바운드 보안 규칙](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 인바운드 보안 규칙")

> [!NOTE]
> 인덱서에서는 데이터를 읽기 위해 SQL 관리 인스턴스를 공용 끝점으로 구성해야 합니다.
> 그러나 현재 규칙()을`public_endpoint_inbound`다음 2개의 규칙으로 대체하여 해당 공용 끝점에 대한 인바운드 액세스를 제한하도록 선택할 수 있습니다.
>
> * `AzureCognitiveSearch` [서비스 태그에서](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) 인바운드 액세스 허용("소스" = `AzureCognitiveSearch` `cognitive_search_inbound`" 이름" = ) )
>
> * 완전히 정규화된 도메인 이름(예: `<your-search-service-name>.search.windows.net`)을 ping하여 얻을 수 있는 검색 서비스의 IP 주소에서 인바운드 액세스를 허용합니다. ("소스" `IP address`= " 이름" = `search_service_inbound`)
>
> 이러한 2개의 규칙 각각에 대해 `3342`"PORT" = `TCP`" 프로토콜" `Any`= " 대상" = "`Allow`

## <a name="get-public-endpoint-connection-string"></a>공용 끝점 연결 문자열 받기
**공용 끝점(포트** 1433이 아닌 포트 3342)에 대한 연결 문자열을 사용해야 합니다.

   ![공용 끝점 연결 문자열](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "공용 끝점 연결 문자열")

## <a name="next-steps"></a>다음 단계
구성을 사용하지 않으면 이제 SQL 관리 인스턴스를 포털 또는 REST API를 사용하여 Azure 인지 검색 인덱서의 데이터 원본으로 지정할 수 있습니다. 자세한 내용은 [인덱서를 사용하여 Azure SQL 데이터베이스에 Azure 인지 검색에 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 을 참조하십시오.
