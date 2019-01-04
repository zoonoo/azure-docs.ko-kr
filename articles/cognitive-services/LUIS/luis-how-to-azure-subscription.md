---
title: Azure 리소스 관리
titleSuffix: Language Understanding - Azure Cognitive Services
description: 이 문서에서는 요금제에 따라 엔드포인트에 대한 무제한 트래픽을 제공하기 위해 LUIS 계정에 대한 엔드포인트 키 요금제를 만듭니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 5d3e837cf644e0cb9b35a0cfc715a9dcace592e3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082988"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Azure 엔드포인트 구독 키 관리

테스트 및 프로토타입에 대해서만 무료(F0) 계층을 사용합니다. 프로덕션 시스템의 경우 [유료](https://aka.ms/luis-price-tier) 계층을 사용합니다. 

> [!NOTE]
> 프로덕션의 엔드포인트 쿼리에 [작성 키](luis-concept-keys.md#authoring-key)를 사용하지 마세요.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>LUIS 엔드포인트 키 만들기

1. **[Microsoft Azure](https://ms.portal.azure.com/)** 에 로그인합니다. 
2. 왼쪽 위 패널에서 녹색 **+** 기호를 클릭하고, 마켓플레이스에서 “LUIS”를 검색한 다음, **Language Understanding**을 클릭하고, **환경 만들기**를 수행하여 LUIS 구독 계정을 만듭니다. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. 계정 이름, 가격 책정 계층 등을 포함한 설정을 사용하여 구독을 구성합니다. 

    ![Azure API 선택](./media/luis-azure-subscription/azure-api-choice.png) 

4. LUIS 서비스를 만든 후 **리소스 관리 -> 키**에서 생성된 액세스 키를 볼 수 있습니다.  

    ![Azure 키](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > 해당 지역의 [LUIS](luis-reference-regions.md) 웹 사이트에 로그인하여 [새로운 LUIS 엔드포인트 키를 할당](luis-how-to-manage-keys.md#assign-endpoint-key)합니다. 3단계의 LUIS 구독 이름이 필요합니다.

## <a name="change-luis-pricing-tier"></a>LUIS 가격 책정 계층 변경

1.  [Azure](https://portal.azure.com)에서 LUIS 구독을 찾습니다. LUIS 구독을 클릭합니다.
    ![LUIS 구독 찾기](./media/luis-usage-tiers/find.png)
2.  사용 가능한 가격 책정 계층을 보려면 **가격 책정 계층**을 클릭합니다. 
    ![가격 책정 계층 보기](./media/luis-usage-tiers/subscription.png)
3.  가격 책정 계층을 클릭하고 **선택**을 클릭하여 변경 내용을 저장합니다. 
    ![LUIS 결제 계층 변경](./media/luis-usage-tiers/plans.png)
4.  가격 변경이 완료되면 팝업 창에서 새로운 가격 책정 계층을 확인합니다. 
    ![LUIS 결제 계층 확인](./media/luis-usage-tiers/updated.png)
5. **게시** 페이지에서 [이 엔드포인트 키를 할당](luis-how-to-manage-keys.md#assign-endpoint-key)하고 모든 엔드포인트 쿼리에서 이 엔드포인트 키를 사용해야 합니다. 

## <a name="exceed-pricing-tier-usage"></a>가격 책정 계층 사용량 초과
각 계층은 특정 요금으로 LUIS 계정에 대한 엔드포인트 요청을 허용합니다. 요청 요금이 분당 또는 월별 계정 요금제의 허용된 요금보다 높은 경우 요청은 HTTP 오류 “429: 요청이 너무 많음”을 수신합니다.

각 계층은 월별 누적 요청을 허용합니다. 총 요청이 허용된 요금보다 높은 경우 요청은 HTTP 오류 “403: 금지됨”을 수신합니다.  

## <a name="viewing-summary-usage"></a>요약 사용량 보기
Azure에서 LUIS 사용량 정보를 볼 수 있습니다. **개요** 페이지에는 호출 및 오류를 포함한 최근 요약 정보가 표시됩니다. LUIS 엔드포인트 요청을 만들고 나서 **개요 페이지**를 즉시 확인할 경우 사용량이 표시되는 데 최대 5분이 걸릴 수 있습니다.

![요약 사용량 보기](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>사용 현황 차트 사용자 지정
메트릭은 데이터에 대한 더 자세한 보기를 제공합니다.

![기본 메트릭](./media/luis-usage-tiers/metrics-default.png)

기간 및 메트릭 유형에 대한 메트릭 차트를 구성할 수 있습니다. 

![사용자 지정 메트릭](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>총 트랜잭션 임계값 경고
언제 특정 트랜잭션 임계값(예: 10,000개의 트랜잭션)에 도달했는지 확인하려는 경우 경고를 만들 수 있습니다. 

![기본 경고](./media/luis-usage-tiers/alert-default.png)

특정 기간 동안 **총 호출** 메트릭에 대한 메트릭 경고를 추가합니다. 경고를 받아야 하는 모든 사람의 메일 주소를 추가합니다. 경고를 받아야 하는 모든 시스템의 웹후크를 추가합니다. 경고가 트리거될 때 논리 앱을 실행할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

[버전](luis-how-to-manage-versions.md)을 사용하여 LUIS 앱에 대한 변경 내용을 관리하는 방법에 대해 알아봅니다.