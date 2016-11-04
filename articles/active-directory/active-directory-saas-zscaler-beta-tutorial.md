---
title: '자습서: Azure Active Directory와 Zscaler Beta 통합 | Microsoft Docs'
description: Azure Active Directory에서 ZScaler Beta을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/16/2016
ms.author: jeedes

---
# 자습서: Azure Active Directory와 Zscaler Beta 통합
이 자습에서는 Azure와 ZScaler Beta 통합을 보여줍니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* ZScaler Beta sign-on이 설정된 구독

이 자습서를 완료하면 ZScaler Beta에 할당한 Azure AD 사용자가 ZScaler Beta 회사 사이트 (로그온을 시작한 서비스 공급자)에서, 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. ZScaler Beta에 응용 프로그램 통합
2. Single Sign-On 구성
3. 프록시 설정 구성
4. 사용자 프로비전 구성
5. 사용자 할당

![시나리오](./media/active-directory-saas-zscaler-beta-tutorial/IC800223.png "시나리오")

## ZScaler Beta에 응용 프로그램 통합
이 섹션에서는 ZScaler Beta에 응용 프로그램 통합 사용 방법을 간략하게 설명합니다.

### ZScaler Beta에 응용 프로그램 통합을 사용하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-zscaler-beta-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-zscaler-beta-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-zscaler-beta-tutorial/IC749321.png "응용 프로그램 추가")
5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-zscaler-beta-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에서 **ZScaler Beta**를 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-zscaler-beta-tutorial/IC800224.png "응용 프로그램 갤러리")
7. 결과 창에서 **ZScaler Beta**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![ZScaler One.](./media/active-directory-saas-zscaler-beta-tutorial/IC800216.png "ZScaler One.")

## Single Sign-On 구성
이 섹션의 목적은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD에서 자신의 계정으로 ZScaler Beta에 인증할 수 있게 하는 방법을 간략하게 설명하는 것입니다. 이 절차의 일부로 base-64로 인코딩 된 인증서를 ZScaler Beta 테넌트로 업로드 해야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

### Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **ZScaler Beta** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-zscaler-beta-tutorial/IC800225.png "Single Sign-On 구성")
2. **ZScaler Beta에 대한 사용자 로그온 방법을 선택하십시오** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택한 후, **다음**을 클릭하세요.
   
   ![Single Sign-On 구성](./media/active-directory-saas-zscaler-beta-tutorial/IC800226.png "Single Sign-On 구성")
3. **ZScaler Beta 로그인 URL** 텍스트 상자의 **앱 URL 구성** 페이지에서 사용자가 ZScaler Beta 응용 프로그램에 로그인할 때 사용하는 URL을 입력한 후, **다음**을 클릭합니다.
   
   ![앱 URL 구성](./media/active-directory-saas-zscaler-beta-tutorial/IC800227.png "앱 URL 구성")
   
   > [!NOTE]
   > 필요할 경우 ZScaler Beta 지원팀에서 사용자 환경의 실제값을 얻을 수 있습니다.
   > 
   > 
4. 인증서를 다운로드 하려면 **ZScaler Beta에서 Single Sign-On 구성**의 **인증서 다운로드**를 클릭하여 컴퓨터에 인증서 파일을 저장합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-zscaler-beta-tutorial/IC800228.png "Single Sign-On 구성")
5. 다른 웹 브라우저 창에서 관리자 권한으로 ZScaler 베타 회사 사이트에 로그인 합니다.
6. 위쪽의 메뉴에서 **관리**를 클릭합니다.
   
   ![관리](./media/active-directory-saas-zscaler-beta-tutorial/IC800206.png "관리")
7. **관리자 & 역할 관리**에서 **사용자 & 인증 관리**를 클릭합니다.
   
   ![사용자 및 인증 관리](./media/active-directory-saas-zscaler-beta-tutorial/IC800207.png "사용자 및 인증 관리")
8. **구성에 대한 인증 옵션 선택** 섹션에서, 다음 단계를 수행합니다.
   
   ![인증](./media/active-directory-saas-zscaler-beta-tutorial/IC800208.png "인증")
   
   1. **SAML Single Sign-On을 사용하여 인증**을 선택합니다.
   2. **SAML Single Sign-On 매개 변수 구성**을 클릭합니다.
