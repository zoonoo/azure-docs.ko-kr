---
title: Single Sign-On 구성 - Azure Active Directory | Microsoft Docs
description: 이 자습서는 Azure Portal을 사용하여 Azure AD(Azure Active Directory)에서 애플리케이션에 대해 SAML 기반 Single Sign-On을 구성합니다.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 12/06/2018
ms.author: barbkess
ms.reviewer: arvinh,luleon
ms.openlocfilehash: aceacdea8b3c86a5c4f26a5f082f4c6cf0b3805d
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011980"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>자습서: Azure Active Directory에서 애플리케이션에 대한 SAML 기반 Single Sign-On 구성

이 자습서는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure AD(Azure Active Directory)에서 애플리케이션에 대해 SAML 기반 Single Sign-On을 구성합니다. [애플리케이션 관련 자습서](../saas-apps/tutorial-list.md)를 사용할 수 없는 경우 이 자습서를 사용합니다. 

이 자습서에서는 다음 작업에 Azure Portal을 사용합니다.

> [!div class="checklist"]
> * SAML 기반 Single Sign-On 모드 선택
> * 애플리케이션 관련 도메인 및 URL 구성
> * 사용자 특성 구성
> * SAML 서명 인증서 만들기
> * 응용 프로그램에 사용자 할당
> * SAML 기반 Single Sign-On에 대한 애플리케이션 구성
> * SAML 설정 테스트

## <a name="before-you-begin"></a>시작하기 전에

1. 애플리케이션이 Azure AD 테넌트에 추가되지 않은 경우 [빠른 시작: Azure AD 테넌트에 애플리케이션 추가](add-application-portal.md)를 참조하세요.

