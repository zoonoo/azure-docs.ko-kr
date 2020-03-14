---
title: Azure AD에서 엔터프라이즈 응용 프로그램의 이름 또는 로고 변경
description: Azure Active Directory에서 사용자 지정 엔터프라이즈 응용 프로그램의 이름 또는 로고를 변경 하는 방법
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
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138504"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 응용 프로그램의 이름 또는 로고 변경

Azure AD(Azure Active Directory)에서 사용자 정의 엔터프라이즈 애플리케이션에 대한 이름 또는 로고를 변경하는 것은 쉽습니다. 이러한 변경을 수행 하려면 적절 한 권한이 있어야 하 고 사용자 지정 응용 프로그램의 작성자 여야 합니다.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>엔터프라이즈 응용 프로그램의 이름 또는 로고를 변경 어떻게 할까요??

1. 디렉터리에 대 한 전역 관리자 인 계정으로 [Azure Active Directory 포털](https://aad.portal.azure.com/) 에 로그인 합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
2. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 엔터프라이즈 응용 프로그램 목록이 표시 됩니다.
3. 응용 프로그램을 선택합니다. 응용 프로그램 개요 페이지가 나타납니다.
4. 응용 프로그램 개요 창의 **관리** 제목 아래에서 **속성**을 선택 합니다. **속성** 페이지가 나타납니다.
5. 이름을 변경 하려면 **이름** 상자를 선택 하 고 새 이름을 입력 한 다음 **enter**키를 누릅니다.
6. 로고를 변경 하려면 **로고** 필드를 찾고 응용 프로그램의 현재 로고 이미지 아래에 있는 **파일 선택** 상자 옆에 있는 폴더 아이콘을 선택 합니다.

   ![속성 명령 선택](./media/change-name-or-logo-portal/change-logo.png)

   그렇지 않고 로고를 변경 하지 않는 경우 8 단계로 이동 합니다.
7. 파일 선택기에서 새 로고로 원하는 파일을 선택 합니다. 파일 이름이 현재 로고 이미지 아래 상자에 나타납니다.

   > [!NOTE]
   > Azure에서는 로고 이미지를 PNG 파일로 사용 하 고 너비, 높이 및 파일 크기에 제한을 적용 합니다. 사용자 지정 로고는 정확 하 게 215 &times; 215 픽셀 크기 이며 PNG 형식 이어야 합니다. 응용 프로그램 로고에서 투명도가 없는 단색 배경을 사용 하 여 사용자에 게 가장 잘 나타나도록 하는 것이 좋습니다.
8. **저장**을 선택합니다. 새 로고를 선택한 경우 **로고** 필드의 이미지가 새 로고 파일을 반영 하도록 변경 됩니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Active Directory에서 조직의 그룹 및 멤버 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](remove-user-or-group-access-portal.md)
* [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](disable-user-sign-in-portal.md)
