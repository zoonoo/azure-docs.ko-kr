---
title: "Azure AD 응용 프로그램 프록시를 사용하여 앱 게시 | Microsoft Docs"
description: "Azure Portal에서 Azure AD 응용 프로그램 프록시를 사용하여 클라우드에 온-프레미스 응용 프로그램을 게시합니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 8cb8aff4c3035ef8557e0ae0d254fcd750a9228a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017

---


# <a name="publish-applications-using-azure-ad-application-proxy---public-preview"></a>Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시

> [!div class="op_single_selector"]
> * [Azure Portal](application-proxy-publish-azure-portal.md)
> * [Azure 클래식 포털](active-directory-application-proxy-publish.md)

Azure AD(Active Directory) 응용 프로그램 프록시를 사용하면 인터넷을 통해 액세스할 수 있는 온-프레미스 응용 프로그램을 게시하여 원격 작업자를 지원할 수 있습니다. Azure Portal을 통해 로컬 네트워크에서 실행되는 응용 프로그램을 게시하고 네트워크 외부에서 안전한 원격 액세스를 제공할 수 있습니다.

이 문서에서는 응용 프로그램 프록시를 통해 온-프레미스 앱을 게시하는 방법을 단계별로 안내합니다. 이 문서를 완료하면 Single Sign-On 개인 설정 정보 또는 보안 요구 사항에 맞춰 응용 프로그램을 구성할 준비가 된 것입니다.

응용 프로그램 프록시를 처음 사용하는 경우 [온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법](active-directory-application-proxy-get-started.md) 문서에서 이 기능에 대해 자세히 알아보세요.


## <a name="publish-an-on-premises-app-for-remote-access"></a>원격 액세스를 위한 온-프레미스 앱 게시


> [!TIP]
> 응용 프로그램 프록시를 처음 사용하는 경우 이미 암호 기반 인증으로 설정된 응용 프로그램을 선택합니다. 응용 프로그램 프록시에서는 다른 유형의 인증을 지원하지만 암호 기반 앱이 가장 빠르고 쉽게 시작하고 실행할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에서 관리자로 로그인합니다.
2. **Azure Active Directory** > **Enterprise 응용 프로그램** > **새로운 응용 프로그램**을 선택합니다.

  ![엔터프라이즈 응용 프로그램 추가](./media/application-proxy-publish-azure-portal/add-app.png)

3. 범주 페이지에서 **온-프레미스 응용 프로그램**을 선택합니다.  

  ![응용 프로그램 직접 추가](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. 응용 프로그램에 대한 다음과 같은 정보를 제공합니다.

   - **이름**: 액세스 패널에 표시될 응용 프로그램의 이름입니다. 

   - **내부 URL**: 응용 프로그램 프록시 커넥터가 개인 네트워크 내부에서 응용 프로그램에 액세스하는 데 사용하는 주소입니다. 나머지 서버는 게시되지 않은 반면 게시할 백 앤드 서버에 특정 경로를 제공할 수 있습니다. 이렇게 하면 다른 앱과 동일한 서버에 여러 사이트를 게시하고 각 사이트에 고유한 이름과 액세스 규칙을 부여할 수 있습니다.

     > [!TIP]
     > 경로를 게시하는 경우 응용 프로그램에 필요한 이미지, 스크립트 및 스타일 시트를 모두 포함하는지 확인합니다. 예를 들어 앱이 https://yourapp/app에 위치하고 https://yourapp/media에 있는 이미지를 사용하는 경우 https://yourapp/을 경로로 게시해야 합니다.

   - **외부 URL**: 네트워크 외부에서 앱에 액세스하기 위해 사용자가 이동할 주소입니다.
   - **사전 인증**: 응용 프로그램 프록시가 사용자에게 응용 프로그램에 대한 액세스 권한을 부여하기 전에 사용자를 확인하는 방법입니다. 

     - Azure Active Directory: 응용 프로그램 프록시는 Azure AD를 사용하여 로그인하는 사용자를 리디렉션하여 디렉터리와 응용 프로그램에 대한 사용 권한을 인증합니다. 이 옵션을 기본적으로 유지하는 것이 좋습니다.
     - 통과: 사용자는 응용 프로그램에 액세스하기 위해 Azure Active Directory에 대해 인증할 필요가 없습니다. 백 엔드에 대한 인증 요구 사항은 여전히 설정할 수 있습니다.
   - **헤더에서 URL 변환?**: 헤더의 URL을 변환할지 또는 원본을 유지할지의 여부를 선택합니다. 
   - **커넥터 그룹**: 커넥터는 응용 프로그램에 대한 원격 액세스를 처리하고, 커넥터 그룹은 지역, 네트워크 또는 용도별로 커넥터와 앱을 구성하는 데 도움을 줍니다. 만든 커넥터 그룹이 아직 없으면 앱이 **기본**에 할당되고 [커넥터 그룹을 만들도록](active-directory-application-proxy-connectors-azure-portal.md) 요청하는 경고 메시지가 표시됩니다.

   ![응용 프로그램 구성](./media/application-proxy-publish-azure-portal/configure-app.png)

8. **추가**를 선택합니다.


## <a name="add-a-test-user"></a>테스트 사용자 추가 

앱이 올바르게 게시되었는지 테스트하려면 액세스 권한이 있는 사용자 계정을 추가합니다. 

1. [빠른 시작] 블레이드로 돌아가서 **테스트할 사용자 지정**을 선택합니다.

  ![테스트할 사용자 지정](./media/application-proxy-publish-azure-portal/assign-user.png)

2. [사용자 및 그룹] 블레이드에서 **추가**를 선택합니다.

  ![사용자 또는 그룹 추가](./media/application-proxy-publish-azure-portal/add-user.png)

3. [할당 추가] 블레이드에서 **사용자 및 그룹**을 선택한 다음 추가할 계정을 선택합니다. 
4. **할당**을 선택합니다.

## <a name="test-your-published-app"></a>게시된 앱 테스트

브라우저에서 게시 단계에서 구성한 외부 URL로 이동합니다. 시작 화면이 표시되고 설정한 테스트 계정으로 로그인할 수 있습니다.

![게시된 앱 테스트](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>다음 단계
- [커넥터 다운로드](active-directory-application-proxy-enable.md) 및 [커넥터 그룹 만들기](active-directory-application-proxy-connectors-azure-portal.md)를 수행하여 별도의 네트워크와 위치에 응용 프로그램을 게시합니다.

- 새로 게시된 앱에 대해 [SSO(Single Sign-On) 설정](application-proxy-sso-azure-portal.md)을 수행합니다.

