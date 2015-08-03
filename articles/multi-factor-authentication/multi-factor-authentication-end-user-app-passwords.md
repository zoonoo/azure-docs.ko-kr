<properties 
	pageTitle="Azure MFA에서 앱 암호란 무엇인가요?" 
	description="이 페이지는 사용자가 앱 암호란 무엇이며 Azure MFA와 관련해서 암호가 어떤 용도로 사용되는지를 이해하는 데 도움이 됩니다." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>




# Azure Multi-factor Authentication에서 앱 암호란 무엇인가요?

Exchange Active Sync를 사용하는 Apple 네이티브 메일 클라이언트와 같은 특정 비브라우저 앱은 현재 다단계 인증을 지원하지 않습니다. 다단계 인증은 사용자 기준으로 사용되도록 설정됩니다. 즉, 다단계 인증을 사용할 수 있도록 설정된 사용자는 비브라우저 앱을 사용할 수 없습니다. 그렇지만 앱 암호를 사용하면 가능해집니다.

>[AZURE.NOTE]Office 2013 클라이언트에 대한 최신 인증
>
> Office 2013 클라이언트(Outlook 포함)는 이제 새 인증 프로토콜을 지원하고 Multi-Factor Authentication을 지원하도록 설정할 수 있습니다. 즉, 일단 이렇게 설정되면 Office 2013 클라이언트에서 앱 암호를 사용할 필요가 없습니다. 자세한 내용은 [발표된 Office 2013 최신 인증 공개 미리 보기](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)를 참조하세요.
 
## 앱 암호를 사용하는 방법

다음은 앱 암호를 사용하는 방법과 관련해서 유의해야 할 사항입니다.

- 실제 암호는 자동으로 생성되며 사용자가 제공하지 않습니다. 자동으로 생성된 암호를 공격자가 추측하기 어렵고 더 안전하기 때문입니다.
- 현재 사용자 당 40개의 암호로 제한되어 있습니다. 이 제한에 도달한 후에 만들려고 시도하면 기존 앱 암호 중 하나를 삭제해야 새로 만들 수 있다는 메시지가 표시됩니다.
- 응용 프로그램이 아닌 장치별로 앱 암호를 만드는 것이 좋습니다. 예를 들어 노트북에 사용할 앱 암호를 하나 만든 다음 노트북의 모든 응용 프로그램에 이 앱 암호를 사용할 수 있습니다.
- 처음 로그인할 때 앱 암호가 부여됩니다. 추가 암호가 필요한 경우 만들 수 있습니다.
 
<center>![Setup](./media/multi-factor-authentication-end-user-app-passwords/app.png)</center>

앱 암호를 만든 후 이러한 비브라우저 앱에서 원래 암호 대신 사용할 수 있습니다. 예를 들어 휴대폰에서 다단계 인증 및 Apple 네이티브 메일 클라이언트를 사용하고 있는 경우 앱 암호를 사용하면 다단계 인증을 바이패스하고 작업을 계속할 수 있습니다.

### 앱 암호 만들기
초기 로그인 중에 사용할 수 있는 앱 암호가 제공됩니다. 또한 나중에 앱 암호를 만들 수도 있습니다. 이 방법은 다단계 인증을 사용하는 방법에 따라 달라집니다. 본인에게 가장 적합한 방법을 선택합니다.

다단계 인증을 사용하는 방법|설명
:------------- | :------------- | 
[Office 365에서 사용](multi-factor-authentication-end-user-manage-o365.md)| Office 365 포털을 통해 앱 암호를 만들 수 있습니다.
[Microsoft Azure에서 사용](multi-factor-authentication-end-user-manage-azure.md)| Azure 포털을 통해 앱 암호를 만들 수 있습니다.
[잘 모름](multi-factor-authentication-end-user-manage-myapps.md)|[https://myapps.microsoft.com](https://myapps.microsoft.com)을 통해 앱 암호를 만들 수 있습니다.




 

<!---HONumber=July15_HO4-->