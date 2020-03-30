---
title: Azure AD 앱에 대한 연결 사인온 - Microsoft ID 플랫폼
description: Microsoft ID 플랫폼(Azure AD)에서 Azure AD 엔터프라이즈 응용 프로그램에 연결된 단일 사인온(SSO) 구성
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063546"
---
# <a name="configure-linked-sign-on"></a>연결된 로그온 구성

갤러리 또는 비갤러리 웹 응용 프로그램을 추가할 때 사용할 수 있는 단일 사인온 옵션 중 하나가 [로그온](what-is-single-sign-on.md)으로 연결됩니다. 조직의 Azure AD 액세스 패널 또는 Office 365 포털에서 응용 프로그램에 대한 링크를 추가하려면 이 옵션을 선택합니다. 이 메서드를 사용 하 여 현재 인증에 대 한 Azure AD 대신 Active Directory 페더레이션 서비스(또는 다른 페더레이션 서비스)를 사용 하는 사용자 지정 웹 응용 프로그램에 대 한 링크를 추가할 수 있습니다. 혹은 사용자의 액세스 패널에 표시하려는 특정 SharePoint 페이지 또는 다른 웹 페이지에 딥 링크를 추가할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

애플리케이션이 Azure AD 테넌트에 추가되지 않은 경우 [갤러리 앱 추가](add-gallery-app.md) 또는 [비갤러리 앱 추가](add-non-gallery-app.md)를 참조하세요.

### <a name="open-the-app-and-select-linked-sign-on"></a>앱을 열고 연결된 사인온을 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 Azure AD 테넌트의 클라우드 애플리케이션 관리자 또는 애플리케이션 관리자로 로그인합니다.

1. Azure **Active Directory** > **엔터프라이즈 응용 프로그램으로 이동합니다.** Azure AD 테넌트에 있는 애플리케이션 샘플이 임의로 표시됩니다. 

1. **애플리케이션 유형** 메뉴에서 **모든 애플리케이션**을 선택한 다음, **적용**을 선택합니다.

1. 검색 상자에서 애플리케이션 이름을 입력한 다음, 결과에서 애플리케이션을 선택합니다.

1. **관리** 섹션 아래에서 **Single Sign-On**을 선택합니다. 

1. **연결된**을 선택합니다.

1. 링크할 응용 프로그램의 URL을 입력합니다. URL을 입력하고 **저장을**선택합니다. 
 
1. 응용 프로그램에 사용자 및 그룹을 할당할 수 있으며, 이로 인해 해당 사용자에 대한 [Office 365 앱 실행 프로그램](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 Azure [AD 액세스 패널에](end-user-experiences.md) 응용 프로그램이 표시됩니다.

1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](methods-for-assigning-users-and-groups.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
