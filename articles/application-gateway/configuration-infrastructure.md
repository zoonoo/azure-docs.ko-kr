---
title: Azure 애플리케이션 게이트웨이 인프라 구성
description: 이 문서에서는 Azure 애플리케이션 게이트웨이 인프라를 구성 하는 방법을 설명 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: ce0e03407349505d54aeb22b164fa8593446967d
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653032"
---
# <a name="application-gateway-infrastructure-configuration"></a>인프라 구성 Application Gateway

Application gateway 인프라에는 가상 네트워크, 서브넷, 네트워크 보안 그룹 및 사용자 정의 경로가 포함 됩니다.

## <a name="virtual-network-and-dedicated-subnet"></a>가상 네트워크 및 전용 서브넷

Application gateway는 가상 네트워크의 전용 배포입니다. 가상 네트워크 내에는 application gateway에 대 한 전용 서브넷이 필요 합니다. 특정 응용 프로그램 게이트웨이 배포의 인스턴스를 서브넷에 여러 개 포함할 수 있습니다. 서브넷에 다른 응용 프로그램 게이트웨이를 배포할 수도 있습니다. 그러나 응용 프로그램 게이트웨이 서브넷에 다른 리소스를 배포할 수 없습니다.

> [!NOTE]
> 동일한 서브넷에서 Standard_v2와 표준 Azure 애플리케이션 게이트웨이를 혼합할 수 없습니다.

### <a name="size-of-the-subnet"></a>서브넷 크기

Application Gateway은 인스턴스당 하나의 개인 IP 주소를 사용 하 고 개인 프런트 엔드 IP가 구성 된 경우 다른 개인 IP 주소를 사용 합니다.

또한 Azure는 내부적으로 사용 하기 위해 각 서브넷에 5 개의 IP 주소를 예약 합니다. 처음 네 개의 ip 주소는 마지막 IP 주소입니다. 예를 들어 개인 프런트 엔드 IP가 없는 15 개의 응용 프로그램 게이트웨이 인스턴스를 생각해 보세요. 이 서브넷에 대 한 IP 주소는 20 개 이상 필요 합니다. 내부 사용은 5이 고 응용 프로그램 게이트웨이 인스턴스의 경우 15입니다.

27 개의 응용 프로그램 게이트웨이 인스턴스와 개인 프런트 엔드 IP의 IP 주소가 있는 서브넷을 고려 합니다. 이 경우에는 응용 프로그램 게이트웨이 인스턴스의 경우 27 개의 IP 주소, 즉 개인 프런트 엔드에 대 한 27 개와 내부용으로 5 개의 IP 주소를 사용 33 해야 합니다.

Application Gateway (Standard 또는 WAF) SKU는 최대 32 인스턴스 (32 인스턴스 IP 주소 + 1 개인 프런트 엔드 IP + 5 Azure 예약 됨)를 지원할 수 있으므로 최소 서브넷 크기인/26을 권장 합니다.

Application Gateway (Standard_v2 또는 WAF_v2 SKU)는 최대 125 인스턴스 (125 인스턴스 IP 주소 + 1 개인 프런트 엔드 IP + 5 Azure 예약 됨)를 지원할 수 있으므로 최소 서브넷 크기인/24를 권장 합니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹

NSGs (네트워크 보안 그룹)는 Application Gateway에서 지원 됩니다. 하지만 다음과 같은 몇 가지 제한 사항이 있습니다.

- 들어오는 인터넷 트래픽을 Application Gateway v1 SKU의 경우 65503-65534 TCP 포트에서 허용하고, 대상 서브넷이 **Any**이고 원본이 **GatewayManager** 서비스 태그인 v2 SKU의 경우 65200-65535 TCP 포트에서 허용해야 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 Azure 인증서에 의해 보호 (잠김) 됩니다. 이러한 끝점에서는 외부 엔터티 (해당 게이트웨이의 고객 포함)를 통신할 수 없습니다.

- 아웃 바운드 인터넷 연결은 차단할 수 없습니다. NSG의 기본 아웃 바운드 규칙은 인터넷 연결을 허용 합니다. 다음을 권장합니다.

  - 기본 아웃 바운드 규칙을 제거 하지 마십시오.
  - 아웃 바운드 연결을 거부 하는 아웃 바운드 규칙은 만들지 마십시오.

- **Azureloadbalancer** 태그에서 대상 서브넷으로의 트래픽은 허용 되어야 **합니다.**

### <a name="allow-access-to-a-few-source-ips"></a>몇 가지 원본 Ip에 대 한 액세스 허용

이 시나리오에서는 Application Gateway 서브넷에 NSGs를 사용 합니다. 이 우선 순위에 따라 서브넷에 다음과 같은 제한 사항을 적용 합니다.

1. 인바운드 액세스 포트 (예: HTTP 액세스용 포트 80)로 전체 Application Gateway 서브넷 주소 범위 및 대상 포트를 대상으로 하는 원본 IP 또는 IP 범위에서 들어오는 트래픽을 허용 합니다.
2. 원본에서 **Gmanager** 서비스 태그 및 **destination으로 들어오는** 요청을 Application Gateway v1 sku의 경우 65503-65534로, [백 엔드 상태 통신](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)의 경우 포트 65200-65535을 사용 하도록 허용 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 Azure 인증서에 의해 보호 (잠김) 됩니다. 적절 한 인증서가 없는 경우 외부 엔터티는 해당 끝점에 대 한 변경 내용을 초기화할 수 없습니다.
3. [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)에서 들어오는 Azure Load Balancer 프로브 (*azureloadbalancer* 태그) 및 인바운드 가상 네트워크 트래픽 (*VirtualNetwork* 태그)을 허용 합니다.
4. 모든 수신 트래픽 거부 규칙을 사용 하 여 차단 합니다.
5. 모든 대상에 대해 인터넷에 대 한 아웃 바운드 트래픽을 허용 합니다.

