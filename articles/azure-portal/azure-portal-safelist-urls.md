---
title: Azure portal 수신 허용 목록 Url | Microsoft Docs
description: Azure portal에서 해당 서비스와 통신에 프록시 서버 무시에 해당이 Url 추가
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305011"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Azure portal 수신 방화벽 또는 프록시 서버의 Url

좋은 성능과 로컬 또는 광역 네트워크와 Azure 클라우드 간에 연결을 위해 Azure portal에 대 한 보안 제한을 사용 하지 않으려면 온-프레미스 보안 장치를 구성 Url입니다. 네트워크 관리자는 종종 프록시 서버, 방화벽 또는 다른 장치를 쉽게 보호 하 고 사용자가 인터넷을 액세스 하는 방식을 제어할 수 있도록 배포입니다. 그러나 사용자를 보호 하기 위해 설계 된 규칙 수 경우에 따라 차단 하거나 합법적인 비즈니스와 관련 된 인터넷 트래픽의 경우와 Azure 간의 통신을 포함 하 여 속도가 저하. 네트워크 및 Azure portal 및 해당 서비스 간의 연결을 최적화 하려면 Azure portal을 추가 하면 권장에 수신 하는 Url입니다.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure portal Url 프록시에 대 한 무시

프록시 서버 또는 제한을 무시 하기 위해 이러한 끝점에 네트워크 트래픽을 허용 하도록 방화벽을 다음과 같은 Url 추가 합니다.

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
* *.wpc.azureedge.net

> [!NOTE]
> 이러한 끝점에 트래픽을 HTTP (80) 및 HTTPS (443)에 대 한 표준 TCP 포트를 사용합니다.
>
>
## <a name="next-steps"></a>다음 단계

* 수신 허용 목록 IP 주소를 해야 합니까? 목록을 다운로드할 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)합니다.
* 다른 Microsoft 서비스 사용 추가 Url 및 IP 주소 연결 합니다. Microsoft 365 서비스에 대 한 네트워크 연결을 최적화 하려면 참조 [Office 365에 대 한 네트워크 설정](/office365/enterprise/set-up-network-for-office-365)합니다.
