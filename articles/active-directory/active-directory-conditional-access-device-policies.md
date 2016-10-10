<properties
	pageTitle="Office 365 서비스에 대한 조건부 액세스 장치 정책 | Microsoft Azure"
	description="장치 기반 조건이 Office 365 서비스에 대한 액세스를 제어하는 방법에 대한 세부 정보입니다. IW(정보 작업자)는 개인 장치에서 회사 또는 학교의 Exchange 및 SharePoint Online과 같은 Office 365 서비스에 액세스하려고 하는 반면 IT 관리자는 액세스 보안을 유지하려고 합니다. IT 관리자는 조건부 액세스 장치 정책을 프로비전하여 규격 장치를 사용하는 IW가 서비스에 액세스할 수 있도록 하는 동시에 회사 리소스를 보호할 수 있습니다."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="femila"/>
# Office 365 서비스에 대한 조건부 액세스 장치 정책

"조건부 액세스"란 용어에는 다단계 인증 사용자, 인증된 장치, 규격 장치 등의 많은 조건이 연결되어 있습니다. 이 항목에서는 주로 Office 365 서비스에 대한 액세스를 제어하는 장치 기반 조건을 집중적으로 살펴봅니다. IW(정보 작업자)는 개인 장치에서 회사 또는 학교의 Exchange 및 SharePoint Online과 같은 Office 365 서비스에 액세스하려고 하는 반면 IT 관리자는 액세스 보안을 유지하려고 합니다. IT 관리자는 조건부 액세스 장치 정책을 프로비전하여 규격 장치를 사용하는 IW가 서비스에 액세스할 수 있도록 하는 동시에 회사 리소스를 보호할 수 있습니다. Microsoft Intune 조건부 액세스 포털에서 Office 365에 대한 조건부 액세스 정책을 구성할 수도 있습니다.

Azure Active Directory는 조건부 액세스 정책을 적용하여 Office 365 서비스에 대한 액세스를 보호합니다. 테넌트 관리자는 비규격 장치 사용자가 O365 서비스에 액세스할 수 없도록 차단하는 조건부 액세스 정책을 만들 수 있습니다. 사용자가 회사의 장치 정책을 준수해야 서비스에 대한 액세스 권한을 받을 수 있습니다. 또는 관리자가 O365 서비스에 대한 액세스 권한을 얻기 위해 장치를 등록하도록 사용자에게 요구하는 정책을 만들 수도 있습니다. 조직의 모든 사용자에게 정책을 적용하거나, 몇 개의 대상 그룹에 제한적으로 정책을 적용한 다음 추가 대상 그룹을 포함하도록 점차 확장할 수 있습니다.

장치 정책 적용을 위한 필수 조건은 사용자가 Azure Active Directory Device Registration 서비스에 장치를 등록하는 것입니다. Azure Active Directory Device Registration 서비스에 대한 장치 등록에 MFA(다단계 인증)를 사용하도록 설정할 수도 있습니다. Azure Active Directory Device Registration 서비스에는 MFA를 사용하는 것이 좋습니다. MFA를 사용하도록 설정하면 Azure Active Directory Device Registration 서비스에 장치를 등록하는 사용자에게 두 번째 인증 단계가 요구됩니다.

##조건부 액세스 정책의 작동 방식

사용자가 지원되는 장치 플랫폼에서 O365 서비스에 대한 액세스를 요청하는 경우 Azure Active Directory는 사용자 및 사용자가 요청을 시작하는 장치를 인증하고, 사용자가 서비스에 대해 설정된 정책을 준수하는 경우에만 서비스에 대한 액세스 권한을 부여합니다. 장치를 등록하지 않은 사용자에게는 장치를 등록하고 회사 O365 서비스에 액세스하기 위한 규정을 준수하는 방법에 대한 수정 지침이 제공됩니다. iOS 및 Android 장치의 사용자는 회사 포털 응용 프로그램을 사용하여 장치를 등록해야 합니다. 사용자가 장치를 등록하면 Azure Active Directory에 장치가 등록되고 장치 관리 및 규정 준수에 대해 등록됩니다. 고객은 Microsoft Intune과 함께 Azure Active Directory Device Registration 서비스를 사용하여 Office 365 서비스에 모바일 장치 관리를 사용하도록 설정해야 합니다. 장치 등록은 장치 정책 적용 시 사용자가 Office 365 서비스에 액세스하기 위한 필수 조건입니다.

사용자가 성공적으로 장치를 등록하면 장치를 신뢰할 수 있습니다. Azure Active Directory는 회사 응용 프로그램 액세스에 대해 Single Sign-On을 제공하며 조건부 액세스 정책을 적용하여 사용자가 처음 액세스를 요청할 때는 물론 사용자가 액세스 갱신을 요청할 때마다 서비스에 대한 액세스 권한을 부여합니다. 로그인 자격 증명이 변경되거나, 장치가 분실/도난당했거나, 갱신 요청 시 정책이 충족되지 않은 경우 서비스에 대한 사용자 액세스가 거부됩니다.

## 배포 고려 사항
장치 등록 시 Azure Active Directory Device Registration 서비스를 사용해야 합니다.

사용자가 온-프레미스에서 인증되어야 하는 경우 AD FS(Active Directory Federation Services)(1.0 이상)가 필요합니다. ID 공급자가 다단계 인증을 수행할 수 없는 경우 작업 공간 연결에 대한 Multi-Factor Authentication이 실패합니다. 예를 들어 AD FS 2.0은 다단계 인증을 수행할 수 없습니다. 테넌트 관리자는 Azure Active Directory Device Registration 서비스에서 MFA를 사용하도록 설정하기 전에 온-프레미스 AD FS가 MFA를 수행할 수 있고 유효한 MFA 방법을 사용하도록 설정했는지 확인해야 합니다. 예를 들어 Windows Server 2012 R2의 AD FS에는 MFA 기능이 있습니다. 또한 Azure Active Directory Device Registration 서비스에서 MFA를 사용하도록 설정하기 전에 AD FS 서버에서 유효한 추가 인증(MFA) 방법을 사용하도록 설정해야 합니다. AD FS에서 지원되는 MFA 방법에 대한 자세한 내용은 AD FS에 대한 추가 인증 방법 구성을 참조하세요.

## FAQ(질문과 대답)

Q: 지원되지 않는 장치 플랫폼에 대한 기본 제외 정책은 무엇인가요?

A: 현재 iOS 및 Android 장치의 사용자에 대해 선택적으로 조건부 액세스 정책이 적용됩니다. 다른 장치 플랫폼의 응용 프로그램은 기본적으로 iOS 및 Android 장치에 대한 조건부 액세스 정책의 영향을 받지 않습니다. 그러나 테넌트 관리자가 지원되지 않는 플랫폼의 사용자에 대해 액세스를 차단하도록 전역 정책을 재정의할 수도 있습니다. Windows를 포함하여 다른 플랫폼의 사용자에게도 조건부 액세스 정책을 확장할 계획입니다.

Q: Office 365 서비스에 대한 조건부 액세스 정책이 언제 브라우저 기반 앱(예: OWA, 브라우저 기반 SharePoint)으로 확장되나요?

A: 현재 Office365 서비스에 대한 조건부 액세스는 장치의 풍부한 응용 프로그램으로 제한됩니다. 브라우저에서 서비스에 액세스하는 사용자에게도 조건부 액세스 정책을 확장할 계획입니다.

<!---HONumber=AcomDC_0928_2016-->