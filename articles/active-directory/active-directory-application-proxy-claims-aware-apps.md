<properties
	pageTitle="응용 프로그램 프록시에서 클레임 인식 앱으로 작업"
	description="Azure AD 응용 프로그램 프록시를 작동시키는 방법에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# 응용 프로그램 프록시에서 클레임 인식 앱으로 작업


클레임 인식 앱은 STS에 리디렉션을 수행하며, 이에 대한 반응으로 사용자를 응용 프로그램에 리디렉션하기 전에 토큰 대신 사용자에게 자격 증명을 요청합니다. 응용 프로그램 프록시가 이러한 리디렉션과 함께 작업할 수 있게 하려면 다음 단계를 수행하여 응용 프로그램 프록시가 클레임 인식 응용 프로그램과 작업할 수 있게 해야 합니다.

> [AZURE.IMPORTANT]응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.


## 필수 요소

이 절차를 수행하기 전에 클레임 인식 앱이 리디렉션되는 STS가 온-프레미스 네트워크 바깥에서 사용 가능한지 확인합니다.

### Azure 포털 구성

1. [응용 프로그램 프록시로 응용 프로그램 게시](active-directory-application-proxy-publish.md)에 설명된 지침에 따라 응용 프로그램을 게시합니다.
2. 응용 프로그램 목록에서 클레임 인식 앱을 선택하고 **구성**을 클릭합니다.
3. **통과**를 **사전 인증 메서드**로 선택한 경우 **HTTPS**를 **외부 URL** 구성표로 선택해야 합니다.
4. Azure Active Directory를 **사전 인증 메서드**로 선택했다면 **없음**을 **내부 인증 방법**으로 선택하십시오.

### ADFS 구성

1. **ADFS 관리**를 엽니다.
2. **신뢰 당사자 트러스트**로 이동하여 응용 프로그램 프록시로 게시하고자 하는 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

![신뢰 당사자 트러스트 앱 이름을 마우스 오른쪽 단추로 클릭 - 스크린샷](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)

3. **끝점** 탭에 있는 **끝점 유형** 아래에서 **WS-페더레이션**을 선택합니다.
4. **신뢰할 수 있는 URL** 아래에 **외부 URL** 아래에 있는 응용 프로그램 프록시에 입력한 URL을 입력하고 **확인**을 클릭합니다.

![끝점 추가 - 신뢰할 수 있는 URL 값 설정 - 스크린샷](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)

<!---HONumber=Oct15_HO3-->