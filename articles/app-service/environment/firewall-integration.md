---
title: Azure App Service Environment 아웃바운드 트래픽 잠금
description: Azure Firewall과 통합하여 아웃바운드 트래픽을 보호하는 방법을 설명합니다
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: 5f2dd31488ae61bec061a81986a208bd328bf39b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093623"
---
# <a name="locking-down-an-app-service-environment"></a>App Service Environment 잠금

ASE(App Service Environment)에는 제대로 작동하기 위해 액세스해야 하는 여러 가지 외부 종속성이 있습니다. ASE는 고객의 Azure VNet(Virtual Network)에 상주합니다. 고객은 ASE 종속성 트래픽을 허용해야 합니다. 이는 VNet의 모든 송신을 잠그려고 하는 고객에게는 문제가 됩니다.

ASE에는 여러 가지 인바운드 종속성이 있습니다. 인바운드 관리 트래픽은 방화벽 장치를 통해 보낼 수 없습니다. 이 트래픽에 대한 원본 주소는 알려져 있으며 [App Service Environment 관리 주소](https://docs.microsoft.com/azure/app-service/environment/management-addresses) 문서에 게시됩니다. 해당 정보를 사용하여 네트워크 보안 그룹 규칙을 만들어 인바운드 트래픽을 보호할 수 있습니다.

ASE 아웃바운드 종속성은 거의 전적으로 뒤에 고정 주소가 없는 FQDN을 사용하여 정의됩니다. 고정 주소가 없으면 NSG(네트워크 보안 그룹)를 사용하여 ASE의 아웃바운드 트래픽을 잠글 수 없습니다. 주소는 현재 확인에 기반한 규칙을 설정하고 NSG를 만드는 데 사용할 수 없을 만큼 자주 변경됩니다. 

아웃바운드 주소를 보호하는 솔루션은 도메인 이름에 따라 아웃바운드 트래픽을 제어할 수 있는 방화벽 장치를 사용하는 것입니다. Azure 네트워킹 팀은 Azure Firewall이라는 새 네트워크 어플라이언스를 미리 보기에 추가했습니다. Azure Firewall은 대상의 DNS 이름에 따라 송신 HTTP 및 HTTPS 트래픽을 제한할 수 있습니다.  

## <a name="configuring-azure-firewall-with-your-ase"></a>ASE를 사용하여 Azure Firewall 구성 

Azure Firewall을 사용하여 ASE의 송신을 잠그는 단계는 다음과 같습니다.

1. ASE가 있거나 배치될 VNet에 Azure Firewall을 만듭니다. [Azure Firewall 설명서](https://docs.microsoft.com/azure/firewall/)
2. Azure Firewall UI에서 App Service Environment FQDN 태그를 선택합니다.
3. 인터넷의 다음 홉을 사용하여 [App Service Environment 관리 주소]( https://docs.microsoft.com/azure/app-service/environment/management-addresses)의 관리 주소가 있는 경로 테이블을 만듭니다. 경로 테이블 항목은 비대칭 라우팅 문제를 방지하는 데 필요합니다. 
4. 인터넷의 다음 홉을 사용하여 IP 주소 종속성 아래에 명시된 IP 주소 종속성에 대한 경로를 추가합니다. 
5. Azure Firewall 네트워크 어플라이언스인 다음 홉을 사용하여 0.0.0.0/0에 대한 경로 테이블에 경로를 추가합니다
6. ASE 서브넷에 대한 서비스 엔드포인트를 Azure SQL 및 Azure Storage에 만듭니다
7. 만든 경로 테이블을 ASE 서브넷에 할당합니다.  

## <a name="application-traffic"></a>응용 프로그램 트래픽 

위의 단계를 통해 ASE가 문제 없이 작동할 수 있습니다. 여전히 응용 프로그램 요구 사항에 맞게 항목을 구성해야 합니다. Azure Firewall을 사용하여 구성된 ASE의 응용 프로그램에는 두 가지 문제가 있습니다.  

- 응용 프로그램 종속성 FQDN을 Azure Firewall 또는 경로 테이블에 추가해야 합니다.
- 비대칭 라우팅 문제를 방지하려면 트래픽이 발생할 주소에 대한 경로를 만들어야 합니다.

응용 프로그램에 종속성이 있으면 Azure Firewall에 추가해야 합니다. 다른 모든 항목에 대해 HTTP/HTTPS 트래픽 및 네트워크 규칙을 허용하는 응용 프로그램 규칙을 만듭니다. 

응용 프로그램 요청 트래픽이 제공되는 주소 범위를 알고 있는 경우 ASE 서브넷에 할당된 경로 테이블에 이를 추가할 수 있습니다. 주소 범위가 크거나 지정되지 않은 경우 Application Gateway와 같은 네트워크 어플라이언스를 사용하여 경로 테이블에 추가할 하나의 주소를 제공할 수 있습니다. ILB ASE를 사용하여 Application Gateway를 구성하는 방법에 대한 자세한 내용은 [ILB ASE와 Application Gateway 통합](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)을 참조하세요.


## <a name="dependencies"></a>종속성

Azure App Service에는 여러 가지 외부 종속성이 있습니다. 이러한 종속성은 다음 몇 가지 주요 영역으로 나눌 수 있습니다.

- 아웃바운드 네트워크 트래픽을 잠그려면 서비스 엔드포인트 지원 서비스에서 서비스 엔드포인트를 설정해야 합니다.
- IP 주소 엔드포인트는 도메인 이름을 사용하여 처리되지 않습니다. 모든 HTTPS 트래픽에서 도메인 이름을 사용해야 하는 방화벽 장치의 경우 문제가 될 수 있습니다. IP 주소 엔드포인트는 ASE 서브넷에 설정된 경로 테이블에 추가해야 합니다.
- FQDN HTTP/HTTPS 엔드포인트는 방화벽 장치에 배치할 수 있습니다.
- 와일드카드 HTTP/HTTPS 엔드포인트는 몇 가지 한정자를 기반으로 하여 ASE에 따라 달라질 수 있는 종속성입니다. 
- Linux 종속성은 Linux 응용 프로그램을 ASE에 배포하는 경우에만 문제가 됩니다. Linux 응용 프로그램을 ASE에 배포하지 않는 경우 이러한 주소는 방화벽에 추가할 필요가 없습니다. 


#### <a name="service-endpoint-capable-dependencies"></a>서비스 엔드포인트 지원 종속성 

| 엔드포인트 |
|----------|
| Azure SQL |
| Azure Storage |
| Azure KeyVault |


#### <a name="ip-address-dependencies"></a>IP 주소 종속성 

| 엔드포인트 |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 종속성 

| 엔드포인트 |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80   |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Wildcard HTTP/HTTPS dependencies 

| 엔드포인트 |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*.servicebus.windows.net:443 |
|grmdsprod\*lini\*.servicebus.windows.net:443 |
|grsecprod\*mini\*.servicebus.windows.net:443 |
|grsecprod\*lini\*.servicebus.windows.net:443 |
|graudprod\*mini\*.servicebus.windows.net:443 |
|graudprod\*lini\*.servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Linux 종속성 

| 엔드포인트 |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

