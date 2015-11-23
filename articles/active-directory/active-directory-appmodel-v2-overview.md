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
	ms.date="11/06/2015"
	ms.author="dastrock"/>

# 앱 모델 v2.0 미리 보기: 단일 앱에서 Microsoft 계정 및 Azure AD 사용자 로그인

> [AZURE.NOTE]이 정보는 v2.0 앱 모델 공용 미리 보기에 적용 됩니다. 일반 공급 Azure AD 서비스와 통합하는 방법에 대한 지침은 [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)를 참조하세요.

과거에 Microsoft 계정과 Azure Active Directory를 지원하려는 앱 개발자는 별도의 두 시스템을 통합해야 했습니다. v2.0 앱 모델을 사용하면 이제 두 유형의 계정으로 사용자가 로그인할 수 있습니다. 간단한 통합을 사용하면 개인 및 작업/학교 계정으로 범위가 수 백만 명의 사용자인 대상에 도달할 수 있습니다.

또한 앱은 계정 중 하나를 사용하여 [Office 365 REST API 집합](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)을 사용할 수 있습니다. 현재, 이러한 API는 Outlook의 메일, 연락처 및 일정 API를 포함합니다. 추가 서비스는 나중에 추가됩니다.<!-- TODO: customer reference article --><!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

v2.0 응용 프로그램 모델은 미리 보기로 제공됩니다. 미리 보기 기간 동안 시도한 새 앱에 대한 여러분의 의견과 경험이 필요합니다. 사용자 의견에 기반하여 주요 서비스를 향상시키기 위해 주요 내용을 변경할 수 있습니다. 이 기간 동안 v2.0 앱을 사용하여 프로덕션 앱을 릴리스하면 안됩니다.<!-- TODO: Get approval on how it looks  -->

## 시작하기
v2.0 앱 모델을 사용하여 두가지 방법으로 앱을 실행할 수 있습니다. [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) 또는 [Open ID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)를 사용하여 프로토콜 메시지를 직접 보내도록 선택할 수 있습니다. 또는 라이브러리를 사용하여 작업을 수행할 수 있습니다. 즐겨 찾기 플랫폼을 아래에서 선택하고 시작합니다.<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## 새로운 기능
여기를 다시 종종 확인하여 v2.0 앱 모델 공용 미리 보기에 이후 변경 사항을 알아봅니다. 또한 @AzureAD를 사용하여 업데이트에 대해 트윗합니다.

- [앱 모델 v2.0로 작성할 수 있는 앱의 종류](active-directory-v2-flows.md)에 대해 알아봅니다.
- Azure Active Directory에 익숙한 개발자의 경우 [v2.0 앱 모델의 프로토콜 및 차이점에 대한 업데이트](active-directory-v2-compare.md)를 확인해야 합니다.
- 현재 [미리 보기 제한, 제한 사항 및 제약 조건](active-directory-v2-limitations.md)입니다.

## 참조
이러한 링크는 플랫폼을 자세히 탐색하는 데 유용합니다.

- [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 또는 [adal](http://stackoverflow.com/questions/tagged/adal) 태그를 사용하여 스택 오버플로에 대한 도움말을 봅니다.
- [사용자 음성](http://feedback.azure.com/forums/169401-azure-active-directory)을 사용하여 미리 보기에 대한 의견을 보내주세요. 사용자의 의견을 듣고 싶습니다! 게시물의 제목에 "AppModelv2:" 구문을 사용해야 찾을 수 있습니다.
- [앱 모델 v2.0 프로토콜 참조](active-directory-v2-protocols.md)
- [앱 모델 v2.0 토큰 참조](active-directory-v2-tokens.md)
- [Office 365 REST API 참조](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [v2 끝점의 범위 및 동의](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->

<!---HONumber=Nov15_HO3-->