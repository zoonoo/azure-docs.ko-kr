---
title: Text Analytics API에 등록
titleSuffix: Azure Cognitive Services
description: Text Analytics 서비스에 가입하고 사용하기 위한 지침입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 53532a19482a33f8727e71d44ae169225b5b1c98
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449966"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>Text Analytics API에 등록하는 방법

Text Analytics 리소스는 클라우드에서 연중무휴 24시간 내내 사용할 수 있습니다. 분석할 콘텐츠를 업로드하려면 먼저 등록하여 액세스 키를 얻어야 합니다. API를 호출할 때마다 요청에 대한 액세스 키가 필요합니다.

+ Azure 구독을 시작하세요. [체험 계정](https://azure.microsoft.com/free/)을 만들어 추가 비용 없이 사용해 볼 수 있습니다.

+ [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만들고, **텍스트 분석 API**를 선택합니다. 등록하면 키가 생성됩니다.

Text Analytics의 경우 탐색 및 평가용 무료 계층과 프로덕션 워크로드용 청구 가능 계층이 있습니다. 각 구독에서 여러 등록을 할 수 있습니다: 무료 하나, 유료 하나 등등. 요청 볼륨이 증가하면 더 많은 트랜잭션을 제공하는 계층으로 전환할 수 있습니다.

미리 보기 또는 무료 계층에 대한 서비스 수준 계약은 없습니다. 자세한 내용은 [Cognitive Services에 대한 SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)를 참조하세요.

## <a name="how-to-change-tiers"></a>계층을 변경하는 방법

무료 계층으로 시작한 후 프로덕션 워크로드용 청구 가능 계층으로 전환할 수 있습니다. 표준 청구는 등급별 수준에서 제공됩니다. 계층을 전환하고도 동일한 엔드포인트 및 액세스 키를 유지할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [서비스를 찾습니다](text-analytics-how-to-access-key.md).

2. **가격 계층**을 클릭합니다.

   ![왼쪽 탐색 메뉴의 가격 계층 명령](../media/portal-pricing-tier.png)

3. 계층을 선택하고 **선택**을 클릭합니다.  선택한 항목이 처리되는 즉시 새 한도가 적용됩니다. 

   ![계층 선택 페이지의 타일 및 선택 단추](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>청구 방법

청구는 트랜잭션 수를 기준으로 합니다. 월간 청구 주기로 특정 계층에서 트랜잭션 블록을 구입할 수 있으며, 한도를 초과하면 트랜잭션당 초과 요금이 적용됩니다. 최대 한도를 수시로 초과하는 경우 더 높은 계층으로 전환하는 것이 좋습니다.

자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)를 참조하세요.

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>텍스트 분석 API에서 트랜잭션을 구성하는 요소는 무엇인가요?
문서에 대한 모든 주석은 트랜잭션으로 간주됩니다. Batch 평가 호출은 해당 트랜잭션에서 평가해야 하는 문서의 수도 고려합니다. 따라서 예를 들어 하나의 API 호출에서 감정 분석을 위해 1000개의 문서를 보내면 1000개의 트랜잭션으로 간주됩니다.

## <a name="see-also"></a>참고 항목 

 [Text Analytics 개요](../overview.md)  
 [FAQ(질문과 대답)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [액세스 키 가져오기](text-analytics-how-to-access-key.md)
