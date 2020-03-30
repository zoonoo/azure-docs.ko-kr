---
title: Azure AD에서 엔터프라이즈 응용 프로그램의 이름 또는 로고 변경
description: Azure Active Directory에서 사용자 지정 엔터프라이즈 응용 프로그램의 이름 또는 로고를 변경하는 방법
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138504"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 응용 프로그램의 이름 또는 로고 변경

Azure AD(Azure Active Directory)에서 사용자 정의 엔터프라이즈 애플리케이션에 대한 이름 또는 로고를 변경하는 것은 쉽습니다. 이러한 변경을 수행할 수 있는 적절한 권한이 있어야 하며 사용자 지정 응용 프로그램의 작성자여야 합니다.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>엔터프라이즈 응용 프로그램의 이름이나 로고를 변경하려면 어떻게 해야 합니까?

1. 디렉터리의 전역 관리자인 계정으로 [Azure Active Directory 포털에](https://aad.portal.azure.com/) 로그인합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
2. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 엔터프라이즈 응용 프로그램 목록이 나타납니다.
3. 애플리케이션을 선택합니다. 응용 프로그램 개요 페이지가 나타납니다.
4. 응용 프로그램 개요 창에서 **관리** 제목 아래에서 **속성을**선택합니다. **속성** 페이지가 나타납니다.
5. 이름을 변경하려면 **이름** 상자를 선택하고 새 이름을 입력한 다음 **Enter**를 누릅니다.
6. 로고를 변경하려면 **로고** 필드를 찾아 응용 프로그램의 현재 로고 이미지 아래에 있는 파일 상자 **선택** 옆에 있는 폴더 아이콘을 선택합니다.

   ![속성 명령 선택](./media/change-name-or-logo-portal/change-logo.png)

   그렇지 않으면 로고를 변경하지 않는 경우 8단계로 이동합니다.
7. 파일 선택기에서 새 로고로 원하는 파일을 선택합니다. 파일 이름이 현재 로고 이미지 아래의 상자에 나타납니다.

   > [!NOTE]
   > Azure는 로고 이미지가 PNG 파일이어야 하며 너비, 높이 및 파일 크기에 제한을 적용합니다. 사용자 지정 로고의 크기는 정확히 215 &times; 215픽셀이어야 하며 PNG 형식이어야 합니다. 응용 프로그램 로고의 투명도가 없는 단색 배경을 사용하여 사용자에게 가장 잘 표시하는 것이 좋습니다.
8. **저장**을 선택합니다. 새 로고를 선택한 경우 **로고** 필드의 이미지가 새 로고 파일을 반영하도록 변경됩니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Active Directory에서 조직의 그룹 및 멤버 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](remove-user-or-group-access-portal.md)
* [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](disable-user-sign-in-portal.md)