2. [도메인 및 URL 구성](#configure-domain-and-urls)에 설명된 정보는 응용 프로그램 공급 업체에 요청하세요.

3. 이 자습서의 단계를 테스트하려면 비-프로덕션 환경을 사용하는 것이 좋습니다. Azure AD 비-프로덕션 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 받을 수 있습니다.

4. [Azure Portal](https://portal.azure.com)에 Azure AD 테넌트의 클라우드 애플리케이션 관리자 또는 애플리케이션 관리자로 로그인합니다.

## <a name="select-a-single-sign-on-mode"></a>Single Sign-On 모드 선택

애플리케이션을 Azure AD 테넌트에 추가하면 애플리케이션에서 Single Sign-On을 구성할 준비가 되었습니다.

Single Sign-On 설정을 열려면:

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory**를 클릭합니다. 

2. **Azure Active Directory** 블레이드에서 **엔터프라이즈 응용 프로그램**을 클릭합니다. Azure AD 테넌트에 있는 임의의 애플리케이션 샘플을 보여주는 **모든 애플리케이션** 블레이드가 열립니다. 

3. **응용 프로그램 종류** 메뉴에서 **모든 응용 프로그램**을 선택하고 **적용**을 클릭합니다.

4. Single Sign-On을 구성하려는 애플리케이션의 이름을 입력합니다. 고유한 애플리케이션을 선택하거나, **GitHub-test**를 입력하여 [애플리케이션 추가](add-application-portal.md) 빠른 시작에 추가한 애플리케이션을 구성합니다.

5. **Single Sign-On**을 클릭합니다. **Single Sign-On 모드**에서 **SAML 기반 로그온**이 기본 옵션으로 표시됩니다. 

    ![구성 옵션](media/configure-single-sign-on-portal/config-options.png)

6. 블레이드 위쪽에서 **저장**을 클릭합니다. 

## <a name="configure-domain-and-urls"></a>도메인 및 URL 구성

도메인 및 URL을 구성하려면:

1. 다음 설정에 대한 정확한 정보를 가져오려면 애플리케이션 공급 업체에 문의하세요.

    | 구성 설정 | SP 시작 | idP 시작 | 설명 |
    |:--|:--|:--|:--|
    | 로그온 URL | 필수 | 지정하지 않음 | 사용자가 이 URL을 열면 서비스 공급자가 Azure AD를 리디렉션하여 사용자를 인증하고 로그온하도록 합니다. Azure AD는 URL을 사용하여 Office 365 또는 Azure AD 액세스 패널에서 애플리케이션을 시작합니다. 비어 있는 경우 Azure AD는 ID 공급자를 사용하여 사용자가 애플리케이션을 시작할 때 Single Sign-On을 시작합니다.|
    | 식별자(엔터티 ID) | 일부 앱의 경우 필수 | 일부 앱의 경우 필수 | 구성될 Single Sign-On에 대해 애플리케이션을 고유하게 식별합니다. Azure AD는 SAML 토큰의 대상 매개 변수로 애플리케이션에 식별자를 보냅니다. 애플리케이션이 식별자의 유효성을 검사해야 합니다. 또한 이 값은 응용 프로그램에서 제공하는 모든 SAML 메타데이터 내에서 엔터티 ID로 표시됩니다.|
    | 회신 URL | 옵션 | 필수 | 애플리케이션이 SAML 토큰을 수신해야 하는 위치를 지정합니다. 회신 URL은 ACS(Assertion Consumer Service) URL이라고도 합니다. |
    | 릴레이 상태 | 옵션 | 옵션 | 인증이 완료되면 사용자를 리디렉션할 위치를 애플리케이션에 지정합니다. 일반적으로 해당 값은 애플리케이션에서 유효한 URL이지만 일부 애플리케이션은 이 필드를 다르게 사용합니다 자세한 내용은 애플리케이션 공급 업체에 요청하세요.

2. 정보를 입력합니다. 모든 설정을 보려면 **고급 URL 설정 표시**를 클릭합니다.

    ![구성 옵션](media/configure-single-sign-on-portal/config-urls.png)

3. 블레이드 위쪽에서 **저장**을 클릭합니다.

4. 이 섹션에는 **SAML 설정 테스트** 단추가 있습니다. [Single Sign-On 테스트](#test-single-sign-on) 섹션에 있는 자습서의 뒷부분에서 이 테스트를 실행합니다.

## <a name="configure-user-attributes"></a>사용자 특성 구성

사용자 특성을 사용하면 사용자가 로그인할 때마다 Azure AD에서 SAML 토큰으로 애플리케이션에 보내는 정보를 제어할 수 있습니다. 예를 들어 Azure AD는 애플리케이션에 사용자의 이름, 이메일 및 직원 ID를 보낼 수 있습니다. 

이러한 특성은 Single Sign-On이 제대로 작동하기 위해 필수 또는 선택 사항일 수 있습니다. 자세한 내용은 [애플리케이션 관련 자습서](../saas-apps/tutorial-list.md) 또는 애플리케이션 공급 업체를 참조하세요.

1. 모든 옵션을 보려면 **기타 모든 사용자 특성 보기 및 편집**을 클릭합니다.

    ![사용자 특성 구성](media/configure-single-sign-on-portal/config-user-attributes.png)

2. **사용자 ID**를 입력합니다.

    사용자 ID는 애플리케이션 내에서 각 사용자를 고유하게 식별합니다. 예를 들어 이메일 주소가 사용자 이름 및 고유 식별자 모두인 경우 값을 *user.mail*로 설정합니다.

3. 자세한 SAML 토큰 특성은 **기타 모든 사용자 특성 보기 및 편집**을 클릭합니다.

4. 특성을 **SAML 토큰 특성**에 추가하려면 **특성 추가**를 클릭합니다. **이름**을 입력하고, 메뉴에서 **값**을 선택합니다.

5. **저장**을 클릭합니다. 테이블에 새 특성이 표시됩니다.
 
## <a name="create-a-saml-signing-certificate"></a>SAML 서명 인증서 만들기

Azure AD에서는 인증서를 사용하여 애플리케이션에 보내는 SAML 토큰에 서명합니다. 

1. 모든 옵션을 보려면 **고급 인증서 서명 표시 옵션**을 클릭합니다.

    ![인증서 구성](media/configure-single-sign-on-portal/config-certificate.png)

2. 인증서를 구성하려면 **새 인증서 만들기**를 클릭합니다.

3. **새 인증서 만들기** 블레이드에서 **만료 날짜**를 설정하고 **저장**을 클릭합니다.

4. **새 인증서 활성화**를 클릭합니다.

5. 자세한 내용은 [고급 인증서 서명 옵션](certificate-signing-options.md)을 참조하세요.

6. 지금까지 수행한 변경 내용을 유지하려면 **Single Sign-On** 블레이드 맨 위에 있는 **저장**을 클릭해야 합니다. 

## <a name="assign-users-to-the-application"></a>응용 프로그램에 사용자 할당

애플리케이션을 조직에 롤아웃하기 전에 여러 사용자 또는 그룹에서 Single Sign-On을 테스트하는 것이 좋습니다.

애플리케이션에 사용자 또는 그룹을 할당하려면:

1. 애플리케이션이 열려 있지 않으면 포털에서 엽니다.
2. 왼쪽 애플리케이션 블레이드에서 **사용자 및 그룹**을 클릭합니다.
3. **사용자 추가**를 클릭합니다.
4. **할당 추가** 블레이드에서 **사용자 및 그룹**을 클릭합니다.
5. 특정 사용자를 찾으려면 **선택** 상자에 사용자 이름을 입력하고, 사용자의 프로필 사진이나 로고 옆의 확인란을 클릭하고, **선택**을 클릭합니다. 
6. 현재 사용자 이름을 찾아 선택합니다. 필요에 따라 더 많은 사용자를 선택할 수 있습니다.
7. **할당 추가** 블레이드에서 **할당**을 클릭합니다. 작업이 완료되면 선택한 사용자가 **사용자 및 그룹** 목록에 표시됩니다.

## <a name="configure-the-application-to-use-azure-ad"></a>Azure AD를 사용하도록 애플리케이션 구성

이제 거의 완료되었습니다.  마지막 단계로 Azure AD를 SAML ID 공급자로 사용하도록 애플리케이션을 구성해야 합니다. 

1. 애플리케이션의 **Single Sign-On** 블레이드에서 끝까지 아래로 스크롤합니다. 

    ![응용 프로그램 구성](media/configure-single-sign-on-portal/configure-app.png)

2. 포털에서 **애플리케이션 구성**을 클릭하고 지침에 따릅니다.
3. Single Sign-On을 테스트하기 위해 애플리케이션에서 사용자 계정을 수동으로 만듭니다. [이전 섹션](#assign-users-to-the-application)에서 응용 프로그램에 할당된 사용자 계정을 만듭니다. 

## <a name="test-single-sign-on"></a>Single Sign-On 테스트

설정을 테스트할 준비가 되었습니다.  

1. 애플리케이션의 Single Sign-On 설정을 엽니다. 
2. **도메인 및 URL 구성** 섹션까지 스크롤합니다.
2. **SAML 설정 테스트**를 클릭합니다. 테스트 옵션이 표시됩니다.

    ![Single Sign-On 테스트 옵션](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. **현재 사용자로 로그인**을 클릭합니다. 이 테스트를 통해 관리자인 사용자에 대해 Single Sign-On이 작동하는지를 먼저 확인할 수 있습니다.
4. 오류가 있으면 오류 메시지가 표시됩니다. 세부 정보를 **어떤 오류인 것 같습니까?** 상자에 복사하고 붙여넣습니다.

    ![해결 지침 가져오기](media/configure-single-sign-on-portal/error-guidance.png)

5. **해결 지침 가져오기**를 클릭합니다. 근본 원인 및 해결 지침이 표시됩니다.  이 예제에서 사용자는 애플리케이션에 할당되지 않았습니다.

    ![오류 수정](media/configure-single-sign-on-portal/fix-error.png)

6. 해결 지침을 읽은 다음, 적절한 경우 **수정**을 클릭합니다.

7. 성공적으로 완료될 때까지 테스트를 다시 실행합니다.



## <a name="next-steps"></a>다음 단계
이 자습서에서는 애플리케이션에 대한 Single Sign-On 설정을 구성했습니다. 구성을 마치면 애플리케이션에 사용자를 할당하고, SAML 기반 Single Sign-On을 사용하도록 애플리케이션을 구성했습니다. 이 작업이 모두 완료되면 SAML 로그온이 제대로 작동하는지 확인했습니다.

다음 작업을 수행했습니다.
> [!div class="checklist"]
> * Single Sign-On 모드에 대해 SAML 선택
> * 도메인 및 URL을 구성하기 위해 애플리케이션 공급 업체 연결
> * 사용자 특성 구성
> * SAML 서명 인증서 생성
> * 애플리케이션에 사용자 또는 그룹 수동으로 할당
> * Azure AD를 SAML ID 공급자로 사용하도록 애플리케이션 구성
> * SAML 기반 Single Sign-On 테스트

조직에서 더 많은 사용자에게 애플리케이션을 롤아웃하려면 자동 사용자 프로비저닝을 사용하는 것이 좋습니다.

> [!div class="nextstepaction"]
>[자동 프로비저닝을 사용하여 사용자를 할당하는 방법 알아보기](configure-automatic-user-provisioning-portal.md)


