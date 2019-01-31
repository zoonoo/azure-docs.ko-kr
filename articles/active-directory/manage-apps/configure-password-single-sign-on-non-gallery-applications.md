---
title: 비-갤러리 애플리케이션에 대해 암호 Single Sign-On을 구성하는 방법 | Microsoft Docs
description: Azure AD 애플리케이션 갤러리에 없는 경우 보안 암호 기반 Single Sign-On에 대한 사용자 지정 비 갤러리 애플리케이션을 구성하는 방법
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: barbkess
ms.openlocfilehash: 9fc6bd6e7196d442f46e364a9d5816c5056f34f0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184379"
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>비-갤러리 애플리케이션에 대해 암호 Single Sign-On을 구성하는 방법

Azure AD 애플리케이션 갤러리 내에서 찾을 수 있는 선택 항목 외에도 원하는 애플리케이션이 나열되어 있지 않은 경우 **비 갤러리 애플리케이션**을 추가하는 옵션도 있습니다. 이 기능을 사용하여 조직에 이미 있는 애플리케이션 또는 [Azure AD 애플리케이션 갤러리](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery)의 일부가 아닌 공급 업체에서 사용할 수 있는 타사 애플리케이션을 추가할 수 있습니다.

비 갤러리 애플리케이션을 추가한 후 [Azure Portal](https://portal.azure.com/)의 엔터프라이즈 애플리케이션에서 **Single Sign-On** 탐색 항목을 선택하여 이 애플리케이션이 사용하는 Single Sign-On 메서드를 구성할 수 있습니다.

사용할 수 있는 Single Sign-On 방법 중 하나는 [암호 기반 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) 옵션입니다. **비 갤러리 애플리케이션 추가** 환경으로 사전 통합된 애플리케이션의 세트에 없는 경우에도 HTML 기반 사용자 이름 및 암호 입력 필드를 렌더링하는 모든 애플리케이션을 통합할 수 있습니다.

이 기능이 작동하는 방법은 사용자 이름 및 암호 입력 필드를 자동으로 감지하고 특정 애플리케이션 인스턴스에 대해 안전하게 저장할 수 있도록 하는 액세스 패널 확장의 일부인 페이지 스크랩 기술입니다. 그런 다음, 사용자가 애플리케이션 액세스 패널에서 해당 애플리케이션으로 이동할 때 사용자 이름 및 암호를 해당 필드에 안전하게 재생합니다.

모든 종류의 애플리케이션을 Azure AD로 신속하게 통합하는 것은 좋은 방법이며 다음을 가능하게 합니다.

-   HTML 사용자 이름 및 암호 입력 필드를 렌더링하는 한 Azure AD 테넌트와 **전세계 모든 애플리케이션** 통합

-   Azure AD와 통합한 애플리케이션에 대한 사용자 이름 및 암호를 안전하게 저장 및 재생하여 **사용자에 대한 Single Sign-On** 활성화

-   모든 애플리케이션에 대한 **입력 필드 자동 감지** 및 자동 감지가 찾지 못하는 경우 액세스 패널 브라우저 확장을 사용하여 이러한 필드를 수동으로 검색할 수 있도록 함

-   로그인하는 데 사용자 이름 및 암호 필드 이상을 필요로 하는 애플리케이션에 대해 **여러 로그인 필드를 필요로 하는 애플리케이션 지원**

-   사용자가 자격 증명을 입력하는 경우 [애플리케이션 액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)에 표시되는 사용자 이름 및 암호 입력 필드의 **레이블 사용자 지정**

-   **사용자**가 [애플리케이션 액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)에 수동으로 입력하는 모든 기존 애플리케이션 계정에 대한 자신의 사용자 이름 및 암호를 제공하도록 허용

-   **비즈니스 그룹의 멤버**가 [셀프 서비스 애플리케이션 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) 기능을 사용하여 사용자에게 할당된 사용자 이름 및 암호를 지정하도록 허용

-   **관리자**가 [애플리케이션에 사용자를 할당](#_How_to_configure_1)할 때 자격 증명 업데이트 기능을 사용하여 사용자에게 할당된 사용자 이름 및 암호를 지정하도록 허용

-   **관리자**가 [애플리케이션에 그룹을 할당](#assign-an-application-to-a-group-directly)할 때 자격 증명 업데이트 기능을 사용하여 사용자 그룹에서 사용하는 공유된 사용자 이름 및 암호를 지정하도록 허용

다음 섹션에서는 **비 갤러리 애플리케이션 추가** 경험을 사용하여 추가하는 모든 애플리케이션에 [암호 기반 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)을 활성화할 수 있는 방법을 설명합니다.

## <a name="overview-of-steps-required"></a>필요한 단계 개요

Azure AD 갤러리에서 애플리케이션을 구성하려면 다음을 수행해야 합니다.

-   [비갤러리 애플리케이션 추가](#add-a-non-gallery-application)

-   [암호 Single Sign-On에 대한 애플리케이션 구성](#configure-the-application-for-password-single-sign-on)

-   [사용자 또는 그룹에 애플리케이션 할당](#assign-the-application-to-a-user-or-a-group)

    -   [애플리케이션에 사용자를 직접 할당](#assign-a-user-to-an-application-directly)

    -   [그룹에 애플리케이션을 직접 할당](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>비갤러리 애플리케이션 추가

Azure AD 갤러리에서 애플리케이션을 추가하려면 아래 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5.  **엔터프라이즈 애플리케이션** 창의 오른쪽 위 모서리에서 **추가** 단추를 클릭합니다.

6.  **비갤러리 애플리케이션**을 클릭합니다.

7.  **이름** 텍스트 상자에 애플리케이션의 이름을 입력합니다. **추가**를 선택합니다.

짧은 시간 후에 애플리케이션의 구성 창을 볼 수 있습니다.

## <a name="configure-the-application-for-password-single-sign-on"></a>암호 Single Sign-On에 대한 애플리케이션 구성

애플리케이션에 Single Sign-On을 구성하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5.  **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

  * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6.  Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

7.  애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **암호 기반 로그온** 모드를 선택합니다.

9.  **로그온 URL**을 입력합니다. 사용자가 로그인하기 위해 사용자 이름과 암호를 입력하는 URL입니다. URL에서 로그인 필드가 표시되는지 확인합니다.

10. 애플리케이션에 사용자를 할당합니다.

11. 또한 사용자의 행을 선택하고 **자격 증명 업데이트**를 클릭하고 사용자를 대신하여 사용자 이름 및 암호를 입력하여 사용자를 대신하여 자격 증명을 제공할 수도 있습니다. 그렇지 않으면 사용자는 시작할 때 자격 증명을 입력하라는 메시지를 받게 됩니다.


## <a name="assign-a-user-to-an-application-directly"></a>애플리케이션에 사용자를 직접 할당

애플리케이션에 하나 이상의 사용자를 직접 할당하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5.  **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

  * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6.  목록에서 사용자를 할당하려는 애플리케이션을 선택합니다.

7.  애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

8.  **할당 추가** 창을 열려면 **사용자 및 그룹** 목록의 맨 위에서 **추가** 단추를 클릭합니다.

9.  **할당 추가** 창에서 **사용자 및 그룹** 선택기를 클릭합니다.

10. **이름 또는 전자 메일 주소로 검색** 검색 상자에 할당하려는 사용자의 **전체 이름** 또는 **전자 메일 주소**를 입력합니다.

11. 목록의 **사용자** 위로 마우스를 이동하여 **확인란**을 표시합니다. 사용자의 프로필 사진이나 로고 옆의 확인란을 클릭하여 사용자를 **선택됨** 목록에 추가합니다.

12. **선택 사항:** **하나를 초과하는 사용자를 추가**하려는 경우 **이름 또는 이메일 주소로 검색** 검색 상자에 다른 **전체 이름** 또는 **이메일 주소**를 입력하고 확인란을 클릭하여 이 사용자를 **선택됨** 목록에 추가합니다.

13. 사용자 선택이 완료되면 **선택** 단추를 클릭하여 애플리케이션에 할당되도록 사용자 및 그룹의 목록에 추가합니다.

14. **선택 사항:** **할당 추가** 창에서 **역할 선택** 선택기를 클릭하여 선택한 사용자에게 할당할 역할을 선택합니다.

15. **할당** 단추를 클릭하여 선택한 사용자에게 애플리케이션을 할당합니다.

## <a name="assign-an-application-to-a-group-directly"></a>그룹에 애플리케이션을 직접 할당

애플리케이션에 하나 이상의 그룹을 직접 할당하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5.  **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

  * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6.  목록에서 사용자를 할당하려는 애플리케이션을 선택합니다.

7.  애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

8.  **할당 추가** 창을 열려면 **사용자 및 그룹** 목록의 맨 위에서 **추가** 단추를 클릭합니다.

9.  **할당 추가** 창에서 **사용자 및 그룹** 선택기를 클릭합니다.

10. **이름 또는 메일 주소로 검색** 검색 상자에 할당하려는 그룹의 **전체 그룹 이름**을 입력합니다.

11. 목록의 **그룹** 위로 마우스를 이동하여 **확인란**을 표시합니다. 그룹의 프로필 사진이나 로고 옆의 확인란을 클릭하여 사용자를 **선택됨** 목록에 추가합니다.

12. **선택 사항:** **하나를 초과하는 그룹을 추가**하려는 경우 **이름 또는 이메일 주소로 검색** 검색 상자에 다른 **전체 그룹 이름**을 입력하고 확인란을 클릭하여 이 그룹을 **선택됨** 목록에 추가합니다.

13. 그룹 선택이 완료되면 **선택** 단추를 클릭하여 애플리케이션에 할당되도록 사용자 및 그룹의 목록에 추가합니다.

14. **선택 사항:** **할당 추가** 창에서 **역할 선택** 선택기를 클릭하여 선택한 그룹에 할당할 역할을 선택합니다.

15. **할당** 단추를 클릭하여 선택한 그룹에 애플리케이션을 할당합니다.

짧은 시간 후에 선택한 사용자는 액세스 패널에서 이러한 애플리케이션을 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[애플리케이션 프록시를 사용하여 앱에 Single Sign-On 제공](application-proxy-configure-single-sign-on-with-kcd.md)
