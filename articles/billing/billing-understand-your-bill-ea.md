---
title: Azure Enterprise에 대한 요금 청구 방식 | Microsoft Docs
description: 기업계약을 체결한 Azure 고객에 대한 사용량 및 청구서를 읽고 이해하는 방법에 대한 자세한 정보
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 36ce4d96e02bac1eae1791acf811da468726b4a6
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902706"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>기업계약을 체결한 Azure 고객에 대한 요금 청구 방식

기업계약을 체결한 Azure 고객은 조직의 크레딧을 초과하거나 크레딧이 적용되지 않는 서비스를 사용할 경우 청구서를 받게 됩니다. 

조직의 크레딧에는 현금 약정 금액이 포함됩니다. 현금 약정 금액은 Azure 서비스를 사용하기 위해 조직이 선불로 지급한 금액입니다. Microsoft 계정 관리자나 재판매인에게 연락하여 기업계약에 현금 약정 금액을 추가할 수 있습니다.  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>크레딧이 초과되거나 적용되지 않는 경우

다음과 같은 경우 하나 이상의 청구서를 받게 됩니다.

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
- **Marketplace 요금**: Azure Marketplace의 구매 및 사용이 조직의 크레딧에 적용되지 않으며 요금이 별도로 청구됩니다. 엔터프라이즈 관리자는 Enterprise Portal에서 조직에 대해 Marketplace 구매를 사용/사용 안 함으로 설정할 수 있습니다. 

청구 기간 동안 서비스 초과분 요금과 별도 청구 요금이 둘 다 부과되는 경우 두 요금 유형을 모두 포함하는 청구서 1개를 받게 됩니다. Marketplaces 요금은 항상 별도로 청구됩니다.

## <a name="review-charges"></a>요금 검토

청구서 요금을 검토하고 확인하려면 엔터프라이즈 관리자여야 합니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](billing-understand-ea-roles.md)를 참조하세요. 조직의 엔터프라이즈 관리자가 누구인지 모르는 경우 [고객 지원팀에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

### <a name="review-service-overage-invoice"></a>서비스 초과분 청구서 검토

Enterprise Portal의 **보고서** > **사용 요약**에 있는 총 사용량 금액을 서비스 초과분 청구서와 비교합니다. 서비스 초과분 청구서에는 조직의 크레딧을 초과한 사용 및/또는 크레딧이 적용되지 않는 서비스가 포함됩니다. **사용 요약**의 금액에는 세금이 포함되어 있지 않습니다. 

1. [Enterprise Portal](https://ea.azure.com)에 로그인합니다.
1. **보고서**를 선택합니다.
1. 탭의 오른쪽 맨 위에서 보기를 **M**에서 **C**로 전환하고 청구서의 기간과 똑같이 설정합니다.
 
   ![사용 요약의 M + C 옵션을 보여 주는 스크린샷](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. **총 사용량** 금액이 서비스 초과분 청구서의 **총 확장 금액**과 일치해야 합니다. 다음 표에는 청구서 및 Enterprise Portal의 **사용 요약**에 표시되는 용어 및 설명이 나와 있습니다.

   |청구서 용어|사용 요약 용어|설명|
   |---|---|---|
   |총 확장 금액|총 사용량|차변이 적용되기 전, 특정 기간에 대한 총 세전 사용량 요금입니다.|
   |약정 사용량|약정 사용량|해당 특정 기간에 적용된 크레딧입니다.|
   |총 판매|총 초과분|크레딧 금액을 초과하는 총 사용량 요금입니다. 이 금액에는 세금이 포함되어 있지 않습니다.|
   |세액|해당 없음|특정 기간의 총 판매 금액에 적용되는 세금입니다.|
   |총 금액|해당 없음|크레딧이 적용되고 세금이 추가된 후 청구서의 지불액입니다.|
1. 요금에 대한 자세한 내용을 보려면 **사용량 다운로드** > **고급 보고서 다운로드**로 이동합니다. 이 보고서에는 세금, 예약료 또는 마켓플레이스 요금이 포함되어 있지 않습니다.

      ![사용량 다운로드 탭의 고급 보고서 다운로드를 보여 주는 스크린샷](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>마켓플레이스 청구서 검토

Enterprise Portal의 **보고서** > **사용 요약**에 있는 Azure Marketplace 합계와 마켓플레이스 청구서를 비교합니다. 마켓플레이스 청구서는 Azure Marketplace 구매 및 사용에만 해당됩니다. **사용 요약**의 금액에는 세금이 포함되어 있지 않습니다. 

1. [Enterprise Portal](https://ea.azure.com)에 로그인합니다.
1. **보고서**를 선택합니다.
1. 탭의 오른쪽 맨 위에서 보기를 **M**에서 **C**로 전환하고 청구서의 기간과 똑같이 설정합니다.

     ![사용 요약의 M + C 옵션을 보여 주는 스크린샷](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. **Azure Marketplace** 합계가 마켓플레이스 청구서의 **총 판매**와 일치해야 합니다.
1. 사용량 기반 요금에 대한 자세한 내용을 보려면 **사용량 다운로드**로 이동합니다. **마켓플레이스 요금** 아래에서 **다운로드**를 선택합니다. 이 보고서는 세금을 포함하지 않거나 일회성 구매를 보여 줍니다.

     ![마켓플레이스 요금 아래의 다운로드 옵션을 보여 주는 스크린샷](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
