---
title: "Microsoft Forefront에서 Azure AD 앱 프록시로 전환 | Microsoft Docs"
description: "Azure AD 응용 프로그램 프록시 커넥터에 대한 기본 사항을 제공합니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9945512d18d66c321c0d24ee1050497d4716fd47
ms.openlocfilehash: 699112846cc1e4e9fc6b04b1b8509152d7aecdef


---
#<a name="transition-to-azure-ad-app-proxy-from-microsoft-forefront"></a>Microsoft Forefront에서 Azure AD 앱 프록시로 전환

이 문서에서는 Microsoft Forefront TMG(Threat Management Gateway) 및 UAG(United Access Gateway) 솔루션에서 Azuare AD 응용 프로그램 프록시인 WAP(웹 응용 프로그램 프록시) 및 AADAP(Azure AD 응용 프로그램 프록시)로 전환하는 방법을 설명합니다. 

> [!NOTE]
> 응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.
> 
 
많은 고객들이 Forefront UAG 및 TMG에서 새 응용 프로그램 프록시를 전환하는 방법을 문의하고 있습니다. 자세한 내용은 전환을 자세히 설명하는 이 [백서](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx)를 참조하세요. 
 
## <a name="tmguag-conversion-to-azure-ad-application-proxy-table"></a>TMG/UAG-Azure AD 응용 프로그램 프록시 변환 테이블
 
|**TMG/UAG 기능**|**WAP(웹 응용 프로그램 프록시)/AADAP(Azure AD 응용 프로그램 프록시)**|
|:-----|:-----|
|브라우저 앱에 대한 선택적 HTTP 게시|Windows Server 2012 R2의 WAP에서 사용 가능(현재 AADAP에서 사용 가능)|
|ADFS 통합|Windows Server 2012 R2의 WAP에서 사용 가능(현재 AADAP에서 사용 가능)|
|풍부한 프로토콜 게시(예: Citrix, Lync, RDG)|Windows Server 2012 R2의 WAP에서 사용 가능(현재 AADAP에서 사용 가능)|
|ActiveSync(HTTP 기본) 및 RDG에 대한 사전 인증|Windows Server vNext의 WAP에서 사용 가능할 예정(AADAP에 제공될 예정)|
|포털|WAP에 대해 Intune/System Center 사용(AADAP에 사용할 수 있는 AAD 액세스 패널 또는 Office 365 앱 시작 관리자)|
|끝점 상태 검색|Intune/System Center 사용|
|SSH 터널링|Windows SSL/VPN 기능 사용|
|계층 2/3 방화벽|Windows Server 기능 사용|
|웹 응용 프로그램 방화벽|현재 Microsoft에서 제공하는 솔루션 없음|
|보안 웹 게이트웨이(정방향 프록시)|현재 Microsoft에서 제공하는 솔루션 없음|


## <a name="next-steps"></a>다음 단계

[블로그: 웹 응용 프로그램 프록시](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[블로그: 응용 프로그램 프록시](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)



<!--HONumber=Feb17_HO1-->


