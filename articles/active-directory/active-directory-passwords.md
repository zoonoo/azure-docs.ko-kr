<properties 
	pageTitle="정의: Azure AD 암호 관리 | Microsoft Azure"
	description="암호 재설정, 변경, 암호 관리 보고 및 로컬 온-프레미스 Active Directory에 쓰기 저장을 포함한 Azure AD의 암호 관리 기능 설명." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# 어디에서나 암호 관리
셀프 서비스는 오랫동안 비용 절감 및 노동 절감 수단으로 전세계 IT 부서에서의 주요 목표가 되어 왔습니다. 실제로, 시장은 클라우드 또는 온-프레미스에서 온-프레미스 그룹, 암호 또는 사용자 프로필을 관리할 수 있는 제품으로 넘쳐납니다. Azure AD는 사용하기 가장 쉬운 방법 및 오늘날 가능한 가장 강력한 셀프서비스 기능을 제공하여 이들 제품과 차별을 두었습니다.

**Azure AD 암호 관리**는 정의한 보안 정책을 준수하는 동안 언제, 언제 어디에서든 모든 장치에서 암호를 관리할 수 있는 기능들입니다.

## 개요
5분 내에 Azure AD 암호 관리의 파일럿을 시작하여 수 시간 내에 전체 조직으로 배포할 수 있습니다. 다음은 서비스를 사용하는데 유용한 몇가지 리소스입니다.

* [**작동 방식**](active-directory-passwords-how-it-works.md) - 6개의 다양한 구성 요소 서비스 및 기능에 대해 알아봅니다.
* [**시작**](active-directory-passwords-getting-started.md) -사용자가 클라우드 또는 온-프레미스 암호를 다시 설정하고 변경할 수 있는 방법에 대해 알아봅니다.
* [**사용자 지정**](active-directory-passwords-customize.md) - 모양과 느낌 및 조직의 요구에 맞게 서비스의 동작을 사용자 지정하는 방법에 대해 알아봅니다
* [**모범 사례**](active-directory-passwords-best-practices.md) - 사용자의 조직에서 신속하게 배포하고 효과적으로 암호를 관리하는 방법에 대해 알아봅니다.
* [**정보 활용**](active-directory-passwords-get-insights.md) -우리의 통합된 보고 기능에 대해 알아봅니다
* [**FAQ**](active-directory-passwords-faq.md) -자주 묻는 질문에 답변합니다.
* [**문제해결**](active-directory-passwords-troubleshoot.md) -신속하게 서비스와의 문제를 해결하는 방법에 대해 알아봅니다.
* [**자세히 알아보기**](active-directory-passwords-learn-more.md) -서비스의 작동 원리 방식의 기술적 측면을 자세히 알아봅니다.


## Azure AD 암호 관리로 가능한 작업은 무엇입니까?
다음은 Azure AD의 암호 관리 기능으로 수행할 수 있는 몇가지 작업입니다.

- **셀프 서비스 암호 변경** 기능을 사용하여 최종 사용자나 관리자는 관리자에게 전화를 하거나 지원 센터에 문의하지 않고 만료되거나 만료되지 않은 암호를 변경할 수 있습니다.
- **셀프 서비스 암호 재설정** 기능을 사용하여 최종 사용자나 관리자는 관리자에게 전화를 하거나 지원 센터에 문의하지 않고도 자동으로 암호를 재설정할 수 있습니다. 셀프 서비스 암호 재설정 기능을 사용하려면 Azure AD Premium 또는 Basic이 필요합니다. 자세한 내용은 Azure Active Directory 버전을 참조하세요.
- **관리자가 시작한 암호 재설정** 기능을 사용하여 관리자는 [Azure 관리 포털](https://manage.windowsazure.com) 내에서 최종 사용자나 다른 관리자의 암호를 재설정할 수 있습니다.
- **암호 관리 작업 보고서**는 조직에서 발생하는 암호 재설정 및 등록 작업에서의 관리자 이해도를 높여줍니다. 
- **암호 쓰기 저장** 기능을 사용하여 클라우드에서 온-프레미스 암호를 관리할 수 있으므로 위의 모든 시나리오에서 수행되거나 페더레이션 및 암호가 동기화된 사용자가 대신 수행할 수 있습니다. 암호 쓰기 저장 기능을 사용하려면 Azure AD Premium이 필요합니다. 자세한 내용은 Azure AD Premium 시작을 참조하세요.

## Azure AD 암호 관리를 사용하는 이유
다음은 Azure AD의 암호 관리 기능을 사용해야 하는 몇가지 이유입니다.

- **비용 절감** -지원 기반 암호 재설정은 일반적으로 조직 IT 지출의 20%입니다.
- **사용자 환경 개선** -사용자가 자신의 암호를 잊어버릴 때마다 기술 지원팀과의 통화에 1시간을 보내고 싶어하지 않습니다.
- **적은 기술 지원팀** -암호 관리는 대부분의 조직에서 가장 큰 단일 기술 지원팀을 필요로 합니다.
- **이동성 사용** -사용자가 어디에서든 암호를 재설정할 수 있습니다

## 최근 업데이트
**SSPR 등록 브랜드** -2015년 4월

- 암호 재설정 등록 페이지는 이제 회사 로고로 브랜드 지정됩니다!

**보안 질문** -2015년 3월

- GA에 대한 보안 질문이 출시되었습니다!

**계정 잠금해제** -2015년 3월

- 이제 암호가 재설정되면 사용자는 계정을 잠금 해제할 수 있습니다.

<br/> <br/> <br/>

**추가 리소스**


* [암호 관리의 작동 원리](active-directory-passwords-how-it-works.md)
* [암호 관리 시작](active-directory-passwords-getting-started.md)
* [암호 관리 사용자 지정](active-directory-passwords-customize.md)
* [암호 관리 모범 사례](active-directory-passwords-best-practices.md)
* [암호 관리 보고서와 함께 Operational Insights를 얻는 방법](active-directory-passwords-get-insights.md)
* [암호 관리 FAQ](active-directory-passwords-faq.md)
* [암호 관리 문제 해결](active-directory-passwords-troubleshoot.md)
* [자세한 정보](active-directory-passwords-learn-more.md)
* [MSDN의 암호 관리](https://msdn.microsoft.com/library/azure/dn510386.aspx) 

<!---HONumber=July15_HO3-->