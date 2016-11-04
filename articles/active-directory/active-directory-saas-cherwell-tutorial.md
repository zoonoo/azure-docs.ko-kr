---
title: '자습서: Cherwell과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory에서 Cherwell를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2016
ms.author: jeedes

---
# 자습서: Cherwell와 Azure Active Directory 통합
이 자습서는 Azure 및 Cherwell의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* Cherwell Single Sign-on이 설정된 구독

이 자습서를 완료한 후 Cherwell에 할당한 Azure AD 사용자가 Cherwell 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. Cherwell에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-cherwell-tutorial/IC798988.png "시나리오")

## Cherwell에 응용 프로그램 통합 사용
이 섹션은 Cherwell에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### Cherwell에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-cherwell-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-cherwell-tutorial/IC749321.png "응용 프로그램 추가")
5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-cherwell-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에 **Cherwell**을 입력합니다.
   
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")
7. 결과 창에서 **Cherwell**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ## Single Sign-On 구성
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Cherwell에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

### Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **Cherwell** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Single Sign-On 구성")
2. **Cherwell에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Single Sign-On 구성")
3. **앱 URL 구성** 페이지에서 다음 단계를 수행합니다.
   
   ![앱 URL 구성](./media/active-directory-saas-cherwell-tutorial/IC798992.png "앱 URL 구성")
   
   a. **로그온 URL** 텍스트 상자에 사용자가 **Cherwell**에 로그인하는 데 사용하는 URL을 입력합니다.(예: *https://\<company name>.cherwellondemand.com/cherwellclient*)
   
   b. **다음**를 클릭합니다.
4. **Cherwell의 Single Sign-On을 구성** 페이지에서 다음 단계를 수행합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Single Sign-On 구성")
   
   a. **인증서 다운로드**를 클릭하고 인증서를 컴퓨터에 로컬로 저장합니다.
   
   b. **ID 공급자 URL**을 복사합니다.
   
   c. **Single Sign-On 서비스 URL**을 복사합니다.
   
   d. **다음**을 클릭합니다.
5. 다운로드한 인증서, **ID 공급자 URL** 및 **Single Sign-On 서비스 URL**을 Cherwell 지원팀에 제출합니다.
   
   > [!NOTE]
   > Cherwell 지원팀은 실제 SSO 구성을 수행해야 합니다.
   > 구독에 SSO를 사용하도록 설정하면 알림을 받을 수 있습니다.
   > 
   > 
6. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Single Sign-On 구성")

## 사용자 프로비전 구성
Azure AD 사용자가 Cherwell에 로그인할 수 있도록 하려면 Cherwell로 프로비전되어야 합니다.  
Cherwell의 경우 사용자 계정을 Cherwell 지원 팀에서 작성해야 합니다.

> [!NOTE]
> Cherwell에서 제공하는 다른 Cherwell 사용자 계정 만들기 도구 또는 API를 사용하여 Azure Active Directory 사용자 계정를 프로비전합니다.
> 
> 

## 사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### Cherwell에 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **Cherwell** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
   ![사용자 할당](./media/active-directory-saas-cherwell-tutorial/IC798995.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-cherwell-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0803_2016-->