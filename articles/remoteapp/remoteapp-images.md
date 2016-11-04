---
title: Azure RemoteApp 템플릿 이미지에 있는 항목 | Microsoft Docs
description: Azure RemoteApp에 포함된 템플릿 이미지에 대해 알아봅니다.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: elizapo

---
# Azure RemoteApp 템플릿 이미지에 있는 항목
> [!IMPORTANT]
> Azure RemoteApp은 중단될 예정입니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148)을 읽어보세요.
> 
> 

Azure RemoteApp 구독에는 다음 세 개의 템플릿 이미지가 포함되어 있습니다.

* Windows Server 2012
* Microsoft Office 365 ProPlus(Office 365 구독 필요)
* Microsoft Office 2013 Professional Plus(평가판 전용)

> [!IMPORTANT]
> Azure RemoteApp 구독은 별도의 구독을 필요로 하는 Office 365 ProPlus 및 프로덕션 환경에서 사용할 수 없는 Office 2013을 제외하고 이미지의 소프트웨어에 대한 액세스 권한을 부여합니다. 이는 템플릿 이미지의 프로그램 또는 응용 프로그램을 사용자와 공유할 수 있다는 것을 의미합니다. 예를 들어 Windows Server 2012 R2 이미지를 사용하는 컬렉션을 만드는 경우 사용자가 RemoteApp을 통해 액세스할 수 있는 System Center Endpoint Protection을 게시할 수 있습니다.
> 
> 자세한 내용은 [RemoteApp 라이선스 정보](remoteapp-licensing.md)를 참조하세요. Office 라이선스 정보는 [Azure RemoteApp과 함께 Office 사용](remoteapp-o365.md)을 참조하세요.
> 
> 

각 이미지에 포함된 항목에 대한 내용도 읽어보세요.

## Windows Server 2012 R2("바닐라 이미지")
이 이미지는 Microsoft Windows Server 2012 R2 Datacenter 운영 체제를 기반으로 하며 Azure RemoteApp 템플릿 이미지에 대한 요구 사항을 충족하기 위해 다음 역할과 기능이 설치되어 있습니다.

* .NET Framework 4.5, 3.5.1, 3.5
* 데스크톱 경험
* 잉크 및 필기 서비스
* 미디어 파운데이션
* 원격 데스크톱 세션 호스트
* Windows PowerShell 4.0
* Windows PowerShell ISE
* WoW64 지원

이 이미지에는 다음 응용 프로그램도 설치되어 있습니다.

* Adobe Flash Player
* Microsoft Silverlight
* Microsoft System Center 2012 Endpoint Protection
* Microsoft Windows Media Player

## Microsoft Office 365 ProPlus(구독 필요)
Office 365는 가장 많이 요청되는 응용 프로그램이므로 작업에 사용할 "사용자 지정" 이미지를 만들었습니다.

이 이미지는 바닐라 이미지의 확장으로, Windows Server 2012 R2 이미지에 설명된 구성 요소 외에 Microsoft Office 365 ProPlus의 다음 구성 요소도 설치되어 있습니다.

* Access
* Excel
* Lync
* OneNote
* 비즈니스용 OneDrive(동기화 에이전트는 Azure RemoteApp과 함께 사용하도록 지원되지 않음)
* Outlook
* PowerPoint
* Word
* Microsoft Office 언어 교정 도구

이미지에는 Visio Pro 및 Project Pro도 포함됩니다.

다음 응용 프로그램도 포함:

* SQL Native client
* ODBC 드라이버
* SQL Server 데이터 마이닝 클라이언트
* MasterDataServices 클라이언트
* Microsoft Publisher
* PowerQuery
* PowerMap

Office 365 ProPlus 계획이 있는 사용자만 Office 365 ProPlus 앱의 모든 기능을 사용할 수 있습니다. Office 365 구독 계획에 대한 자세한 내용은 [Office 365 서비스 계획](http://technet.microsoft.com/library/office-365-plan-options.aspx)을 참조하세요. 질문이 있으십니까? [Office 365 + RemoteApp](remoteapp-o365.md) 정보를 확인하세요. 또한 새 문서 [Azure RemoteApp와 함께 Office 365 구독을 사용하는 방법](remoteapp-officesubscription.md)을 확인하세요.

Office 365 ProPlus, Visio Pro 및 Project Pro 라이선스를 별도로 구입해야 하는 경우 각각 해당 라이선스가 있습니다.

## Microsoft Office 2013 Professional Plus(평가판 전용)
무료 평가 기간 동안 Office 2013 이미지를 사용하여 서비스를 테스트할 수 있습니다.

이 이미지는 바닐라 이미지의 확장으로, Windows Server 2012 R2 이미지에 설명된 구성 요소 외에 Microsoft Office 2013 Professional Plus의 다음 구성 요소도 설치되어 있습니다.

* Access
* Excel
* Lync
* OneNote
* 비즈니스용 OneDrive(동기화 에이전트는 Azure RemoteApp과 함께 사용하도록 지원되지 않음)
* Outlook
* PowerPoint
* Project
* Visio
* Word
* Microsoft Office 언어 교정 도구

> [!IMPORTANT]
> **법적 정보:** 이 이미지에는 Microsoft Office 라이선스를 포함하지 않으며 *프로덕션에 사용할 수 없습니다*. Office 2013 Professional Plus 이미지는 평가 용도로만 사용됩니다. 프로덕션에 대해 Azure RemoteApp에서 Office 앱을 사용하려는 경우 Office 365 ProPlus 이미지를 사용해야 합니다. 라이선스 Office에 대한 자세한 내용은 [Azure RemoteApp과 함께 Office 365 사용](remoteapp-o365.md)을 참조하세요.
> 
> 

<!---HONumber=AcomDC_0817_2016-->