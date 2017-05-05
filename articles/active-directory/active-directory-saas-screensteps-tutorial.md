---
title: "자습서: ScreenSteps와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 ScreenSteps를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7fbb8874367bda9be618332947ffa2c9942fe4ec
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>자습서: ScreenSteps와 Azure Active Directory 통합
이 자습서는 Azure와 ScreenSteps의 통합을 보여주기 위한 것입니다.
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* ScreenSteps 테넌트

이 자습서를 완료한 후 ScreenSteps에 할당한 Azure AD 사용자가 ScreenSteps 회사 사이트(서비스 공급자가 시작한 로그온)에서 SSO(Single Sign-On)를 사용하거나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 로그인할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. ScreenSteps에 응용 프로그램 통합 사용
2. SSO(Single Sign-On) 구성 
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-screensteps-tutorial/IC778516.png "시나리오")

## <a name="enable-the-application-integration-for-screensteps"></a>ScreenSteps에 응용 프로그램 통합 사용
이 섹션은 ScreenSteps에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

**ScreenSteps에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-screensteps-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-screensteps-tutorial/IC749321.png "응용 프로그램 추가")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-screensteps-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에 **ScreenSteps**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-screensteps-tutorial/IC778517.png "응용 프로그램 갤러리")
7. 결과 창에서 **ScreenSteps**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
## <a name="configure-single-sign-on"></a>Single Sign-On 구성
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 ScreenSteps에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

**Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **ScreenSteps** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여** Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Single Sign-On 구성")
2. **ScreenSteps에 대한 사용자 로그온 방법** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Single Sign-On 구성")

3. 다른 웹 브라우저 창에서 ScreenSteps 회사 사이트에 관리자로 로그인합니다.

4. **계정 관리**를 클릭합니다.

    ![계정 관리](./media/active-directory-saas-screensteps-tutorial/IC778523.png "계정 관리")

5. **Single Sign-On**을 클릭합니다.

    ![원격 인증](./media/active-directory-saas-screensteps-tutorial/IC778524.png "원격 인증")

6. **Single Sign-on 끝점 만들기**를 클릭합니다.

    ![원격 인증](./media/active-directory-saas-screensteps-tutorial/IC778525.png "원격 인증")

7. **Single Sign-on 끝점 만들기** 섹션에서 다음 단계를 수행합니다.

    ![인증 끝점 만들기](./media/active-directory-saas-screensteps-tutorial/IC778526.png "인증 끝점 만들기")

    1. **제목** 텍스트 상자에 제목을 입력합니다.
    2. **모드** 목록에서 **SAML**을 선택합니다.
    3. **만들기**를 클릭합니다.

8. 새 끝점을 편집합니다.

    ![끝점 편집](./media/active-directory-saas-screensteps-tutorial/IC778528.png "끝점 편집")

9. **Single Sign-on 끝점 편집** 섹션에서 다음 단계를 수행합니다.

    ![원격 인증 끝점](./media/active-directory-saas-screensteps-tutorial/IC778527.png "원격 인증 끝점")

    1. **SAML 소비자 URL**을 클립보드로 복사합니다.

10. Azure 클래식 포털의 **앱 URL 구성** 페이지에 있는 **ScreenSteps 로그인 URL** 텍스트 상자에서 **SAML 소비자 URL**을 붙여 넣고 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-screensteps-tutorial/IC778521.png "앱 URL 구성")

11. **ScreenSteps에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Single Sign-On 구성")


12. **Single Sign-on 끝점 편집** 섹션으로 돌아가 다음 단계를 수행합니다.

    ![원격 인증 끝점](./media/active-directory-saas-screensteps-tutorial/IC778527.png "원격 인증 끝점")

    1. **새 SAML 인증서 파일 업로드**를 클릭한 다음 다운로드한 인증서를 업로드합니다.
    2. Azure 클래식 포털의 **ScreenSteps에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **원격 로그인 URL** 텍스트 상자에 붙여넣습니다.
    3. Azure 클래식 포털의 **ScreenSteps에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    4. 사용자가 프로비전될 때 사용자를 할당할 **그룹**을 선택합니다.
    5. **업데이트**를 클릭합니다.
    6. **Single Sign-On 끝점 편집**으로 돌아갑니다.
    7. **계정의 기본값으로 지정** 단추를 클릭하여 ScreenSteps에 로그온하는 모든 사용자에 대해 이 끝점을 사용합니다. 또는 **사이트에 추가** 단추를 클릭하여 이 끝점을 **ScreenSteps**의 특정 사이트에 대해 사용할 수 있습니다.


12. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Single Sign-On 구성")

## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성



## <a name="assign-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

**ScreenSteps에 사용자를 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **ScreenSteps** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-screensteps-tutorial/IC778548.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.


SSO 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.


