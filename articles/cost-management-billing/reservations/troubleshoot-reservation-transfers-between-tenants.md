---
title: Azure 예약 디렉터리 변경
description: 이 문서는 예약 소유자가 한 Azure Active Directory 테넌트(디렉터리)에서 다른 테넌트로 예약 주문을 전송하도록 도와줍니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.openlocfilehash: 183071454cbe6c9185ecb1868abfe1ac217e9519
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108143668"
---
# <a name="change-an-azure-reservation-directory-between-tenants"></a>테넌트 간에 Azure 예약 디렉터리 변경

이 문서는 예약 소유자가 Azure Active Directory 테넌트(디렉터리) 하나에서 다른 테넌트로 예약 주문 디렉터리를 변경하는 데 도움이 됩니다. 예약 주문의 디렉터리를 변경하면 예약 주문 및 종속 예약에 대한 Azure RBAC 액세스 권한이 제거됩니다. 변경 후에는 사용자만 액세스할 수 있습니다. 디렉터리를 변경해도 예약 주문에 대한 청구 소유권은 변경되지 않습니다. 부모 예약 순서 및 종속 예약에 대한 디렉터리가 변경됩니다.

예약 주문 디렉터리를 변경하는 데 예약 교환과 취소는 필요하지 않습니다.

예약 디렉터리를 다른 테넌트로 변경한 후에 예약에 다른 소유자를 추가하려고 할 수도 있습니다. 자세한 내용은 [기본적으로 예약을 관리할 수 있는 사용자](view-reservations.md#who-can-manage-a-reservation-by-default)를 참조하세요.

예약 주문 디렉터리를 변경하면 주문의 모든 예약이 함께 전송됩니다.

## <a name="change-a-reservation-orders-directory"></a>예약 주문 디렉터리 변경

다음 단계를 수행하여 예약 주문 디렉터리와 종속 예약을 다른 테넌트로 변경합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 청구 관리자는 아니지만 예약 소유자이면 **예약** 으로 이동한 다음, 5단계로 건너뜁니다.
1. **Cost Management + 청구** 로 이동합니다.
    - EA 관리자인 경우 왼쪽 메뉴에서 **청구 범위** 를 선택한 다음, 청구 범위 목록에서 하나를 선택합니다.
    - Microsoft 고객 계약 청구 프로필 소유자인 경우 왼쪽 메뉴에서 **청구 프로필** 을 선택합니다. 청구 프로필 목록에서 하나를 선택합니다.
1. EA 등록 또는 청구 프로필에 대한 전체 예약 목록이 표시됩니다.
1. 전송할 예약을 선택합니다.
1. 예약 세부 정보에서 예약 주문 ID를 선택합니다.
1. 예약 순서에서 **디렉터리 변경** 을 선택합니다.
1. 디렉터리 변경 창에서 예약을 전송할 Azure AD 디렉터리를 선택한 다음, **확인** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- 예약에 대한 자세한 내용은 [Azure Reservations란?](save-compute-costs-reservations.md)을 참조하세요.