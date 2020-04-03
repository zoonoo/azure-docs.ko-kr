---
title: 아웃바운드 트래픽 잠금
description: Azure 방화벽과 통합하여 앱 서비스 환경 내에서 아웃바운드 트래픽을 보호하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 03/31/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3dadb57c6358623974de1a27e1601d99b28fee32
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584327"
---
# <a name="locking-down-an-app-service-environment"></a>App Service Environment 잠금

ASE(App Service Environment)에는 제대로 작동하기 위해 액세스해야 하는 여러 가지 외부 종속성이 있습니다. ASE는 고객의 Azure VNet(Virtual Network)에 상주합니다. 고객은 ASE 종속성 트래픽을 허용해야 합니다. 이는 VNet의 모든 송신을 잠그려고 하는 고객에게는 문제가 됩니다.

ASE를 관리하는 데 사용되는 인바운드 끝점이 많이 있습니다. 인바운드 관리 트래픽은 방화벽 디바이스를 통해 보낼 수 없습니다. 이 트래픽에 대한 원본 주소는 알려져 있으며 [App Service Environment 관리 주소](https://docs.microsoft.com/azure/app-service/environment/management-addresses) 문서에 게시됩니다. 또한 인바운드 트래픽을 보호하기 위해 NSG(네트워크 보안 그룹)와 함께 사용할 수 있는 AppServiceManagement라는 서비스 태그도 있습니다.

ASE 아웃바운드 종속성은 거의 전적으로 뒤에 고정 주소가 없는 FQDN을 사용하여 정의됩니다. 정적 주소가 부족하면 네트워크 보안 그룹을 사용하여 ASE에서 아웃바운드 트래픽을 잠글 수 없습니다. 주소는 현재 확인에 기반한 규칙을 설정하고 NSG를 만드는 데 사용할 수 없을 만큼 자주 변경됩니다. 

아웃바운드 주소를 보호하는 솔루션은 도메인 이름에 따라 아웃바운드 트래픽을 제어할 수 있는 방화벽 디바이스를 사용하는 것입니다. Azure Firewall은 대상의 FQDN을 기반으로 아웃바운드 HTTP 및 HTTPS 트래픽을 제한할 수 있습니다.  

## <a name="system-architecture"></a>시스템 아키텍처

방화벽 장치를 통과하는 아웃바운드 트래픽이 있는 ASE를 배포하려면 ASE 서브넷에서 경로를 변경해야 합니다. 경로는 IP 수준에서 작동합니다. 경로를 정의하는 데 주의를 기울여야 하지 않으면 TCP 회신 트래픽을 다른 주소의 소스로 강제할 수 있습니다. 회신 주소가 전송된 주소 트래픽과 다른 경우 문제를 비대칭 라우팅이라고 하며 TCP가 중단됩니다.

ASE로의 인바운드 트래픽이 트래픽이 들어온 것과 동일한 방식으로 회신할 수 있도록 정의된 경로가 있어야 합니다. 인바운드 관리 요청 및 인바운드 응용 프로그램 요청에 대해 경로를 정의해야 합니다.

ASE를 오//도 인하여 다음 규칙을 준수해야 합니다.

* Azure SQL, 저장소 및 이벤트 허브에 대한 트래픽은 방화벽 장치를 사용하여 지원되지 않습니다. 이 트래픽은 해당 서비스로 직접 전송되어야 합니다. 이를 실현하는 방법은 이러한 세 서비스에 대한 서비스 끝점을 구성하는 것입니다. 
* 경로 테이블 규칙은 인바운드 관리 트래픽을 발생한 위치에서 다시 보내는 것으로 정의되어야 합니다.
* 경로 테이블 규칙은 인바운드 응용 프로그램 트래픽을 발생한 위치에서 다시 보내는 것을 정의해야 합니다. 
* ASE를 떠나는 다른 모든 트래픽은 경로 테이블 규칙을 사용하여 방화벽 장치로 전송할 수 있습니다.

![Azure Firewall 연결 흐름 포함 ASE][5]

## <a name="locking-down-inbound-management-traffic"></a>인바운드 관리 트래픽 잠금

ASE 서브넷에 NSG가 아직 할당되지 않은 경우 하나를 만듭니다. NSG 내에서 포트 454, 455에서 AppServiceManagement라는 서비스 태그의 트래픽을 허용하는 첫 번째 규칙을 설정합니다. AppServiceManagement 태그에서 액세스를 허용하는 규칙은 ASE를 관리하기 위해 공용 IP에서 필요한 유일한 것입니다. 해당 서비스 태그 뒤에 있는 주소는 Azure 앱 서비스를 관리하는 데만 사용됩니다. 이러한 연결을 통해 흐르는 관리 트래픽은 인증 인증서로 암호화되고 보호됩니다. 이 채널의 일반적인 트래픽에는 고객이 시작한 명령 및 상태 프로브와 같은 트래픽이 포함됩니다. 

새 서브넷으로 포털을 통해 만들어진 ASA는 AppServiceManagement 태그에 대한 허용 규칙을 포함하는 NSG로 만들어집니다.  

또한 ASE는 포트 16001의 로드 밸러서 태그에서 인바운드 요청을 허용해야 합니다. 포트 16001의 로드 밸러서의 요청은 로드 밸러블러와 ASE 프런트 엔드 간에 계속 유지됩니다. 포트 16001이 차단되면 ASE가 비정상으로 바갑니다.

## <a name="configuring-azure-firewall-with-your-ase"></a>ASE를 사용하여 Azure Firewall 구성 

Azure Firewall을 사용하여 기존 ASE의 송신을 잠그는 단계는 다음과 같습니다.

1. ASE 서브넷에서 서비스 엔드포인트를 SQL, Storage 및 Event Hub에 사용하도록 설정합니다. 서비스 끝점을 사용하려면 서브넷을 > 네트워킹 포털로 이동하여 서비스 끝점 드롭다운에서 Microsoft.EventHub, Microsoft.SQL 및 Microsoft.Storage를 선택합니다. 서비스 엔드포인트를 Azure SQL에 사용하도록 설정하면 앱에 있는 Azure SQL 종속성도 서비스 엔드포인트로 구성되어야 합니다. 

   ![서비스 엔드포인트 선택][2]
  
1. ASE가 있는 VNet에서 AzureFirewallSubnet이라는 서브넷을 만듭니다. [Azure Firewall 설명서](https://docs.microsoft.com/azure/firewall/)의 지침에 따라 Azure Firewall을 만듭니다.

1. [Azure Firewall UI] > [규칙] > [애플리케이션 규칙 컬렉션]에서 [애플리케이션 규칙 컬렉션 추가]를 선택합니다. 이름, 우선 순위를 제공하고 허용을 설정합니다. FQDN 태그 섹션에서 이름을 입력하고, 원본 주소를 *로 설정하며, App Service Environment FQDN 태그 및 Windows 업데이트를 선택합니다. 
   
   ![애플리케이션 규칙 추가][1]
   
1. [Azure Firewall UI] > [규칙] > [애플리케이션 규칙 컬렉션]에서 [네트워크 규칙 컬렉션 추가]를 선택합니다. 이름, 우선 순위를 제공하고 허용을 설정합니다. IP 주소 아래의 규칙 섹션에서 이름을 제공하고, **Any의**ptocol을 선택하고, *를 소스 및 대상 주소로 설정하고, 포트를 123으로 설정합니다. 이 규칙은 시스템이 NTP를 사용하여 클록 동기화를 수행할 수 있게 합니다. 시스템 문제를 분류하기 위해 포트 12000과 동일한 방식으로 다른 규칙을 만듭니다. 

   ![NTP 네트워크 규칙 추가][3]
   
1. [Azure Firewall UI] > [규칙] > [애플리케이션 규칙 컬렉션]에서 [네트워크 규칙 컬렉션 추가]를 선택합니다. 이름, 우선 순위를 제공하고 허용을 설정합니다. 서비스 태그 아래의 규칙 섹션에서 이름을 제공하고, **Any의**프로토콜을 선택하고, *를 소스 주소로 설정하고, AzureMonitor의 서비스 태그를 선택하고, 포트를 80, 443으로 설정합니다. 이 규칙을 사용하면 시스템에서 Azure 모니터에 상태 및 메트릭 정보를 제공할 수 있습니다.

   ![NTP 서비스 태그 네트워크 규칙 추가][6]
   
1. 인터넷의 다음 홉을 사용하여 [App Service Environment 관리 주소]( https://docs.microsoft.com/azure/app-service/environment/management-addresses)의 관리 주소가 있는 경로 테이블을 만듭니다. 경로 테이블 항목은 비대칭 라우팅 문제를 방지하는 데 필요합니다. 인터넷의 다음 홉을 사용하여 IP 주소 종속성 아래에 명시된 IP 주소 종속성에 대한 경로를 추가합니다. Azure Firewall 개인 IP 주소인 다음 홉을 사용하여 0.0.0.0/0에 대한 경로 테이블에 Virtual Appliance 경로를 추가합니다. 

   ![경로 테이블 만들기][4]
   
1. 만든 경로 테이블을 ASE 서브넷에 할당합니다.

#### <a name="deploying-your-ase-behind-a-firewall"></a>방화벽 뒤에 ASE 배포

방화벽 뒤에 ASE를 배치하는 단계는 ASE 서브넷을 만든 다음, 이전 단계를 수행해야 한다는 점을 제외하고는 Azure Firewall로 기존 ASE를 구성하는 것과 동일합니다. 기존 서브넷에 ASE를 만들려면 [Resource Manager 템플릿으로 ASE 만들기](https://docs.microsoft.com/azure/app-service/environment/create-from-template) 문서에 설명된 대로 Resource Manager 템플릿을 사용해야 합니다.

## <a name="application-traffic"></a>애플리케이션 트래픽 

위의 단계를 통해 ASE가 문제 없이 작동할 수 있습니다. 여전히 애플리케이션 요구 사항에 맞게 항목을 구성해야 합니다. Azure Firewall을 사용하여 구성된 ASE의 애플리케이션에는 두 가지 문제가 있습니다.  

- 애플리케이션 종속성을 Azure Firewall 또는 경로 테이블에 추가해야 합니다. 
- 비대칭 라우팅 문제를 방지하려면 애플리케이션 트래픽에 대한 경로를 만들어야 합니다.

애플리케이션에 종속성이 있으면 Azure Firewall에 추가해야 합니다. 다른 모든 항목에 대해 HTTP/HTTPS 트래픽 및 네트워크 규칙을 허용하는 애플리케이션 규칙을 만듭니다. 

애플리케이션 요청 트래픽이 제공되는 주소 범위를 알고 있는 경우 ASE 서브넷에 할당된 경로 테이블에 이를 추가할 수 있습니다. 주소 범위가 크거나 지정되지 않은 경우 Application Gateway와 같은 네트워크 어플라이언스를 사용하여 경로 테이블에 추가할 하나의 주소를 제공할 수 있습니다. ILB ASE를 사용하여 Application Gateway를 구성하는 방법에 대한 자세한 내용은 [ILB ASE와 Application Gateway 통합](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)을 참조하세요.

이러한 Application Gateway의 사용은 시스템을 구성하는 방법의 한 예일 뿐입니다. 이 경로를 따른 경우 ASE 서브넷 경로 테이블에 경로를 추가해야 Application Gateway로 전송된 회신 트래픽이 직접 이동하게 됩니다. 

## <a name="logging"></a>로깅 

Azure Firewall은 로그를 Azure Storage, Event Hub 또는 Azure Monitor 로그에 보낼 수 있습니다. 앱을 지원되는 모든 대상과 통합하려면 [Azure Firewall 포털] > [진단 로그]로 이동하여 원하는 대상에 대한 로그를 설정합니다. Azure Monitor 로그와 통합하면 Azure Firewall에 전송된 모든 트래픽에 대한 로깅을 볼 수 있습니다. 거부되는 트래픽을 보려면 Log Analytics 작업 영역 포털 > 로그를 열고 다음과 같은 쿼리를 입력합니다. 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Azure 방화벽을 Azure Monitor 로그와 통합하면 모든 응용 프로그램 종속성을 인식하지 못하는 경우 응용 프로그램이 처음 작동할 때 유용합니다. Azure 모니터 에서 [로그 데이터 분석에서](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)Azure 모니터 로그에 대해 자세히 알아볼 수 있습니다.
 
## <a name="dependencies"></a>종속성

다음 정보는 Azure Firewall 이외의 방화벽 어플라이언스를 구성하는 경우에만 필요합니다. 

- 서비스 엔드포인트 지원 서비스는 서비스 엔드포인트로 구성되어야 합니다.
- IP 주소 종속성은 HTTP/S가 아닌 트래픽(TCP 및 UDP 모두)에 대한 것입니다.
- FQDN HTTP/HTTPS 엔드포인트는 방화벽 디바이스에 배치할 수 있습니다.
- 와일드카드 HTTP/HTTPS 엔드포인트는 몇 가지 한정자를 기반으로 하여 ASE에 따라 달라질 수 있는 종속성입니다. 
- Linux 종속성은 Linux 응용 프로그램을 ASE에 배포하는 경우에만 문제가 됩니다. Linux 응용 프로그램을 ASE에 배포하지 않는 경우 이러한 주소는 방화벽에 추가할 필요가 없습니다. 

#### <a name="service-endpoint-capable-dependencies"></a>서비스 엔드포인트 지원 종속성 

| 엔드포인트 |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP 주소 종속성

| 엔드포인트 | 세부 정보 |
|----------| ----- |
| \*:123 | NTP 클록 확인. 트래픽이 포트 123의 여러 엔드포인트에서 확인됩니다. |
| \*:12000 | 이 포트는 일부 시스템 모니터링에 사용됩니다. 차단된 경우 일부 문제는 심사하기 어렵지만 ASE는 계속 작동합니다. |
| 40.77.24.27:80 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 40.77.24.27:443 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 13.90.249.229:80 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 13.90.249.229:443 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 104.45.230.69:80 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 104.45.230.69:443 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 13.82.184.151:80 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 13.82.184.151:443 | ASE 문제를 모니터링하고 경고하는 데 필요 |

Azure Firewall을 사용하면 FQDN 태그로 구성된 모든 항목을 자동으로 가져옵니다. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 종속성 

| 엔드포인트 |
|----------|
|graph.microsoft.com:443 |
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
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
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
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Wildcard HTTP/HTTPS dependencies 

| 엔드포인트 |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*.ctldl.windowsupdate.com:443 |

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
| \*.cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>미국 정부 종속성

미국 정부 지역의 ASE의 경우 이 문서의 [ASE 섹션으로 Azure 방화벽 구성의](https://docs.microsoft.com/azure/app-service/environment/firewall-integration#configuring-azure-firewall-with-your-ase) 지침에 따라 ASE를 사용하여 Azure 방화벽을 구성합니다.

미국 정부에서 Azure 방화벽 이외의 장치를 사용하려는 경우 

* 서비스 엔드포인트 지원 서비스는 서비스 엔드포인트로 구성되어야 합니다.
* FQDN HTTP/HTTPS 엔드포인트는 방화벽 디바이스에 배치할 수 있습니다.
* 와일드카드 HTTP/HTTPS 엔드포인트는 몇 가지 한정자를 기반으로 하여 ASE에 따라 달라질 수 있는 종속성입니다.

Linux는 미국 정부 지역에서 사용할 수 없으므로 선택적 구성으로 나열되지 않습니다.

#### <a name="service-endpoint-capable-dependencies"></a>서비스 엔드포인트 지원 종속성 ####

| 엔드포인트 |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP 주소 종속성

| 엔드포인트 | 세부 정보 |
|----------| ----- |
| \*:123 | NTP 클록 확인. 트래픽이 포트 123의 여러 엔드포인트에서 확인됩니다. |
| \*:12000 | 이 포트는 일부 시스템 모니터링에 사용됩니다. 차단된 경우 일부 문제는 심사하기 어렵지만 ASE는 계속 작동합니다. |
| 40.77.24.27:80 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 40.77.24.27:443 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 13.90.249.229:80 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 13.90.249.229:443 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 104.45.230.69:80 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 104.45.230.69:443 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 13.82.184.151:80 | ASE 문제를 모니터링하고 경고하는 데 필요 |
| 13.82.184.151:443 | ASE 문제를 모니터링하고 경고하는 데 필요 |

#### <a name="dependencies"></a>종속성 ####

| 엔드포인트 |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com
|ocsp.digicert.0 |
|ocsp.msocsp.co|
|ocsp.verisign.0 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
