<properties
	pageTitle="엔터프라이즈 상태 로밍 개요 | Microsoft Azure"
	description="Windows 장치의 엔터프라이즈 상태 로밍 설정에 대한 정보를 제공합니다. 엔터프라이즈 상태 로밍은 사용자의 모든 Windows 장치에 통합된 환경을 제공하고 새 장치 구성에 드는 시간을 줄여 줍니다."
	services="active-directory"
    keywords="엔터프라이즈 상태 로밍이란, 엔터프라이즈 동기화, windows 클라우드"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# 엔터프라이즈 상태 로밍 개요

Windows 10에서는 [Azure Active Directory(Azure AD)](active-directory-whatis.md) 사용자가 사용자 설정 및 응용 프로그램 설정 데이터를 클라우드와 안전하게 동기화할 수 있습니다. 엔터프라이즈 상태 로밍은 사용자의 모든 Windows 장치에 통합된 환경을 제공하고 새 장치 구성에 드는 시간을 줄여 줍니다. 엔터프라이즈 상태 로밍은 Windows 8에서 처음 도입된 표준 [소비자 설정 동기화](http://windows.microsoft.com/ko-KR/windows-8/sync-settings-pcs)와 비슷한 방식으로 작동합니다. 뿐만 아니라 엔터프라이즈 상태 로밍은 다음 기능을 제공합니다.

- **기업 데이터와 소비자 데이터 분리** - 조직에서 데이터를 제어할 수 있으며, 소비자 클라우드 계정에 회사 데이터가 섞이거나 엔터프라이즈 클라우드 계정에 기업 데이터가 섞이지 않습니다. 
- **보안 강화** – 데이터가 사용자의 Windows 10 장치에서 나가기 전에 Azure RMS(Azure Rights Management)를 사용하여 자동으로 암호화되고, 암호화된 상태로 클라우드에 보존됩니다. 설정 이름 및 Windows 앱 이름 같은 네임 스페이스를 제외한 모든 콘텐츠가 암호화된 상태로 클라우드에 보존됩니다.  
- **관리 및 모니터링 서비스** – 조직에서 누가, 어떤 장치에서 설정을 동기화할 수 있는지 보다 정교하게 제어하고 확인할 수 있습니다. 
- **클라우드에서 데이터의 지리적 위치** – Azure AD 도메인의 국가를 기반으로 Azure 지역에 데이터가 저장됩니다. 



| 문서 | 설명 |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure Active Directory에서 엔터프라이즈 상태 로밍 활성화](active-directory-windows-enterprise-state-roaming-enable.md) | Enterprise 상태 로밍은 프리미엄 Azure Active Directory(Azure AD)를 구독하는 모든 조직에 제공됩니다. Azure AD 구독 방법에 대한 자세한 내용은 Azure AD 제품 페이지를 참조하세요. |
| [설정 및 데이터 로밍 FAQ](active-directory-windows-enterprise-state-roaming-faqs.md) | 이 토픽에서는 설정 및 앱 데이터 동기화에 대한 IT 관리자의 질문에 답변합니다. |
| [설정 동기화에 대한 그룹 정책 및 MDM 설정](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) | Windows 10은 설정 동기화를 제한하는 그룹 정책 및 MDM(모바일 장치 관리) 정책 설정을 제공합니다. |
| [Windows 10 로밍 설정 참조](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) | 다음은 Windows 10에서 로밍 및/또는 백업되는 모든 설정의 전체 목록입니다. |

<!---HONumber=AcomDC_0204_2016-->