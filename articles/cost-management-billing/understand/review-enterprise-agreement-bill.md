---
title: Azure 기업계약 청구서 검토
description: Azure 기업계약에 대한 사용량 및 청구서를 읽고 이해하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: banders
ms.openlocfilehash: ca1064ce3414f4a796110c022125b68c0674e0d6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270182"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Azure 기업계약 청구서 이해

기업계약을 체결한 Azure 고객은 조직의 크레딧을 초과하거나 크레딧이 적용되지 않는 서비스를 사용할 경우 청구서를 받게 됩니다.

조직의 크레딧에는 현금 약정 금액이 포함됩니다. 현금 약정 금액은 Azure 서비스를 사용하기 위해 조직이 선불로 지급한 금액입니다. Microsoft 계정 관리자나 재판매인에게 연락하여 기업계약에 현금 약정 금액을 추가할 수 있습니다.

이 자습서는 Microsoft 기업계약을 체결한 Azure 고객에게만 적용됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 송장 발부 요금 검토
> * 서비스 초과 요금 검토
> * Marketplace 청구서 검토

## <a name="prerequisites"></a>사전 요구 사항

청구서 요금을 검토하고 확인하려면 엔터프라이즈 관리자여야 합니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](../manage/understand-ea-roles.md)를 참조하세요. 조직의 엔터프라이즈 관리자가 누구인지 모르는 경우 [고객 지원팀에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

## <a name="review-invoiced-charges-for-most-customers"></a>대부분의 고객에 대한 송장 발부 요금 검토

이 섹션은 오스트레일리아, 일본 또는 싱가포르의 Azure 고객에게는 적용되지 않습니다.

청구 기간 중에 다음 이벤트 중 하나가 발생하면 Azure 청구서를 받게 됩니다.

- **서비스 초과**: 조직의 사용 요금이 크레딧 잔액을 초과합니다.
- **별도 청구 요금**: 조직에서 사용한 서비스가 크레딧에 적용되지 않습니다. 다음 서비스에 대해서는 크레딧 잔액과 관계없이 청구서를 받게 됩니다.
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - OpenLogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise(월간)
    - Visual Studio Enterprise(연간)
    - Visual Studio Professional(월간)
    - Visual Studio Professional(연간)
- **Marketplace 요금**: Azure Marketplace의 구매 및 사용량이 조직의 크레딧에 적용되지 않습니다. 따라서 Marketplace 요금에 대해서는 크레딧 잔액과 관계없이 청구서를 받게 됩니다. Enterprise Portal에서 엔터프라이즈 관리자는 Marketplace 구매를 사용하거나 사용하지 않도록 설정할 수 있습니다.

청구서에는 Azure 사용량 요금과 관련 비용이 먼저 표시되고, 그 다음에 마켓플레이스 요금이 표시됩니다. 크레딧 잔액이 있는 경우 Azure 사용량에 적용되며, Azure 사용량과 마켓플레이스 사용량이 아무런 비용 없이 청구서에 표시됩니다.

Enterprise Portal의 **보고서** > **사용량 요약**에 표시된 총 결합 금액을 Azure 청구서와 비교합니다. **사용량 요약**의 금액에는 세금이 포함되어 있지 않습니다.

[Azure EA Portal](https://ea.azure.com)에 로그인합니다. 그런 다음, **보고서**를 선택합니다. 탭의 오른쪽 맨 위 모서리에서 보기를 **M**에서 **C**로 전환하고 청구서의 기간과 똑같이 설정합니다.  

![사용 요약의 M + C 옵션을 보여주는 스크린샷.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**총 사용량** 및 **Azure Marketplace**의 결합된 금액은 송장의 **총 확장 금액**과 일치해야 합니다. 요금에 대한 자세한 내용을 보려면 **사용량 다운로드**로 이동하세요.  

![사용량 다운로드 탭을 보여주는 스크린샷](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>기타 고객에 대한 송장 발부 요금 검토

이 섹션은 오스트레일리아, 일본 또는 싱가포르의 Azure 고객에게만 적용됩니다.

다음 이벤트 중 하나라도 발생하면 하나 이상의 Azure 청구서를 받게 됩니다.

- **서비스 초과**: 조직의 사용 요금이 크레딧 잔액을 초과합니다.
- **별도 청구 요금**: 조직에서 사용한 서비스가 크레딧에 적용되지 않습니다. 다음 서비스에 대해 송장이 청구됩니다.
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - OpenLogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise(월간)
    - Visual Studio Enterprise(연간)
    - Visual Studio Professional(월간)
    - Visual Studio Professional(연간)
- **Marketplace 요금**: Azure Marketplace의 구매 및 사용량ㅇ은 조직의 크레딧에 적용되지 않으며 요금이 별도로 청구됩니다. Enterprise Portal에서 엔터프라이즈 관리자는 Marketplace 구매를 사용하거나 사용하지 않도록 설정할 수 있습니다.

청구 기간 동안 서비스 초과분 요금과 별도 청구 요금이 부과되는 경우 청구서 1개를 받게 됩니다. 여기에는 두 가지 유형의 요금이 모두 포함됩니다. Marketplaces 요금은 항상 별도로 청구됩니다.

## <a name="review-service-overage-charges-for-other-customers"></a>기타 고객에 대한 서비스 초과 요금 검토

이 섹션은 오스트레일리아, 일본 또는 싱가포르에 있는 경우에만 적용됩니다.

Enterprise Portal의 **보고서** > **사용 요약**에 있는 총 사용량 금액을 서비스 초과분 청구서와 비교합니다. 서비스 초과분 청구서에는 조직의 크레딧을 초과한 사용 및/또는 크레딧이 적용되지 않는 서비스가 포함됩니다. **사용 요약**의 금액에는 세금이 포함되어 있지 않습니다.

[Azure EA Portal](https://ea.azure.com)에 로그인한 다음, **보고서**를 선택합니다. 탭의 오른쪽 맨 위 모서리에서 보기를 **M**에서 **C**로 전환하고 청구서의 기간과 똑같이 설정합니다.  

![사용 요약의 M + C 옵션을 보여주는 스크린샷.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**총 사용량** 금액이 서비스 초과분 청구서의 **총 확장 금액**과 일치해야 합니다. 요금에 대한 자세한 내용을 보려면 **사용량 다운로드** > **고급 보고서 다운로드**로 이동합니다. 이 보고서에는 세금, 예약료 또는 Marketplace 요금이 포함되어 있지 않습니다.  

![사용량 다운로드 탭의 고급 보고서 다운로드를 보여 주는 스크린샷](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

다음 표에는 청구서 및 Enterprise Portal의 **사용 요약**에 표시되는 용어 및 설명이 나와 있습니다.

|청구서 용어|사용 요약 용어|Description|
|---|---|---|
|총 확장 금액|총 사용량|차변이 적용되기 전, 특정 기간에 대한 총 세전 사용량 요금입니다.|
|약정 사용량|약정 사용량|해당 특정 기간에 적용된 크레딧입니다.|
|총 판매|총 초과분|크레딧 금액을 초과하는 총 사용량 요금입니다. 이 금액에는 세금이 포함되어 있지 않습니다.|
|세액|해당 없음|특정 기간의 총 판매 금액에 적용되는 세금입니다.|
|총 금액|해당 없음|크레딧이 적용되고 세금이 추가된 후 청구서의 지불액입니다.|

### <a name="review-marketplace-invoice"></a>Marketplace 청구서 검토

이 섹션은 오스트레일리아, 일본 또는 싱가포르에 있는 경우에만 적용됩니다.

Enterprise Portal의 **보고서** > **사용 요약**에 있는 Azure Marketplace 합계와 마켓플레이스 청구서를 비교합니다. 마켓플레이스 청구서는 Azure Marketplace 구매 및 사용에만 해당됩니다. **사용 요약**의 금액에는 세금이 포함되어 있지 않습니다.

[Enterprise Portal](https://ea.azure.com)에 로그인한 다음, **보고서**를 선택합니다. 탭의 오른쪽 맨 위 모서리에서 보기를 **M**에서 **C**로 전환하고 청구서의 기간과 똑같이 설정합니다.  

![사용 요약의 M + C 옵션을 보여 주는 스크린샷](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

**Azure Marketplace** 합계가 마켓플레이스 청구서의 **총 판매**와 일치해야 합니다. 사용량 기반 요금에 대한 자세한 내용을 보려면 **사용량 다운로드**로 이동합니다. **마켓플레이스 요금** 아래에서 **다운로드**를 선택합니다. 마켓플레이스 가격은 게시자가 결정한 세금을 포함합니다. 고객은 트랜잭션에 대한 세금을 징수하기 위해 게시자로부터 별도의 송장을 받지 않습니다.

![마켓플레이스 요금 아래의 다운로드 옵션을 보여 주는 스크린샷](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 송장 발부 요금 검토
> * 서비스 초과 요금 검토
> * Marketplace 청구서 검토

Azure EA Portal을 사용하여 자세히 알아보려면 다음 문서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure EA Portal 시작](../manage/ea-portal-get-started.md)
