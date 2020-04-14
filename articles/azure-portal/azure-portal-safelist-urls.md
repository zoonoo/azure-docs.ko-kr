---
title: 방화벽 또는 프록시 서버에서 Azure 포털 URL 을 보호 목록
description: 프록시 서버 바이패스에 이러한 URL을 추가하여 Azure 포털 및 해당 서비스와 통신
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255052"
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
