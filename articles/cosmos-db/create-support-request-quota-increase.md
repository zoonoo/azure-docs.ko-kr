---
title: Azure Cosmos DB 리소스에 대 한 할당량 증가를 요청 하는 방법
description: Azure Cosmos DB 리소스에 대 한 할당량 증가를 요청 하는 방법을 알아봅니다. 구독에서 지역에 액세스 하도록 설정 하는 방법에 대해서도 설명 합니다.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: cd2bc3ec7e70049ee7e2c700731515a272e541dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097986"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Azure Cosmos DB 리소스에 대 한 할당량 증가를 요청 하는 방법

Azure Cosmos DB의 리소스에는 [기본 할당량/제한이](concepts-limits.md)있습니다. 그러나 작업에 기본값 보다 더 많은 할당량이 필요한 경우를 들 수 있습니다. 이 경우 할당량 증가를 요청 하려면 Azure Cosmos DB 팀에 문의 해야 합니다. 이 문서에서는 Azure Cosmos DB 리소스에 대 한 할당량 증가를 요청 하는 방법을 설명 합니다. 구독에서 지역에 액세스 하도록 설정 하는 방법에 대해서도 설명 합니다.

## <a name="create-a-new-support-request"></a>새 지원 요청 만들기

할당량 증가를 요청 하려면 작업 세부 정보를 사용 하 여 새 지원 요청을 만들어야 합니다. 그러면 Azure Cosmos DB 팀이 요청을 평가 하 고 승인 합니다. Azure Portal에서 새 지원 요청을 만들려면 다음 단계를 사용 합니다.

1. Azure Portal에 로그인합니다.

1. 왼쪽 메뉴에서 **도움말 + 지원** 을 선택 하 고 **새 지원 요청**을 선택 합니다.

1. **기본 사항** 탭에서 다음 세부 정보를 입력 합니다.

   * **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 을 선택합니다.
   * **구독**에서 할당량을 증가 시킬 구독을 선택 합니다.
   * **할당량 유형**에 대해 **Cosmos DB** 를 선택 합니다.

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="할당량 증가에 대 한 새 Cosmos DB 지원 요청 만들기":::

1. **세부 정보** 탭에서 할당량 요청에 해당 하는 세부 정보를 입력 합니다. 이 탭에 제공 된 정보는 문제를 추가로 평가 하 고 지원 엔지니어가 문제를 해결 하는 데 도움이 됩니다.

1. 다음 세부 정보를이 형식으로 입력 합니다.

   * **설명**: 워크 로드와 같은 요청에 대 한 간단한 설명을 제공 합니다. 기본값이 충분 하지 않습니다. 할당량 증가를 원하는 리소스 종류에 따라 **설명** 필드에 다음 세부 정보를 제공 해야 합니다.

     **지역 요청** 요청이 허용 목록에 영역을 추가 하는 것과 일치 하는 경우 다음 값을 제공 해야 합니다.

        * 지역 이름
        * 구독 ID

     **처리량 제한 요청** 요청이 처리량 할당량을 증가 시키는 것에 해당 하는 경우 다음 값을 제공 해야 합니다.

        * 데이터베이스 이름
        * 구독 ID
        * 새 처리량 제한

     **데이터베이스 계정 제한 요청** 요청이 구독의 데이터베이스 계정 수에 대 한 할당량을 증가 시키는 것에 해당 하는 경우 다음 값을 제공 해야 합니다.

       * 구독 ID
       * 새 데이터베이스 계정 제한

   * **파일 업로드**: 지원 요청에 관련 된 것으로 생각 되는 진단 파일 또는 다른 파일을 업로드 합니다. 파일 업로드 지침에 대해 자세히 알아보려면 [Azure 지원]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) 문서를 참조 하세요.

   * **심각도**: 비즈니스 영향을 기준으로 사용 가능한 심각도 수준 중 하나를 선택 합니다.

   * **기본 연락 방법**: **전자 메일** 또는 **전화로**연락 하도록 선택할 수 있습니다.

1. 양식의 가용성, 지원 언어, 연락처 정보, 전자 메일 및 전화 번호와 같은 나머지 세부 정보를 입력 합니다.

1. **다음: 검토 + 만들기**를 선택 합니다. 제공 된 정보의 유효성을 검사 하 고 **만들기** 를 선택 하 여 지원 요청을 만듭니다.

24 시간 이내에 Azure Cosmos DB 지원 팀은 요청을 평가 하 고 사용자에 게 다시 문의 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 기본 서비스 할당량](concepts-limits.md) 을 참조 하세요.
