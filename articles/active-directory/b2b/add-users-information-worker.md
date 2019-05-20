---
title: B2B 협업 사용자를 정보 근로자로 추가 - Azure Active Directory | Microsoft Docs
description: B2B 협업을 사용하여 액세스를 위해 정보 근로자와 앱 소유자가 게스트 사용자를 Azure AD에 추가 | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8606a0d4e203e1a910a5cd15ca83a622f5286bd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812545"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>조직의 사용자가 게스트 사용자를 앱에 초대할 수 있는 방법

Azure AD의 디렉터리에 게스트 사용자가 추가되면, 애플리케이션 소유자는 공유하려는 앱에 대한 직접 링크를 게스트 사용자에게 보낼 수 있습니다. Azure AD 관리자는 Azure AD 테넌트에서 갤러리 또는 SAML 기반 앱의 셀프 서비스 관리를 설정할 수도 있습니다. 이러한 방식으로 게스트 사용자가 아직 디렉터리에 추가되지 않은 경우에도 애플리케이션 소유자가 자신의 게스트 사용자를 관리할 수 있습니다. 앱이 셀프 서비스로 구성되면 애플리케이션 소유자는 해당 액세스 패널을 사용하여 게스트 사용자를 앱에 초대하거나 게스트 사용자를 앱에 액세스할 수 있는 그룹에 추가합니다. 갤러리 및 SAML 기반 앱의 셀프 서비스 앱 관리를 위해서는 관리자가 일부 초기 설정을 해야 합니다. 설정 단계에 대한 요약은 다음과 같습니다(자세한 내용은 이 페이지의 뒷부분에서 [필수 구성 요소](#prerequisites) 참조).

 - 테넌트를 위한 셀프 서비스 그룹 관리 사용
 - 앱에 할당할 그룹을 만들고 사용자를 소유자로 만들기
 - 셀프 서비스에 대한 앱 구성 및 앱에 그룹 할당

> [!NOTE]
> 이 문서에서는 Azure AD 테넌트에 추가한 갤러리 및 SAML 기반 앱의 셀프 서비스 관리를 설정하는 방법을 설명합니다. 사용자가 자신의 Office 365 그룹에 대한 액세스를 관리할 수 있도록 [셀프 서비스 Office 365 그룹을 설정](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)할 수도 있습니다. 보다 다양한 방법으로 사용자는 게스트 사용자와 Office 파일 및 앱을 공유할 수 있습니다. [Office 365 그룹에 대한 게스트 액세스](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) 및 [SharePoint 파일 또는 폴더 공유](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c)를 참조하세요.

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>액세스 패널에서 앱에 게스트 사용자 초대

앱이 셀프 서비스로 구성되면 애플리케이션 소유자는 자신의 액세스 패널을 사용하여 게스트 사용자를 공유하려는 앱에 초대할 수 있습니다. 게스트 사용자는 Azure AD에 반드시 미리 추가할 필요는 없습니다. 

1. `https://myapps.microsoft.com`으로 이동하여 액세스 패널을 엽니다.
2. 앱을 가리키도록 줄임표(**...**)를 선택한 다음, **앱 관리**를 선택합니다.
 
   ![Salesforce 앱에 대 한 관리 응용 프로그램 하위 메뉴를 보여 주는 스크린샷](media/add-users-iw/access-panel-manage-app.png)
 
3. 사용자 목록의 맨 위에서 **+** 를 선택합니다.
   
   ![앱에 구성원을 추가 하는 것에 대 한 더하기 기호를 보여 주는 스크린샷](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. **멤버 추가** 검색 상자에서 게스트 사용자의 이메일 주소를 입력합니다. 필요에 따라 환영 메시지가 포함됩니다.
   
   ![스크린샷 추가 게스트를 추가 하기 위한 멤버 창](media/add-users-iw/access-panel-invitation.png)
   
5. **추가**를 선택하여 게스트 사용자에게 초대를 발송합니다. 초대를 발송한 후 사용자 계정이 디렉터리에 게스트로 자동 추가됩니다.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>앱에 액세스할 수 있는 그룹에 가입할 누군가 초대하기
앱이 셀프 서비스로 구성되면 애플리케이션 소유자는 공유하려는 앱에 액세스할 수 있는 관리하는 그룹에 게스트 사용자를 초대할 수 있습니다. 게스트 사용자는 디렉터리에 아직 존재하지 않아도 됩니다. 애플리케이션 소유자는 다음 단계에 따라 그룹에 게스트 사용자를 초대하여 이들이 앱에 액세스할 수 있도록 합니다.

1. 공유하려는 앱에 대한 액세스 권한이 있는 셀프 서비스 그룹의 소유자여야 합니다.
2. `https://myapps.microsoft.com`으로 이동하여 액세스 패널을 엽니다.
3. **그룹** 앱을 선택합니다.
   
   ![액세스 패널에서 그룹 앱을 보여 주는 스크린샷](media/add-users-iw/access-panel-groups.png)
   
4. **내가 소유한 그룹**에서 공유하려는 앱에 액세스할 수 있는 그룹을 선택합니다.
   
   ![내가 소유한 그룹에서 그룹을 선택 하는 위치를 보여 주는 스크린샷](media/add-users-iw/access-panel-groups-i-own.png)
   
5. 사용자 멤버 목록의 맨 위에서 **+** 를 선택합니다.
   
   ![그룹에 구성원을 추가 하는 것에 대 한 더하기 기호를 보여 주는 스크린샷](media/add-users-iw/access-panel-groups-add-member.png)
   
6. **멤버 추가** 검색 상자에서 게스트 사용자의 이메일 주소를 입력합니다. 필요에 따라 환영 메시지가 포함됩니다.
   
   ![스크린샷 추가 게스트를 추가 하기 위한 멤버 창](media/add-users-iw/access-panel-invitation.png)
   
7. **추가**를 선택하여 게스트 사용자에게 자동으로 초대를 발송합니다. 초대를 발송한 후 사용자 계정이 디렉터리에 게스트로 자동 추가됩니다.


## <a name="prerequisites"></a>필수 조건

셀프 서비스 앱 관리를 위해서는 글로벌 관리자 및 Azure AD 관리자가 일부 초기 설정을 해야 합니다. 이 설정의 일부로 셀프 서비스를 위해 앱을 구성하고 애플리케이션 소유자가 관리할 수 있는 그룹을 앱에 할당합니다. 모든 사용자가 멤버 자격을 요청할 수 있지만 그룹 소유자의 승인이 필요하도록 그룹을 구성할 수도 있습니다. ([셀프 서비스 그룹 관리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)에 대해 자세히 알아보세요.) 

> [!NOTE]
> 동적 그룹 또는 온-프레미스 Active Directory와 동기화된 그룹에는 게스트 사용자를 추가할 수 없습니다.

### <a name="enable-self-service-group-management-for-your-tenant"></a>테넌트를 위한 셀프 서비스 그룹 관리 사용
1. 글로벌 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **그룹**을 선택합니다.
4. **설정**에서 **일반**을 선택합니다.
5. **셀프 서비스 그룹 관리** 아래, **소유자가 액세스 패널에서 그룹 멤버 자격 요청을 관리할 수 있음** 옆에서 **예**를 선택합니다.
6. **저장**을 선택합니다.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>앱에 할당할 그룹을 만들고 사용자를 소유자로 만들기
1. Azure AD 관리자 또는 글로벌 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **그룹**을 선택합니다.
4. **새 그룹**을 선택합니다.
5. **그룹 유형**에서 **보안**을 선택합니다.
6. **그룹 이름** 및 **그룹 설명**을 입력합니다.
7. **멤버 자격 유형**에서 **할당됨**을 선택합니다.
8. **만들기**를 선택하고 **그룹** 페이지를 닫습니다.
9. **그룹 - 모든 그룹** 페이지에서 그룹을 엽니다. 
10. **관리**에서 **소유자** > **소유자 추가**를 선택합니다. 애플리케이션에 대한 액세스를 관리해야 하는 사용자를 검색합니다. 사용자를 선택한 다음, **선택**을 클릭합니다.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>셀프 서비스에 대한 앱 구성 및 앱에 그룹 할당
1. Azure AD 관리자 또는 글로벌 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.
4. 애플리케이션 목록에서 앱을 찾아 엽니다.
5. **관리**에서 **Single Sign-On**을 선택하고 Single Sign-On에 대한 애플리케이션을 구성합니다. (자세한 내용은 [엔터프라이즈 앱에 대한 Single Sign-On 관리 방법](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)을 참조하세요.)
6. **관리**에서 **셀프 서비스**를 선택하고 셀프 서비스 앱 액세스를 설정합니다. (자세한 내용은 [셀프 서비스 앱 액세스 사용 방법](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)을 참조하세요.) 

    > [!NOTE]
    > **할당된 사용자는 어느 그룹에 추가되어야 합니까?** 설정에 대해서는 이전 섹션에서 만든 그룹을 선택합니다.
7. **관리**에서 **사용자 및 그룹**을 선택하고 생성한 셀프 서비스 그룹이 목록에 나타나는지 확인합니다.
8. 그룹 소유자의 액세스 패널에 앱을 추가하려면 **사용자 추가** > **사용자 및 그룹**을 선택합니다. 그룹 소유자를 검색하여 사용자를 선택하고, **선택**을 클릭한 다음, **할당**을 클릭하여 사용자를 앱에 추가합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
- [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](add-users-administrator.md)
- [B2B 공동 작업 초대 상환](redemption-experience.md)
- [Azure AD B2B 공동 작업 라이선스](licensing-guidance.md)
