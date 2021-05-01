---
title: 방화벽 또는 프록시 서버에서 Azure Portal URL 수신 허용 목록
description: 이러한 URL을 프록시 서버 무시에 추가하여 Azure Portal 및 해당 서비스와 통신
ms.date: 04/10/2020
ms.topic: conceptual
ms.openlocfilehash: 7d9c8222ee85c0c16ec1e1926335ac06e0389797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745879"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>방화벽 또는 프록시 서버에서 Azure Portal URL 수신 허용 목록

온-프레미스 Azure Portal URL에 대한 보안 제한을 무시하도록 보안 디바이스를 구성할 수 있습니다. 이 구성은 LAN 또는 WAN과 Azure 클라우드 간의 성능 및 연결을 향상시킬 수 있습니다.

네트워크 관리자는 일반적으로 프록시 서버, 방화벽 또는 기타 디바이스를 배포합니다. 이러한 디바이스를 사용하면 사용자가 인터넷에 액세스하는 방법을 안전하게 제어할 수 있습니다. 사용자를 보호하도록 설계된 규칙은 경우에 따라 합법적인 비즈니스 관련 인터넷 트래픽을 차단하거나 느리게 만들 수 있습니다. 이 트래픽은 사용자와 Azure 간의 통신을 포함합니다. 네트워크와 Azure Portal 및 해당 서비스 간의 연결을 최적화하려면 안전 목록에 Azure Portal URL을 추가하는 것이 좋습니다.

## <a name="azure-portal-urls-for-proxy-bypass"></a>프록시 무시에 대한 Azure Portal URL

Azure Portal용 안전 목록에 대한 URL 엔드포인트는 조직이 배포되는 Azure 클라우드와 관련이 있습니다. 이러한 엔드포인트에 대한 네트워크 트래픽이 제한을 무시하도록 허용하려면 클라우드를 선택합니다. 그런 다음, URL 목록을 프록시 서버 또는 방화벽에 추가합니다.

#### <a name="public-cloud"></a>[퍼블릭 클라우드](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azurefd.net
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
*.applicationinsights.us
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
> 이러한 엔드포인트에 대한 트래픽은 HTTP에 대한 표준 TCP 포트(80) 및 HTTPS에 표준 TCP 포트(443)를 사용합니다.
>
>
