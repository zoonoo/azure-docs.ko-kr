<properties title="What is in the RemoteApp template images?" pageTitle="RemoteApp 템플릿 이미지에 있는 항목" description="서식 파일에 알아봅니다 RemoteApp에 포함 된 이미지입니다." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#RemoteApp 템플릿 이미지에 있는 항목

Azure RemoteApp 구독에는 다음 세 개의 템플릿 이미지가 포함되어 있습니다.


- Windows Server 2012
- Microsoft Office 365 ProPlus(Office 365 구독 필요)
- Microsoft Office 2013 Professional Plus(평가판 전용)

사용하는 이미지에 관계없이 앱을 사용자와 공유할 때마다 고려해야 하는 라이선스 관련 사항이 있습니다. 자세한 내용은 [RemoteApp 라이선스 정보](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-licensing/)를 참조하세요.

각 이미지에 포함된 항목에 대한 내용도 읽어보세요.

##Windows Server 2012 R2("바닐라 이미지")
이 이미지는 Microsoft Windows Server 2012 R2 Datacenter 운영 체제를 기반으로 하며 Azure RemoteApp 템플릿 이미지의 요구 사항을 충족하기 위해 다음 역할과 기능이 설치되어 있습니다. 


- .NET Framework 4.5, 3.5.1, 3.5
- 데스크톱 경험
- 잉크 및 필기 서비스
- 미디어 파운데이션
- 원격 데스크톱 세션 호스트
- Windows PowerShell 4.0
- Windows PowerShell ISE
- WoW64 지원 

이 이미지에는 다음 응용 프로그램도 설치되어 있습니다. 

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player 


##Microsoft Office 365 ProPlus(구독 필요)
Office 365는 가장 많이 요청되는 응용 프로그램이므로 작업에 사용할 "사용자 지정" 이미지를 만들었습니다. 

이 이미지는 바닐라 이미지의 확장으로, Windows Server 2012 R2 이미지에 설명된 구성 요소 외에 Microsoft Office 365 ProPlus의 다음 구성 요소도 설치되어 있습니다. 


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Microsoft Office 언어 교정 도구 

Office 365 ProPlus 계획이 있는 사용자만 Office 365 ProPlus 앱의 모든 기능을 사용할 수 있습니다. Office 365 구독 계획에 대한 자세한 내용은 [Office 365 서비스 계획](http://technet.microsoft.com/library/office-365-plan-options.aspx)을 참조하세요. RemoteApp의 라이선스에 대한 자세한 내용은 [Azure RemoteApp의 라이선스 작동 방식](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-licensing/)을 참조하세요. 

##Microsoft Office 2013 Professional Plus(평가판 전용)
무료 평가 기간 동안 Office 2013 이미지를 사용하여 서비스를 테스트할 수 있습니다. 

이 이미지는 바닐라 이미지의 확장으로, Windows Server 2012 R2 이미지에 설명된 구성 요소 외에 Microsoft Office 2013 Professional Plus의 다음 구성 요소도 설치되어 있습니다. 


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Microsoft Office 언어 교정 도구 

**중요 법적 정보:** 이 이미지는 Microsoft Office 라이선스에 포함되지 않으며 *프로덕션에 사용할 수 없습니다*. Office 2013 Professional Plus 이미지는 평가판 전용입니다. 프로덕션용 Azure RemoteApp에서 Office 앱을 사용하려면 Office 365 ProPlus 이미지를 사용해야 합니다. RemoteApp의 라이선스에 대한 자세한 내용은 [Azure RemoteApp의 라이선스 작동 방식](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-licensing/)을 참조하세요.

<!--HONumber=35.2-->
