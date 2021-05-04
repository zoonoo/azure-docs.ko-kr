---
title: 내 로그인 페이지에서 최근 로그인 활동 보기 및 검색 - Azure Active Directory | Microsoft Docs
description: 내 계정 포털의 내 로그인 페이지에서 최근 로그인 활동을 보고 검색하는 방법을 자세히 설명합니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/22/2021
ms.author: curtand
ms.openlocfilehash: 593ec6588e2f55cb9f379dcfac437956bcabc10b
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107951355"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>내 로그인 페이지에서 최근 로그인 활동 보기 및 검색

**내 계정** 포털의 **내 로그인** 페이지에서 모든 최근 회사 또는 학교 계정 로그인 활동을 볼 수 있습니다. 로그인 기록을 검토하면 다음을 확인하여 비정상적인 활동이 있는지 확인할 수 있습니다.

- 누군가가 암호를 추측하려고 하고 있는지 여부
- 공격자가 계정에 성공적으로 로그인했는지 여부 및 해당 위치
- 공격자가 액세스하려고 시도한 앱

>[!Note]
> 개인 Microsoft 계정으로 로그인하는 동안 오류가 표시되는 경우, 다음 URL 중 하나에서 조직의 도메인 이름(예: contoso.com) 또는 관리자의 조직 **테넌트 ID** 를 사용하여 로그인할 수 있습니다.
>
>   - `https://myaccount.microsoft.com?tenantId=<your_domain_name>`
>   - `https://myaccount.microsoft.com?tenant=<your_tenant_ID>`

## <a name="view-your-recent-sign-in-activity"></a>최근 로그인 활동 보기

1. 회사 또는 학교 계정에 로그인한 다음, https://myaccount.microsoft.com/ 페이지로 이동합니다.

1. 왼쪽 탐색 창에서 **내 로그인** 을 선택하거나 **내 로그인** 블록에서 **최근 활동 검토** 링크를 선택합니다.

    ![강조 표시된 최근 활동 링크가 표시된 내 계정 페이지](media/my-account-portal/my-account-portal-sign-ins.png)

