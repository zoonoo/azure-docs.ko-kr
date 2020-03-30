---
title: SAP HA 시나리오에서 표준 ILB를&Azure VM에 대한 공용 엔드포인트 연결
description: SAP 고가용성 시나리오에서 Azure 표준 로드 밸런서를 사용하는 가상 시스템에 대한 공용 엔드포인트 연결
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293909"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>SAP 고가용성 시나리오에서 Azure 표준 로드 밸런서를 사용하는 가상 시스템에 대한 공용 엔드포인트 연결

이 문서의 범위는 공용 끝점에 대한 아웃바운드 연결을 가능하게 하는 구성을 설명하는 것입니다. 구성은 주로 SUSE / RHEL용 맥박 조정기와 고가용성의 맥락에서입니다.  

고가용성 솔루션에서 Azure 울타리 에이전트를 사용하여 Pacemaker를 사용하는 경우 VM은 Azure 관리 API에 대한 아웃바운드 연결이 있어야 합니다.  
이 문서에서는 시나리오에 가장 적합한 옵션을 선택할 수 있는 몇 가지 옵션을 제공합니다.  

## <a name="overview"></a>개요

클러스터링을 통해 SAP 솔루션에 대한 고가용성을 구현할 때 필요한 구성 요소 중 하나는 [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)입니다. Azure는 표준 및 기본 의 두 가지 로드 밸러커 SCO를 제공합니다.

표준 Azure 로드 밸러블러는 기본 로드 밸러커에 비해 몇 가지 이점을 제공합니다. 예를 들어 Azure 가용성 영역에서 작동하며 더 나은 모니터링 및 로깅 기능을 통해 문제 해결을 쉽게 하고 대기 시간을 줄입니다. "HA 포트" 기능은 모든 포트를 다룹니다.  

Azure 로드 밸러블러의 기본 SKU와 표준 SKU 간에는 몇 가지 중요한 차이점이 있습니다. 그 중 하나는 공용 끝점에 아웃 바운드 트래픽을 처리하는 것입니다. 전체 기본 및 표준 SKU 로드 밸레인저 비교에 대한 자세한 내용은 [로드 밸레인저 SKU 비교를](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)참조하십시오.  
 
공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 로드 밸런서의 백 엔드 풀에 배치되면 추가 구성이 수행되지 않는 한 공용 끝점에 대한 아웃바운드 연결이 없습니다.  

VM에 공용 IP 주소가 할당되거나 VM이 공용 IP 주소가 있는 로드 밸런서의 백 엔드 풀에 있는 경우 공용 끝점에 대한 아웃바운드 연결이 있습니다.  

SAP 시스템에는 중요한 비즈니스 데이터가 포함되어 있는 경우가 많습니다. SAP 시스템을 호스팅하는 VM이 공용 IP 주소를 가질 수 있는 경우는 거의 없습니다. 동시에 VM에서 공용 끝점에 대한 아웃바운드 연결이 필요한 시나리오가 있습니다.  

Azure 공용 끝점에 대한 액세스가 필요한 시나리오의 예는 다음과 같습니다.  
- 페이스 메이커 클러스터에서 펜싱 메커니즘으로 Azure 울타리 에이전트사용
- Azure Backup
- Azure Site Recovery  
- 운영 체제 패치를 위한 공용 리포지토리 사용
- SAP 애플리케이션 데이터 흐름에 공용 엔드포인트에 대한 아웃바운드 연결이 필요할 수 있습니다.

SAP 배포에서 공용 끝점에 대한 아웃바운드 연결이 필요하지 않은 경우 추가 구성을 구현할 필요가 없습니다. 공용 끝점에서 인바운드 연결이 필요하지 않다는 가정하에 고가용성 시나리오에 대한 내부 표준 SKU Azure 로드 밸런서를 만드는 것으로 충분합니다.  

> [!Note]
> 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 로드 밸런서의 백 엔드 풀에 배치되면 공용 끝점으로 라우팅을 허용하기 위한 추가 구성이 수행되지 않는 한 아웃바운드 인터넷 연결이 없습니다.  
>VM에 공용 IP 주소가 있거나 공용 IP 주소가 있는 Azure Load 밸런서의 백 엔드 풀에 이미 있는 경우 VM은 이미 공용 끝점에 대한 아웃바운드 연결을 갖습니다.


다음 논문을 먼저 읽는다.

* Azure 표준 로드 밸러저
  * [Azure 표준 로드 밸러저 개요](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) - Azure 표준 로드 밸러터, 중요한 원칙, 개념 및 자습서에 대한 포괄적인 개요 
  * [Azure의 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) - Azure에서 아웃바운드 연결을 달성하는 방법에 대한 시나리오
  * [로드 밸러블러 아웃바운드 규칙](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)- 로드 밸러워 아웃바운드 규칙의 개념과 아웃바운드 규칙을 만드는 방법에 대해 설명합니다.
