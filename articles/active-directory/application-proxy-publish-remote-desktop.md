---
title: "Azure Active Directory 응용 프로그램 프록시로 원격 데스크톱 게시 | Microsoft Docs"
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0d9f2f5c9ad5c47eb5e1a8a8ec3fee2952caef63
ms.openlocfilehash: 5c01f89c335a6c85b5b50a4c8491ae1b02740578
ms.lasthandoff: 02/27/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용하여 원격 데스크톱 게시

이 문서에서는 원격 사용자용으로 액세스할 수 있는 Windows 원격 데스크톱을 배포하는 방법을 설명합니다. 원격 데스크톱 배포는 온-프레미스 또는 IaaS 배포와 같은 개인 네트워크에 상주할 수 있습니다.

> [!NOTE]
> 응용 프로그램 프록시 기능은 Premium 또는 Basic 버전의 Azure Active Directory(Azure AD)로 업그레이드하는 경우에만 사용할 수 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

Azure AD 응용 프로그램 프록시를 통해 RDP(원격 데스크톱 프로토콜) 트래픽을 통과 프록시 응용 프로그램으로 게시할 수 있습니다. 이 솔루션은 연결 문제를 해결하며 네트워크 버퍼링, 강화된 인터넷 프런트 엔드 및 DDoS(배포된 서비스 거부) 보호와 같은 기본적인 보호 기능을 제공합니다.

## <a name="remote-desktop-deployment"></a>원격 데스크톱 배포

원격 데스크톱 게이트웨이는 원격 데스크톱 배포 내에서 게시되어 HTTPS 트래픽을 통한 RPC(원격 프로시저 호출)을 UDP(사용자 데이터그램 프로토콜) 트래픽을 통한 RDP로 변환할 수 있습니다.

Azure AD 응용 프로그램 프록시에 액세스하는 데 원격 데스크톱 클라이언트(예: MSTSC.exe)를 사용하도록 클라이언트를 구성할 수 있습니다. 이러한 방식으로 해당 커넥터를 사용하여 원격 데스크톱 게이트웨이에 대해 새 HTTPS 연결을 만들 수 있습니다. 결과적으로 게이트웨이가 인터넷에 직접 노출되지 않으며 모든 HTTPS 요청이 먼저 클라우드에서 종료됩니다.

토폴로지는 다음 다이어그램에 나와 있습니다.

 ![Azure AD 서비스 로컬 다이어그램](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>원격 데스크톱 게이트웨이 URL 구성

사용자가 원격 데스크톱 게이트웨이 URL을 구성하고 일반적인 방식으로 RDP 트래픽을 트리거하는 경우 파일 및 기타 메서드에 액세스할 수 있게 됩니다.

응용 프로그램 프록시에서 제공한 도메인 이름(msappproxy.net)을 사용하거나 Azure AD에서 구성된 사용자 지정 도메인 이름(예: rdg.contoso.com)을 사용하여 게시할 수 있습니다.

원격 데스크톱 게이트웨이 URL로 클라이언트 장치 및 RDP 파일이 이미 구성된 경우 동일한 도메인 이름을 사용하도록 선택하여 변경을 회피할 수 있습니다. 이 경우에 이 도메인에 적용되는 인증서는 응용 프로그램 프록시에 제공되고 해당 CRL(인증서 해지 목록)은 인터넷을 통해 액세스될 수 있어야 합니다.

원격 데스크톱 게이트웨이 URL을 구성하지 않은 경우 사용자 또는 관리자가 여기에서와 같이 원격 데스크톱 연결 대화 상자를 사용하여 MSTSC(원격 데스크톱 클라이언트)에 지정할 수 있습니다.

 ![원격 데스크톱 연결 대화 상자](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

**고급** 탭에서 **설정**을 클릭하면 **연결 설정** 대화 상자가 나타납니다.

 ![원격 데스크톱 연결 대화 상자의 연결 설정 창](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>원격 데스크톱 웹 액세스

조직에서 RDWA(원격 데스크톱 웹 액세스) 포털을 사용하는 경우 Azure AD 응용 프로그램 프록시를 사용하여 게시할 수도 있습니다. 사전 인증 및 SSO(Single Sign-On)를 사용하여 이 포털에 게시할 수 있습니다.

RDWA 시나리오의 토폴로지는 다음 다이어그램에 나와 있습니다.

 ![RDWA 시나리오의 다이어그램](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

위의 경우에 사용자는 RDWA에 액세스하기 전에 Azure AD에서 인증됩니다. Azur AD에서 이미 인증된 경우(예: Office 365를 사용하는 경우) RDWA에 대해 다시 인증할 필요가 없습니다.

사용자가 RDP 세션을 시작하면, RDP 채널을 통해 다시 인증해야 합니다. RDWA에서 원격 데스크톱 게이트웨이로의 SSO가 ActiveX를 사용하여 클라이언트에서 사용자 자격 증명을 저장하는 것을 기반으로 하기 때문입니다. 이 프로세스는 RDWA 양식 기반 인증에서 트리거됩니다. RDWA 인증에서 Kerbros를 사용하는 경우 양식 기반 인증이 제시되지 않으므로 RDWA에서 RDP로 SSO는 작동하지 않습니다.

RDWA에서 RDP 트래픽에 대해 SSO가 필요하거나 RDWA 양식 기반 인증이 과도하게 사용자 지정된 경우 사전 인증 없이 RDWA를 게시할 수 있습니다.

이 시나리오의 토폴로지는 다음 다이어그램에 나와 있습니다.

 ![RDWA 시나리오의 다이어그램](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

앞의 경우에 사용자는 양식 기반 인증을 사용하여 RDWA에 대해 인증해야 하지만 RDP를 통해서는 인증하지 않아도 됩니다.

>[!NOTE]
>앞의 두 경우 모두 RDP 트래픽에서 사전 인증이 필요하지 않습니다. 따라서 사용자는 RDWA를 먼저 완료하지 않고 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 응용 프로그램 프록시를 사용하여 SharePoint에 원격 액세스 사용하도록 설정](application-proxy-enable-remote-access-sharepoint.md)  
[Azure Portal에서 응용 프로그램 프록시 사용](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)

