---
title: Microsoft Azure Data Box Gateway 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box Gateway의 소프트웨어 및 네트워킹 요구 사항에 관해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/17/2018
ms.author: alkohli
ms.openlocfilehash: f70cd5de29cc3050142dfff905acae2008dab115
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379125"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Azure Data Box Gateway 시스템 요구 사항(미리 보기)

이 문서에서는 Microsoft Azure Data Box Gateway 솔루션 및 Azure Data Box Gateway에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항을 설명합니다. Data Box Gateway를 배포하기 전에 정보를 신중하게 검토하고 배포 및 후속 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

Data Box Gateway 가상 장치의 시스템 요구 사항은 다음과 같습니다.

- **호스트의 소프트웨어 요구 사항** - 지원되는 플랫폼, 로컬 구성 UI용 브라우저, SMB 클라이언트 및 장치에 연결하는 호스트에 대한 추가 요구 사항을 설명합니다.
- **장치의 네트워킹 요구 사항** - 가상 장치가 작동하기 위한 네트워킹 요구 사항에 관한 정보를 제공합니다.

> [!IMPORTANT]
> Data Box Gateway는 미리 보기로 제공되고 있습니다. 이 솔루션을 배포하기 전에 [미리 보기에 대한 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요. 

## <a name="supported-os-for-clients-connected-to-device"></a>장치에 연결된 클라이언트에 지원되는 OS

다음은 Data Box Gateway에 연결된 클라이언트 또는 호스트에 대해 지원되는 운영 체제 목록입니다.

| **운영 체제/플랫폼** | **버전** |
| --- | --- |
| Windows Server |2012 R2 <br> 2016 |
| Windows |8, 10 |
| SUSE Linux |Enterprise Server 12(x86_64)|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>장치에 액세스하는 클라이언트에 지원되는 프로토콜

|**프로토콜** |**버전**   |**참고 사항**  |
|---------|---------|---------|
|SMB    | 2.X, 3.X      | SMB 1은 지원되지 않습니다.|
|NFS     | V3 및 V4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>장치에 지원되는 가상화 플랫폼

| **운영 체제/플랫폼**  |**버전**   |**참고 사항**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5        |VMware 도구는 지원되지 않습니다.         |


## <a name="supported-storage-accounts"></a>지원되는 저장소 계정

Data Box Gateway에 대해 지원되는 저장소 형식의 목록은 다음과 같습니다.

| **Storage 계정** | **참고 사항** |
| --- | --- |
| 클래식 | Standard |
| 범용  |표준; V1 및 V2 모두 지원됩니다. 핫 및 쿨 계층은 모두 지원됩니다. |


## <a name="supported-storage-types"></a>지원되는 저장소 형식

Data Box Gateway에 대해 지원되는 저장소 형식의 목록은 다음과 같습니다.

| **파일 형식** | **참고 사항** |
| --- | --- |
| Azure 블록 Blob | |
| Azure 페이지 Blob  | |
| Azure 파일 | |

## <a name="supported-browsers-for-local-web-ui"></a>로컬 웹 UI에 지원되는 브라우저

다음은 가상 장치의 로컬 웹 UI에 대해 지원되는 브라우저 목록입니다.

|브라우저  |버전  |추가 요구 사항/메모  |
|---------|---------|---------|
|Google Chrome   |최신 버전         |         |
|Microsoft Edge    | 최신 버전        |         |
|Internet Explorer     | 최신 버전        |         |
|FireFox    |최신 버전         |         |


## <a name="networking-requirements"></a>네트워킹 요구 사항

다음 테이블에는 SMB, 클라우드 또는 관리 트래픽을 고려하여 방화벽에서 열려야 하는 포트가 나열되어 있습니다. 이 테이블에서 *인* 또는 *인바운드*는 장치에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. *아웃* 또는 *아웃바운드*는 배포 후 데이터를 외부로 보내는 Data Box Gateway 장치에서 방향을 참조합니다.

| 포트 번호| 인 또는 아웃 | 포트 범위| 필수|   메모                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80(HTTP)|아웃|WAN |아니요|업데이트 복구를 위한 인터넷 액세스에는 아웃바운드 포트가 사용됩니다. <br>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다. |                          
| TCP 443(HTTPS)|아웃|WAN|yes|아웃바운드 포트는 클라우드의 데이터에 액세스하는 데 사용됩니다.<br>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다.|   
| UDP 53(DNS)|아웃|WAN|일부 경우<br>참고 사항 보기|이 포트는 인터넷 기반 DNS 서버로 사용하는 경우에만 필요합니다.<br>로컬 DNS 서버를 사용하는 것이 좋습니다. |
| UDP 123(NTP)|아웃|WAN|일부 경우<br>참고 사항 보기|이 포트는 인터넷 기반 NTP 서버로 사용하는 경우에만 필요합니다.  |
| UDP 67(DHCP)|아웃|WAN|일부 경우<br>참고 사항 보기|이 포트는 DHCP 서버를 사용 중인 경우에만 필요합니다.  |
| TCP 80(HTTP)|그런 다음|LAN|yes|로컬 관리용 장치의 로컬 UI에 대한 인바운드 포트입니다. <br>HTTP를 통해 로컬 UI에 액세스할 경우 HTTPS로 자동으로 리디렉션됩니다.  | 
| TCP 443(HTTPS)|그런 다음|LAN|yes|로컬 관리용 장치의 로컬 UI에 대한 인바운드 포트입니다. | 

## <a name="url-patterns-for-firewall-rules"></a>방화벽 규칙에 대한 URL 패턴

네트워크 관리자는 URL 패턴을 기준으로 하는 고급 방화벽 규칙이 인바운드 및 아웃바운드 트래픽을 필터링하도록 구성할 수 있습니다. Data Box Gateway 장치 및 Data Box Gateway 서비스는 Azure Service Bus, Azure Active Directory Access Control, 저장소 계정 및 Microsoft 업데이트 서버 등의 다른 Microsoft 응용 프로그램에 의존합니다. 이러한 응용 프로그램과 연결된 URL 패턴을 사용하여 방화벽 규칙을 구성할 수 있습니다. 이러한 응용 프로그램과 연결된 URL 패턴은 달라질 수 있습니다. 따라서 네트워크 관리자는 Data Box Gateway에 대한 방화벽 규칙을 모니터링하고 필요에 따라 업데이트해야 합니다.

Data Box Gateway 고정 IP 주소에 따라 대부분의 경우에서 자유롭게 아웃바운드 트래픽에 대한 방화벽 규칙을 설정하는 것이 좋습니다. 그러나 보안 환경을 만드는 데 필요한 고급 방화벽 규칙을 설정하려면 아래 정보를 사용할 수 있습니다.

> [!NOTE]
> - 장치(원본) IP는 항상 클라우드를 사용하도록 설정된 네트워크 인터페이스로 설정해야 합니다.
> - 대상 IP는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)로 설정해야 합니다.

