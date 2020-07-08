---
title: 내 로그인(미리 보기) 페이지에서 최근 로그인 활동 보기 및 검색 - Azure Active Directory | Microsoft Docs
description: 내 계정 포털의 내 로그인 페이지에서 최근 로그인 활동을 보고 검색하는 방법을 자세히 설명합니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: 0b3b0d686ae10f9b376c977bf165eccddda32239
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744520"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>내 로그인(미리 보기) 페이지에서 최근 로그인 활동 보기 및 검색

**내 계정** 포털의 **내 로그인** 페이지에서 모든 최근 회사 또는 학교 계정 로그인 활동을 볼 수 있습니다. 로그인 기록을 검토하면 다음을 확인하여 비정상적인 활동이 있는지 확인할 수 있습니다.

- 누군가가 암호를 추측하려고 하고 있는지 여부

- 공격자가 계정에 성공적으로 로그인했는지 여부 및 해당 위치

- 공격자가 액세스하려고 시도한 앱

## <a name="view-your-recent-sign-in-activity"></a>최근 로그인 활동 보기

1. 회사 또는 학교 계정에 로그인한 다음, https://myaccount.microsoft.com/ 페이지로 이동합니다.

2. 왼쪽 탐색 창에서 **내 로그인(미리 보기)** 을 선택하거나 **내 로그인(미리 보기)** 블록에서 **최근 활동 검토** 링크를 선택합니다.

    ![강조 표시된 최근 활동 링크가 표시된 내 계정 페이지](media/my-account-portal/my-account-portal-sign-ins.png)

3. 각 로그인 항목을 확장하고 검토하여 각 항목을 인식할 수 있는지 확인합니다. 친숙하지 않은 로그인 항목이 있으면 계정이 손상된 경우 계정을 보호할 수 있도록 암호를 변경하는 것이 좋습니다.

    ![확장 로그인 세부 정보가 포함된 최근 활동 페이지](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>성공한 로그인이 표시되는 경우

자체 활동을 정상적인 것으로 인식해야 합니다. 그러나 이상한 위치, 브라우저 또는 운영 체제에서 성공적으로 로그인한 경우 공격자가 계정에 액세스할 수 있음을 의미할 수 있습니다. 이 경우 암호를 즉시 변경하고 [보안 정보](https://mysignins.microsoft.com/security-info) 페이지로 이동하여 보안 설정을 업데이트하는 것이 좋습니다.

잘못된 항목을 확인하기 전에 거짓 긍정이 표시되지 않는지 확인해야 합니다. 이 경우 항목에 문제가 있어 보이지만 괜찮습니다. 예를 들어 IP 주소를 기준으로 대략적인 위치와 지도를 확인합니다. 모바일 네트워크는 때때로 먼 위치를 거쳐 트래픽을 라우팅하기 때문에 정확한 위치를 알아내는 것이 특히 어렵습니다. 따라서 워싱턴주에 있는 모바일 디바이스를 사용하여 로그인한 경우에는 해당 위치에 캘리포니아에서 들어오는 로그인이 표시될 수 있습니다. 따라서 위치 뿐만 아니라 자세한 정보를 확인하는 것이 좋습니다. 또한 운영 체제, 브라우저 및 앱이 모두 적합한지 확인해야 합니다.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>실패한 로그인이 표시되는 경우

세션 작업 없이 실패한 로그인은 기본 확인 방법(사용자 이름/암호)이 실패했음을 의미합니다. 즉, 사용자 이름 또는 암호를 잘못 입력한 것을 의미할 수도 있지만 공격자가 암호를 추측하려고 시도했을 수도 있습니다. 공격자가 암호를 추측하지 못한 것으로 생각되는 경우 암호를 변경할 필요가 없지만 Azure MFA(Multi-Factor Authentication)에 등록하는 것이 좋습니다. MFA를 사용하는 경우 해커가 암호를 추측하더라도 계정에 충분히 액세스하지는 못합니다.

세션 작업 아래에 **추가 확인 실패, 잘못된 코드**라는 메모와 함께 실패한 로그인이 표시되는 경우에는 기본 인증(사용자 이름/암호)은 성공했지만 MFA에 실패했음을 의미합니다. 공격자 입장에서 보면 암호를 정확하게 추측했지만 여전히 MFA 챌린지를 통과할 수 없는 것입니다. 이 경우 공격자가 해당 부분을 알고 있는 것이므로 여전히 암호를 변경하고, [보안 정보](https://mysignins.microsoft.com/security-info) 페이지로 이동하여 보안 설정을 업데이트하는 것이 좋습니다.

## <a name="search-for-specific-sign-in-activity"></a>특정 로그인 작업 검색

사용 가능한 정보 중 하나를 사용하여 최근 로그인 작업을 검색할 수 있습니다. 예를 들어, 운영 체제, 위치, 앱 등을 기준으로 최근 로그인 작업을 검색할 수 있습니다.

1. **최근 작업 검토** 페이지에서 **검색** 표시줄에 검색할 정보를 입력합니다. 예를 들어 `My Account`를 입력하여 내 계정 앱에서 수집된 모든 작업을 검색합니다.

2. **검색** 단추를 선택하여 검색을 시작합니다.

    ![강조 표시된 검색 표시줄, 검색 단추 및 결과를 표시하는 최근 작업 페이지](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>다음 단계

최근 로그인 작업을 확인한 후에 다음을 수행할 수 있습니다.

- [보안 정보](user-help-security-info-overview.md)를 보거나 관리합니다.

- 연결된 [디바이스](my-account-portal-devices-page.md)를 보거나 관리합니다.

- [조직](my-account-portal-organizations-page.md)을 보거나 관리합니다.

- 조직에서 [개인 정보 관련 데이터를 사용](my-account-portal-privacy-page.md)하는 방법을 확인합니다.
