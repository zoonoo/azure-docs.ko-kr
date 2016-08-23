<properties
	pageTitle="O365 신뢰 당사자 트러스트에 대한 서명 해시 알고리즘 변경 | Microsoft Azure"
	description="이 페이지에서는 O365와의 페더레이션 트러스트에 대한 SHA 알고리즘을 변경하는 방법에 대한 지침을 제공합니다."
    keywords="SHA1,SHA256,O365,페더레이션,aadconnect,adfs,ad fs,sha 변경,페더레이션 트러스트,신뢰 당사자 트러스트"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="samueld"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>

#O365 신뢰 당사자 트러스트에 대한 서명 해시 알고리즘 변경

##개요

AD FS는 해당 토큰을 Azure Active Directory에 서명하여 훼손할 수 없도록 합니다. 이 서명은 SHA1 또는 SHA256을 기반으로 할 수 있습니다. 이제 Microsoft Azure Active Directory에서는 SHA256 알고리즘으로 서명된 토큰을 지원하며, 최고 수준의 보안을 위해 토큰 서명 알고리즘을 SHA256으로 설정하도록 권장합니다. 이 문서에서는 토큰 서명 알고리즘을 보다 안전한 SHA256 수준으로 설정하는 단계를 제공합니다.

##토큰 서명 알고리즘 변경

아래 단계에 따라 서명 알고리즘을 설정하면 AD FS에서 O365 신뢰 당사자 트러스트에 대한 토큰을 SHA-256으로 서명하기 시작합니다. 필요한 추가 구성 변경은 없으며 이 변경 사항은 Office 365 또는 다른 Azure AD 응용 프로그램에 액세스하는 최종 사용자에게 영향을 주지 않습니다.

###관리 콘솔 사용

* 기본 AD FS 서버에서 AD FS 관리 콘솔을 엽니다.
* AD FS 노드를 확장하고 신뢰 당사자 트러스트를 클릭합니다.
* O365/Azure 신뢰 당사자 트러스트를 마우스 오른쪽 단추로 클릭하고 속성을 선택합니다.
* 고급 탭을 선택하고 보안 해시 알고리즘을 SHA256으로 선택합니다.
* 확인을 클릭합니다.

![SHA256 서명 알고리즘 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

###AD FS PowerShell cmdlet 사용

* AD FS 서버에서 관리자 권한으로 PowerShell을 엽니다
* Set-AdfsRelyingPartyTrust cmdlet을 사용하여 보안 해시 알고리즘을 설정합니다.

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

##참조 항목

* [AAD Connect를 사용하여 O365 트러스트 복구](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0810_2016-->