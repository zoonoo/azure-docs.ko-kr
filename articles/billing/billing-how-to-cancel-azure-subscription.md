---
title: Azure 구독 취소 | Microsoft Docs
description: 무료 평가판 구독과 같은 Azure 구독을 취소하는 방법을 설명합니다.
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666441"
---
# <a name="cancel-your-azure-subscription"></a>Azure 구독 취소

구독이 더 이상 필요 하지 않은 경우 Azure Portal에서 Azure 구독을 취소할 수 있습니다. 

구독을 취소하기 전에 다음을 수행합니다.
* 데이터를 백업합니다. 예를 들어 Azure Storage 또는 SQL에 데이터를 저장하는 경우 복사본을 다운로드합니다. 가상 머신을 사용하는 경우 해당 이미지를 로컬로 저장합니다.
* 서비스를 종료합니다. [관리 포털의 페이지 리소스](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)로 이동한 후 실행 중인 가상 머신, 애플리케이션 또는 기타 서비스를 모두 **중지**합니다.
* 데이터를 마이그레이션하는 것이 좋습니다. [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.
* 모든 리소스 및 모든 리소스 그룹을 삭제 합니다. 구독을 취소 하려면 먼저 해당 항목을 삭제 해야 합니다. 각 리소스 그룹은 개별적으로 삭제 해야 합니다. 리소스 그룹을 삭제 하는 동안 리소스 그룹 이름을 입력 하 여 삭제를 확인 해야 합니다.
* 에서 `AssignableScopes`이 구독을 참조 하는 사용자 지정 역할이 있는 경우 해당 사용자 지정 역할을 업데이트 하 여 구독을 제거 해야 합니다. 구독을 취소 한 후 사용자 지정 역할을 업데이트 하려고 하면 오류가 발생할 수 있습니다. 자세한 내용은 [사용자 지정 역할과 관련 된 문제 해결](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) 및 [Azure 리소스에 대 한 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조 하세요.

유료 Azure 지원 플랜을 취소 하면 나머지 구독 기간에 대 한 요금이 청구 됩니다. 자세한 내용은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)을 참조하세요.

## <a name="cancel-subscription-in-the-azure-portal"></a>Azure Portal에서 구독 취소

1. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택 합니다.
2. 취소할 구독을 선택합니다.
3. **개요**를 선택한 다음, **구독 취소**를 선택합니다.
    ![취소 단추를 보여 주는 스크린샷](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. 메시지에 따라 취소를 완료합니다.


## <a name="who-can-cancel-a-subscription"></a>누가 구독을 취소할 수 있나요?

아래 표에서는 구독을 취소 하는 데 필요한 사용 권한을 설명 합니다.

|구독 유형     |취소할 수 있는 사람  |
|---------|---------|
|Azure 웹 사이트를 통해 Azure에 등록할 때 생성 되는 구독입니다. 예를 들어 [Azure 무료 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 등록 하는 경우 [종 량 제 요금](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [Visual studio 구독자](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)로 계정을 사용할 수 있습니다. |  구독에 대 한 계정 관리자, 소유자 및 참가자  |
|[Microsoft 기업계약](https://azure.microsoft.com/pricing/enterprise-agreement/) 및 [엔터프라이즈 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  구독에 대 한 계정 소유자, 소유자 및 참가자       |
|[DevTest에 대 한](https://azure.microsoft.com/offers/ms-azr-0148g/) [Azure 계획](https://azure.microsoft.com/offers/ms-azr-0017g/) 및 azure 계획     |  구독에 대 한 소유자 및 기여자      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>구독을 취소한 후에는 어떻게 되나요?

취소 한 후 대금 청구는 즉시 중지 됩니다. 그러나 취소가 포털에 표시되기까지 최대 10분이 걸릴 수 있습니다. 청구 기간 중간에 취소 하는 경우 기간이 종료 된 후 일반 청구서 날짜에 최종 송장을 보냅니다.

취소 한 후에는 서비스를 사용할 수 없습니다. 즉, 가상 머신의 할당이 취소되고 임시 IP 주소가 해제되며 스토리지는 읽기 전용이 됩니다.

다시 액세스해야 하거나 마음이 바뀐 경우를 대비해서 90일 동안 기다렸다가 데이터를 영구 삭제합니다. 데이터 보존 요금은 부과되지 않습니다. 자세한 내용은 [Microsoft 보안 센터 - 데이터 관리 방법](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)을 참조하세요.

## <a name="reactivate-subscription"></a>구독 다시 활성화

종 량 제 요금이 실수로 구독을 취소 하 [는 경우 계정 센터에서 다시 활성화할](billing-subscription-become-disable.md)수 있습니다.

구독이 종 량 제 요금으로 구독 되지 않는 경우 구독을 다시 활성화 하려면 90 일 이내에 지원 담당자에 게 문의 하세요.

## <a name="need-help-contact-us"></a>도움이 필요하십니까? 문의하세요.

궁금한 사항이 있거나 도움이 필요 하면 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
