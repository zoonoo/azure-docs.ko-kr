---
title: 검색 인덱싱에 대 한 Azure SQL Managed Instance 연결-Azure Search
description: Azure Search의 인덱서에서 SQL 관리 되는 인스턴스에 대 한 연결을 허용 하도록 공용 끝점을 설정 합니다.
author: vl8163264128
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: 07bc1a55c0222fda87b28acbaa8bfe552fb8e6ed
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186632"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Azure Search 인덱서에 SQL Managed Instance에 대 한 연결 구성
[인덱서를 사용 하 여 Azure Search에 Azure SQL Database 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)에 설명 된 대로 **SQL 관리** 되는 인스턴스에 대 한 인덱서 생성은 공용 끝점을 통해 Azure Search에서 지원 됩니다.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>공용 끝점을 사용 하 여 Azure SQL Managed Instance 만들기
**공용 끝점 사용** 옵션을 선택 하 여 SQL Managed Instance를 만듭니다.

   ![공용 끝점 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "공용 끝점 사용")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Azure SQL Managed Instance 공용 끝점 사용
**보안**가상네트워크 > **공용 끝점**사용에서 기존 SQL Managed Instance에 대 한 공용 끝점을 사용 하도록 설정할 수도 있습니다. >  > 

   ![공용 끝점 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "공용 끝점 사용")

## <a name="verify-nsg-rules"></a>NSG 규칙 확인
네트워크 보안 그룹에 Azure 서비스 로부터의 연결을 허용 하는 올바른 **인바운드 보안 규칙이** 있는지 확인 합니다.

   ![Nsg 인바운드 보안 규칙](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Nsg 인바운드 보안 규칙")

## <a name="get-public-endpoint-connection-string"></a>공용 끝점 연결 문자열 가져오기
**공용 끝점** 에 대 한 연결 문자열을 사용 해야 합니다 (포트 1433이 아닌 포트 3342).

   ![공용 끝점 연결 문자열](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "공용 끝점 연결 문자열")

## <a name="next-steps"></a>다음 단계
이제 구성에서 포털 또는 REST API를 사용 하 여 SQL Managed Instance를 Azure Search 인덱서의 데이터 원본으로 지정할 수 있습니다. 자세한 내용은 [인덱서를 사용하여 Azure Search에 Azure SQL Database 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)을 참조하세요.
