---
title: Azure Active Directory에서 내 앱 포털에 대 한 작업 영역 만들기 Microsoft Docs
description: 내 앱 작업 영역을 사용 하 여 내 앱 페이지를 사용자 지정 하 여 최종 사용자에 게 더 간단한 내 앱 환경을 제공 합니다. 별도의 탭을 사용 하 여 응용 프로그램을 그룹으로 구성 합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199835"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>내 앱 (미리 보기) 포털에서 작업 영역 만들기

사용자는 내 앱 (미리 보기) 포털을 사용 하 여 액세스 권한이 있는 클라우드 기반 응용 프로그램을 보고 시작할 수 있습니다. 기본적으로 사용자가 액세스할 수 있는 모든 응용 프로그램은 단일 페이지에 함께 나열 됩니다. 사용자에 대해이 페이지를 더 잘 구성 하기 위해 P1 또는 P2 라이선스가 Azure AD Premium 경우 작업 영역을 설정할 수 있습니다. 작업 영역을 사용 하 여 관련 된 응용 프로그램 (예: 작업 역할, 작업 또는 프로젝트)을 함께 그룹화 하 고 별도의 탭에 표시할 수 있습니다. 작업 영역은 기본적으로 사용자가 액세스할 수 있는 응용 프로그램에 필터를 적용 하므로 사용자는 작업 영역에서 할당 된 응용 프로그램만 볼 수 있습니다.

> [!NOTE]
> 이 문서에서는 관리자가 작업 영역을 사용 하도록 설정 하 고 만드는 방법을 설명 합니다. 내 앱 포털 및 작업 영역을 사용 하는 방법에 대 한 최종 사용자 정보는 [작업 영역 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)을 참조 하세요.

## <a name="enable-my-apps-preview-features"></a>내 앱 미리 보기 기능 사용

1. [**Azure Portal**](https://portal.azure.com/) 를 열고 사용자 관리자 또는 전역 관리자 권한으로 로그인 합니다.

2. **Azure Active Directory** > **사용자 설정**으로 이동 합니다.

3. **사용자 기능**미리 보기에서 **사용자 기능 미리 보기 설정 관리**를 선택 합니다.

4. **사용자가 내 앱의 미리 보기 기능을 사용할 수 있음**에서 다음 옵션 중 하나를 선택 합니다.
   * **선택** -특정 그룹의 미리 보기 기능을 사용 하도록 설정 합니다. **그룹 선택** 옵션을 사용 하 여 미리 보기 기능을 사용 하도록 설정할 그룹을 선택할 수 있습니다.  
   * **모두** -모든 사용자에 대해 미리 보기 기능을 사용 하도록 설정 합니다.

   ![사용자 미리 보기 기능](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> 사용자는 내 앱 포털을 열기 위해 `https://myapps.microsoft.com/contoso.com`와 같은 조직에 대 한 사용자 지정 링크 또는 링크 `https://myapps.microsoft.com` 사용할 수 있습니다. 사용자가 내 앱 미리 보기 버전으로 리디렉션되지 않는 경우 사용자는 `https://myapplications.microsoft.com` 또는 `https://myapplications.microsoft.com/contoso.com`를 시도해 야 합니다.

## <a name="create-a-workspace"></a>작업 영역 생성

작업 영역을 만들려면 Azure AD Premium P1 또는 P2 라이선스가 있어야 합니다.

1. [**Azure Portal**](https://portal.azure.com/) 를 열고 Azure AD Premium P1 또는 P2 라이선스를 사용 하 여 관리자로 로그인 합니다.

2. **Azure Active Directory** > **Enterprise 응용 프로그램**으로 이동 합니다.

3. **관리**에서 **작업 영역 (미리 보기)** 을 선택 합니다.

4. **새 작업 영역**을 선택 합니다. **새 작업 영역** 페이지에서 작업 영역의 **이름을** 입력 합니다. 이름에는 "작업 영역"을 사용 하지 않는 것이 좋습니다. 그런 다음 **설명을**입력 합니다.

   ![새 작업 영역 만들기](media/access-panel-workspaces/new-workspace.png)

5. **검토 + 만들기**를 선택합니다. 새 작업 영역에 대 한 속성이 표시 됩니다.

6. **응용 프로그램** 탭을 선택 합니다. **응용 프로그램 추가**에서 작업 영역에 추가 하려는 모든 응용 프로그램을 선택 하거나 **검색** 상자를 사용 하 여 응용 프로그램을 찾습니다. 

   ![작업 영역에 응용 프로그램 추가](media/access-panel-workspaces/add-applications.png)

7. **추가**를 선택합니다. 선택한 응용 프로그램 목록이 표시 됩니다. 위쪽 및 아래쪽 화살표를 사용 하 여 목록에서 응용 프로그램의 순서를 변경할 수 있습니다.

   ![작업 영역의 응용 프로그램 목록](media/access-panel-workspaces/add-applications-list.png)

8. **사용자 및 그룹** 탭을 선택 합니다. 사용자 또는 그룹을 추가 하려면 **사용자 추가**를 선택 합니다. 

9. **구성원 선택** 페이지에서 작업 영역을 할당 하려는 사용자 또는 그룹을 선택 합니다. 또는 **검색** 상자를 사용 하 여 사용자 또는 그룹을 찾을 수 있습니다.

   ![작업 영역에 사용자 및 그룹 할당](media/access-panel-workspaces/add-users-and-groups.png)

10. 사용자 및 그룹 선택을 완료 한 후 **선택**을 선택 합니다.

11. 사용자의 역할을 **읽기 액세스** 에서 **소유자** 로 또는 그 반대로 변경 하려면 현재 역할을 클릭 하 고 새 역할을 선택 합니다.

    ![사용자 및 그룹에 역할 할당](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>감사 로그 보기

감사 로그는 작업 영역 생성 최종 사용자 동작을 포함 하 여 내 앱 작업 영역 작업을 기록 합니다. 내 앱에서 생성 되는 이벤트는 다음과 같습니다.

* 작업 영역 만들기
* 작업 영역 편집
* 작업 영역 삭제
* 응용 프로그램 시작 (최종 사용자)
* 셀프 서비스 응용 프로그램 추가 (최종 사용자)
* 셀프 서비스 응용 프로그램 삭제 (최종 사용자)

작업 섹션에서 **Azure Active Directory** > **엔터프라이즈 응용 프로그램** > **감사 로그** 를 선택 하 여 [Azure Portal](https://portal.azure.com) 에서 감사 로그에 액세스할 수 있습니다. **서비스**의 경우 **내 앱**을 선택 합니다.

   ![사용자 및 그룹에 역할 할당](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>다음 단계
[Azure Active Directory의 응용 프로그램에 대 한 최종 사용자 환경](end-user-experiences.md)