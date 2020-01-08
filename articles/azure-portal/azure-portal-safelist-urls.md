---
title: Azure Portal Url Safelist | Microsoft Docs
description: 이러한 Url을 프록시 서버 바이패스에 추가 하 여 Azure Portal 및 해당 서비스와 통신
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 4f4badbd923b10cf2cd66f7df9742a6bc657a01c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75637545"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>방화벽 또는 프록시 서버에서 Azure Portal Url Safelist

로컬 또는 광역 네트워크와 Azure 클라우드 간의 양호한 성능과 연결을 위해 온-프레미스 보안 장치를 구성 하 여 Azure Portal Url에 대 한 보안 제한을 우회 합니다. 네트워크 관리자는 사용자가 인터넷에 액세스 하는 방법을 안전 하 게 보호 하기 위해 프록시 서버, 방화벽 또는 기타 장치를 배포 하는 경우가 많습니다. 그러나 사용자를 보호 하도록 설계 된 규칙은 경우에 따라 사용자와 Azure 간의 통신을 비롯 하 여 합법적인 비즈니스 관련 인터넷 트래픽을 차단 하거나 늦출 수 있습니다. 네트워크와 Azure Portal 및 해당 서비스 간의 연결을 최적화 하려면 safelist에 Azure Portal Url을 추가 하는 것이 좋습니다.

## <a name="azure-portal-urls-for-proxy-bypass"></a>프록시 바이패스에 대 한 Azure Portal Url

Azure Portal에 대해 safelist URL 끝점은 조직이 배포 되는 Azure 클라우드와 관련이 있습니다. 클라우드를 선택 하 고 프록시 서버 또는 방화벽에 Url 목록을 추가 하 여 이러한 끝점에 대 한 네트워크 트래픽이 제한을 우회 하도록 허용 합니다.

#### <a name="public-cloudtabpublic-cloud"></a>[공용 클라우드](#tab/public-cloud)
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

#### <a name="us-government-cloudtabus-government-cloud"></a>[미국 정부 클라우드](#tab/us-government-cloud)
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

#### <a name="china-government-cloudtabchina-government-cloud"></a>[중국 정부 클라우드](#tab/china-government-cloud)
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
> 이러한 끝점에 대 한 트래픽은 HTTP (80) 및 HTTPS (443)에 표준 TCP 포트를 사용 합니다.
>
>
## <a name="next-steps"></a>다음 단계

IP 주소를 safelist 해야 하나요? 클라우드의 Microsoft Azure 데이터 센터 IP 범위 목록을 다운로드 합니다.

* [전](https://www.microsoft.com/download/details.aspx?id=56519)
* [미국 정부](https://www.microsoft.com/download/details.aspx?id=57063)
* [독일](https://www.microsoft.com/download/details.aspx?id=57064)
* [중국](https://www.microsoft.com/download/details.aspx?id=57062)

다른 Microsoft 서비스는 연결에 대 한 추가 Url 및 IP 주소를 사용 합니다. Microsoft 365 서비스에 대 한 네트워크 연결을 최적화 하려면 [Office 365에 대 한 네트워크 설정](/office365/enterprise/set-up-network-for-office-365)을 참조 하세요.
