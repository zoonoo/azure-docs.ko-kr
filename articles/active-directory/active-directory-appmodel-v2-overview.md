<properties
	pageTitle="앱 모델 v2.0 개요 | Microsoft Azure"
	description="Microsoft 계정 및 Azure Active Directory 로그인을 사용하는 앱 구축을 소개합니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# 단일 앱에서 Microsoft 계정 및 Azure AD 사용자 로그인

과거에 Microsoft 계정과 Azure Active Directory를 지원하려는 앱 개발자는 별도의 두 시스템을 통합해야 했습니다. 지금까지 Azure AD 시스템을 사용하여 두 가지 유형의 계정을 모두 사용하여 사용자가 로그인할 수 있게 해주는 새 인증 API 버전을 살펴보았습니다. 이 수렴된 인증 시스템을 **v2.0 끝점**이라고 합니다. v2.0 끝점을 사용하면 간단한 통합을 사용하여 개인 및 작업/학교 계정으로 범위가 수 백만 명의 사용자인 대상에 도달할 수 있습니다.

또한 v2.0 끝점을 사용하는 앱은 두 가지 계정 유형 중 하나 이상을 사용하여 [Microsoft Graph](https://graph.microsoft.io) 및 [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)에서 REST API를 사용할 수 있습니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.


## 시작
아래에서 원하는 플랫폼을 선택하여 당사의 오픈 소스 라이브러리 및 프레임워크를 사용하여 앱을 만듭니다. 또는 인증 라이브러리를 사용하지 않고 당사의 OAuth 2.0 및 OpenID Connect 프로토콜 설명서를 사용하여 프로토콜 메시지를 직접 보내고 받을 수 있습니다. <!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## 새로운 기능	
여기서 설명하는 개념 정보는 v2.0 끝점을 사용하여 할 수 있는 일과 할 수 없는 일을 이해하는 데 유용합니다.

- v2.0 끝점 2015년 미리 보기 기간 중에 앱을 만드는 경우 당사가 최근 실행한 [최신 프로토콜 변경에 대해 읽어야](active-directory-v2-preview-oidc-changes.md) 합니다.
- [v2.0 끝점을 사용하여 만들 수 있는 앱의 종류](active-directory-v2-flows.md)에 대해 읽으십시오.
- Azure Active Directory에 익숙한 개발자의 경우 [v2.0 끝점의 프로토콜 및 차이점에 대한 업데이트](active-directory-v2-compare.md)를 확인해야 합니다.
- v2.0 끝점을 사용의 [제한 사항, 제한 및 제약 조건](active-directory-v2-limitations.md)을 이해해야 합니다.

## 참조
이러한 링크는 플랫폼을 자세히 탐색하는 데 유용합니다.

- [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 또는 [adal](http://stackoverflow.com/questions/tagged/adal) 태그를 사용하여 스택 오버플로에 대한 도움말을 봅니다.
- [v2.0 프로토콜 참조](active-directory-v2-protocols.md)
- [v2.0 토큰 참조](active-directory-v2-tokens.md)
- [v2.0 끝점의 범위 및 동의](active-directory-v2-scopes.md)
- [Microsoft 앱 등록 포털](https://apps.dev.microsoft.com)
- [Office 365 REST API 참조](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- 다음은 v2.0 끝점을 사용하여 시험한 오픈 소스 클라이언트 라이브러리 및 샘플입니다. 참고로 [OpenID Connect 동적 클라이언트 등록](https://openid.net/specs/openid-connect-registration-1_0.html) 및 토큰 유효성 검사 끝점 등과 같은 기능은 아직 지원되지 않으며 v2 끝점을 사용하여 작업하려면 라이브러리에서 이를 사용하지 않도록 설정해야 할 수 있습니다.  

  - [Java WSO2 ID 서버](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu 페더레이션](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect 기본 클라이언트](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Android OpenID Connect 샘플](https://github.com/learning-layers/android-openid-connect)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
- Give us your thoughts on the preview using [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) - we want to hear them!  Use the phrase "AppModelv2:" in the title of your post so we can find it.
-->

<!---HONumber=AcomDC_0224_2016-->