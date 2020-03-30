---
title: 액세스 요청 승인 또는 거부 - Azure AD 권한 관리
description: 내 액세스 포털을 사용하여 Azure Active Directory 권한 관리에서 액세스 패키지에 대한 요청을 승인하거나 거부하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261724"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 요청 승인 또는 거부

Azure AD 권한 관리를 사용하면 액세스 패키지에 대한 승인을 요구하는 정책을 구성하고 하나 이상의 승인자를 선택할 수 있습니다. 이 문서에서는 지정된 승인자가 액세스 패키지에 대한 요청을 승인하거나 거부하는 방법을 설명합니다.

## <a name="open-request"></a>요청 열기

액세스 요청을 승인하거나 거부하는 첫 번째 단계는 승인 대기 중인 액세스 요청을 찾아 여는 것입니다. 액세스 요청을 여는 방법에는 두 가지가 있습니다.

**필수 구성 조건 역할:** 승인자

1. 요청을 승인하거나 거부하도록 요청하는 Microsoft Azure의 전자 메일을 찾습니다. 다음은 예제 이메일입니다.

    ![패키지 이메일 액세스 요청 승인](./media/entitlement-management-shared/approver-request-email.png)

1. 요청 **승인 또는 거부** 링크를 클릭하여 액세스 요청을 엽니다.

1. 내 액세스 포털에 로그인합니다.

이메일이 없는 경우 다음 단계에 따라 승인 대기 중인 액세스 요청을 찾을 수 있습니다.

1. 내 액세스 포털에 로그인합니다. [https://myaccess.microsoft.com](https://myaccess.microsoft.com)  (미국 정부의 경우 내 액세스 포털 링크의 `myaccess.microsoft.us`도메인이 됩니다.)

1. 왼쪽 메뉴에서 **승인을** 클릭하여 승인 대기 중인 액세스 요청 목록을 확인합니다.

1. 보류 **중인** 탭에서 요청을 찾습니다.

## <a name="approve-or-deny-request"></a>요청 승인 또는 거부

승인 대기 중인 액세스 요청을 연 후 승인 또는 거부 결정을 내리는 데 도움이 되는 세부 정보를 볼 수 있습니다.

**필수 구성 조건 역할:** 승인자

1. **보기** 링크를 클릭하여 액세스 요청 창을 엽니다.

1. 액세스 요청에 대한 세부 정보를 보려면 **세부 정보를** 클릭합니다.

    세부 정보에는 제공된 경우 사용자의 이름, 조직, 액세스 시작 및 종료 날짜, 비즈니스 타당성, 요청이 제출된 시기 및 요청이 만료되는 시기가 포함됩니다.

1. **승인** 또는 **거부를**클릭합니다.

1. 필요한 경우 이유를 입력합니다.

    ![내 액세스 포털 - 액세스 요청](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. **제출**을 클릭하여 결정을 제출합니다.

    정책이 여러 승인자로 구성된 경우 승인자 한 명만 보류 중인 승인에 대해 결정을 내려야 합니다. 승인자가 액세스 요청에 대한 결정을 제출한 후 요청이 완료되고 다른 승인자가 요청을 승인하거나 거부할 수 없습니다. 다른 승인자는 내 액세스 포털에서 요청 결정 및 의사 결정자를 볼 수 있습니다. 현재는 단일 단계 승인만 지원됩니다.

    구성된 승인자 중 누구도 액세스 요청을 승인하거나 거부할 수 없는 경우 구성된 요청 기간 이후에 요청이 만료됩니다. 사용자에게 액세스 요청이 만료되었으며 액세스 요청을 다시 제출해야 한다는 알림을 받습니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 액세스 요청](entitlement-management-request-access.md)
- [프로세스 및 이메일 알림 요청](entitlement-management-process.md)