## <a name="supported-user-defined-routes"></a>지원 되는 사용자 정의 경로 

> [!IMPORTANT]
> Application Gateway 서브넷에서 UDRs를 사용 하면 [백 엔드 상태 보기](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) 의 상태가 **알 수 없음**으로 표시 될 수 있습니다. 이로 인해 Application Gateway 로그 및 메트릭이 생성 되지 않을 수도 있습니다. 백 엔드 상태, 로그 및 메트릭을 볼 수 있도록 Application Gateway 서브넷에서 UDRs를 사용 하지 않는 것이 좋습니다.

- **v1**

   V1 SKU의 경우 UDRs (사용자 정의 경로)는 종단 간 요청/응답 통신을 변경 하지 않는 한 Application Gateway 서브넷에서 지원 됩니다. 예를 들어 Application Gateway 서브넷에서 UDR을 설정 하 여 패킷 검사를 위한 방화벽 어플라이언스를 가리키도록 할 수 있습니다. 그러나 검사 후 패킷이 의도 한 대상에 도달할 수 있는지 확인 해야 합니다. 그렇지 않으면 잘못 된 상태 프로브 또는 트래픽 라우팅 동작이 발생할 수 있습니다. 여기에는 Azure Express 경로 또는 가상 네트워크의 VPN 게이트웨이에서 전파 되는 학습 된 경로 또는 기본 0.0.0.0/0 경로가 포함 됩니다. 0.0.0.0/0을 온-프레미스 (강제 터널링)에서 리디렉션해야 하는 모든 시나리오는 v1에서 지원 되지 않습니다.

- **v2**

   V2 SKU의 경우 지원 되는 시나리오와 지원 되지 않는 시나리오는 다음과 같습니다.

   **v2 지원 시나리오**
   > [!WARNING]
   > 경로 테이블을 잘못 구성 하면 Application Gateway v 2에서 비대칭 라우팅이 발생할 수 있습니다. 모든 관리/제어 평면 트래픽이 가상 어플라이언스를 통하지 않고 인터넷으로 직접 전송 되는지 확인 합니다. 로깅 및 메트릭도 영향을 받을 수 있습니다.


  **시나리오 1**: udr을 사용 하 여 Application Gateway 서브넷에 대 한 BGP (Border Gateway Protocol) 경로 전파 사용 안 함

   경우에 따라 기본 게이트웨이 경로 (0.0.0.0/0)가 Application Gateway 가상 네트워크와 연결 된 Express 경로 또는 VPN 게이트웨이를 통해 보급 됩니다. 이렇게 하면 인터넷에 직접 경로를 사용 해야 하는 관리 평면 트래픽이 중단 됩니다. 이러한 시나리오에서는 UDR을 사용 하 여 BGP 경로 전파를 사용 하지 않도록 설정할 수 있습니다. 

   BGP 경로 전파를 사용 하지 않도록 설정 하려면 다음 단계를 사용 합니다.

   1. Azure에서 경로 테이블 리소스를 만듭니다.
   2. **가상 네트워크 게이트웨이 경로 전파** 매개 변수를 사용 하지 않도록 설정 합니다. 
   3. 적절 한 서브넷에 경로 테이블을 연결 합니다. 

   이 시나리오에 UDR을 사용 하도록 설정 하면 기존 설정이 중단 되지 않습니다.

  **시나리오 2**: Udr-인터넷에 0.0.0.0/0을 전달 합니다.

   UDR을 만들어 0.0.0.0/0 트래픽을 인터넷으로 직접 보낼 수 있습니다. 

  **시나리오 3**: kubenet를 사용 하는 Azure Kubernetes Service에 대 한 udr

  AKS (Azure Kubernetes Service) 및 AGIC (Application Gateway 수신 컨트롤러)와 함께 kubenet를 사용 하는 경우 Application Gateway에서 pod로 전송 된 트래픽을 올바른 노드로 라우팅할 수 있도록 경로 테이블이 필요 합니다. Azure CNI를 사용 하는 경우에는이 작업이 필요 하지 않습니다. 

  Kubenet가 작동할 수 있도록 경로 테이블을 사용 하려면 다음 단계를 수행 합니다.

  1. AKS에서 만든 리소스 그룹으로 이동 합니다 (리소스 그룹의 이름은 "MC_"로 시작 해야 함).
  2. 해당 리소스 그룹에서 AKS에 의해 생성 된 경로 테이블을 찾습니다. 경로 테이블은 다음 정보로 채워야 합니다.
     - 주소 접두사는 AKS에서 연결 하려는 pod의 IP 범위 여야 합니다. 
     - 다음 홉 형식은 가상 어플라이언스 여야 합니다. 
     - 다음 홉 주소는 pod을 호스트 하는 노드의 IP 주소 여야 합니다.
  3. 이 경로 테이블을 Application Gateway 서브넷에 연결 합니다. 
    
  **v2 지원 되지 않는 시나리오**

  **시나리오 1**: 가상 어플라이언스에 대 한 udr

  가상 어플라이언스, 허브/스포크 가상 네트워크 또는 온-프레미스 (강제 터널링)를 통해 0.0.0.0/0을 리디렉션해야 하는 모든 시나리오는 s 2에서 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [프런트 엔드 IP 주소 구성에 대해 알아봅니다](configuration-front-end-ip.md).