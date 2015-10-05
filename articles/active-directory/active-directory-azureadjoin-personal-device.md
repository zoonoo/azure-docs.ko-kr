<properties
	pageTitle="개인 장치를 조직에 조인| Microsoft Azure"
	description="사용자가 자신의 개인 Windows 10 컴퓨터를 회사 네트워크에 등록하는 방법에 대해 설명하는 항목입니다."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="femila"/>

# 개인 장치를 조직에 조인

Windows 10 장치를 조직에 조인하려면
--------------------------------------------------------------------------------------------
1.	**시작** 메뉴에서 **설정**을 선택합니다.
2.	**계정**를 선택한 다음 **계정**을 클릭합니다.
3.	**회사 또는 학교 계정 추가**를 클릭한 다음 조직 계정을 입력합니다.
4.	그런 다음 사용자의 조직의 로그인 페이지로 이동합니다. 사용자 이름 및 암호를 입력하고 **확인**을 클릭합니다.
5.	다중요소 인증 시도라는 메시지가 표시됩니다. 이는 IT에 의해 구성할 수 있습니다.
6.	그런 다음 Azure AD가 사용자/장치와 모바일 장치 관리(MDM) 등록에 필요한지 여부를 확인합니다.
7.	그 후 Windows가 Azure AD에서 조직의 디렉터리에 장치를 등록하고 MDM에 등록합니다.
8.	이 작업이 완료되면 관리 사용자의 경우, Windows가 설치 프로세스를 마무리하고 자동 로그인 화면을 통해 사용자를 데스크톱으로 이동시킵니다.
9.	페더레이션 사용자의 경우, Windows 로그인 화면으로 이동하고 자격 증명을 입력해야 합니다.

## 추가 정보
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-overview.md)
* [Azure AD 조인에 대한 사용 시나리오와 배포 고려 사항에 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

<!---HONumber=Sept15_HO4-->