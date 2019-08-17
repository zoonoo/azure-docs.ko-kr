---
title: Azure AD 자격 관리 (미리 보기)에서 액세스 패키지에 대 한 액세스 요청-Azure Active Directory
description: 내 액세스 포털을 사용 하 여 Azure Active Directory 자격 관리 (미리 보기)에서 액세스 패키지에 대 한 액세스를 요청 하는 방법을 알아봅니다.
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
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b5be74b0734a27b02030e2c6995b43ef0c02162
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562147"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD 자격 관리 (미리 보기)에서 액세스 패키지에 대 한 액세스 요청

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure AD 자격 관리를 사용 하면 액세스 패키지의 수명 동안 자동으로 액세스를 관리 하는 리소스 및 정책의 일회성 설치를 사용할 수 있습니다. 

액세스 패키지 관리자는 사용자가 액세스 패키지에 액세스할 수 있도록 승인을 요구 하는 정책을 구성할 수 있습니다. 액세스 패키지에 대 한 액세스 권한이 필요한 사용자는 액세스 권한을 얻기 위한 요청을 제출할 수 있습니다. 이 문서에서는 액세스 요청을 제출 하는 방법을 설명 합니다.

## <a name="sign-in-to-the-my-access-portal"></a>내 액세스 포털에 로그인 합니다.

첫 번째 단계는 액세스 패키지에 대 한 액세스를 요청할 수 있는 내 액세스 포털에 로그인 하는 것입니다.

**필수 역할:** 요청자

1. 작업 중인 프로젝트 또는 비즈니스 관리자에서 전자 메일 또는 메시지를 찾습니다. 전자 메일에는 액세스 권한을 필요로 하는 액세스 패키지에 대 한 링크가 포함 되어야 합니다. 링크는 다음으로 시작 합니다.

    `https://myaccess.microsoft.com`

1. 링크를 엽니다.

1. 내 액세스 포털에 로그인 합니다.

    조직 (회사 또는 학교) 계정을 사용 해야 합니다. 잘 모르겠으면 프로젝트 또는 비즈니스 관리자에 게 문의 하세요.

## <a name="request-an-access-package"></a>액세스 패키지 요청

내 액세스 포털에서 액세스 패키지를 찾았으면 요청을 제출할 수 있습니다.

**필수 역할:** 요청자

1. 목록에서 액세스 패키지를 찾습니다.  필요한 경우 검색 문자열을 입력 하 고 **이름**, **카탈로그**또는 **리소스** 필터를 선택 하 여 검색할 수 있습니다.

    ![내 액세스 포털-리소스 검색](./media/entitlement-management-request-access/elm-myaccess-resource-search.png)
1. 확인 표시를 클릭 하 여 액세스 패키지를 선택 합니다.

    ![내 액세스 포털 - 액세스 패키지](./media/entitlement-management-shared/my-access-access-packages.png)

1. **액세스 요청**을 클릭하여 액세스 요청 창을 엽니다.

1. **비즈니스 근거** 상자가 표시 되 면 액세스를 필요로 하는 근거를 입력 합니다.

1. **특정 기간에 대 한 요청** 을 사용 하도록 설정 되어 있으면 **예** 또는 **아니요**를 선택 합니다.

1. 필요한 경우 시작 날짜와 종료 날짜를 지정 합니다.

    ![내 액세스 포털 - 액세스 요청](./media/entitlement-management-shared/my-access-request-access.png)

1. 완료 되 면 **제출** 을 클릭 하 여 요청을 제출 합니다.

1. 요청 **기록** 을 클릭 하 여 요청 목록과 상태를 표시 합니다.

    액세스 패키지에 승인이 필요한 경우 요청은 이제 보류 중인 승인 상태가 됩니다.

## <a name="cancel-a-request"></a>요청 취소

액세스 요청을 제출 하 고 요청이 아직 **보류 중인 승인** 상태에 있는 경우 요청을 취소할 수 있습니다.

**필수 역할:** 요청자

1. 내 액세스 포털의 왼쪽에서 **요청 기록** 을 클릭 하 여 요청 목록과 상태를 표시 합니다.

1. 취소 하려는 요청에 대 한 **보기** 링크를 클릭 합니다.

1. 요청이 아직 **보류 중인 승인** 상태 이면 요청 **취소** 를 클릭 하 여 요청을 취소할 수 있습니다.

    ![내 액세스 포털-요청 취소](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 요청 **기록** 을 클릭 하 여 요청이 취소 되었는지 확인 합니다.

## <a name="select-a-policy"></a>정책 선택

여러 정책이 적용 되는 액세스 패키지에 대 한 액세스를 요청 하는 경우 정책을 선택 하 라는 메시지가 표시 될 수 있습니다. 예를 들어 액세스 패키지 관리자는 두 개의 내부 직원 그룹에 대해 두 개의 정책으로 액세스 패키지를 구성할 수 있습니다. 첫 번째 정책은 60 일에 대 한 액세스를 허용 하 고 승인을 받아야 할 수 있습니다. 두 번째 정책은 2 일간의 액세스를 허용할 수 있으며 승인이 필요 하지 않습니다. 이 시나리오가 발생 하는 경우 사용 하려는 정책을 선택 해야 합니다.

**필수 역할:** 요청자

## <a name="next-steps"></a>다음 단계

- [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md)
- [요청 프로세스 및 전자 메일 알림](entitlement-management-process.md)
