---
title: Azure 지출 한도 | Microsoft Docs
description: 이 문서에서는 Azure 지출 한도의 작동 방식 및 제거 하는 방법을 설명 합니다.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114445"
---
# <a name="azure-spending-limit"></a>Azure 지출 한도

Azure의 지출 한도는 크레딧 금액을 초과 하는 비용을 방지 합니다. 여러 달 동안의 크레딧을 포함 하는 Azure 평가판 또는 제안에 등록 하는 모든 신규 고객은 기본적으로 지출 한도가 설정 됩니다. 지출 한도는 $0 이며 변경할 수 없습니다. 예를 들어 지출 한도를 $100으로 변경할 수 없습니다. 그러나 지출 한도를 제거할 수 있습니다. 따라서 제한이 없거나 대부분의 지출을 방지 하는 0의 제한이 있습니다. 지출 한도는 종 량 제 가격 책정을 사용 하는 계획 및 계획의 일부 요금제에서 구독에 사용할 수 없습니다. [전체 Azure 제품 목록 및 지출 한도 가용성](https://azure.microsoft.com/support/legal/offer-details/)를 참조하세요.

## <a name="reaching-a-spending-limit"></a>지출 한도에 도달

사용량에 따라 Azure 구독에 포함 된 월별 금액이 고갈 되는 경우 해당 청구 기간의 나머지 기간 동안 배포한 서비스가 비활성화 됩니다.

예를 들어 구독에 포함 된 크레딧을 모두 소비 하는 경우 배포 된 Azure 리소스가 프로덕션에서 제거 되 고 Azure 가상 머신이 중지 되 고 할당 취소 됩니다. 저장소 계정의 데이터는 읽기 전용으로 사용할 수 있습니다.

다음 청구 기간이 시작 될 때 구독 제품에 여러 달 동안의 크레딧이 포함 된 경우 구독이 자동으로 다시 설정 됩니다. 그런 다음 Azure 리소스를 다시 배포 하 고 저장소 계정 및 데이터베이스에 대 한 모든 액세스 권한을 가질 수 있습니다.

구독에 대 한 지출 한도에 도달 하면 Azure에서 전자 메일 알림을 보냅니다. [계정 센터](https://account.windowsazure.com/Subscriptions) 에 로그인 하 여 지출 한도에 도달한 구독에 대 한 알림을 확인 합니다.

무료 평가판 구독을 보유 하 고 지출 한도에 도달 하는 경우 [종 량](billing-upgrade-azure-subscription.md) 제 가격이 포함 된 계획으로 업그레이드 하 여 지출 한도를 제거 하 고 구독을 자동으로 사용 하도록 설정할 수 있습니다.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>계정 센터에서 지출 한도 제거

Azure 구독과 연결 된 유효한 지불 방법이 있는 경우 언제 든 지 지출 한도를 제거할 수 있습니다. 여러 달에 걸쳐 크레딧이 있는 제안의 경우 다음 청구 기간이 시작 될 때 지출 한도를 사용 하도록 설정할 수 있습니다.

지출 한도를 제거하려면 다음 단계를 수행합니다.

1. [계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인합니다.
1. 구독을 선택합니다. 지출 한도에 도달 하 여 구독을 사용할 수 없는 경우 다음 알림을 클릭 합니다. **구독이 지출 한도에 도달 하 여 요금 청구를 방지 하기 위해 사용 하지 않도록 설정 되었습니다.** 그렇지 않으면 **구독 상태** 영역에서 **지출 한도 제거**를 클릭합니다.
1. 적절한 옵션을 선택합니다.

![지출 한도 제거를 위한 옵션 선택](./media/billing-spending-limit/remove-spending-limit.PNG)

| 옵션 | 영향 |
| --- | --- |
| 지출 한도 무기한 제거 | 다음 청구 기간 시작 시 자동으로 설정되지 않도록 지출 한도를 제거합니다. |
| 현재 청구 기간에 대한 지출 한도 제거 | 다음 청구 기간 시작 시 자동으로 다시 설정되도록 지출 한도를 제거합니다. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>지출 한도를 제거 해야 하는 이유

지출 한도에 도달하면 특정 타사 및 Microsoft 서비스를 배포 또는 사용할 수 없게 됩니다. 구독에 대 한 지출 한도를 제거 해야 하는 경우는 다음과 같습니다.

-  Oracle 등의 자사 이미지와 Azure DevOps Services 등의 서비스를 배포하려는 경우. 이 경우 지출 한도를 거의 즉시 초과 하 여 구독을 사용 하지 않도록 설정할 수 있습니다.
- 중단 하지 않으려는 서비스가 있습니다.
- 손실되지 않아야 하는 가상 IP 주소와 같은 설정을 사용하는 서비스 및 리소스가 있는 경우. 서비스 및 리소스의 할당이 취소되면 이러한 설정이 손실됩니다.

## <a name="turn-on-the-spending-limit-after-removing"></a>제거 후 지출 한도 켜기

이 기능은 지출 한도가 무기한으로 제거된 경우에만 사용할 수 있습니다. 다음 청구 기간이 시작될 때 자동으로 설정하도록 변경합니다.

1. [계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인합니다.
1. 지출 한도 옵션을 변경하려면 노란색 배너를 클릭합니다.
1. **다음 청구 기간에서 지출 한도 설정\<청구 기간의 시작 날짜\>** 선택

## <a name="custom-spending-limit"></a>사용자 지정 지출 한도

사용자 지정 지출 한도는 사용할 수 없습니다.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>지출 한도가 모든 요금을 방지 하는 것은 아닙니다.

[Azure Marketplace에 게시 된 일부 외부 서비스](billing-understand-your-azure-marketplace-charges.md) 는 구독 크레딧을 사용할 수 없으며 지출 한도가 설정 된 경우에도 별도의 요금이 부과 될 수 있습니다. Visual Studio 라이선스, Azure Active Directory 프리미엄, 지원 플랜 및 대부분의 타사 브랜드 서비스를 예로 들 수 있습니다. 새 외부 서비스를 프로비전할 때 서비스 요금이 별도로 청구됨을 알리는 경고가 표시됩니다.

![Marketplace 구매 경고](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

궁금한 사항이 있거나 도움이 필요 하면 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [종 량](billing-upgrade-azure-subscription.md) 제 가격으로 계획으로 업그레이드 합니다.