9. **SAML Single Sign-On 매개 변수 구성**대화 상자 페이지에서 다음 단계를 수행하고 **완료**를 클릭합니다.
   
   ![SSO(Single sign-on)](./media/active-directory-saas-zscaler-beta-tutorial/IC800209.png "SSO\(Single sign-on\)")
   
   1. Azure 클래식 포털의 **ZScaler Beta에서 Single Sign-On 구성** 대화 상자 페이지에서 **인증 요청 URL**을 복사하여 **사용자가 인증을 위해 보낸 SAML 포털의 URL** 텍스트 상자에 붙여 넣습니다.
   2. **로그인 이름을 포함한 특성** 텍스트 상자에 **NameID**를 입력합니다.
   3. **Zscaler pem**을 클릭하여 다운로드한 인증서를 업로드합니다.
   4. **SAML 자동 프로비전 사용**을 선택합니다.
10. **사용자 인증 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
    
    ![관리](./media/active-directory-saas-zscaler-beta-tutorial/IC800210.png "관리")
    
    1. **Save**를 클릭합니다.
    2. **지금 활성화**를 클릭합니다.
11. Azure 클래식 포털의 **ZScaler Beta에서 Single Sign-On 구성** 대화 상자 페이지에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭합니다.
    
    ![Single Sign-On 구성](./media/active-directory-saas-zscaler-beta-tutorial/IC800229.png "Single Sign-On 구성")

## 프록시 설정 구성
### Internet Explorer에서 프록시 설정을 구성하려면
1. **Internet Explorer**를 시작합니다.
2. **도구** 메뉴에서 **인터넷 옵션**을 선택하여 **인터넷 옵션** 대화 상자를 엽니다.
   
   ![인터넷 옵션](./media/active-directory-saas-zscaler-beta-tutorial/IC769492.png "인터넷 옵션")
3. **연결** 탭을 클릭합니다.
   
   ![연결](./media/active-directory-saas-zscaler-beta-tutorial/IC769493.png "연결")
4. **LAN 설정**을 클릭하여 **LAN 설정** 대화 상자를 엽니다.
5. 프록시 서버 섹션에서 다음 단계를 수행합니다.
   
   ![프록시 서버](./media/active-directory-saas-zscaler-beta-tutorial/IC769494.png "프록시 서버")
   
   1. 사용자 LAN의 프록시 서버 사용을 선택합니다.
   2. 주소 텍스트 상자에**gateway.zscalerBeta.net**을 입력합니다.
   3. 포트 텍스트 상자에 **80**을 입력합니다.
   4. **로컬 주소의 바이패스 프록시 서버**를 선택합니다.
   5. **확인**을 클릭하여 **LAN(Local Area Network) 설정** 대화 상자를 닫습니다.
6. **확인**을 클릭하여 **인터넷 옵션** 대화 상자를 닫습니다.

## 사용자 프로비전 구성
Azure AD 사용자가 ZScaler Beta에 로그인할 수 있도록 하려면 사용자 계정이 ZScaler Beta로 프로비전되어야 합니다. ZScaler 베타의 경우, 수동으로 프로비전합니다.

### 사용자 프로비저닝을 구성하려면
1. **Zscaler** 테넌트에 로그인 합니다.
2. **관리**를 클릭합니다.
   
   ![관리](./media/active-directory-saas-zscaler-beta-tutorial/IC781035.png "관리")
3. **사용자 관리**를 클릭합니다.
   
   ![추가](./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "추가")
4. **사용자** 탭에서 **추가**를 클릭합니다.
   
   ![추가](./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "추가")
5. 사용자 추가 섹션에서 다음 단계를 수행합니다.
   
   ![사용자 추가](./media/active-directory-saas-zscaler-beta-tutorial/IC781038.png "사용자 추가")
   
   1. **사용자ID**, **사용자 표시 이름**, **암호**, **암호 확인**을 입력하고, 프로비전하고자 하는 유효한 AAD 계정의 **그룹** 및 **부서**를 선택합니다.
   2. **Save**를 클릭합니다.

> [!NOTE]
> 다른 ZScaler Beta 사용자 계정 생성 도구 또는 Zendesk에서 제공하는 APIs를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
> 
> 

## 사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### ZScaler 베타로 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **ZScaler Beta** 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
   ![사용자 할당](./media/active-directory-saas-zscaler-beta-tutorial/IC800230.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-zscaler-beta-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

<!---HONumber=AcomDC_0817_2016-->