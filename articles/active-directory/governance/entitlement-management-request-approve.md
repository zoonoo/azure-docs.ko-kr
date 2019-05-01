---
title: 승인 또는 Azure AD 권한 관리 (미리 보기)-Azure Active Directory에서에서 액세스 요청 거부
description: 내 액세스 포털을 사용 하 여 Azure Active Directory 권한 관리 (미리 보기)에 대 한 액세스 패키지에는 요청을 승인 하거나 거부 하는 방법에 알아봅니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541527"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>승인 또는 Azure AD 권한 관리 (미리 보기)에 대 한 액세스 요청을 거부

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 권한 관리는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure AD 권한 관리를 사용 하 여 액세스 패키지에 대 한 승인이 필요 하도록 정책을 구성할 수 있으며 하나 이상의 승인자를 선택할 수 있습니다. 이 문서에서는 설명 하는 방법을 지정 된 승인자를 승인 하거나 액세스 패키지에 대 한 요청을 거부할 수 있습니다.

## <a name="open-request"></a>열기 요청

액세스 요청을 승인 하거나 거부 하는 첫 번째 단계를 찾아 엽니다 액세스 요청이 승인 보류 중입니다. 액세스 요청을 여는 방법은 두 가지가 있습니다.

**필수 구성 요소 역할:** 승인자

1. 요청 승인 또는 요청을 거부 하는 Microsoft Azure에서 전자 메일을 찾습니다. 다음은 예제 메일이입니다.

    ![패키지 전자 메일에 액세스 요청 승인](./media/entitlement-management-shared/email-approve-request.png)

1. 클릭 합니다 **승인 요청 거부 또는** 액세스 요청을 여는 링크입니다.

1. 내 액세스 포털에 로그인 합니다.

전자 메일에 없는 경우 다음이 단계를 수행 하 여 귀하의 승인이 보류 중인 액세스 요청을 찾을 수 있습니다.

1. 내 액세스 포털에 로그인 [ https://myaccess.microsoft.com ](https://myaccess.microsoft.com)합니다.

1. 왼쪽된 메뉴에서 클릭 **승인** 승인 보류 중인 액세스 요청의 목록을 보려면.

1. 에 **보류 중인** 탭에서 요청을 찾을.

## <a name="approve-or-deny-request"></a>승인 또는 요청 거부

승인 보류 중인 액세스 요청을 연 후 수 있도록를 승인 또는 거부 결정 하는 데 도움이 되는 세부 정보를 볼 수 있습니다.

**필수 구성 요소 역할:** 승인자

1. 클릭 합니다 **보기** 링크 하 여 액세스 요청 창을 엽니다.

1. 클릭 **세부 정보** 액세스 요청에 대 한 세부 정보를 확인 합니다.

    세부 정보를 사용자의 이름, 조직, 시작 및 종료 날짜를 제공 하면 비즈니스 근거는 요청이 제출 된 시간과 요청의 만료 시점에 액세스 합니다.

1. 클릭 **승인할** 하거나 **거부**합니다.

1. 필요한 경우 이유를 입력 합니다.

    ![내 액세스 포털-액세스 요청](./media/entitlement-management-shared/my-access-approve-request.png)

1. 클릭 **제출** 결정을 제출 합니다.

    정책을 여러 승인자를 구성 된 경우 하나의 승인자 보류 중인 승인에 대 한 결정을 내리는 데 필요 합니다. 승인자는 액세스 요청에는 의사 결정을 제출한 후 요청이 완료 되 고 더 이상 다른 승인자가 승인 또는 요청을 거부 하도록 사용할 수 없습니다. 다른 승인자 요청 의사 결정 및 의사 결정권자를 위한 해당 내 액세스 포털에서 볼 수 있습니다. 이 경우 단일 단계 승인만 지원 됩니다.

    승인 또는 액세스 요청을 거부 하는 일을 할 경우 구성 된 승인자 하나도 요청 구성된 요청 기간 후 만료 됩니다. 사용자가 액세스 요청 만료 및 액세스 요청을 다시 제출 해야 하는 알림을 가져옵니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대 한 액세스 요청](entitlement-management-request-access.md)
- [프로세스 및 메일 알림 요청](entitlement-management-process.md)
