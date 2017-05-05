---
title: "Azure 지출 한도 이해 | Microsoft Docs"
description: "Azure 지출 한도의 작동 방식 및 제거 방법을 설명합니다."
services: 
documentationcenter: 
author: genlin
manager: vikdesai
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 39ae134d8927f5123667b64bbd0c659cd5f62ffc
ms.lasthandoff: 04/21/2017


---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Azure 지출 한도의 작동 방식 및 제거 방법 이해

Azure 지출 한도는 Azure 구독이 소비할 수 있는 지출을 제한합니다. 평가판 제안이나 여러 달의 크레딧을 포함하는 제안에 등록하는 모든 신규 고객은 기본적으로 지출 한도가 설정됩니다. 지출 한도는 $0입니다. 이 값은 변경할 수 없습니다. 종량제 구독 및 약정 플랜과 같은 구독 유형에는 지출 한도를 사용할 수 없습니다. [전체 Azure 제품 목록 및 지출 한도 가용성](https://azure.microsoft.com/support/legal/offer-details/)를 참조하세요.

## <a name="what-happens-when-i-reach-the-spending-limit"></a>지출 한도에 도달하면 어떻게 되나요?

제안에 포함된 월별 할당량을 모두 사용할 경우 해당 청구월의 나머지 기간 동안 배포한 서비스가 비활성화됩니다. 예를 들어 배포한 Cloud Services가 프로덕션에서 제거되고 Azure Virtual Machines가 중지되고 할당 최소됩니다. 서비스 비활성화를 방지하려면 지출 한도를 제거할 수 있습니다. 서비스가 비활성화되면 저장소 계정 및 데이터베이스의 데이터는 관리자가 읽기 전용으로 사용할 수 있습니다. 다음 청구 달이 시작하면 제품에 여러 달에 대한 크레딧이 포함될 경우 구독이 다시 사용하도록 설정됩니다. 그런 다음 Cloud Services를 다시 배포하고 저장소 계정 및 데이터베이스에 대한 모든 권한을 가질 수 있습니다.

무료 평가판 구독이 지출 한도에 도달한 후에는 90일 이내에 구독을 다시 사용하도록 설정하고 자동으로 [표준 종량제 제품으로 업그레이드](billing-upgrade-azure-subscription.md)할 수 있습니다.

제품에 포함된 지출 한도에 도달할 경우 알림을 받게 됩니다. [Azure 계정 센터](https://account.windowsazure.com)에 로그인하고 **계정**을 선택한 다음 **구독**을 선택합니다. 지출 한도에 도달한 구독에 대한 알림이 표시됩니다.

## <a name="things-you-are-charged-for-even-if-you-have-a-spending-limit-enabled"></a>이 경우 지출 한도를 사용하도록 설정한 경우에도 요금이 부과됩니다.

일부 Azure 서비스 및 [Marketplace 구매 제품](https://azure.microsoft.com/marketplace/)의 경우 지출 한도가 설정되어 있어도 CC(결제 방법)에 따라 요금이 발생할 수 있습니다. Visual Studio 라이선스, Azure Active Directory Premium, 지원 플랜 및 Marketplace에서 판매되는 대부분의 타사 브랜드 서비스를 예로 들 수 있습니다.


## <a name="when-not-to-use-the-spending-limit"></a>지출 한도를 사용하지 않아야 하는 경우

지출 한도에 도달하면 특정 마켓플레이스 및 Microsoft 서비스를 배포 또는 사용할 수 없게 됩니다. 다음은 구독에 대한 지출 한도를 제거해야 하는 시나리오입니다.

- Oracle, Visual Studio Team Services 등의 서비스와 같은 자사 이미지를 배포할 계획인 경우. 이 경우 지출 한도가 거의 순식간에 초과되어 구독이 비활성화됩니다.

- 중단하면 안 되는 서비스가 있는 경우

- 손실되지 않아야 하는 가상 IP 주소와 같은 설정을 사용하는 서비스 및 리소스가 있는 경우. 서비스 및 리소스의 할당이 취소되면 이러한 설정이 손실됩니다.


## <a name="remove-the-spending-limit"></a>지출 한도 제거

유효한 결제 방법이 구독과 연결되어 있는 경우 언제든 지출 한도를 제거할 수 있습니다. 여러 달의 크렛딧을 포함하는 제안의 경우 다음 청구 주기가 시작할 때 지출 한도를 다시 사용하도록 설정할 수도 있습니다.

지출 한도를 제거하려면 다음 단계를 수행합니다.

1. [Azure 계정 센터](https://account.windowsazure.com)에 로그인합니다.

2. 구독을 선택합니다.

3. 지출 한도에 도달하여 구독이 비활성화된 경우 "구독이 지출 한도에 도달하여 요금이 부과되지 않도록 비활성화되었습니다."라는 알림을 클릭합니다. 그렇지 않으면 **구독 상태** 영역에서 **지출 한도 제거**를 클릭합니다.

4. 적절한 옵션을 선택합니다.

|옵션|결과|
|-------|-----|
|지출 한도 무기한 제거|다음 청구 기간 시작 시 자동으로 설정되지 않도록 지출 한도를 제거합니다.|
|현재 청구 기간에 대한 지출 한도 제거|다음 청구 기간 시작 시 자동으로 다시 설정되도록 지출 한도를 제거합니다.|

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요.
다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
