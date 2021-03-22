---
title: 테넌트 간 Azure 예약 전송 문제 해결
description: 이 문서는 예약 소유자가 한 Azure Active Directory 테넌트(디렉터리)에서 다른 테넌트로 예약 주문을 전송하도록 도와줍니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055803"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>테넌트 간 예약 전송 문제 해결

이 문서는 예약 소유자가 한 Azure Active Directory 테넌트(디렉터리)에서 다른 테넌트로 예약 주문을 전송하도록 도와줍니다. 예약 주문의 디렉터리를 변경하면 예약 주문 및 종속 예약에 대한 Azure RBAC 액세스 권한이 제거됩니다. 변경 후에는 사용자만 액세스할 수 있습니다. 디렉터리를 변경해도 예약 주문에 대한 청구 소유권은 변경되지 않습니다. 부모 예약 순서 및 종속 예약에 대한 디렉터리가 변경됩니다.

테넌트 간에 전송하는 데는 예약 교환 및 취소가 필요하지 않습니다.

예약을 다른 테넌트에 전송한 후에는 예약에 다른 소유자를 추가하려고 할 수도 있습니다. 자세한 내용은 [기본적으로 예약을 관리할 수 있는 사용자](view-reservations.md#who-can-manage-a-reservation-by-default)를 참조하세요.

예약 주문을 전송하면 주문의 모든 예약이 함께 전송됩니다.

## <a name="transfer-a-reservation"></a>예약 전송

다음 단계에 따라 예약 주문을 전송하고 다른 테넌트에 종속 예약을 전송합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 청구 관리자는 아니지만 예약 소유자인 경우 **예약** 으로 이동한 다음, 6단계로 건너뜁니다.
1. **Cost Management + 청구** 로 이동합니다.
    - EA 관리자인 경우 왼쪽 메뉴에서 **청구 범위** 를 선택한 다음, 청구 범위 목록에서 하나를 선택합니다.
    - Microsoft 고객 계약 청구 프로필 소유자인 경우 왼쪽 메뉴에서 **청구 프로필** 을 선택합니다. 청구 프로필 목록에서 하나를 선택합니다.
1. 왼쪽 메뉴에서 **예약 트랜잭션** 을 선택합니다. 예약 트랜잭션 목록이 표시됩니다.
1. 페이지 상단에 있는 배너에서 *지금 청구 관리자가 예약을 관리할 수 있습니다. 예약을 관리하려면 여기를 클릭합니다.* 를 읽습니다. 배너를 선택합니다.
1. EA 등록 또는 청구 프로필에 대한 전체 예약 목록이 표시됩니다.
1. 전송할 예약을 선택합니다.
1. 예약 세부 정보에서 예약 주문 ID를 선택합니다.
1. 예약 순서에서 **디렉터리 변경** 을 선택합니다.
1. 디렉터리 변경 창에서 예약을 전송할 Azure AD 디렉터리를 선택한 다음, **확인** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- 예약에 대한 자세한 내용은 [Azure Reservations란?](save-compute-costs-reservations.md)을 참조하세요.