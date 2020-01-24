---
title: 내 로그인 (미리 보기) 페이지에서 최근 로그인 활동 보기 및 검색-Azure Active Directory | Microsoft Docs
description: 내 계정 포털의 내 로그인 페이지에서 최근 로그인 활동을 보고 검색 하는 방법에 대 한 세부 정보입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.openlocfilehash: 2d31519c1c7e09c4eb8db64e2c37b8412fee9ab5
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705108"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>내 로그인 (미리 보기) 페이지에서 최근 로그인 활동 보기 및 검색

**내 계정** 포털의 **내 로그인** 페이지에서 최근 회사 또는 학교 계정 로그인 활동을 모두 볼 수 있습니다. 로그인 기록을 검토 하면 다음을 확인 하 여 비정상적인 활동을 확인할 수 있습니다.

- 누군가가 암호를 추측 하려고 합니다.

- 공격자가 계정에 성공적으로 로그인 한 경우 위치를 확인 합니다.

- 공격자가 액세스 하려고 시도한 앱입니다.

## <a name="view-your-recent-sign-in-activity"></a>최근 로그인 활동 보기

1. 회사 또는 학교 계정에 로그인한 다음, https://myprofile.microsoft.com/ 페이지로 이동합니다.

2. 왼쪽 탐색 창에서 **내 로그인 (미리 보기)** 을 선택 하거나 **내 로그인 (미리 보기)** 블록에서 **최근 작업 검토** 링크를 선택 합니다.

    ![내 계정 페이지, 강조 표시 된 최근 활동 링크 표시](media/my-account-portal/my-account-portal-sign-ins.png)

3. 각 로그인 항목을 확장하고 검토하여 각 항목을 인식할 수 있는지 확인합니다. 친숙 하지 않은 로그인 항목을 찾은 경우 계정이 손상 된 경우 계정을 보호 하기 위해 암호를 변경 하는 것이 좋습니다.

    ![확장 로그인 세부 정보가 포함된 최근 활동 페이지](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>성공한 로그인이 표시 되는 경우

정상적인 활동을 인식 해야 합니다. 그러나 이상한 위치, 브라우저 또는 운영 체제에서 성공적으로 로그인 한 경우 공격자가 계정에 액세스할 수 있음을 의미할 수 있습니다. 이 경우 암호를 즉시 변경 하 고 [보안 정보](https://mysignins.microsoft.com/security-info) 페이지로 이동 하 여 보안 설정을 업데이트 하는 것이 좋습니다.

잘못 된 항목을 확인 하기 전에 거짓 긍정이 표시 되지 않는지 확인 해야 합니다 .이 경우 항목은 문제가 되지 않지만 괜찮습니다. 예를 들어 IP 주소를 기준으로 대략적인 위치와 지도를 확인 합니다. 모바일 네트워크는 때때로 먼 위치를 통해 트래픽을 라우팅하는 것 이기 때문에 특히 정확 합니다. 따라서 워싱턴 주에 있는 모바일 장치를 사용 하 여 로그인 한 경우에는 캘리포니아에서 들어오는 로그인이 위치에 표시 될 수 있습니다. 따라서 위치 뿐만 아니라 자세한 정보를 확인 하는 것이 좋습니다. 또한 운영 체제, 브라우저 및 앱이 모두 적합 한지 확인 해야 합니다.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>실패 한 로그인이 표시 되는 경우

세션 작업 없이 실패 한 로그인은 기본 확인 방법 (사용자 이름/암호)이 실패 했음을 의미 합니다. 이는 사용자 이름 또는 암호를 잘못 입력 하는 것을 의미할 수도 있지만 공격자가 암호를 추측 하려고 시도 했을 수도 있습니다. 공격자가 암호를 추측 하지 못한 것으로 생각 되는 경우 암호를 변경할 필요가 없지만 Azure Multi-Factor Authentication (MFA)에 등록 하는 것이 좋습니다. MFA를 사용 하는 경우 해커가 암호를 추측 하더라도 계정에 액세스 하는 데 충분 하지 않습니다.

성공 하지 못한 로그인이 표시 되는 경우 **추가 확인에 실패 했습니다**. 라는 메시지가 표시 되 고, 잘못 된 코드는 기본 인증 (사용자 이름/암호)이 성공 했지만 MFA에 실패 했음을 의미 합니다. 공격자 인 경우 암호를 정확 하 게 추측 했지만 여전히 MFA 챌린지를 전달할 수 없습니다. 이 경우 공격자가 권한을가지고 있으므로 보안 [정보](https://mysignins.microsoft.com/security-info) 페이지로 이동 하 여 보안 설정을 업데이트 하기 때문에 여전히 암호를 변경 하는 것이 좋습니다.

## <a name="search-for-specific-sign-in-activity"></a>특정 로그인 활동 검색

사용 가능한 정보 중 하나를 사용 하 여 최근 로그인 활동을 검색할 수 있습니다. 예를 들어, 운영 체제, 위치, 앱 등을 기준으로 최근 로그인 활동을 검색할 수 있습니다.

1. **최근 작업 검토** 페이지에서 **검색 창에 검색 하려는** 정보를 입력 합니다. 예를 들어 `My Account`를 입력 하 여 내 계정 앱에서 수집 된 모든 작업을 검색 합니다.

2. **검색** 단추를 선택 하 여 검색을 시작 합니다.

    ![최근 작업 페이지, 강조 표시 된 검색 표시줄, 검색 단추 및 결과 표시](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>다음 단계

최근 로그인 활동을 본 후 다음을 수행할 수 있습니다.

- [보안 정보](user-help-security-info-overview.md)를 보거나 관리 합니다.

- 연결 된 [장치](my-account-portal-devices-page.md)를 보거나 관리 합니다.

- [조직을](my-account-portal-organizations-page.md)보거나 관리 합니다.

- 조직에서 [개인 정보 관련 데이터를 사용](my-account-portal-privacy-page.md)하는 방법을 확인 합니다.
