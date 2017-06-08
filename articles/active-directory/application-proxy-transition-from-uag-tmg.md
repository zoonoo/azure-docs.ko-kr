---
title: "Azure AD 응용 프로그램 프록시로 업그레이드 | Microsoft Docs"
description: "Microsoft Forefront 또는 Unified Access Gateway를 업그레이드하는 경우 가장 좋은 프록시 솔루션을 선택합니다."
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
ms.date: 04/27/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e9d5caa4d11012744ce9f26648166371f3aa17ba
ms.contentlocale: ko-kr
ms.lasthandoff: 05/13/2017

---
# <a name="upgrade-to-azure-ad-proxies-from-microsoft-forefront-or-unified-access-gateway"></a>Microsoft Forefront 또는 Unified Access Gateway의 Azure AD 프록시 업그레이드

이 문서에서는 Microsoft Forefront TMG(Threat Management Gateway) 및 UAG(United Access Gateway) 솔루션에서 Azure AD 응용 프로그램 프록시로 이동하는 방법을 설명합니다.

Forefront TMG 및 UAG에서 응용 프로그램 프록시로 전환하는 방법에 대한 자세한 내용은 [Microsoft의 관련 백서](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/)를 참조하세요.

## <a name="functionality-details-for-the-conversion"></a>변환에 대한 기능 정보

|**TMG/UAG 기능**|**최신 솔루션**|
|:-----|:-----|
|브라우저 앱에 대한 선택적 HTTP 게시|Azure AD 응용 프로그램 프록시|
|AD FS(Active Directory Federation Services) 통합|Azure AD 응용 프로그램 프록시|
|풍부한 프로토콜 게시(예: Citrix, Lync, RDG)|Azure AD 응용 프로그램 프록시|
|포털|Azure AD 응용 프로그램 프록시용 Azure AD 액세스 패널 또는 Office 365 앱 시작 관리자|
|끝점 상태 검색|Intune 또는 System Center|
|SSL 터널링|Windows SSL 또는 VPN|
|계층 2/3 방화벽|Windows Server|
|ActiveSync(HTTP 기본) 및 RDG에 대한 사전 인증|현재 Microsoft에서 제공하는 솔루션 없음|
|웹 응용 프로그램 방화벽|현재 Microsoft에서 제공하는 솔루션이 없습니다.|
|보안 웹 게이트웨이(정방향 프록시)|현재 Microsoft에서 제공하는 솔루션이 없습니다.|


## <a name="next-steps"></a>다음 단계

[블로그: 웹 응용 프로그램 프록시](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[블로그: Azure AD 응용 프로그램 프록시](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

