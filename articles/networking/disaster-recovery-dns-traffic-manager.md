---
title: Azure DNS 및 Traffic Manager를 사용하여 재해 복구 | Microsoft Docs
description: Azure DNS 및 Traffic Manager를 사용한 재해 복구 솔루션의 개요
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: ce3e8f31c7fee6afdeabf931485a49934e98f81b
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391354"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Azure DNS 및 Traffic Manager를 사용하여 재해 복구

재해 복구는 애플리케이션 기능의 심각한 손실에서 복구하는 데 집중합니다. 재해 복구 솔루션을 선택하려면 비즈니스 및 기술 소유자는 먼저 사용 불가능, 축소된 기능이나 지연된 가용성을 통한 부분 사용 가능 또는 완전 사용 가능과 같이 재해 중에 필요한 기능 수준을 결정해야 합니다.
대부분의 기업 고객은 애플리케이션 또는 인프라 수준 장애 조치(failover)에 대한 복원력을 위해 다중 지역 아키텍처를 선택하고 있습니다. 고객은 중복 아키텍처를 통해 장애 조치(failover) 및 고가용성을 얻기 위해 여러 가지 방법을 선택할 수 있습니다. 다음은 몇 가지 인기 있는 방법입니다.

- **활성-수동(수동 대기)**: 이 장애 조치(failover) 솔루션에서는 대기 지역에서 실행되는 VM 및 기타 어플라이언스가 장애 조치(failover)를 수행해야 할 때까지 활성 상태가 아닙니다. 그러나 프로덕션 환경은 백업, VM 이미지 또는 Resource Manager 템플릿의 형식으로 다른 지역에 복제됩니다. 이 장애 조치(failover) 메커니즘은 비용 효율적이지만 완전한 장애 조치(failover)를 수행하는 데 시간이 더 오래 걸립니다.
 
    ![활성/수동(수동 대기)](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    ‘그림 - 활성/수동(수동 대기) 재해 복구 구성’

- **활성/수동(표시등)**: 이 장애 조치(failover) 솔루션에서는 대기 환경이 최소한의 구성으로 설정됩니다. 설정에는 최소한의 중요한 애플리케이션 집합만 지원하기 위해 실행되는 필요한 서비스만 포함됩니다. 네이티브 양식에서 이 시나리오는 최소 기능만을 실행할 수 있지만, 장애 조치(failover)가 발생하는 경우 대량의 프로덕션 부하를 수용하기 위해 추가 서비스를 확장하고 생성할 수 있습니다.
    
    ![활성/수동(표시등)](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *‘그림: 활성/수동(표시등) 재해 복구 구성’*

- **활성/수동(웜 대기)**: 이 장애 조치(failover)에서는 대기 지역이 미리 준비되어 기본 부하를 수용할 준비가 되고, 자동 크기 조정이 켜지고, 모든 인스턴스가 실행됩니다. 이 솔루션은 전체 프로덕션 부하를 수용하도록 크기가 조정되지만 작동하며, 모든 서비스가 실행됩니다. 이 솔루션은 표시등 방법의 확장 버전입니다.
    
    ![활성/수동(웜 대기)](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    ‘그림: 활성/수동(웜 대기) 재해 복구 구성’
    
장애 조치(failover) 및 고가용성에 대한 자세한 내용은 [Azure 애플리케이션에 대한 재해 복구](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)를 참조하세요.


## <a name="planning-your-disaster-recovery-architecture"></a>재해 복구 아키텍처 계획

재해 복구 아키텍처 설정에는 두 가지 기술적인 측면이 있습니다.
-  배포 메커니즘을 사용하여 주 및 대기 환경 간에 인스턴스, 데이터 및 구성 복제. 이 유형의 재해 복구는 Veritas 또는 NetApp과 같은 Microsoft Azure 파트너 어플라이언스/서비스를 통한 Azure Site Recovery를 통해 기본적으로 수행할 수 있습니다. 
- 주 사이트에서 대기 사이트로 네트워크/웹 트래픽을 전환하는 솔루션 개발. 이 유형의 재해 복구는 Azure DNS, Azure Traffic Manager(DNS) 또는 타사 전역 부하 분산 장치를 통해 수행할 수 있습니다.

이 문서는 네트워크 및 웹 트래픽 리디렉션을 통한 접근 방식으로 제한됩니다. Azure Site Recovery 설정에 대한 자세한 내용은 [Azure Site Recovery 설명서](https://docs.microsoft.com/azure/site-recovery/)를 참조하세요.
DNS는 일반적으로 전역으로 적용되고 데이터 센터 외부에 있으며 지역 또는 AZ(가용성 영역) 수준 장애로부터 격리되어 있으므로 네트워크 트래픽을 전환하는 데 가장 효율적인 메커니즘 중 하나입니다. DNS 기반 장애 조치(failover) 메커니즘을 사용할 수 있고, Azure에서는 두 개의 DNS 서비스인 Azure DNS(신뢰할 수 있는 DNS) 및 Azure Traffic Manager(DNS 기반 스마트 트래픽 라우팅)가 어떤 방식으로 동일한 작업을 수행할 수 있습니다. 

이 문서에 제공된 솔루션을 설명하는 데 광범위하게 사용되는 DNS의 몇 가지 개념을 이해해야 합니다.
- **DNS A 레코드** - A 레코드는 도메인을 IPv4 주소로 가리키는 포인터입니다. 
- **CNAME 또는 정식 이름** - 이 레코드 유형은 다른 DNS 레코드를 가리키는 데 사용됩니다. CNAME은 IP 주소로 응답하지 않고, IP 주소를 포함하는 레코드에 대한 포인터로 응답합니다. 
- **가중 라우팅** - 서비스 엔드포인트에 가중치를 연결한 다음, 할당된 가중치를 기준으로 트래픽을 분배하도록 선택할 수 있습니다. 이 라우팅 방법은 Traffic Manager 내에서 사용할 수 있는 네 개의 트래픽 라우팅 메커니즘 중 하나입니다. 자세한 내용은 [가중 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md#weighted)을 참조하세요.
- **우선 순위 라우팅** - 우선 순위 라우팅은 엔드포인트의 상태 확인을 기반으로 합니다. 기본적으로 Azure Traffic Manager는 우선순위가 가장 높은 엔드포인트에 모든 트래픽을 보내고, 장애 또는 재해 발생 시 Traffic Manager는 트래픽을 보조 엔드포인트로 라우팅합니다. 자세한 내용은 [우선 순위 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md#priority)을 참조하세요.

## <a name="manual-failover-using-azure-dns"></a>Azure DNS를 사용한 수동 장애 조치(failover)
재해 복구를 위한 Azure DNS 수동 장애 조치(failover) 솔루션은 표준 DNS 메커니즘을 사용하여 백업 사이트로 장애 조치(failover)합니다. Azure DNS를 통한 수동 옵션은 수동 대기 또는 표시등 방법과 함께 사용할 경우 가장 잘 작동합니다. 

![Azure DNS를 사용한 수동 장애 조치(failover)](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

‘그림 - Azure DNS를 사용한 수동 장애 조치(failover)’

솔루션에 대한 가정은 다음과 같습니다.
-   기본 및 보조 엔드포인트에는 자주 변경되지 않는 정적 IP가 있습니다. 주 사이트의 IP가 100.168.124.44이고 보조 사이트의 IP가 100.168.124.43이라고 가정해 보겠습니다.
-   Azure DNS 영역은 주 사이트와 보조 사이트에 모두 있습니다. 주 사이트의 엔드포인트는 prod.contoso.com이고 백업 사이트의 엔드포인트는 dr.contoso.com이라고 가정해 보겠습니다. www.contoso.com이라고 하는 기본 애플리케이션에 대한 DNS 레코드도 있습니다.   
-   TTL은 조직에서 설정된 RTO SLA 수준 이하입니다. 예를 들어, 기업에서 애플리케이션 재해 응답의 RTO를 60분으로 설정하는 경우, TTL은 60분 미만이어야 하고 낮을수록 더 좋습니다. 다음과 같이 수동 장애 조치(failover)에 대한 Azure DNS를 설정할 수 있습니다.
1. DNS 영역 만들기
2. DNS 영역 레코드 만들기
3. CNAME 레코드 업데이트

### <a name="step-1-create-a-dns"></a>1단계: DNS 만들기
다음과 같이 DNS 영역을 만듭니다(예: www.contoso.com).

![Azure에서 DNS 영역 만들기](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

‘그림 - Azure에서 DNS 영역 만들기’

### <a name="step-2-create-dns-zone-records"></a>2단계: DNS 영역 레코드 만들기

이 영역에서는 다음과 같이 세 개의 레코드를 만듭니다(예 - www.contoso.com, prod.contoso.com 및 dr.consoto.com).

![DNS 영역 레코드 만들기](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

‘그림 - Azure에서 DNS 영역 레코드 만들기’

이 시나리오에서 사이트 www.contoso.com은 명시된 RTO보다 훨씬 낮은 30분의 TTL을 포함하고 프로덕션 사이트 prod.contoso.com을 가리키고 있습니다. 이 구성은 정상적인 비즈니스 작업 중에 이루어집니다. prod.contoso.com 및 dr.contoso.com의 TTL은 300초 또는 5분으로 설정되었습니다. Azure Monitor 또는 Azure App Insights와 같은 Azure 모니터링 서비스 또는 Dynatrace와 같은 파트너 모니터링 솔루션을 사용할 수 있고, 애플리케이션 또는 가상 인프라 수준 장애를 모니터링하거나 감지할 수 있는 자사 솔루션도 사용할 수 있습니다.

### <a name="step-3-update-the-cname-record"></a>3단계: CNAME 레코드 업데이트

오류가 감지되면 다음과 같이 레코드 값을 dr.contoso.com으로 변경합니다.
       
![CNAME 레코드 업데이트](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

‘그림 - Azure에서 CNAME 레코드 업데이트’

대부분의 확인자가 캐시된 영역 파일을 새로 고치는 30분 내에 www.contoso.com에 대한 쿼리는 dr.contoso.com으로 리디렉션됩니다.
다음 Azure CLI 명령을 실행하여 CNAME 값을 변경할 수도 있습니다.
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
이 단계는 수동으로 또는 자동화를 통해 실행할 수 있습니다. 콘솔 또는 Azure CLI를 통해 수동으로 수행할 수 있습니다. Azure SDK 및 API를 사용하여 수동 개입이 필요하지 않도록 CNAME 업데이트를 자동화할 수 있습니다. 자동화는 Azure Functions를 통해, 타사 모니터링 애플리케이션 내에서 또는 온-프레미스에서도 빌드할 수 있습니다.

### <a name="how-manual-failover-works-using-azure-dns"></a>Azure DNS를 사용한 수동 장애 조치(failover)의 작동 방식
DNS 서버가 장애 조치(failover) 또는 재해 영역 외부에 있으므로 가동 중지 시간에서 격리됩니다. 이를 통해 사용자는 비용 효율적인 단순한 장애 조치(failover) 시나리오를 설계할 수 있고 이 기능은 운영자가 재해 중에 네트워크에 연결되어 있고 전환할 수 있다고 가정하여 항상 작동합니다. 솔루션이 스크립팅되는 경우에는 스크립트를 실행하는 서버 또는 서비스가 프로덕션 환경에 영향을 주는 문제에서 격리되어야 합니다. 전 세계의 확인자가 장기간 캐시된 엔드포인트를 유지하지 않고 고객이 RTO 내에서 사이트에 액세스할 수 있도록 영역에 대해 설정된 낮은 TTL에 주의하세요. 수동 대기 및 표시등의 경우, 일부 사전 준비 및 기타 관리 활동이 필요할 수 있으므로 전환하기 전에 충분한 시간을 제공해야 합니다.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Azure Traffic Manager를 사용한 자동 장애 조치(failover)
동일한 기능을 수행할 수 있는 복잡한 아키텍처 및 여러 리소스 집합이 있는 경우 리소스의 상태를 확인하고 비정상 리소스에서 정상 리소스로 트래픽을 라우팅하도록 Azure Traffic Manager(DNS 기반)를 구성할 수 있습니다. 다음 예제에서 주 지역 및 보조 지역에는 각각 전체 배포가 있습니다. 이 배포에는 클라우드 서비스와 동기화된 데이터베이스가 포함됩니다. 

![Azure Traffic Manager를 사용한 자동 장애 조치(failover)](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

‘그림 - Azure Traffic Manager를 사용한 자동 장애 조치(failover)’

그러나 주 지역만이 사용자의 네트워크 요청을 활발히 처리합니다. 보조 지역은 주 지역에서 서비스 중단이 발생하는 경우에만 활성화됩니다. 이 경우에 모든 새로운 네트워크 요청은 보조 지역으로 라우팅됩니다. 데이터베이스 백업은 거의 즉각적으로 이루어지고, 부하 분산 장치에는 상태를 확인할 수 있는 IP가 포함되고, 인스턴스는 항상 실행 중이므로, 이 토폴로지는 수동 개입 없이 낮은 RTO 및 장애 조치(failover)를 적용하는 옵션을 제공합니다. 보조 장애 조치(failover) 지역은 주 지역에 장애가 발생한 후 즉시 활성화할 준비가 되어야 합니다.
http/https 및 TCP를 포함하여 다양한 유형의 상태 확인을 위한 내장 프로브가 있는 Azure Traffic Manager를 사용하는 경우, 이 시나리오가 가장 적합합니다. Azure Traffic Manager에는 아래 설명된 대로 장애가 발생할 경우 장애 조치(failover)를 수행하도록 구성할 수 있는 규칙 엔진도 있습니다. Traffic Manager를 사용하는 다음 솔루션을 살펴보겠습니다.
- 고객에게는 정적 IP가 100.168.124.44인 지역 #1 엔드포인트 prod.contoso.com 및 정적 IP가 100.168.124.43인 지역 #2 엔드포인트 dr.contoso.com이 있습니다. 
-   이러한 각 환경은 부하 분산 장치 같은 공용 주소 속성을 통해 제어됩니다. 위에 표시된 대로 부하 분산 장치는 DNS 기반 엔드포인트 또는 FQDN(정규화된 도메인 이름)을 포함하도록 구성할 수 있습니다.
-   지역 2의 모든 인스턴스는 지역 1에서 실시간으로 복제됩니다. 또한 머신 이미지는 최신 상태이고 모든 소프트웨어/구성 데이터는 패치되고 지역 1과 일치합니다.  
-   자동 크기 조정은 미리 구성되어 있습니다. 

Azure Traffic Manager로 장애 조치(failover)를 구성하는 단계는 다음과 같습니다.
1. 새 Traffic Manager 프로필 만들기
2. Traffic Manager 프로필 내에서 엔드포인트 만들기
3. 상태 확인 및 장애 조치(failover) 구성 설정

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>1단계: 새 Azure Traffic Manager 프로필 만들기
이름이 contoso123인 새 Azure Traffic Manager 프로필을 만들고 라우팅 방법을 우선 순위로 선택합니다. 연결할 기존 리소스 그룹이 있는 경우 기존 리소스 그룹을 선택하고, 없는 경우에는 새 리소스 그룹을 만듭니다.

![Traffic Manager 프로필 만들기](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)
‘그림 - Traffic Manager 프로필 만들기’**

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>2단계: Traffic Manager 프로필 내에서 엔드포인트 만들기

이 단계에서는 프로덕션 및 재해 복구 사이트를 가리키는 엔드포인트를 만듭니다. 여기서 **유형**을 외부 엔드포인트로 선택하지만, 리소스가 Azure에서 호스트되는 경우 **Azure 엔드포인트**도 선택할 수 있습니다. **Azure 엔드포인트**를 선택한 경우, Azure에서 할당되는 **App Service** 또는 **공용 IP** 중에 **대상 리소스**를 선택합니다. 우선 순위는 지역 1의 주 서비스이므로 **1**로 설정됩니다.
마찬가지로 Traffic Manager 내에서도 재해 복구 엔드포인트를 만듭니다.

![재해 복구 엔드포인트 만들기](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

‘그림 - 재해 복구 엔드포인트 만들기’

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>3단계: 상태 확인 및 장애 조치(failover) 구성 설정

이 단계에서는 대부분의 인터넷 연결 재귀 확인자가 적용하는 10초로 DNS TTL을 설정합니다. 이 구성은 DNS 확인자가 10초 이상 정보를 캐시하지 않음을 의미합니다. 엔드포인트 모니터 설정의 경우 경로는 / 또는 루트의 현재 설정이지만, 경로를 평가하기 위해 엔드포인트 설정을 사용자 지정할 수 있습니다(예: prod.contoso.com/index). 아래 예제에서는 프로빙 프로토콜로서 **https**를 보여 줍니다. 그러나 **http** 또는 **tcp**를 선택할 수도 있습니다. 프로토콜 선택은 엔드 애플리케이션에 따라 달라집니다. 프로빙 간격은 빠른 프로빙을 가능하게 하는 10초로 설정되고 재시도는 3으로 설정됩니다. 결과적으로 3회 연속 간격으로 장애가 등록되는 경우 Traffic Manager는 두 번째 엔드포인트로 장애 조치(failover)됩니다. 다음 수식은 자동 장애 조치(failover)의 총 시간을 정의합니다. 장애 조치(failover) 시간 = TTL + 재시도 * 프로빙 간격. 이 경우 값은 10 + 3 * 10 = 40초(최대)입니다.
재시도가 1로 설정되고 TTL이 10초로 설정된 경우 장애 조치(failover) 시간은 10 + 1 * 10 = 20초입니다. 가양성 또는 사소하고 일시적인 네트워크 문제로 인한 장애 조치(failover)를 수행하지 않으려면 재시도를 **1**보다 큰 값으로 설정합니다. 


![상태 확인 설정](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

‘그림 - 상태 확인 및 장애 조치(failover) 구성 설정’

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Azure Traffic Manager를 사용한 자동 장애 조치(failover)의 작동 방식

재해가 발생하는 동안 기본 엔드포인트가 프로브되고 상태가 **성능 저하**로 변경되고 재해 복구 사이트는 **온라인** 상태로 유지됩니다. 기본적으로 Traffic Manager는 모든 트래픽을 기본(가장 높은 우선 순위) 엔드포인트로 전송합니다. 기본 엔드포인트의 성능이 저하된 것으로 보이면 Traffic Manager는 트래픽을 정상 상태인 두 번째 엔드포인트로 라우팅합니다. 추가적인 장애 조치(failover) 엔드포인트로 또는 엔드포인트 간에 부하를 공유하는 부하 분산 장치로 사용할 수 있는 추가적인 장애 조치(failover) 엔드포인트를 Traffic Manager 내에 구성하는 옵션이 제공됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)에 대해 자세히 알아보세요.
- [Azure DNS](../dns/dns-overview.md)에 대해 자세히 알아봅니다.









