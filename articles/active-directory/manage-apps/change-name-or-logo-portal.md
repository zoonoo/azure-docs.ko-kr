---
title: 이름 또는 Azure Active Directory에서 엔터프라이즈 응용 프로그램의 로고 변경 | Microsoft Docs
description: 이름 또는 Azure Active Directory에서 사용자 지정 엔터프라이즈 응용 프로그램 로고를 변경 하는 방법
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
ms.openlocfilehash: 30da8d6843c27c42d4d99adef50b9ad98a131c95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780913"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>이름 또는 Azure Active Directory에서 엔터프라이즈 응용 프로그램의 로고를 변경 합니다.

Azure AD(Azure Active Directory)에서 사용자 정의 엔터프라이즈 애플리케이션에 대한 이름 또는 로고를 변경하는 것은 쉽습니다. 이러한 변경을 수행 하려면 적절 한 권한이 있어야 합니다 및 사용자 지정 응용 프로그램의 작성자 이어야 합니다.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>엔터프라이즈 응용 프로그램의 이름 또는 로고를 변경 하려면 어떻게 해야 합니까?

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/) 디렉터리에 대 한 전역 관리자 계정으로 합니다. 합니다 **Azure Active Directory 관리 센터** 페이지가 나타납니다.
2. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 엔터프라이즈 응용 프로그램 목록이 표시 됩니다.
3. 응용 프로그램을 선택 합니다. 응용 프로그램 개요 페이지가 표시 됩니다.
4. 응용 프로그램 개요 창에서 아래 합니다 **관리** 을 선택 **속성**합니다. 합니다 **속성** 페이지가 나타납니다.
5. 이름을 변경 하려는 경우 선택 합니다 **이름을** 상자, 새 이름 입력 하 고 키를 눌러 **Enter**합니다.
6. 로고를 변경 하려는 경우는 **로고** 필드를 선택한 다음 폴더 아이콘을 선택 합니다 **파일을 선택** 응용 프로그램의 현재 로고 이미지 아래에 있는 상자입니다.

   ![속성 명령 선택](./media/change-name-or-logo-portal/change-logo.png)

   그렇지 않으면 로고를 변경 하지 경우 8 단계로 이동 합니다.
7. 파일 선택에서 새 로고로 가져오려는 파일을 선택 합니다. 파일의 이름을 현재 로고 이미지 아래에 있는 상자에 표시 됩니다.

   > [!NOTE]
   > Azure 로고 이미지를 PNG 파일 이어야 하며 너비, 높이 및 파일 크기에 제한을 적용 합니다.
8. **저장**을 선택합니다. 새 로고를 선택 하는 경우는 **로고** 새 로고 파일을 반영 하 여 필드의 이미지 변경 합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Active Directory에서 조직 그룹 및 구성원 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](remove-user-or-group-access-portal.md)
* [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](disable-user-sign-in-portal.md)
