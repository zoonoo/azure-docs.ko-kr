---
title: "Azure 데이터 센터 통합 스택-끝점 게시"
description: "데이터 센터에서 Azure 스택 끝점을 게시 하는 방법을 알아봅니다"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/18/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: 0d15252079b62f6a74a1279309fb9b1b3ed5711e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure 데이터 센터 통합 스택-끝점 게시

*적용 대상: Azure 스택 시스템 통합*

Azure 스택 인프라 역할에 대 한 다양 한 끝점 (Vip-가상 IP 주소)를 설정합니다. 해당이 Vip는 공용 IP 주소 풀에서 할당 됩니다. 각 VIP 소프트웨어 정의 네트워크 계층에서 액세스 제어 목록 (ACL)로 보호 됩니다. Acl은 솔루션을 더욱 강화 하기 위해 각기 물리적 스위치 (될 수 있습니다 앞뒤 및 BMC)도 사용 됩니다. 배포 시에 지정 된 외부 DNS 영역에서 각 끝점에 대 한 DNS 항목이 생성 됩니다.


다음 아키텍처 다이어그램에는 여러 네트워크 계층 및 Acl 보여 줍니다.

![아키텍처 다이어그램](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>포트 및 프로토콜 (인바운드)

외부 네트워크에 Azure 스택 끝점 게시에 필요한 인프라 Vip는 다음 표에 나열 됩니다. 목록은 각 끝점의 필요한 포트 및 프로토콜입니다. SQL 리소스 공급자 등과 같은 추가 리소스 공급자에 필요한 끝점은 특정 리소스 공급자 배포 설명서에서 다룹니다.

Azure 스택 게시에 필요 하지 않기 때문에 내부 인프라 Vip 나열 되지 않습니다.

> [!NOTE]
> 사용자 Vip는 동적 이며 Azure 스택 연산자가 없는 제어를 사용한 사용자가 정의 합니다.


|끝점 (VIP)|DNS 호스트 A 레코드|프로토콜|포트|
|---------|---------|---------|---------|
|AD FS|`Adfs.[Region].[External FQDN]`|HTTPS|443|
|포털 (관리자)|`Adminportal.[Region].[External FQDN]`|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure 리소스 관리자 (관리자)|`Adminmanagement.[Region].[External FQDN]`|HTTPS|443<br>30024|
|포털 (사용자)|`Portal. [Region].[External FQDN]`|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure 리소스 관리자 (사용자)|`Management.[Region].[External FQDN]`|HTTPS|443<br>30024|
|그래프|`Graph.[Region].[External FQDN]`|HTTPS|443|
|인증서 해지 목록|`Crl.[Region].[External FQDN]`|HTTP|80|
|DNS|`*.[Region].[External FQDN]`|TCP 및 UDP|53|
|주요 자격 증명 모음 (사용자)|`*.vault.[Region].[External FQDN]`|TCP|443|
|주요 자격 증명 모음 (관리자)|`*.adminvault.[Region].[External FQDN]`|TCP|443|
|저장소 큐|`*.queue.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|저장소 테이블|`*.table.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|저장소 Blob|`*.blob.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>포트 및 Url (아웃 바운드)

Azure 스택 투명 프록시 서버만 지원합니다. 배포에서 여기서 기존 프록시 서버에 투명 프록시 업링크 허용 해야 하는 다음 포트 및 Url 아웃 바운드 통신에 대 한:


|목적|URL|프로토콜|포트|
|---------|---------|---------|---------|
|ID|`login.windows.net`<br>`login.microsoftonline.com`<br>`graph.windows.net`|HTTP<br>HTTPS|80<br>443|
|마켓플레이스에서 배포|`https://management.azure.com`<br>`https://*.blob.core.windows.net`<br>`https://*.azureedge.net`<br>`https://*.microsoftazurestack.com`|HTTPS|443|
|패치 및 업데이트|`https://*.azureedge.net`|HTTPS|443|
|등록|`https://management.azure.com`|HTTPS|443|
|사용|`https://*.microsoftazurestack.com`<br>`https://*.trafficmanager.com`|HTTPS|443|

## <a name="firewall-publishing"></a>방화벽 게시

이전 섹션에 나열 된 포트에 적용할 기존 방화벽을 통해 Azure 스택 서비스를 게시할 때 인바운드 통신 합니다.

Azure 스택 보안을 위해 방화벽 장치를 사용 하는 것이 좋습니다. 그러나 엄격한 요구 사항은 아닙니다. 하지만 방화벽 등으로 분산 된 서비스 거부 (DDOS) 공격 및 콘텐츠 검사에 도움이 될 수, blob, 테이블 및 큐와 같은 Azure 저장소 서비스에 대 한 처리량 병목 지점이 될 수도 있습니다.

Id 모델 (Azure AD 또는 AD FS)에 따라, 그렇지 AD FS 끝점을 게시 하지 않아도 될 수 있습니다. 연결이 끊긴된 배포 모드를 사용 하는 경우 AD FS 끝점을 게시 해야 합니다. (자세한 내용은 데이터 센터 통합 identity 항목 참조).

Azure 리소스 관리자 (관리자), 관리자 포털 및 주요 자격 증명 모음 (관리자) 끝점 하지 않아도 외부 게시 합니다. 시나리오에 따라 다릅니다. 예를 들어 서비스 공급자로 하려는 공격 노출 영역 제한 및 내부 네트워크 및 인터넷에서에서 Azure 스택만 관리 합니다.

엔터프라이즈 조직 외부 네트워크에 기존 회사 네트워크 가능 합니다. 이 시나리오에서는 Azure 스택 회사 네트워크에서 작동 하도록 해당 끝점을 게시 해야 합니다.

## <a name="edge-firewall-scenario"></a>가장자리 방화벽 시나리오

가장자리 배포에서 Azure 스택 유무에 방화벽 앞에 관계 없이 (ISP에서 제공)에 지 라우터 뒤에 직접 배포 됩니다.

![Azure 스택 지 배포의 아키텍처 다이어그램](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

일반적으로 라우팅 가능한 공용 IP 주소는 공용 VIP 풀에 대 한 지 배포에 배포 시간에 지정 됩니다. 이 시나리오에는 공용에서 Azure와 같은 클라우드 처럼 전체 자체 제어 클라우드 환경을 구현 하려면 사용자 수 있습니다.

### <a name="using-nat"></a>NAT를 사용 하 여

오버 헤드로 인해 권장 되지는 않지만 끝점 게시에 대 한 네트워크 주소 변환 (NAT)를 사용할 수 있습니다. 사용자가 완전히 제어 하는 끝점 게시 하기 위해 사용자가 사용할 수 있는 모든 포트를 포함 하는 VIP 사용자 당 NAT 규칙이 있어야 합니다.

또 다른 고려 사항은 Azure VPN 터널을 Azure 사용한 하이브리드 클라우드 시나리오에서 NAT를 사용 하는 끝점 설정을 지원 하지 않습니다.

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>기업/인트라넷 경계 네트워크 방화벽 시나리오

경계/enterprise/인트라넷 배포에서 Azure 스택 일반적으로 경계 네트워크 (DMZ 라고도 함)의 일부인 두 번째 방화벽 외부 배포 됩니다.

![Azure 스택 방화벽 시나리오](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

Azure 스택의 공용 VIP 풀에 대 한 라우팅 가능한 공용 IP 주소가 지정 된 경우 이러한 주소 논리적으로 경계 네트워크에 속하며 기본 방화벽에서 게시 규칙을 필요로 합니다.

### <a name="using-nat"></a>NAT를 사용 하 여

Azure 스택 공용 VIP 풀에 대 한 public이 아닌 라우팅 가능한 IP 주소를 사용 하면 NAT Azure 스택 끝점 게시 하려면 보조 방화벽에서 사용 됩니다. 이 시나리오에서는 보조 방화벽 및 너머로, 기본 방화벽에서 게시 규칙을 구성 해야 합니다. NAT를 사용 하려는 경우 다음 사항을 고려 하십시오.

- 사용자가 고유한 끝점 및 소프트웨어 정의 네트워킹 (SDN) 스택에 직접 게시 규칙을 제어 하기 때문에 방화벽 규칙을 관리 하는 경우 NAT 오버 헤드가 추가 합니다. 사용자가 자신의 Vip를 게시 하 고 포트 목록을 업데이트 하는 Azure 스택 연산자를 연결 해야 합니다.
- 사용자 환경을 제한 하는 NAT를 사용 하는 동안이 전체 운영자에 게 효과적으로 제어할을 수 게시 요청을 처리 합니다.
- Azure 사용한 하이브리드 클라우드 시나리오에 대 한 Azure 설정을 NAT.를 사용 하는 끝점에 대 한 VPN 터널을 지원 하지 않습니다 것이 좋습니다


## <a name="next-steps"></a>다음 단계

[Azure 스택 데이터 센터 통합-보안](azure-stack-integrate-security.md)