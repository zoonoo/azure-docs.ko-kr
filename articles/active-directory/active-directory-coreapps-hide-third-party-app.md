---
title: "Azure Active Directory의 사용자 환경에서 타사 응용 프로그램 숨기기 | Microsoft Docs"
description: "Azure Active Directory의 사용자 환경에서 타사 응용 프로그램을 숨기는 방법"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 976cbb1341493186b9996d250ebca8f2f3688fdf
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2017
---
# <a name="hide-a-third-party-application-from-users-experience-in-azure-active-directory"></a>Azure Active Directory의 사용자 환경에서 타사 응용 프로그램 숨기기

사용자 액세스 패널이나 또는 Office 365 시작 관리자에 표시되지 않게 하고 싶은 타사 응용 프로그램(Microsoft가 아닌 타사가 게시한 앱)이 있는 경우 이 앱 타일을 숨기는 옵션이 있습니다. 앱을 숨긴다면 앱 사용자가 여전히 앱에 대한 권한을 갖지만 앱은 앱 시작 관리자에 나타나지 않습니다. 엔터프라이즈 앱을 관리하려면 적절한 권한이 있어야 하고 해당 디렉터리에 대한 전역 관리자여야 합니다.

## <a name="hiding-a-third-party-app-from-a-users-experience"></a>사용자의 환경에서 타사 앱 숨기기
사용자의 액세스 패널과 Office 365 앱 시작 관리자에서 타사 앱을 숨기려면 다음 단계를 사용합니다.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>사용자의 액세스 패널 및 O365 앱 시작 관리자로부터 타사 앱을 숨기려면 어떻게 해야 하나요?

1.  디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  **더 많은 서비스**를 선택하고 텍스트 상자에서 **Azure Active Directory**를 입력한 다음 **Enter**를 선택합니다.
3.  **Azure Active Directory - *directoryname*** 화면(즉, 관리 중인 디렉터리에 대한 Azure AD 화면)에서 **Enterprise 응용 프로그램**을 선택합니다.
![Enterprise 앱](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  **Enterprise 응용 프로그램** 화면에서 **모든 응용 프로그램**을 선택합니다. 관리할 수 있는 앱의 목록이 표시됩니다.
5.  **Enterprise 응용 프로그램 - 모든 응용 프로그램** 화면에서 앱을 선택합니다.</br>
![Enterprise 앱](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  ***appname*** 화면(즉, 제목에서 선택된 앱의 이름을 사용한 화면)에서 속성을 선택합니다.
7.  ***appname* -속성** 화면에서, **사용자에게 보입니까?**에 대해 **예**를 선택합니다.
![Enterprise 앱](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  **저장** 명령을 선택합니다.

## <a name="next-steps"></a>다음 단계
* [내 그룹 모두 보기](active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](active-directory-coreapps-remove-assignment-azure-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](active-directory-coreapps-change-app-logo-user-azure-portal.md)