|     URL 패턴                                                                                                                                                                                                                                                                                                                                                                                                                                       |     구성 요소/기능                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Azure Data Box Gateway 서비스<br>Azure Service Bus<br>인증 서비스    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    장치 활성화                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    인증서 해지                                                                               |
|    https://*.core.windows.net/*   https://*.data.microsoft.com   http://*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Azure 저장소 계정 및 모니터링                                                                |
|    http://windowsupdate.microsoft.com<br>http://*.windowsupdate.microsoft.com<br>https://*.windowsupdate.microsoft.com<br>http://*.update.microsoft.com<br>https://*.update.microsoft.com<br>http://*.windowsupdate.com<br>http://download.microsoft.com<br>http://*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*.ws.microsoft.com<br>https://*.ws.microsoft.com<br>http://*.mp.microsoft.com        |    Microsoft 업데이트 서버                                                                             |
|    http://*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Akamai CDN                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    지원 패키지                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Windows의 원격 분석 서비스는 사용자 환경 및 진단 원격 분석 업데이트를 참조하세요.      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>인터넷 대역폭

다음 요구 사항은 Data Box Gateway 장치에 사용할 수 있는 최소 인터넷 대역폭에 적용됩니다.

- Data Box Gateway에는 항상 사용 가능한 전용 20MB 인터넷 대역폭(또는 이상)이 있습니다. 이 대역폭은 다른 응용 프로그램과 공유하면 안됩니다. 
- Data Box Gateway에는 네트워크 조절을 사용할 때 사용할 수 있는 전용 32Mbps 인터넷 대역폭이 있습니다.

## <a name="next-step"></a>다음 단계

* [Azure Data Box Gateway 배포](data-box-gateway-deploy-prep.md)

