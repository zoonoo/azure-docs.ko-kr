---
title: Azure AD 갤러리 애플리케이션에 대해 암호 Single Sign-On을 구성하는 방법 | Microsoft Docs
description: Azure AD 응용 프로그램 갤러리에 이미 나열 되어 있는 경우 보안 암호 기반 Single Sign-On에 대 한 응용 프로그램을 구성 하는 방법
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146881"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD 갤러리 응용 프로그램에 대 한 암호 Single Sign-On 구성

[Azure Active Directory (AZURE AD) 응용 프로그램 갤러리](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)에서 응용 프로그램을 추가 하는 경우 사용자가 해당 응용 프로그램에 로그인 하는 방법을 선택할 수 있습니다. [Azure Portal](https://portal.azure.com/)에서 엔터프라이즈 응용 프로그램에 대 **한 Single sign-on** 을 선택 하 여 언제 든 지이 옵션을 구성할 수 있습니다.

사용 가능한 SSO (Single Sign-On) 옵션 중 하나는 [암호 기반 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)입니다. 응용 프로그램을 Azure AD로 신속 하 게 통합 하기 시작 하는 좋은 방법입니다. 이를 통해 다음을 수행할 수 있습니다.

-   Azure AD와 통합 한 응용 프로그램에 대 한 사용자 이름 및 암호를 안전 하 게 저장 하 고 재생 합니다.

-   사용자 이름 및 암호 필드 외에 여러 로그인 필드를 필요로 하는 응용 프로그램에 대 한 지원을 제공 합니다.

-   사용자가 자격 증명을 입력할 때 [응용 프로그램 액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 에서 사용자에 게 표시 되는 사용자 이름 및 암호 필드의 레이블을 사용자 지정할 수 있습니다.

-   사용자가 [응용 프로그램 액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 에서 수동으로 입력 하는 기존 응용 프로그램 계정에 대 한 고유한 사용자 이름 및 암호를 제공할 수 있습니다.

-   비즈니스 그룹의 구성원이 [셀프 서비스 응용 프로그램 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) 기능을 사용 하 여 사용자에 게 할당 된 사용자 이름 및 암호를 지정할 수 있도록 허용 합니다.

-   관리자가 [사용자를 응용 프로그램에 할당할](#assign-a-user-to-an-application-directly) 때 자격 증명 업데이트 기능을 사용 하 여 사용자에 게 할당 된 사용자 이름 및 암호를 지정할 수 있습니다.

-   관리자가 사용자가 [응용 프로그램에 그룹을 할당할](#assign-an-application-to-a-group-directly) 때 자격 증명 업데이트 기능을 사용 하 여 사용자 그룹의 공유 사용자 이름 또는 암호를 지정할 수 있습니다.

다음 섹션에서는 [AZURE AD 응용 프로그램 갤러리](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)에 이미 있는 응용 프로그램에 대해 [암호 기반 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) 를 사용 하도록 설정 하는 방법을 설명 합니다.

## <a name="overview-of-required-steps"></a>필수 단계 개요
Azure AD 갤러리에서 응용 프로그램을 구성 하려면 다음을 수행 해야 합니다.

-   [Azure AD 갤러리에서 애플리케이션 추가](#add-an-application-from-the-azure-ad-gallery)

-   [암호 Single Sign-On에 대한 애플리케이션 구성](#configure-the-application-for-password-single-sign-on)

-   사용자 또는 그룹에 응용 프로그램을 할당 합니다.

    -   [애플리케이션에 사용자를 직접 할당](#assign-a-user-to-an-application-directly)

    -   [그룹에 애플리케이션을 직접 할당](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 애플리케이션 추가

Azure AD 갤러리에서 응용 프로그램을 추가 하려면 다음 단계를 수행 합니다.

1.  [Azure Portal](https://portal.azure.com)을 열고 **전역 관리자** 또는 **공동 관리자**권한으로 로그인 합니다.

2.  왼쪽 메뉴의 맨 위에서 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.

3.  검색 상자에 **azure active directory** 를 입력 한 다음 **Azure Active Directory** 항목을 선택 합니다.

4.  왼쪽의 Azure AD 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5.  **엔터프라이즈 응용 프로그램** 창의 오른쪽 위 모퉁이에 있는 **추가** 단추를 선택 합니다.

6.  **갤러리에서 추가** 섹션의 **이름 입력** 상자에 응용 프로그램의 이름을 입력 합니다.

7.  Single Sign-On에 대해 구성 하려는 응용 프로그램을 선택 합니다.

8.  응용 프로그램을 추가 하기 전에 **이름** 상자에서 이름을 변경할 수 있습니다.

9.  **추가** 를 선택 하 여 응용 프로그램을 추가 합니다.

잠시 후에 애플리케이션의 구성 창을 볼 수 있습니다.

## <a name="configure-the-application-for-password-single-sign-on"></a>암호 Single Sign-On에 대한 애플리케이션 구성

애플리케이션에 Single Sign-On을 구성하려면 아래 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/) 을 열고 **전역 관리자** 또는 **공동 관리자**권한으로 로그인 합니다.

2. 왼쪽 메뉴의 맨 위에서 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.

3. 검색 상자에 **azure active directory** 를 입력 한 다음 **Azure Active Directory** 항목을 선택 합니다.

4. 왼쪽의 Azure Active Directory 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

   여기에서 원하는 응용 프로그램이 표시 되지 않으면 **모든 응용 프로그램 목록**의 맨 위에 있는 **필터** 컨트롤을 사용 하 고 **표시** 옵션을 **모든 응용 프로그램**으로 설정 합니다.

6. Single Sign-On에 대해 구성 하려는 응용 프로그램을 선택 합니다.

7. 응용 프로그램이 로드 된 후 왼쪽의 응용 프로그램 메뉴에서 **Single sign-on** 을 선택 합니다.

8. **암호 기반 로그온** 모드를 선택 합니다.

9. [애플리케이션에 사용자를 할당합니다](#assign-a-user-to-an-application-directly).

10. 사용자의 행을 선택 하 고 **자격 증명 업데이트**를 선택한 다음 사용자 이름 및 암호를 입력 하 여 사용자 대신 자격 증명을 제공할 수도 있습니다. 그렇지 않으면 사용자가 응용 프로그램을 시작할 때 자격 증명을 입력 하 라는 메시지가 표시 됩니다.

## <a name="assign-a-user-to-an-application-directly"></a>애플리케이션에 사용자를 직접 할당

애플리케이션에 하나 이상의 사용자를 직접 할당하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 **전역 관리자 권한**으로 로그인 합니다.

2. 왼쪽 메뉴의 맨 위에서 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.

3. 검색 상자에 **azure active directory** 를 입력 한 다음 **Azure Active Directory** 항목을 선택 합니다.

4. 왼쪽의 Azure Active Directory 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

   여기에서 원하는 응용 프로그램이 표시 되지 않으면 **모든 응용 프로그램 목록**의 맨 위에 있는 **필터** 컨트롤을 사용 하 고 **표시** 옵션을 **모든 응용 프로그램**으로 설정 합니다.

6. 사용자를 할당 하려는 응용 프로그램을 선택 합니다.

7. 응용 프로그램이 로드 된 후 왼쪽의 응용 프로그램 메뉴에서 **사용자 및 그룹** 을 선택 합니다.

8. **사용자 및 그룹** 목록 맨 위에 있는 **추가** 단추를 선택 하 여 **할당 추가** 창을 엽니다.

9. **할당 추가** 창에서 **사용자 및 그룹** 을 선택 합니다.

10. **이름 또는 전자 메일 주소로 검색** 검색 상자에 사용자의 전체 이름 또는 전자 메일 주소를 입력 합니다.

11. 목록에서 사용자를 마우스로 가리킨 다음 사용자의 프로필 사진이 나 로고 옆의 확인란을 선택 하 여 **선택한** 목록에 추가 합니다.

12. 선택 사항: 사용자를 두 명 이상 추가 하려면 **이름 또는 전자 메일 주소로 검색** 상자에 다른 전체 이름 또는 전자 메일 주소를 입력 하 고 해당 사용자의 확인란을 선택 하 여 **선택한** 목록에 추가 합니다.

13. 사용자 선택이 완료 되 면 **선택** 단추를 사용 하 여 응용 프로그램에 할당 될 사용자 및 그룹의 목록에 추가 합니다.

14. 선택 사항: **할당 추가** 창에서 **역할 선택** 명령을 사용 하 여 선택한 사용자에 게 할당할 역할을 선택할 수 있습니다.

15. **할당** 을 선택 하 여 선택한 사용자에 게 응용 프로그램을 할당 합니다.

## <a name="assign-an-application-to-a-group-directly"></a>그룹에 애플리케이션을 직접 할당

애플리케이션에 하나 이상의 그룹을 직접 할당하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 **전역 관리자 권한**으로 로그인 합니다.

2. 왼쪽 메뉴의 맨 위에서 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.

3. 검색 상자에 **azure active directory** 를 입력 한 다음 **Azure Active Directory** 항목을 선택 합니다.

4. 왼쪽의 Azure AD 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

   여기에서 원하는 응용 프로그램이 표시 되지 않으면 **모든 응용 프로그램 목록** 의 맨 위에 있는 **필터** 컨트롤을 사용 하 고 **표시** 옵션을 **모든 응용 프로그램**으로 설정 합니다.

6. 사용자를 할당 하려는 응용 프로그램을 선택 합니다.

7. 응용 프로그램이 로드 된 후 왼쪽의 응용 프로그램 메뉴에서 **사용자 및 그룹** 을 선택 합니다.

8. **사용자 및 그룹** 목록의 맨 위에 있는 **추가** 단추를 선택 하 여 **할당 추가** 창을 엽니다.

9. **할당 추가** 창에서 **사용자 및 그룹** 을 선택 합니다.

10. **이름 또는 전자 메일 주소로 검색** 검색 상자에 할당 하려는 그룹의 전체 그룹 이름을 입력 합니다.

11. 목록에서 **그룹** 을 마우스로 가리킨 다음 그룹의 프로필 사진이 나 로고 옆의 확인란을 선택 하 여 그룹을 **선택한** 목록에 추가 합니다.

12. 선택 사항: 둘 이상의 그룹을 추가 하려면 **이름 또는 전자 메일 주소로 검색** 검색 상자에 다른 전체 그룹 이름을 입력 한 다음 해당 확인란을 선택 하 여이 그룹을 **선택한** 목록에 추가 합니다.

13. 그룹 선택을 마치면 **선택** 단추를 사용 하 여 응용 프로그램에 할당 될 사용자 및 그룹의 목록에 추가 합니다.

14. 선택 사항: **할당 추가** 창에서 **역할 선택** 명령을 사용 하 여 선택한 그룹에 할당할 역할을 선택할 수 있습니다.

15. **할당** 을 선택 하 여 선택한 그룹에 응용 프로그램을 할당 합니다.

잠시 후 선택한 사용자는 액세스 패널에서 이러한 응용 프로그램을 시작할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계
[응용 프로그램 프록시를 통해 앱에 Single Sign-On을 제공](application-proxy-configure-single-sign-on-with-kcd.md)합니다.
