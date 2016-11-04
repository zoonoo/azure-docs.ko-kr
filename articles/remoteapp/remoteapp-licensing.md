---
title: Azure RemoteApp 라이선스 요구 사항 | Microsoft Docs
description: Azure RemoteApp의 라이선스 작동 방식에 대해 알아봅니다.
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
# Azure RemoteApp의 라이선스 작동 방식
> [!IMPORTANT]
> Azure RemoteApp은 중단될 예정입니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148)을 읽어보세요.
> 
> 

이제 Azure RemoteApp 서비스를 설정하고 템플릿을 만들었으며 사용자에게 앱을 게시할 준비가 되었습니다. 하지만 마지막으로 확인해야 할 사항이 있습니다. 바로 라이선스입니다. RemoteApp 및 RemoteApp을 통해 공유하는 앱에 대해 라이선스는 어떻게 작동하나요?

RemoteApp에는 Windows 라이선스 또는 원격 데스크톱 CAL이 필요하지 않습니다. 구독은 RemoteApp 쪽 자체를 담당합니다. ([가격 계획](https://azure.microsoft.com/pricing/details/remoteapp)의 세부 정보를 확인하세요.)

구독에 포함된 이미지 중 하나를 사용하는 경우 별도의 라이선스 없이 해당 이미지에 설치된 모든 앱을 공유할 수 있습니다. 예를 들어 Windows Server 2012 R2 템플릿 이미지를 사용하여 컬렉션을 빌드하는 경우 System Center Endpoint Protection을 사용자와 공유할 수 있습니다. 이 규칙에 대한 유일한 예외는 Office 365 ProPlus의 경우 별도 구독이 필요하고 Office 2013의 경우 프로덕션 컬렉션에서 공유할 수 없다는 점입니다.

Azure RemoteApp과 함께 제공된 Office 365 템플릿 이미지를 사용하려면 *기존* Office 365 ProPlus 계획이 있어야 합니다. 사용자 지정 템플릿을 사용하여 게시하는 Office 365 앱의 경우도 마찬가지입니다. 소유한 구독으로 앱을 활성화해야 합니다. 이 내용은 평가판 및 유료 구독 둘 다에 적용됩니다. 평가판 사용 중 Office 365 템플릿 이미지를 사용하려는 경우 *구독이 아직 없으면* Office 365 페이지로 이동하여 평가판 구독을 [등록](https://go.microsoft.com/fwlink/p/?LinkID=403802)하세요. 자세한 내용은 [RemoteApp과 Office가 함께 작동하는 방식](remoteapp-o365.md)을 참조하세요.

평가 기간 중 Office 365 평가판 구독을 가져오지 않으려면 RemoteApp과 함께 제공된 Office 2013 Professional Plus 템플릿 이미지를 사용하세요. 이 템플릿 이미지는 30일 동안만 사용할 수 있으며 유료 컬렉션으로 변환할 수 없습니다.

다른 앱의 경우, 앱을 공유하기 위한 라이선스가 있는지 확인해야 합니다.

참 합리적이지 않은가요? 법적으로 공유할 자격이 있는 모든 앱을 게시할 수 있습니다. 또한 프로그램을 공유할 자격이 있는지 확인해야 합니다.

클라우드 컬렉션의 CAL 또는 볼륨 라이선스 계약은 사용할 수 없습니다. 볼륨 라이선스 계약을 사용하여 하이브리드 컬렉션의 응용 프로그램을 활성화할 *수 있습니다*(Office 제외). 볼륨 라이선스 미디어에서 해당 템플릿 이미지에 설치하면 됩니다. 응용 프로그램 공급업체의 정보에 따라 원격 데스크톱 환경에 라이선스를 설치하세요.

<!---HONumber=AcomDC_0817_2016-->