* Azure Firewall
  * [Azure 방화벽 개요](https://docs.microsoft.com/azure/firewall/overview)- Azure 방화벽 개요
  * [자습서: Azure 방화벽 배포 및 구성](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) - Azure 포털을 통해 Azure 방화벽을 구성하는 방법에 대한 지침
* [가상 네트워크 -사용자 정의 규칙](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) - Azure 라우팅 개념 및 규칙  
* [보안 그룹 서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) - 서비스 태그를 사용하여 네트워크 보안 그룹 및 방화벽 구성을 단순화하는 방법

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>인터넷에 대한 아웃바운드 연결을 위한 추가 외부 Azure 표준 로드 밸러저

공용 끝점에서 VM에 대한 인바운드 연결을 허용하지 않고 공용 끝점에 대한 아웃바운드 연결을 달성하는 한 가지 옵션은 공용 IP 주소가 있는 두 번째 로드 밸런서를 만들고, 두 번째 로드 밸런서의 백 엔드 풀에 VM을 추가하고 [아웃바운드 규칙만](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)정의하는 것입니다.  
[네트워크 보안 그룹을](https://docs.microsoft.com/azure/virtual-network/security-overview) 사용하여 VM에서 아웃바운드 호출에 액세스할 수 있는 공용 끝점을 제어합니다.  
자세한 내용은 문서 [아웃바운드 연결의](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)시나리오 2를 참조하십시오.  
구성은 다음과 같습니다.  

![네트워크 보안 그룹으로 공용 엔드포인트에 대한 연결 제어](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>중요 고려 사항

- 동일한 서브넷에서 여러 VM에 대해 하나의 추가 공용 로드 밸런서를 사용하여 공용 엔드포인트에 대한 아웃바운드 연결을 달성하고 비용을 최적화할 수 있습니다.  
- [네트워크 보안 그룹을](https://docs.microsoft.com/azure/virtual-network/security-overview) 사용하여 VM에서 액세스할 수 있는 공용 끝점을 제어합니다. 네트워크 보안 그룹을 서브넷 또는 각 VM에 할당할 수 있습니다. 가능한 경우 [서비스 태그를](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 사용하여 보안 규칙의 복잡성을 줄입니다.  
- 공용 IP 주소 및 아웃바운드 규칙이 있는 Azure 표준 로드 밸런서를 사용하면 공용 끝점에 직접 액세스할 수 있습니다. 감사 및 로깅을 위해 중앙 집중식 회사 솔루션을 통해 모든 아웃바운드 트래픽이 전달되어야 하는 회사 보안 요구 사항이 있는 경우 이 시나리오에서 요구 사항을 충족하지 못할 수 있습니다.  

>[!TIP]
>가능한 경우 [서비스 태그를](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 사용하여 네트워크 보안 그룹의 복잡성을 줄입니다. 

### <a name="deployment-steps"></a>배포 단계

1. 부하 분산 장치 만들기  
   1. Azure [포털에서](https://portal.azure.com) 모든 리소스를 클릭하고 추가한 다음 **로드 밸러서를** 검색합니다.  
   1. **만들기를 클릭합니다.** 
   1. 로드 밸런서 이름 **myPublicILB**  
   1. **공용을** 유형으로 선택, **표준으로** SKU로 선택  
   1. **공용 IP 주소 만들기를** 선택하고 **MyPublicILBFrondEndIP** 이름으로 지정합니다.  
   1. 사용 가능 **영역으로 영역 중복** 을 선택합니다.  
   1. 검토 및 만들기를 클릭한 다음 만들기를 클릭합니다.  
2. 백 엔드 **풀을 만들고 PublicILB를** 추가합니다.  
   1. 가상 네트워크 선택  
   1. VM 및 해당 IP 주소를 선택하고 백 엔드 풀에 추가  
3. [아웃바운드 규칙을 만듭니다.](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule) 현재 Azure 포털에서 아웃바운드 규칙을 만들 수 없습니다. [Azure CLI를](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)사용하여 아웃바운드 규칙을 만들 수 있습니다.  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 네트워크 보안 그룹 규칙을 만들어 특정 공용 끝점에 대한 액세스를 제한합니다. 기존 네트워크 보안 그룹이 있는 경우 조정할 수 있습니다. 아래 예제에서는 Azure 관리 API에 대한 액세스를 활성화하는 방법을 보여 주었습니다. 
   1. 네트워크 보안 그룹으로 이동
   1. 아웃바운드 보안 규칙을 클릭합니다.
   1. **인터넷에**대한 모든 아웃바운드 액세스를 **거부하는** 규칙을 추가합니다.
   1. 모든 인터넷 액세스를 거부하는 규칙의 우선 순위보다 우선 순위가 낮은 AzureCloud에 대한 액세스를 **허용하는** 규칙을 **추가합니다.**


   아웃바운드 보안 규칙은 다음과 같습니다. 

   ![공용 IP를 갖춘 두 번째 로드 밸런서와의 아웃바운드 연결](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Azure 네트워크 보안 그룹에 대한 자세한 내용은 [보안 그룹을 ](https://docs.microsoft.com/azure/virtual-network/security-overview)참조하십시오. 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>인터넷에 아웃바운드 연결을 위한 Azure 방화벽

공용 끝점에서 VM에 대한 인바운드 연결을 허용하지 않고 공용 끝점에 대한 아웃바운드 연결을 달성하는 또 다른 옵션은 Azure 방화벽입니다. Azure 방화벽은 고가용성이 내장된 관리형 서비스이며 여러 가용성 영역에 걸쳐 있을 수 있습니다.  
또한 Azure 방화벽을 통해 트래픽을 라우팅하기 위해 VM 및 Azure 로드 밸러버가 배포되는 서브넷과 연결된 [사용자 정의 경로(사용자 정의 경로)를](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)배포해야 합니다.  
Azure 방화벽을 배포하는 방법에 대한 자세한 내용은 [Azure 방화벽 배포 및 구성](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)을 참조하십시오.  

아키텍처는 다음과 같습니다.

![Azure 방화벽을 사용하는 아웃바운드 연결](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>중요 고려 사항

- Azure 방화벽은 고가용성이 내장된 클라우드 네이티브 서비스이며 지역 배포를 지원합니다.
- AzureFirewallSubnet이라는 이름의 추가 서브넷이 필요합니다. 
- SAP VM이 있는 가상 네트워크의 아웃바운드, 다른 가상 네트워크의 VM 또는 공용 엔드포인트로 큰 데이터 세트를 전송하는 경우 비용 효율적인 솔루션이 아닐 수 있습니다. 이러한 예 중 하나는 가상 네트워크에서 대규모 백업을 복사하는 것입니다. 자세한 내용은 Azure 방화벽 가격을 참조하십시오.  
- 회사 방화벽 솔루션이 Azure 방화벽이 아니고 중앙 집중식 회사 솔루션에도 불구하고 모든 아웃바운드 트래픽이 전달되는 보안 요구 사항이 있는 경우 이 솔루션은 실용적이지 않을 수 있습니다.  

>[!TIP]
>가능한 경우 [서비스 태그를](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 사용하여 Azure 방화벽 규칙의 복잡성을 줄입니다.  

### <a name="deployment-steps"></a>배포 단계

1. 배포 단계에서는 VM에 대해 정의된 가상 네트워크 및 서브넷이 이미 있다고 가정합니다.  
2. VMS 및 표준 로드 밸러버가 배포되는 동일한 가상 네트워크에서 서브넷 **AzureFirewallSubnet을** 만듭니다.  
   1. Azure 포털에서 가상 네트워크로 이동: 모든 리소스를 클릭하고, 가상 네트워크를 검색하고, 가상 네트워크를 클릭하고, 서브넷을 선택합니다.  
   1. 서브넷 추가를 클릭합니다. **Azure방화벽서브넷을 이름으로 입력합니다.** 적절한 주소 범위를 입력합니다. 저장합니다.  
3. Azure 방화벽을 만듭니다.  
   1. Azure 포털에서 모든 리소스를 선택, 추가, 방화벽, 만들기를 클릭합니다. 리소스 그룹을 선택합니다(가상 네트워크가 있는 동일한 리소스 그룹을 선택합니다).  
   1. Azure 방화벽 리소스의 이름을 입력합니다. 예를 들어, **MyAzure방화벽**.  
   1. 지역을 선택하고 VM이 배포되는 가용성 영역에 맞춰 둘 이상의 가용성 영역을 선택합니다.  
   1. SAP VM 및 Azure 표준 로드 밸러버가 배포되는 가상 네트워크를 선택합니다.  
   1. 공용 IP 주소: 이름 만들기를 클릭하고 입력합니다. 예를 들어 **My방화벽퍼무는**.  
4. 지정된 공용 끝점에 아웃바운드 연결을 허용하도록 Azure 방화벽 규칙을 만듭니다. 이 예제에서는 Azure Management API 공용 끝점에 대한 액세스를 허용하는 방법을 보여 주었습니다.  
   1. 규칙, 네트워크 규칙 컬렉션을 선택한 다음 네트워크 규칙 컬렉션 추가를 클릭합니다.  
   1. 이름: **myOutboundRule,** 우선 순위를 입력, 선택 작업 **허용**.  
   1. 서비스: 이름 **ToAzureAPI**.  프로토콜: **임의의**선택 . 원본 주소: VM 및 표준 로드 밸러서가 배포되는 서브넷의 범위를 입력합니다: **11.97.0.0/24.** 대상 포트: <b>*</b>enter.  
   1. 저장
   1. Azure 방화벽에 계속 위치하는 동안 개요를 선택합니다. Azure 방화벽의 개인 IP 주소를 기록합니다.  
5. Azure 방화벽에 대한 경로 만들기  
   1. Azure 포털에서 모든 리소스를 선택한 다음 추가, 경로 테이블, 만들기를 클릭합니다.  
   1. MyRouteTable 이름을 입력하고 구독, 리소스 그룹 및 위치(가상 네트워크 및 방화벽의 위치와 일치)를 선택합니다.  
   1. 저장  

   방화벽 규칙은 다음과 같습니다: ![Azure 방화벽을 사용하는 아웃바운드 연결](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. VM의 서브넷에서 **MyAzureFirewall의**개인 IP로 사용자 정의 경로를 만듭니다.
   1. 경로 테이블에 위치하는 동안 경로를 클릭합니다. 추가를 선택합니다. 
   1. 경로 이름: ToMyAzureFirewall, 주소 접두사: **0.0.0.0/0**. 다음 홉 유형: 가상 어플라이언스를 선택합니다. 다음 홉 주소: 구성 한 방화벽의 개인 IP 주소를 입력: **11.97.1.4**.  
   1. 저장

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Azure 관리 API에 대한 Pacemaker 호출에 프록시 사용

프록시를 사용하여 Pacemaker가 Azure 관리 API 공용 끝점에 대한 호출을 허용할 수 있습니다.  

### <a name="important-considerations"></a>중요 고려 사항

  - 이미 회사 프록시가 있는 경우 아웃바운드 호출을 공용 끝점으로 라우팅할 수 있습니다. 공용 엔드 포인트에 대한 아웃바운드 호출은 기업 제어 지점을 통과합니다.  
  - 프록시 구성에서 Azure 관리 API에 대한 아웃바운드 연결을 허용하는지 확인합니다.`https://management.azure.com`  
  - VM에서 프록시로 가는 경로가 있는지 확인합니다.  
  - 프록시는 HTTP/HTTPS 호출만 처리합니다. RFC와 같은 다른 프로토콜을 통해 공용 엔드포인트로 아웃바운드 호출을 수행해야 하는 경우 대체 솔루션이 필요합니다.  
  - 프록시 솔루션은 Pacemaker 클러스터의 불안정을 방지할 수 있어야 합니다.  
  - 프록시 위치에 따라 Azure Fence 에이전트에서 Azure 관리 API로의 호출에 추가 대기 시간이 발생할 수 있습니다. 회사 프록시가 여전히 구내에 있는 경우 Pacemaker 클러스터가 Azure에 있는 동안 대기 시간을 측정하고 이 솔루션이 적합한지 고려하십시오.  
  - 이미 고가용성 기업 프록시가 없는 경우 고객이 추가 비용과 복잡성을 발생시킬 수 있으므로 이 옵션을 사용하지 않는 것이 좋습니다. 그럼에도 불구하고 추가 프록시 솔루션을 배포하기로 결정한 경우 Pacemaker에서 Azure Management 공용 API로아웃바운드 연결을 허용하기 위해 프록시를 가용성이 높고 VM에서 프록시까지의 대기 시간이 짧은지 확인합니다.  

### <a name="pacemaker-configuration-with-proxy"></a>프록시를 갖춘 맥박 조정기 구성 

업계에서 사용할 수 있는 여러 프록시 옵션이 있습니다. 프록시 배포에 대한 단계별 지침은 이 문서의 범위를 벗어납니다. 아래 예제에서는 프록시가 **MyProxyService에** 응답하고 **MyProxyPort**를 수신 대기하고 있다고 가정합니다.  
페이스메이커가 Azure 관리 API와 통신할 수 있도록 하려면 모든 클러스터 노드에서 다음 단계를 수행합니다.  

1. 심박동기 구성 파일/etc/sysconfig/심박동기를 편집하고 다음 줄(모든 클러스터 노드)을 추가합니다.

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. **모든** 클러스터 노드에서 맥박 조정기 서비스를 다시 시작합니다.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>다음 단계

* [Azure에서 SUSE에서 맥박 조정기를 구성하는 방법에 대해 알아봅니다.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Azure에서 Red Hat에서 심박동기를 구성하는 방법에 대해 알아보십시오.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
