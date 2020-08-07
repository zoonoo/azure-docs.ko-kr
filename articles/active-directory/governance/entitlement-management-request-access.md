---
title: 액세스 패키지 요청-Azure AD 자격 관리
description: 내 액세스 포털을 사용 하 여 Azure Active Directory 자격 관리에서 액세스 패키지에 대 한 액세스를 요청 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 527255653bf2779b36e0e463cd32a0b334c00f96
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798497"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지에 대 한 액세스 요청

Azure AD 자격 관리를 통해 액세스 패키지는 액세스 패키지의 수명 동안 자동으로 액세스를 관리 하는 리소스 및 정책의 일회성 설치를 가능 하 게 합니다. 

액세스 패키지 관리자는 사용자가 액세스 패키지에 액세스할 수 있도록 승인을 요구 하는 정책을 구성할 수 있습니다. 액세스 패키지에 대 한 액세스 권한이 필요한 사용자는 액세스 권한을 얻기 위한 요청을 제출할 수 있습니다. 이 문서에서는 액세스 요청을 제출 하는 방법을 설명 합니다.

## <a name="sign-in-to-the-my-access-portal"></a>내 액세스 포털에 로그인 합니다.

첫 번째 단계는 액세스 패키지에 대 한 액세스를 요청할 수 있는 내 액세스 포털에 로그인 하는 것입니다.

**필수 역할:** 요청

1. 작업 중인 프로젝트 또는 비즈니스 관리자에서 전자 메일 또는 메시지를 찾습니다. 전자 메일에는 액세스 권한을 필요로 하는 액세스 패키지에 대 한 링크가 포함 되어야 합니다. 링크는로 시작 하 `myaccess` 고, 디렉터리 힌트를 포함 하 고, 액세스 패키지 ID로 끝납니다.  (미국 정부의 경우 도메인을 대신 사용할 수 있습니다 `https://myaccess.microsoft.us` .)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 링크를 엽니다.

1. 내 액세스 포털에 로그인 합니다.

    조직 (회사 또는 학교) 계정을 사용 해야 합니다. 잘 모르겠으면 프로젝트 또는 비즈니스 관리자에 게 문의 하세요.

## <a name="request-an-access-package"></a>액세스 패키지 요청

내 액세스 포털에서 액세스 패키지를 찾았으면 요청을 제출할 수 있습니다.

**필수 역할:** 요청

1. 목록에서 액세스 패키지를 찾습니다.  필요한 경우 검색 문자열을 입력 하 고 **이름**, **카탈로그**또는 **리소스** 필터를 선택 하 여 검색할 수 있습니다.

    ![내 액세스 포털-리소스 검색](./media/entitlement-management-request-access/my-access-resource-search.png)

1. 확인 표시를 클릭 하 여 액세스 패키지를 선택 합니다.

1. **액세스 요청**을 클릭하여 액세스 요청 창을 엽니다.

    ![내 액세스 포털 - 액세스 패키지](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. **비즈니스 근거** 상자가 표시 되 면 액세스를 필요로 하는 근거를 입력 합니다.

1. **특정 기간에 대 한 요청** 을 사용 하도록 설정 되어 있으면 **예** 또는 **아니요**를 선택 합니다.

1. 필요한 경우 시작 날짜와 종료 날짜를 지정 합니다.

    ![내 액세스 포털 - 액세스 요청](./media/entitlement-management-shared/my-access-request-access.png)

1. 완료 되 면 **제출** 을 클릭 하 여 요청을 제출 합니다.

1. 요청 **기록** 을 클릭 하 여 요청 목록과 상태를 표시 합니다.

    액세스 패키지에 승인이 필요한 경우 요청은 이제 보류 중인 승인 상태가 됩니다.

### <a name="select-a-policy"></a>정책 선택

여러 정책이 적용 되는 액세스 패키지에 대 한 액세스를 요청 하는 경우 정책을 선택 하 라는 메시지가 표시 될 수 있습니다. 예를 들어 액세스 패키지 관리자는 두 개의 내부 직원 그룹에 대해 두 개의 정책으로 액세스 패키지를 구성할 수 있습니다. 첫 번째 정책은 60 일에 대 한 액세스를 허용 하 고 승인을 받아야 할 수 있습니다. 두 번째 정책은 2 일간의 액세스를 허용할 수 있으며 승인이 필요 하지 않습니다. 이 시나리오가 발생 하는 경우 사용 하려는 정책을 선택 해야 합니다.

![내 액세스 포털-액세스 요청-여러 정책](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>요청 다시 제출

액세스 패키지에 대 한 액세스를 요청 하면 요청이 거부 되거나 승인자가 시간 내에 응답 하지 않을 경우 요청이 만료 될 수 있습니다. 액세스 권한이 필요한 경우 다시 시도 하 고 요청을 다시 제출할 수 있습니다. 다음 절차에서는 액세스 요청을 다시 전송 하는 방법을 설명 합니다.

**필수 역할:** 요청

1. **내 액세스** 포털에 로그인 합니다.

1. 왼쪽의 탐색 메뉴에서 **기록 요청** 을 클릭 합니다.

1. 요청을 다시 제출 하는 액세스 패키지를 찾습니다.

1. 확인 표시를 클릭 하 여 액세스 패키지를 선택 합니다.

1. 선택한 액세스 패키지의 오른쪽에 있는 파란색 **보기** 링크를 클릭 합니다.
    
    ![액세스 패키지 및 링크 보기를 선택 합니다.](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    액세스 패키지에 대 한 요청 기록이 포함 된 창이 오른쪽에 열립니다.
    
    ![다시 제출 단추 선택](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. 창 아래쪽에 있는 다시 **제출** 단추를 클릭 합니다.

## <a name="cancel-a-request"></a>요청 취소

액세스 요청을 제출 하 고 요청이 아직 **보류 중인 승인** 상태에 있는 경우 요청을 취소할 수 있습니다.

**필수 역할:** 요청

1. 내 액세스 포털의 왼쪽에서 **요청 기록** 을 클릭 하 여 요청 목록과 상태를 표시 합니다.

1. 취소 하려는 요청에 대 한 **보기** 링크를 클릭 합니다.

1. 요청이 아직 **보류 중인 승인** 상태 이면 요청 **취소** 를 클릭 하 여 요청을 취소할 수 있습니다.

    ![내 액세스 포털-요청 취소](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 요청 **기록** 을 클릭 하 여 요청이 취소 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md)
- [요청 프로세스 및 전자 메일 알림](entitlement-management-process.md)
