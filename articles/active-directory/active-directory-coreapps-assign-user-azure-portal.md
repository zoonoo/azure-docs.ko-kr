---
title: "Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당 | Microsoft Docs"
description: "Azure Active Directory에서 사용자 또는 그룹을 할당할 엔터프라이즈 앱을 선택하는 방법"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: ee784704ada9238b5cd048f99aaa4cb192ec7d57
ms.contentlocale: ko-kr
ms.lasthandoff: 05/05/2017

---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당
Azure AD(Azure Active Directory)에서 엔터프라이즈 응용 프로그램에 사용자 또는 그룹을 할당하는 것은 쉽습니다. 엔터프라이즈 앱을 관리하려면 적절한 권한이 있어야 하고 해당 디렉터리에 대한 전역 관리자여야 합니다.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>엔터프라이즈 앱에 사용자 액세스를 어떻게 할당하나요?
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure 포털](https://portal.azure.com) 에 로그인합니다.
2. **더 많은 서비스**를 선택하고 텍스트 상자에서 Azure Active Directory를 입력한 다음 **Enter**를 선택합니다.
3. **Azure Active Directory - *directoryname*** 블레이드(즉, 관리 중인 디렉터리에 대한 Azure AD 블레이드)에서 **엔터프라이즈 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 앱 열기](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. **엔터프라이즈 응용 프로그램** 블레이드에서 **모든 응용 프로그램**을 선택합니다. 관리할 수 있는 앱의 목록이 표시됩니다.
5. **엔터프라이즈 응용 프로그램 - 모든 응용 프로그램** 블레이드에서 앱을 선택합니다.
6. ***appname*** 블레이드(즉, 제목에서 선택된 앱의 이름을 가진 블레이드)에서 **사용자 및 그룹**을 선택합니다.

    ![모든 응용 프로그램 명령 선택](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. ***appname*** **- 사용자 및 그룹 할당** 블레이드에서 **추가** 명령을 선택합니다.
8. **할당 추가** 블레이드에서 **사용자 및 그룹**을 선택합니다.

    ![앱에 사용자 또는 그룹 할당](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. **사용자 및 그룹** 블레이드의 목록에서 하나 이상의 사용자 또는 그룹을 선택한 다음 블레이드 맨 아래의 **선택** 단추를 선택합니다.
10. **할당 추가** 블레이드에서 **역할**을 선택합니다. 그런 다음 **역할 선택** 블레이드에서 선택한 사용자 또는 그룹에 적용할 역할을 선택한 다음 블레이드 맨 아래의 **확인** 단추를 선택합니다.
11. **할당 추가** 블레이드에서 블레이드 맨 아래의 **할당** 단추를 선택합니다. 할당된 사용자 또는 그룹은 이 엔터프라이즈 앱에 대한 선택된 역할로 정의된 사용 권한을 갖습니다.

## <a name="next-steps"></a>다음 단계
* [내 그룹 모두 보기](active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](active-directory-coreapps-remove-assignment-azure-portal.md)
* [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](active-directory-coreapps-disable-app-azure-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](active-directory-coreapps-change-app-logo-user-azure-portal.md)

