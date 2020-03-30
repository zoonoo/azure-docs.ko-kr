---
title: 액세스 패키지 요청 - Azure AD 권한 관리
description: 내 액세스 포털을 사용하여 Azure Active Directory 권한 관리에서 액세스 패키지에 대한 액세스를 요청하는 방법을 알아봅니다.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261750"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 액세스 요청

Azure AD 권한 관리를 사용하면 액세스 패키지를 사용하여 액세스 패키지의 수명 동안 액세스를 자동으로 관리하는 리소스 및 정책을 일회성으로 설정할 수 있습니다. 

액세스 패키지 관리자는 사용자가 패키지에 액세스하도록 승인을 요구하는 정책을 구성할 수 있습니다. 액세스 패키지에 액세스해야 하는 사용자는 액세스 요청을 제출할 수 있습니다. 이 문서에서는 액세스 요청을 제출하는 방법을 설명합니다.

## <a name="sign-in-to-the-my-access-portal"></a>내 액세스 포털에 로그인

첫 번째 단계는 액세스 패키지에 대한 액세스를 요청할 수 있는 내 액세스 포털에 로그인하는 것입니다.

**필수 구성 조건 역할:** 요청자

1. 작업 중인 프로젝트 또는 비즈니스 관리자의 전자 메일이나 메시지를 찾습니다. 이메일에는 액세스해야 하는 액세스 패키지에 대한 링크가 포함되어야 합니다. 링크는 `myaccess`에서 시작하여 디렉터리 힌트를 포함하고 액세스 패키지 ID로 끝납니다.  (미국 정부의 경우 도메인이 `https://myaccess.microsoft.us` 대신 도메인일 수 있습니다.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 링크를 엽니다.

1. 내 액세스 포털에 로그인합니다.

    조직(직장 또는 학교) 계정을 사용해야 합니다. 확실하지 않은 경우 프로젝트 또는 비즈니스 관리자에게 문의하세요.

## <a name="request-an-access-package"></a>액세스 패키지 요청

내 액세스 포털에서 액세스 패키지를 찾은 후 요청을 제출할 수 있습니다.

**필수 구성 조건 역할:** 요청자

1. 목록에서 액세스 패키지를 찾습니다.  필요한 경우 검색 문자열을 입력한 다음 **이름,** **카탈로그**또는 **리소스** 필터를 선택하여 검색할 수 있습니다.

    ![내 액세스 포털 - 리소스 검색](./media/entitlement-management-request-access/my-access-resource-search.png)

1. 확인 표시를 클릭하여 액세스 패키지를 선택합니다.

1. **액세스 요청**을 클릭하여 액세스 요청 창을 엽니다.

    ![내 액세스 포털 - 액세스 패키지](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. 비즈니스 **자리** 맞추기 상자가 표시되는 경우 액세스가 필요한 근거를 입력합니다.

1. **특정 기간에 대한 요청이** 활성화된 경우 **예** 또는 **아니요를**선택합니다.

1. 필요한 경우 시작 날짜와 종료 날짜를 지정합니다.

    ![내 액세스 포털 - 액세스 요청](./media/entitlement-management-shared/my-access-request-access.png)

1. 완료되면 **제출을** 클릭하여 요청을 제출합니다.

1. **요청 기록을** 클릭하여 요청 목록과 상태를 확인합니다.

    액세스 패키지에 승인이 필요한 경우 요청은 현재 보류 중인 승인 상태입니다.

### <a name="select-a-policy"></a>정책 선택

여러 정책이 적용되는 액세스 패키지에 대한 액세스를 요청하는 경우 정책을 선택하라는 메시지가 표시됩니다. 예를 들어 액세스 패키지 관리자는 내부 직원의 두 그룹에 대해 두 개의 정책으로 액세스 패키지를 구성할 수 있습니다. 첫 번째 정책은 60일 동안 액세스를 허용하고 승인이 필요할 수 있습니다. 두 번째 정책은 2일 동안 액세스를 허용하고 승인이 필요하지 않을 수 있습니다. 이 시나리오가 발생하면 사용하려는 정책을 선택해야 합니다.

![내 액세스 포털 - 액세스 요청 - 여러 정책](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>요청 다시 제출

액세스 패키지에 대한 액세스를 요청하면 승인자가 제 시간에 응답하지 않으면 요청이 거부되거나 요청이 만료될 수 있습니다. 액세스가 필요한 경우 다시 시도하고 요청을 다시 제출할 수 있습니다. 다음 절차에서 액세스 요청을 다시 제출하는 방법을 설명합니다.

**필수 구성 조건 역할:** 요청자

1. **내 액세스** 포털에 로그인합니다.

1. 탐색 메뉴에서 왼쪽의 **기록 요청을** 클릭합니다.

1. 요청을 다시 제출하는 액세스 패키지를 찾습니다.

1. 확인 표시를 클릭하여 액세스 패키지를 선택합니다.

1. 선택한 액세스 패키지의 오른쪽에 있는 파란색 **보기** 링크를 클릭합니다.
    
    ![액세스 패키지 및 보기 링크 선택](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    액세스 패키지에 대한 요청 기록이 있는 창이 오른쪽에 열립니다.
    
    ![다시 제출 단추 선택](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. 창 하단의 **재제출** 버튼을 클릭합니다.

## <a name="cancel-a-request"></a>요청 취소

액세스 요청을 제출하고 요청이 **보류 중인 승인** 상태로 남아 있는 경우 요청을 취소할 수 있습니다.

**필수 구성 조건 역할:** 요청자

1. 내 액세스 포털에서 왼쪽에서 **기록 요청을** 클릭하여 요청 목록과 상태를 확인합니다.

1. 취소하려는 요청의 **보기** 링크를 클릭합니다.

1. 요청이 보류 중인 **승인** 상태에 있는 경우 **요청 취소를** 클릭하여 요청을 취소할 수 있습니다.

    ![내 액세스 포털 - 요청 취소](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. **요청 기록을** 클릭하여 요청이 취소되었으며 확인합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md)
- [프로세스 및 이메일 알림 요청](entitlement-management-process.md)
