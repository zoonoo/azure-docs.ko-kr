---
title: Azure Portal Url Safelist | Microsoft Docs
description: 이러한 Url을 프록시 서버 바이패스에 추가 하 여 Azure Portal 및 해당 서비스와 통신
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667482"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>방화벽 또는 프록시 서버에서 Azure Portal Url Safelist

로컬 또는 광역 네트워크와 Azure 클라우드 간의 양호한 성능과 연결을 위해 온-프레미스 보안 장치를 구성 하 여 Azure Portal Url에 대 한 보안 제한을 우회 합니다. 네트워크 관리자는 사용자가 인터넷에 액세스 하는 방법을 안전 하 게 보호 하기 위해 프록시 서버, 방화벽 또는 기타 장치를 배포 하는 경우가 많습니다. 그러나 사용자를 보호 하도록 설계 된 규칙은 경우에 따라 사용자와 Azure 간의 통신을 비롯 하 여 합법적인 비즈니스 관련 인터넷 트래픽을 차단 하거나 늦출 수 있습니다. 네트워크와 Azure Portal 및 해당 서비스 간의 연결을 최적화 하려면 safelist에 Azure Portal Url을 추가 하는 것이 좋습니다.

## <a name="azure-portal-urls-for-proxy-bypass"></a>프록시 바이패스에 대 한 Azure Portal Url

다음 Url 목록을 프록시 서버 또는 방화벽에 추가 하 여 이러한 끝점에 대 한 네트워크 트래픽이 제한을 무시 하도록 허용 합니다.

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> 이러한 끝점에 대 한 트래픽은 HTTP (80) 및 HTTPS (443)에 표준 TCP 포트를 사용 합니다.
>
>
## <a name="next-steps"></a>다음 단계

* IP 주소를 safelist 해야 하나요? [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)목록을 다운로드 합니다.
* 다른 Microsoft 서비스는 연결에 대 한 추가 Url 및 IP 주소를 사용 합니다. Microsoft 365 서비스에 대 한 네트워크 연결을 최적화 하려면 [Office 365에 대 한 네트워크 설정](/office365/enterprise/set-up-network-for-office-365)을 참조 하세요.
