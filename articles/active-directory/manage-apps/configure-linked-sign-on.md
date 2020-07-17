---
title: Azure AD 앱에 대 한 연결 된 로그인-Microsoft identity platform
description: Microsoft id 플랫폼 (Azure AD)에서 Azure AD 엔터프라이즈 응용 프로그램에 대 한 SSO (연결 된 Single Sign-On) 구성
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfcece43ae1b7d7bcf0c38feba14f1e82b29f18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763485"
---
# <a name="configure-linked-sign-on"></a>연결된 로그온 구성

갤러리 또는 비 갤러리 웹 응용 프로그램을 추가할 때 사용할 수 있는 Single Sign-On 옵션 중 하나는 [연결 된 로그온](what-is-single-sign-on.md)입니다. 조직의 Azure AD 액세스 패널 또는 Office 365 포털에서 응용 프로그램에 대 한 링크를 추가 하려면이 옵션을 선택 합니다. 이 방법을 사용 하 여 인증을 위해 현재 Azure AD 대신 Active Directory Federation Services (또는 다른 페더레이션 서비스)를 사용 하는 사용자 지정 웹 응용 프로그램에 대 한 링크를 추가할 수 있습니다. 혹은 사용자의 액세스 패널에 표시하려는 특정 SharePoint 페이지 또는 다른 웹 페이지에 딥 링크를 추가할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

애플리케이션이 Azure AD 테넌트에 추가되지 않은 경우 [갤러리 앱 추가](add-gallery-app.md) 또는 [비갤러리 앱 추가](add-non-gallery-app.md)를 참조하세요.

### <a name="open-the-app-and-select-linked-sign-on"></a>앱을 열고 연결 된 로그온을 선택 합니다.

1. [Azure Portal](https://portal.azure.com)에 Azure AD 테넌트의 클라우드 애플리케이션 관리자 또는 애플리케이션 관리자로 로그인합니다.

1. **Azure Active Directory**  >  **엔터프라이즈 응용 프로그램**으로 이동 합니다. Azure AD 테넌트에 있는 애플리케이션 샘플이 임의로 표시됩니다. 

1. **애플리케이션 유형** 메뉴에서 **모든 애플리케이션**을 선택한 다음, **적용**을 선택합니다.

1. 검색 상자에서 애플리케이션 이름을 입력한 다음, 결과에서 애플리케이션을 선택합니다.

1. **관리** 섹션 아래에서 **Single Sign-On**을 선택합니다. 

1. **연결**됨을 선택 합니다.

1. 링크할 응용 프로그램의 URL을 입력 합니다. URL을 입력 하 고 **저장**을 선택 합니다. 
 
1. 응용 프로그램에 사용자 및 그룹을 할당할 수 있습니다. 그러면 해당 사용자에 대 한 [Office 365 앱 시작 관리자](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](end-user-experiences.md) 에 응용 프로그램이 표시 됩니다.

1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](methods-for-assigning-users-and-groups.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
