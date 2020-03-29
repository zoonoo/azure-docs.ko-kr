---
title: 방화벽 또는 프록시 서버에서 Azure 포털 URL 을 보호 목록
description: 프록시 서버 바이패스에 이러한 URL을 추가하여 Azure 포털 및 해당 서비스와 통신
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900664"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>방화벽 또는 프록시 서버에서 Azure 포털 URL 을 보호 목록

Azure 포털 URL에 대한 보안 제한을 우회하도록 온-프레미스 보안 장치를 구성할 수 있습니다. 이 구성은 로컬 또는 광역 네트워크와 Azure 클라우드 간의 성능 및 연결을 향상시킬 수 있습니다.

네트워크 관리자는 프록시 서버, 방화벽 또는 기타 장치를 배포하는 경우가 많습니다. 이러한 장치는 사용자가 인터넷에 액세스하는 방법을 보호하고 제어할 수 있도록 도와줍니다. 사용자를 보호하기 위해 고안된 규칙은 합법적인 비즈니스 관련 인터넷 트래픽을 차단하거나 느리게 할 수 있습니다. 이 트래픽에는 사용자와 Azure 간의 통신이 포함됩니다. 네트워크와 Azure 포털 및 해당 서비스 간의 연결을 최적화하려면 Azure 포털 URL을 보호 목록에 추가하는 것이 좋습니다.

## <a name="azure-portal-urls-for-proxy-bypass"></a>프록시 바이패스를 위한 Azure 포털 URL

Azure 포털에 대한 보호 목록에 대한 URL 끝점은 조직이 배포된 Azure 클라우드에 만 해당합니다. 이러한 끝점에 대한 네트워크 트래픽이 제한을 우회하도록 허용하려면 클라우드를 선택합니다. 그런 다음 프록시 서버 또는 방화벽에 URL 목록을 추가합니다.

#### <a name="public-cloud"></a>[퍼블릭 클라우드](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[미국 정부 클라우드](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[중국 정부 클라우드](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> 이러한 끝점에 대한 트래픽은 HTTP(80) 및 HTTPS(443)에 대한 표준 TCP 포트를 사용합니다.
>
>
## <a name="next-steps"></a>다음 단계

IP 주소를 보호 목록에 추가해야 합니까? 클라우드에 대한 Microsoft Azure 데이터 센터 IP 범위 목록을 다운로드합니다.

* [전 세계](https://www.microsoft.com/download/details.aspx?id=56519)
* [미국 정부](https://www.microsoft.com/download/details.aspx?id=57063)
* [독일](https://www.microsoft.com/download/details.aspx?id=57064)
* [중국](https://www.microsoft.com/download/details.aspx?id=57062)

다른 Microsoft 서비스는 연결을 위해 추가 URL 및 IP 주소를 사용합니다. Microsoft 365 서비스에 대한 네트워크 연결을 최적화하려면 [Office 365에 대한 네트워크 설정을](/office365/enterprise/set-up-network-for-office-365)참조하십시오.
