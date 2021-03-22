---
title: Azure 리소스에 대한 예약 보기
description: Azure Portal에서 Azure 예약을 보는 방법을 알아봅니다. API, PowerShell, CLI 및 Power BI를 사용하여 예약 및 사용률을 봅니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/24/2021
ms.author: banders
ms.openlocfilehash: 477dff9db28672f8231710af34786ac387f43b71
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050344"
---
# <a name="view-azure-reservations"></a>Azure 예약 보기

이 문서에서는 Azure Portal에서 Azure 예약을 보는 방법을 설명합니다. Azure Portal에서 구매한 예약을 보고 관리할 수 있습니다.

## <a name="who-can-manage-a-reservation-by-default"></a>기본적으로 예약을 관리할 수 있는 사용자

기본적으로 다음 사용자는 예약을 보고 관리할 수 있습니다.

- 예약을 구매하는 사용자와 예약 구매에 사용한 청구 구독의 계정 관리자가 예약 주문에 추가됩니다.
- 기업계약 및 Microsoft 고객 계약 청구 관리자.

다른 사용자가 예약을 관리할 수 있게 하려면 다음 두 가지 옵션을 사용하면 됩니다.

- 개별 예약 주문에 대한 액세스 관리를 위임합니다.
    1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
    1. **모든 서비스** > **예약** 을 선택하여 액세스할 수 있는 예약을 나열합니다.
    1. 다른 사용자에게 액세스 권한을 위임하려는 예약을 선택합니다.
    1. 예약 세부 정보에서 예약 순서를 선택합니다.
    1. **액세스 제어(IAM)** 를 선택합니다.
    1. **역할 할당 추가** > **역할** > **소유자** 를 선택합니다. 제한된 액세스 권한을 부여하려면 다른 역할을 선택합니다.
    1. 소유자로 추가할 사용자의 메일 주소를 입력합니다.
    1. 사용자를 선택한 다음 **저장** 을 선택합니다.

- 기업계약 또는 Microsoft 고객 계약에 사용자를 청구 관리자로 추가합니다.
    - 기업계약의 경우 _엔터프라이즈 관리자_ 역할이 있는 사용자를 추가하여 기업계약에 적용되는 모든 예약 주문을 보고 관리합니다. _엔터프라이즈 관리자(읽기 전용)_ 역할이 있는 사용자는 예약만 볼 수 있습니다. 부서 관리자 및 계정 소유자는 액세스 제어(IAM)를 사용하여 명시적으로 추가하지 _않는 한_ 예약을 볼 수 없습니다. 자세한 내용은 [Azure Enterprise 역할 관리](../manage/understand-ea-roles.md)를 참조하세요.

        _엔터프라이즈 관리자는 예약 주문의 소유권을 가질 수 있으며, 액세스 제어(IAM)를 사용하여 예약에 다른 사용자를 추가할 수 있습니다._
    - Microsoft 고객 계약의 경우 청구 프로필 소유자 역할 또는 청구 프로필 기여자 역할을 가진 사용자는 청구 프로필을 사용하여 이루어진 모든 예약 구매를 관리할 수 있습니다. 청구 프로필 리더와 청구서 관리자는 청구 프로필로 지불되는 모든 예약을 볼 수 있습니다. 그러나 예약을 변경할 수는 없습니다.
    자세한 내용은 [청구 프로필 역할 및 작업](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

### <a name="how-billing-administrators-view-or-manage-reservations"></a>청구 관리자가 예약을 보거나 관리하는 방법

Azure RBAC 액세스 권한으로 예약 또는 예약 주문에 액세스할 수 있는 경우 예약으로 이동할 때 예약 트랜잭션의 하위 집합만 표시되거나 아무것도 표시되지 않을 수 있습니다. 다음 단계에 따라 모든 예약 및 예약 트랜잭션을 보고 관리할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Cost Management + Billing** 으로 이동합니다.
    - EA 관리자인 경우 왼쪽 메뉴에서 **청구 범위** 를 선택한 다음, 청구 범위 목록에서 하나를 선택합니다.
    - Microsoft 고객 계약 청구 프로필 소유자인 경우 왼쪽 메뉴에서 **청구 프로필** 을 선택합니다. 청구 프로필 목록에서 하나를 선택합니다.
1. 왼쪽 메뉴에서 **예약 트랜잭션** 을 선택합니다. 예약 트랜잭션 목록이 표시됩니다.
1. 페이지 상단에 있는 배너에서 *지금 청구 관리자가 예약을 관리할 수 있습니다. 예약을 관리하려면 여기를 클릭합니다.* 를 읽습니다. 배너를 선택합니다.
1. EA 등록 또는 청구 프로필에 대한 전체 예약 목록이 표시됩니다.
1. 예약 소유권을 가져오려면 해당 예약을 선택합니다. 그런 다음, 권한 설정 페이지에서 **액세스 권한 부여** 를 선택합니다. 예약 및 예약 주문에 대한 소유자 액세스 권한이 부여됩니다.

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Azure Portal에서 예약 및 사용률 보기

소유자 또는 Reader로 예약을 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. [예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)으로 이동합니다.
3. 목록에는 소유자 또는 Reader 역할을 보유한 예약 목록이 표시됩니다. 각 예약에는 마지막으로 알려진 사용률이 표시됩니다.
4. 사용률을 선택하여 사용률 기록 및 세부 정보를 봅니다. 아래 비디오에서 세부 정보를 참조하세요.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>API, PowerShell 및 CLI를 사용하여 예약 및 사용률 가져오기

다음 리소스를 사용하여 모든 예약 목록을 가져옵니다.

- [API: 예약 주문 - 목록](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: 예약 주문 - 목록](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: 예약 주문 - 목록](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

예약 인스턴스 사용량 API를 사용하여 [예약 사용률](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)을 가져올 수도 있습니다. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Power BI에서 예약 및 사용률 보기

Power BI 사용자에게는 두 가지 옵션이 있습니다.
- 콘텐츠 팩: 예약 구매 날짜 및 사용률 데이터는 [Consumption Insights Power BI 콘텐츠 팩](/power-bi/desktop-connect-azure-cost-management)에서 사용할 수 있습니다. 콘텐츠 팩을 사용하여 원하는 보고서를 만드세요. 
- 비용 관리 앱: 추가로 사용자 지정할 수 있는 사전 생성 보고서에 [Cost Management 앱](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)을 사용합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Azure 예약 관리](manage-reserved-vm-instance.md)
- [종량제 구독의 예약 사용량을 이해](understand-reserved-instance-usage.md)합니다.
- [엔터프라이즈 등록에서 예약 사용량을 이해](understand-reserved-instance-usage-ea.md)합니다.
- [CSP 구독의 예약 사용량을 이해](/partner-center/azure-reservations)합니다.

