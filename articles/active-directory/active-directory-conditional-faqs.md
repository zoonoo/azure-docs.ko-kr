<properties
	pageTitle="조건부 액세스 FAQ | Microsoft Azure"
	description="조건부 액세스에 대해 자주 묻는 질문과 대답"
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
	ms.date="06/15/2016"
	ms.author="femila"/>

# 조건부 액세스 FAQ

## 조건부 액세스 정책이 적용되는 응용 프로그램은 무엇입니까?
[조건부 액세스 - 지원되는 응용 프로그램](active-directory-conditional-access-supported-apps.md) 항목을 참조하세요.

## B2B 공동 작업과 게스트 사용자에게 조건부 액세스 정책이 적용됩니까?
B2B 공동 작업 사용자에 대한 정책이 적용됩니다. 그러나 경우에 따라 사용자가 정책 요구 사항을 충족하지 못할 수 있습니다. 예를 들어, 사용자의 조직이 다단계 인증을 지원하지 않는 경우가 해당합니다. 현재 이 정책은 SharePoint 게스트 사용자에게 적용되지 않습니다. 게스트 관계는 SharePoint 내부에서 관리되므로 게스트 사용자 계정은 인증 서버의 액세스 정책이 적용되지 않습니다. 게스트 사용자는 SharePoint에서 관리할 수 있습니다.

## SharePoint Online 정책도 비즈니스용 OneDrive에 적용됩니까?
예.
 
## Word나 Outlook 등의 클라이언트 앱에 정책을 설정할 수 없는 이유는 무엇입니까?
조건부 액세스 정책은 서비스 액세스 요구 사항을 설정하고, 해당 서비스에서 인증하는 경우에 적용됩니다. 그러므로 클라이언트 응용 프로그램에서 정책이 설정되는 것이 아니라 서비스가 호출될 때 적용됩니다. 예를 들어, SharePoint에서 설정된 정책은 SharePoint를 호출하는 클라이언트에, Exchange에서 설정된 정책은 Outlook에 적용됩니다.

<!---HONumber=AcomDC_0615_2016-->