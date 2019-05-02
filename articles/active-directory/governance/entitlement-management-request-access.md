---
title: Azure AD 권한 관리 (미리 보기)-Azure Active Directory에서에서 액세스 패키지에 대 한 액세스 요청
description: 내 액세스 포털을 사용 하 여 Azure Active Directory 권한 관리 (미리 보기)에 대 한 액세스 패키지에 대 한 액세스를 요청 하는 방법에 알아봅니다.
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
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541557"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD 권한 관리 (미리 보기)에 대 한 액세스 패키지에 대 한 액세스 요청

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 권한 관리는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="sign-in-to-the-my-access-portal"></a>내 액세스 포털에 로그인

첫 번째 단계는 액세스 패키지에 대 한 액세스를 요청할 수 있는 내 액세스 포털에 로그인 하는 것입니다.

**필수 구성 요소 역할:** 요청자

1. 사용 하는 프로젝트 또는 business manager에서 전자 메일 또는 메시지를 찾습니다. 전자 메일에 대 한 액세스를 해야 액세스 패키지에 대 한 링크를 포함 해야 합니다. 링크를 사용 하 여 시작 됩니다.

    `https://myaccess.microsoft.com`

1. 링크를 엽니다.

1. 내 액세스 포털에 로그인 합니다.

    조직 계정을 사용 해야 합니다. 확인 하는 경우를 알 수 없는 프로젝트 또는 비즈니스 관리자를 사용 하 여 합니다.

## <a name="request-an-access-package"></a>패키지를 액세스를 요청 합니다.

내 액세스 포털에서 액세스 패키지를 발견 하면 요청을 제출할 수 있습니다.

**필수 구성 요소 역할:** 요청자

1. 액세스 패키지를 선택 하려면 확인 표시를 클릭 합니다.

    ![내 액세스 포털-액세스 패키지](./media/entitlement-management-shared/my-access-access-packages.png)

1. 클릭 **액세스를 요청** 요청 액세스 창을 엽니다.

1. 경우는 **비즈니스 근거** 상자가 표시 됩니다, 액세스 해야 하는 것에 대 한 근거를 입력 합니다.

1. 경우 **특정 기간에 대 한 요청?** 사용 되 고, 선택 **예** 하거나 **아니요**합니다.

1. 필요한 경우에 시작 날짜를 지정 하 고 종료 날짜입니다.

    ![내 액세스 포털-액세스 요청](./media/entitlement-management-shared/my-access-request-access.png)

1. 완료 되 면, 클릭 **제출** 요청을 제출 합니다.

1. 클릭 **요청 기록** 요청 및 상태 목록이 표시 됩니다.

    액세스 패키지에는 승인 필요, 요청이 승인 보류 중 상태가 되었습니다.

## <a name="cancel-a-request"></a>요청 취소

액세스 요청을 제출한 경우 요청은 여전히 합니다 **승인을 보류 중인** 상태 요청을 취소할 수 있습니다.

**필수 구성 요소 역할:** 요청자

1. 내 액세스 포털 왼쪽에서의 클릭 **요청 기록** 요청 및 상태 목록이 표시 됩니다.

1. 클릭 합니다 **보기** 취소 하려는 요청에 대 한 링크입니다.

1. 요청이 여전히 이면 합니다 **승인 보류 중** 상태를 클릭할 수 있습니다 **취소 요청** 요청을 취소 하려면.

    ![내 액세스 포털-취소 요청](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 클릭 **요청 기록** 요청이 취소 되었다는 확인 합니다.

## <a name="select-a-policy"></a>정책 선택

적용 되는 여러 정책이 포함 된 액세스 패키지에 대 한 액세스를 요청 하는 경우 정책을 선택 하 라는 메시지가 표시 될 수 있습니다. 예를 들어 액세스 패키지 관리자를 내부 직원의 두 그룹에 대 한 두 정책을 사용 하 여 액세스 패키지를 구성 될 수 있습니다. 첫 번째 정책이 60 일에 대 한 액세스를 허용 하 고 승인이 필요할 수 있습니다. 두 번째 정책 2 일에 대 한 액세스를 허용 하 고 승인이 필요 하지 않을 수 있습니다. 이 시나리오에서 발생 하는 경우 사용 하려는 정책을 선택 해야 합니다.

**필수 구성 요소 역할:** 요청자

## <a name="next-steps"></a>다음 단계

- [승인 하거나 거부할 액세스 요청](entitlement-management-request-approve.md)
- [프로세스 및 메일 알림 요청](entitlement-management-process.md)
