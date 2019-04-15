---
title: Azure Active Directory에서 엔터프라이즈 앱에 대한 사용자 로그인 비활성화 | Microsoft Docs
description: Azure Active Directory에서 사용자가 로그인하지 않도록 엔터프라이즈 애플리케이션을 비활성화하는 방법
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a168c3415996ff97137c4e312348f74a48e9531b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564995"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱에 대한 사용자 로그인 비활성화
사용자가 Azure Active Directory (Azure AD)에서에 로그인 할 수 있도록 엔터프라이즈 응용 프로그램을 사용 하지 않도록 설정 하는 것이 쉽습니다. 엔터프라이즈 앱을 관리 하는 적절 한 권한이 필요 합니다. 및 디렉터리에 대 한 전역 관리자 여야 합니다.

## <a name="how-do-i-disable-user-sign-ins"></a>사용자 로그인을 비활성화하려면 어떻게 합니까?
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스**를 선택하고 텍스트 상자에 **Azure Active Directory**를 입력한 다음, **입력**을 선택합니다.
1. 에 **Azure Active Directory** -  ***directoryname*** 창 (즉, Azure AD 창 관리 중인 디렉터리에 대 한), 선택 **엔터프라이즈 응용 프로그램**.
1. 에 **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 창 관리할 수 있는 앱 목록을 표시 합니다. 앱을 선택 합니다.
1. ***appname*** 창, 즉 제목에서 선택된 앱의 이름을 사용한 창에서 **속성**을 선택합니다.
1. ***appname*** - **속성** 창에서 **사용자가 로그인할 수 있습니까?** 에 대해 **아니요**를 선택합니다.
1. **저장** 명령을 선택합니다.

## <a name="next-steps"></a>다음 단계
* [내 그룹 모두 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](remove-user-or-group-access-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](change-name-or-logo-portal.md)
