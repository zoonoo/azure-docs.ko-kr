<properties
	pageTitle="방법: Azure AD 암호 등록 및 재설정 | Microsoft Azure"
	description="Azure Active Directory에서 암호 재설정을 위해 등록하는 방법을 알아봅니다"
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="asteen"/>

# 계정에 대한 액세스를 손실하지 마세요!

> [AZURE.IMPORTANT]
**왜 이것이 표시되나요?** 링크를 따라 여기에 왔다면 관리자는 사용자에게 암호 재설정을 위해 등록하여 앱에 대한 액세스 권한을 얻도록 요구하기 때문에 이것을 보게 될 수도 있습니다. 보안 질문을 설정하거나, 전화 또는 전자 메일 정보를 요청받을 수도 있습니다. 걱정하지 마세요. 이 정보를 스팸 발송에 사용하지 않을 것이며, 단지 사용자 계정을 더 안전하게 지키기 위한 용도로만 사용할 것입니다. 여기에 제시된 단계는 목표에 도달하는 데 도움이 됩니다.

암호 재설정을 위해 등록하는 가장 빠른 방법은 [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)으로 이동하는 것입니다.

 1. [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)으로 이동합니다.
 2. 사용자 이름 및 암호를 입력합니다.
 3. **지금 설정**을 클릭하여 등록에 옵션을 선택합니다. 이 경우에 **인증 전화** 등록을 설명합니다.

    ![][011]

 4. 드롭다운 목록에서 국가 코드를 선택하고 **전체 전화번호 + 지역 번호**를 입력합니다.

    ![][012] ![][013]

 5. **문자로 인증** 또는 **전화로 인증** 옵션 중 하나를 선택합니다. 이 경우에 **문자로 인증**을 선택하면 6자리 코드가 내 전화에 전송됩니다. 휴대폰에서 도착하는 코드를 기다립니다.

    ![][014]

 6. 코드가 도착하면 입력 상자에 입력한 다음 "확인"을 클릭합니다.
 7. **감사합니다**가 표시되면 됩니다! 언제든지 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)으로 이동하여 암호를 재설정하기 위해 등록한 옵션을 사용할 수있습니다.

    ![][015]

 >[AZURE.IMPORTANT] 관리자가 둘 이상의 옵션에 등록하도록 하는 경우 휴대폰 또는 전자 메일 액세스를 분실할 경우를 대비하여 백업 옵션을 등록하는 것이 좋습니다.

## 고유한 암호를 업데이트하는 다른 방법
암호 변경 또는 암호 재설정과 같은 일반적인 단계를 수행하는 방법을 알아보려면 아래를 읽어보세요.

* [**Office 365에서 암호를 변경하는 방법**](active-directory-passwords-update-methods.md#how-to-change-your-password-from-o365)
* [**액세스 패널에서 암호를 변경하는 방법**](active-directory-passwords-update-methods.md#how-to-change-your-password-from-the-access-panel)
* [**암호를 재설정하는 방법**](active-directory-passwords-update-methods.md#how-to-reset-your-password)
* [**사용자 계정의 잠금을 해제하는 방법**](active-directory-passwords-update-methods.md#how-to-unlock-your-account)
* [**일반적인 문제 및 해결 방법**](active-directory-passwords-update-methods.md#common-problems-and-their-solutions)

## 암호 재설정 설명서에 대한 링크
다음은 모든 다른 Azure AD 암호 관리 설명서 페이지에 대한 링크입니다.

* [**작동 방식**](active-directory-passwords-how-it-works.md) - 6개의 다양한 구성 요소 서비스 및 기능에 대해 알아봅니다.
* [**시작**](active-directory-passwords-getting-started.md) -사용자가 클라우드 또는 온-프레미스 암호를 다시 설정하고 변경할 수 있는 방법에 대해 알아봅니다.
* [**사용자 지정**](active-directory-passwords-customize.md) - 모양과 느낌 및 조직의 요구에 맞게 서비스의 동작을 사용자 지정하는 방법에 대해 알아봅니다
* [**모범 사례**](active-directory-passwords-best-practices.md) - 사용자의 조직에서 신속하게 배포하고 효과적으로 암호를 관리하는 방법에 대해 알아봅니다.
* [**정보 활용**](active-directory-passwords-get-insights.md) -우리의 통합된 보고 기능에 대해 알아봅니다
* [**FAQ**](active-directory-passwords-faq.md) -자주 묻는 질문에 답변합니다.
* [**문제해결**](active-directory-passwords-troubleshoot.md) -신속하게 서비스와의 문제를 해결하는 방법에 대해 알아봅니다.
* [**자세히 알아보기**](active-directory-passwords-learn-more.md) -서비스의 작동 원리 방식의 기술적 측면을 자세히 알아봅니다.



[001]: ./media/active-directory-passwords-update-your-own-password/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-update-your-own-password/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-update-your-own-password/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-update-your-own-password/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-update-your-own-password/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-update-your-own-password/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-update-your-own-password/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-update-your-own-password/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-update-your-own-password/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-update-your-own-password/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-update-your-own-password/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-update-your-own-password/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-update-your-own-password/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-update-your-own-password/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-update-your-own-password/015.jpg "Image_015.jpg"

<!---HONumber=AcomDC_0824_2016-->