1. 각 로그인 항목을 확장하고 검토하여 각 항목을 인식할 수 있는지 확인합니다. 친숙하지 않은 로그인 항목이 있으면 계정이 손상될 경우를 대비하여 암호를 변경하여 계정을 보호하세요.

    ![확장 로그인 세부 정보가 포함된 최근 활동 페이지](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>성공한 로그인이 표시되는 경우

경우에 따라 사용자 고유의 일반적인 로그인 활동을 검토하는 동안 익숙하지 않은 위치, 브라우저 또는 운영 체제에서 성공적으로 로그인한 것을 볼 수 있습니다. 익숙하지 않은 로그인은 공격자가 계정에 액세스할 수 있음을 의미할 수 있습니다. 권한이 없는 활동이 표시되면 암호를 즉시 변경한 다음, [보안 정보](https://mysignins.microsoft.com/security-info)로 이동하여 보안 설정을 업데이트하는 것이 좋습니다.

잘못된 항목을 확인하기 전에 거짓 긍정이 표시되지 않는지 확인해야 합니다. 이 경우 항목에 문제가 있어 보이지만 괜찮습니다. 예를 들어 IP 주소를 기준으로 대략적인 위치와 지도를 확인합니다. 모바일 네트워크는 때때로 먼 위치를 거쳐 트래픽을 라우팅하기 때문에 정확한 위치를 알아내는 것이 특히 어렵습니다. 워싱턴주에 있는 모바일 디바이스를 사용하여 로그인하더라도 해당 위치에 캘리포니아에서 들어오는 로그인이 표시될 수 있습니다. 위치뿐만 아니라 자세한 정보를 확인하는 것이 좋습니다. 운영 체제, 브라우저 및 앱이 모두 적합한지 확인합니다.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>실패한 로그인이 표시되는 경우

실패한 로그인이 표시되면 자격 증명을 잘못 입력한 것일 수 있습니다. 공격자가 사용자의 암호를 추측하려 했다는 것을 의미할 수도 있습니다. 이 위험에 대처하기 위해 암호를 변경하지 않아도 되지만 Azure AD MFA(Multi-Factor Authentication)에 등록하는 것이 좋습니다. 다단계 인증을 사용하면 해커가 암호를 추측하더라도 계정에 액세스하는 데 충분하지 않습니다.

![실패한 로그인 타일](media/my-account-portal-sign-ins-page/unsuccessful.png)

**세션 작업** 아래에 `Additional verification failed, invalid code`라는 메모와 함께 실패한 로그인이 표시되는 경우에는 기본 인증 자격 증명은 성공했지만 다단계 인증에 실패했음을 의미합니다. 이 조건은 공격자가 암호를 정확하게 추측했지만 다단계 인증 챌린지를 통과하지 못했음을 의미할 수 있습니다. 공격자가 암호를 이미 가지고 있을 수 있으므로 계속 암호를 변경하고, [보안 정보](https://mysignins.microsoft.com/security-info) 페이지로 이동하여 보안 설정을 업데이트하는 것이 좋습니다.

## <a name="search-for-specific-sign-in-activity"></a>특정 로그인 작업 검색

사용 가능한 정보 중 하나를 사용하여 최근 로그인 작업을 검색할 수 있습니다. 예를 들어, 운영 체제, 위치, 앱 등을 기준으로 최근 로그인 작업을 검색할 수 있습니다.

1. **최근 작업 검토** 페이지에서 **검색** 표시줄에 검색할 정보를 입력합니다. 예를 들어 `Unsuccessful`을 입력하여 내 계정 앱에서 수집된 모든 실패한 로그인 작업을 검색합니다.

2. **검색** 단추를 선택하여 검색을 시작합니다.

    ![강조 표시된 검색 표시줄, 검색 단추 및 결과를 표시하는 최근 작업 페이지](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>비정상적인 활동 확인

비정상적인 활동으로 플래그가 지정된 로그인은 **내 로그인** 페이지의 해당 활동에 대한 타일에서 확인할 수 있습니다.

![로그인을 시도했거나 시도하지 않았음을 확인하는 비정상적인 로그인 타일](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="view-sign-in-activity-on-android"></a>Android에서 로그인 활동 보기

보안 정보를 보거나 변경할 때마다 회사 또는 학교 계정에 로그인해야 계속 진행할 수 있습니다.

1. Microsoft Authenticator 앱을 열고 직장 또는 학교 계정의 홈 페이지를 선택합니다.

1. **최근 계정 작업** 을 선택합니다.

    ![최근 로그인 활동 링크가 강조 표시된 직장 또는 학교 계정 홈페이지](media/my-account-portal-sign-ins-page/android-recent-activity.png)

1. 로그인에 성공하면 각 로그인 항목을 확장하고 검토하여 각 항목을 인식할 수 있는지 확인합니다. 친숙하지 않은 로그인 항목이 있으면 계정이 손상될 경우를 대비하여 암호를 변경하여 계정을 보호하세요.

    ![로그인 세부 정보가 포함된 최근 활동 페이지](media/my-account-portal-sign-ins-page/android-activity-page.png)

## <a name="change-your-password-on-android"></a>Android에서 암호 변경

관리자는 사용자가 인증 앱으로부터 암호를 재설정할 수 있도록 할 수 있습니다. 보안 정보를 보거나 변경하기 위해 로그인할 때마다 회사 또는 학교 계정에 로그인해야 계속 진행할 수 있습니다.

1. Microsoft Authenticator 앱을 열고 직장 또는 학교 계정의 홈 페이지를 선택합니다.

1. **암호 변경** 을 선택합니다.

    ![암호 변경 링크가 강조 표시된 회사 또는 학교 계정 홈 페이지](media/my-account-portal-sign-ins-page/android-change-password.png)

1. 로그인에 성공하면 **암호 변경** 페이지에서 암호 정보를 업데이트할 수 있습니다.

    ![이전 및 새 암호 항목이 있는 최근 활동 페이지](media/my-account-portal-sign-ins-page/android-password-page.png)

## <a name="update-security-info-on-android"></a>Android에서 보안 정보 업데이트

보안 정보를 보거나 변경하기 위해 로그인할 때마다 회사 또는 학교 계정에 로그인해야 계속 진행할 수 있습니다.

1. Microsoft Authenticator 앱을 열고 직장 또는 학교 계정의 홈 페이지를 선택합니다.

1. **보안 정보 업데이트** 를 선택합니다.

    ![보안 정보 업데이트 링크가 강조 표시된 회사 또는 학교 계정 홈 페이지](media/my-account-portal-sign-ins-page/android-update-security-info.png)

1. 로그인에 성공하면 **보안 정보** 페이지의 보안 세부 정보를 업데이트할 수 있습니다.

    ![보안 정보가 있는 최근 작업 페이지](media/my-account-portal-sign-ins-page/android-security-info-page.png)

## <a name="next-steps"></a>다음 단계

최근 로그인 정보를 확인한 후 다음을 수행할 수 있습니다.

- [보안 정보](./security-info-setup-signin.md)를 보거나 관리합니다.

- 연결된 [디바이스](my-account-portal-devices-page.md)를 보거나 관리합니다.

- [조직](my-account-portal-organizations-page.md)을 보거나 관리합니다.

- 조직에서 [개인 정보 관련 데이터를 사용](my-account-portal-privacy-page.md)하는 방법을 확인합니다.

- [내 계정 포털 설정](my-account-portal-settings.md) 변경