<properties 
    pageTitle="Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그하는 방법 | Microsoft Azure" 
    description="Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/18/2015" 
    ms.author="asmalser" />

#Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그하는 방법

SAML 기반 응용 프로그램 통합을 디버그할 때 종종 [Fiddler](http://www.telerik.com/fiddler)와 같은 도구를 사용하여 SAML 요청, SAML 응답 및 응용 프로그램에 발급된 실제 SAML 토큰을 확인하는 것이 유용합니다. SAML 토큰을 검사하면 모든 필수 특성, SAML 주체의 사용자 이름 및 발급자 URI가 예상대로 제공되는지 확인할 수 있습니다.

![][1]

SAML 토큰이 포함된 Azure AD의 응답은 일반적으로 https://login.windows.net에서 HTTP 302가 리디렉션되고 응용 프로그램의 구성된 **회신 URL**로 전송된 후에 발생하는 응답입니다.
 
이 줄을 선택한 다음 오른쪽 패널에서 **Inspectors > WebForms** 탭을 선택하여 SAML 토큰을 볼 수 있습니다. 여기에서 **SAMLResponse** 값을 마우스 오른쪽 단추로 클릭하고 **Send to TextWizard**를 선택합니다. 그런 다음 **Transform** 메뉴에서 **From Base64**를 선택하여 토큰을 디코딩하고 해당 콘텐츠를 확인합니다.
 
**참고**: 이 HTTP 요청의 콘텐츠를 보려는 경우 Fiddler에서 수행해야 하는 HTTPS 트래픽의 암호 해독을 구성하라는 메시지를 표시할 수 있습니다.

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png

<!---HONumber=Nov15_HO4-->