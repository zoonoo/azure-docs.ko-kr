---
title: Azure 데이터 센터 통합 Stack-끝점을 게시 | Microsoft Docs
description: 데이터 센터에서 Azure Stack 끝점을 게시 하는 방법 알아보기
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 07/16/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: bfb50a3e1b2276a63d79f5a6b8b1ab721f039d9d
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090557"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure 데이터 센터 통합 스택-끝점 게시
Azure Stack 인프라 역할에 대 한 가상 IP 주소 (Vip)를 설정합니다. 이러한 Vip의 공용 IP 주소 풀에서 할당 됩니다. 각 VIP는 소프트웨어 정의 네트워크 계층에서 액세스 제어 목록 (ACL)을 사용 하 여 보호 됩니다. Acl은 솔루션 강화 (에서 및 BMC) 실제 스위치 간에 사용 됩니다. 배포 시 지정 된 외부 DNS 영역에서 각 끝점에 대 한 DNS 항목을 생성 됩니다.


다음 아키텍처 다이어그램은 서로 다른 네트워크 레이어 및 Acl을 보여 줍니다.

![구조적 그림](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>포트 및 프로토콜 (인바운드)

Vip는 외부 네트워크에 Azure Stack 끝점 게시에 필요한 인프라의 집합입니다. 합니다 *끝점 (VIP)* 표에서 각 끝점에서 필요한 포트 및 프로토콜입니다. SQL 리소스 공급자와 같은 추가 리소스 공급자를 필요로 하는 끝점에 대 한 특정 리소스 공급자 배포 설명서를 참조 하십시오.

내부 인프라 Vip 게시 Azure Stack에 대 한 필수 이기 때문에 표시 되지 않습니다.

> [!NOTE]
> 사용자 Vip는 Azure Stack 연산자가 없는 컨트롤을 사용 하 여 사용자가 정의 하는 동적입니다.


|끝점 (VIP)|DNS 호스트 A 레코드|프로토콜|포트|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|포털 (관리자)|Adminportal 합니다.  *&lt;지역 >.&lt; fqdn >*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Azure 리소스 관리자 (관리자)|Adminmanagement 합니다.  *&lt;지역 >.&lt; fqdn >*|HTTPS|443<br>30024|
|포털 (사용자)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (사용자)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|그래프|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|인증서 해지 목록|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP 및 UDP|53|
|Key Vault (사용자)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (관리자)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|저장소 큐|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|저장소 테이블|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|저장소 Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 리소스 공급자|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 리소스 공급자|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|VPN Gateway|     |     |[VPN gateway FAQ를 참조 하세요.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability)합니다.|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>포트 및 Url (아웃 바운드)

Azure Stack은 투명 프록시 서버만 지원 합니다. 배포의 경우 기존 프록시 서버에 투명 프록시 업링크를 허용 해야 합니다 다음 포트 및 Url 아웃 바운드 통신에:


|목적|URL|프로토콜|포트|
|---------|---------|---------|---------|
|ID|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com|HTTP<br>HTTPS|80<br>443|
|마켓플레이스 배포|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|패치 및 업데이트|https://&#42;.azureedge.net|HTTPS|443|
|등록|https://management.azure.com|HTTPS|443|
|사용 현황|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net|HTTPS|443|
|Windows Defender|. wdcp.microsoft.com<br>. wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>http://crl.microsoft.com/pki/crl/products<br>http://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|
|NTP|     |UDP|123|
|DNS|     |TCP<br>UDP|53|
|     |     |     |     |



## <a name="next-steps"></a>다음 단계

[Azure Stack PKI 요구 사항](azure-stack-pki-certs.md)
