<properties
   pageTitle="다중 테넌트 응용 프로그램에 대한 ID 관리 | Microsoft Azure"
   description="다중 테넌트 앱에서 인증, 권한 부여 및 ID 관리에 대한 모범 사례입니다."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Microsoft Azure에서 다중 테넌트 응용 프로그램에 대한 ID 관리

이 시리즈에서는 인증 및 ID 관리에 Azure AD를 사용할 때 다중 테넌트 지원에 대한 모범 사례를 설명합니다.

다중 테넌트 응용 프로그램을 빌드할 때 첫 번째 과제 중 하나는 이제 모든 사용자가 테넌트에 속하므로 사용자 ID를 관리하는 것입니다. 예:

- 사용자가 조직 자격 증명으로 로그인합니다.
- 사용자는 조직의 데이터에는 액세스할 수 있지만 다른 테넌트에 속한 데이터에는 액세스하지 못합니다.
- 조직은 응용 프로그램을 등록한 다음 조직의 멤버에게 응용 프로그램 역할을 할당할 수 있습니다.

Azure AD(Azure Active Directory)에는 이러한 모든 시나리오를 지원하는 일부 유용한 기능이 있습니다.

이 문서 시리즈와 함께 사용하기 위해 다중 테넌트 앱의 완전한 [종단간 구현][tailspin]도 만들었습니다. 이 문서에는 응용 프로그램 빌드 프로세스에서 습득한 내용이 반영되어 있습니다. 응용 프로그램을 시작하려면 [설문 조사 응용 프로그램 실행](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md)을 참조하세요.


## 목차

- [소개](guidance-multitenant-identity-intro.md)
- [Tailspin 설문 조사 응용 프로그램 정보](guidance-multitenant-identity-tailspin.md)
- [Azure AD로 인증](guidance-multitenant-identity-authenticate.md)
- [클레임 기반 ID 사용](guidance-multitenant-identity-claims.md)
- [등록 및 테넌트 온보딩](guidance-multitenant-identity-signup.md)
- [응용 프로그램 역할 정의 및 관리](guidance-multitenant-identity-app-roles.md)
- [권한 부여](guidance-multitenant-identity-authorize.md)
- [백 엔드 Web API 보안](guidance-multitenant-identity-web-api.md)
- [OAuth2 액세스 토큰 캐싱](guidance-multitenant-identity-token-cache.md)
- [고객의 AD FS로 페더레이션](guidance-multitenant-identity-adfs.md)
- [Azure AD에서 액세스 토큰을 가져오는 데 클라이언트 어설션 사용](guidance-multitenant-identity-client-assertion.md)
- [응용 프로그램 암호를 보호하는 데 주요 자격 증명 모음 사용](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->