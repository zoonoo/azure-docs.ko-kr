---
title: "Azure 앱에서 온-프레미스 Active Directory를 사용하여 인증 | Microsoft Docs"
description: "온-프레미스 Active Directory를 사용하여 인증하는 Azure App Service에서 LOB(기간 업무) 앱의 다른 옵션에 대해 알아봅니다."
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: dde6b7e6-bf6a-4fa5-8390-3a18155d21bd
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/31/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f989ea86c76706af9413b2bb945956b1c6432e1d


---
# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Azure 앱에서 온-프레미스 Active Directory를 사용하여 인증
이 문서에서는 [Azure App Service](../app-service/app-service-value-prop-what-is.md)에서 온-프레미스 AD(Active Directory)를 사용하여 인증하는 방법을 보여 줍니다. Azure 앱은 클라우드에서 호스팅되지만 온-프레미스 AD 사용자가 안전하게 인증하는 방법이 있습니다. 

## <a name="authenticate-through-azure-active-directory"></a>Azure Active Directory를 통해 인증
Azure Active Directory 테넌트는 온-프레미스 AD를 사용하여 디렉터리 동기화될 수 있습니다. 이 방식을 통해 AD 사용자는 인터넷에서 앱에 액세스하고 온-프레미스 자격 증명을 사용하여 인증할 수 있습니다. 또한 Azure App Service는 [이 메서드에 대한 턴키 솔루션](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)을 제공합니다. 단추를 몇 번 클릭하여 Azure 앱에 디렉터리 동기화된 테넌트를 사용하여 인증할 수 있습니다. 이 방법에는 다음과 같은 장점이 있습니다.

* 앱에서 인증 코드가 필요하지 않습니다. App Service를 통해 인증을 수행하고 앱에서 기능을 제공하는 데 시간을 사용합니다.
* [Azure AD Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx) 를 통해 Azure 앱에서 디렉터리 데이터에 액세스할 수 있습니다.
* Office 365, Dynamics CRM Online, Microsoft Intune 및 수천 개의 타사 클라우드 응용 프로그램을 포함하여 [Azure Active Directory에서 지원하는 모든 응용 프로그램](/marketplace/active-directory/)에 SSO를 제공합니다. 
* Azure Active Directory는 역할 기반 액세스 제어를 지원합니다. 코드에 대한 최소한으로 변경하여 [Authorize(Roles="X")] 패턴을 사용할 수 있습니다.

Azure Active Directory를 인증하는 LOB(기간 업무) Azure 앱을 작성하는 방법은 [Azure Active Directory 인증을 사용하여 LOB(기간 업무) Azure 앱 만들기](web-sites-dotnet-lob-application-azure-ad.md)를 참조하세요.

## <a name="authenticate-through-an-on-premises-sts"></a>온-프레미스 STS를 통해 인증
AD FS(Active Directory Federation Services)와 같은 온-프레미스 STS(보안 토큰 서비스)가 있는 경우 Azure 앱에 대한 인증을 페더레이션하는 데 사용할 수 있습니다. 이 방법은 회사 정책에서 AD 데이터를 Azure에 저장하지 않도록 금지하는 경우에 가장 적합합니다. 그러나 다음 사항에 유의하세요.

* STS 토폴로지를 온-프레미스에 배포해야 하므로 비용 및 관리 오버헤드가 발생합니다.
* AD FS 관리자만 [신뢰 당사자 트러스트 및 클레임 규칙](http://technet.microsoft.com/library/dd807108.aspx)을 구성할 수 있으며 이는 개발자의 옵션을 제한할 수 있습니다. 반면에 응용 프로그램 기준으로 [클레임](http://technet.microsoft.com/library/ee913571.aspx) 을 관리하고 사용자 지정할 수 있습니다.
* 온-프레미스 AD 데이터에 액세스하려면 회사 방화벽을 통한 별도의 솔루션이 필요합니다.

온-프레미스 STS를 인증하는 LOB(기간 업무) Azure 앱을 작성하는 방법은 [AD FS 인증을 사용하여 LOB(기간 업무) Azure 앱 만들기](web-sites-dotnet-lob-application-adfs.md)를 참조하세요.




<!--HONumber=Nov16_HO3-->


