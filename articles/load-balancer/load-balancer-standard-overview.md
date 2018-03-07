---
title: "Azure Load Balancer 표준 개요 | Microsoft Docs"
description: "Azure Load Balancer 표준 기능 개요"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2018
ms.author: kumud
ms.openlocfilehash: 8f98fd7773acb960ae79e743663aceb216f217c4
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Azure Load Balancer 표준 개요(미리 보기)

Azure Load Balancer 표준 SKU 및 공용 IP 표준 SKU를 함께 사용하여 확장성이 매우 크고 신뢰할 수 있는 아키텍처를 만들 수 있습니다. Load Balancer 표준을 사용하는 응용 프로그램은 새로운 기능을 활용할 수 있습니다. 모든 TCP 및 UDP 응용 프로그램의 수백만 흐름에 대해 짧은 대기 시간, 높은 처리량 및 규모가 제공됩니다.

>[!NOTE]
> Load Balancer 표준 SKU는 현재 미리 보기 상태입니다. 미리 보기 중 이 기능은 일반 공급 릴리스에 있는 기능과 동일한 수준의 가용성 및 안정성을 제공하지 못할 수도 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 Microsoft Azure 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 프로덕션 서비스의 경우 일반 공급 [Load Balancer 기본 SKU](load-balancer-overview.md)를 사용합니다. 이 미리 보기와 함께 [가용성 영역 미리 보기](https://aka.ms/availabilityzones)를 사용하려면 Load Balancer [표준 미리 보기](#preview-sign-up) 등록뿐 아니라 [별도의 등록](https://aka.ms/availabilityzones)이 필요합니다.

## <a name="why-use-load-balancer-standard"></a>Load Balancer 표준을 사용하는 이유

Load Balancer 표준은 다양한 가상 데이터 센터에 사용할 수 있습니다. 소규모 배포에서부터 크고 복잡한 다중 영역 아키텍처까지 Load Balancer 표준을 사용하여 다음 기능을 활용합니다.

- Load Balancer 표준을 통해 [엔터프라이즈급](#enterprisescale)을 구현할 수 있습니다. 이 기능은 가상 네트워크 내에서 최대 1000개의 VM 인스턴스로 모든 가상 머신 인스턴스에 사용할 수 있습니다.

- [새로운 진단 정보](#diagnosticinsights)를 사용하여 가상 데이터 센터의 이 중요한 구성 요소를 이해하고 관리하고 문제를 해결할 수 있습니다. Azure Monitor(미리 보기)를 사용하여 연속 데이터 경로 상태 측정을 위한 새로운 다차원 메트릭을 표시하고 필터링하고 그룹화할 수 있습니다. 프런트 엔드, VM, 끝점 상태 프로브, TCP 연결 시도, 아웃바운드 연결 등에 대한 데이터를 모니터링할 수 있습니다.

- 이제 Load Balancer 표준 SKU 또는 공용 IP 표준 SKU와 연결된 모든 VM 인스턴스에 대해 [네트워크 보안 그룹](#nsg)이 필요합니다. NSG(네트워크 보안 그룹)는 시나리오에 따라 향상된 보안을 제공합니다.

- NVA(네트워크 가상 어플라이언스) 및 기타 응용 프로그램 시나리오에 대해 [HA(고가용성) 포트는 높은 신뢰성](#highreliability) 및 규모를 제공합니다. HA 포트는 VM 인스턴스의 풀에 Azure ILB(Internal Load Balancer) 프런트 엔드의 모든 포트에 대한 부하를 분산합니다.

- [아웃바운드 연결](#outboundconnections)은 이제 큰 복원력 및 규모를 제공하는 새로운 SNAT(Source Network Address Translation) 포트 할당 모델을 사용합니다.

- [가용성 영역이 있는 Load Balancer 표준](#availabilityzones)은 영역 중복 및 영역 아키텍처를 만드는 데 사용할 수 있습니다. 이러한 아키텍처는 모두 영역 간 부하 분산을 포함할 수 있습니다. DNS 레코드에 대한 종속성 없이도 영역 중복을 얻을 수 있습니다. 단일 IP 주소는 기본적으로 영역 중복입니다.  단일 IP 주소는 모든 가용성 영역을 포함하는 지역 내에서 가상 네트워크의 모든 VM에 연결할 수 있습니다.


Load Balancer 표준을 공용 또는 내부 구성에서 사용하여 다음과 같은 기본 시나리오를 지원할 수 있습니다.

- 정상 백 엔드 인스턴스에 인바운드 트래픽을 부하 분산합니다.
- 단일 백 엔드 인스턴스에 인바운드 트래픽을 포트 전달합니다.
- 가상 네트워크 내의 개인 IP 주소의 아웃바운드 트래픽을 공용 IP 주소로 변환합니다.

### <a name = "enterprisescale"></a>엔터프라이즈급 규모

 Load Balancer 표준을 사용하여 고성능 가상 데이터 센터를 설계하고 모든 TCP 또는 UDP 응용 프로그램을 지원할 수 있습니다. 독립 실행형 VM 인스턴스를 사용하거나 백 엔드 풀에서 최대 1,000개의 가상 머신 확장 집합 인스턴스를 사용할 수 있습니다. 완전히 관리되는 Azure 서비스에서 낮은 전달 대기 시간, 높은 처리량 성능 및 수백만의 흐름에 대한 규모를 계속 사용할 수 있습니다.
 
Load Balancer 표준은 지역의 가상 네트워크에서 모든 VM 인스턴스에 트래픽을 전달할 수 있습니다. 백 엔드 풀 크기는 다음 VM 시나리오 조합으로 최대 1,000개의 인스턴스가 될 수 있습니다.

- 가용성 집합이 없는 독립 실행형 VM
- 가용성 집합이 있는 독립 실행형 VM
- 가상 머신 크기 확장 집합, 최대 1,000개 인스턴스
- 여러 가상 머신 확장 집합
- VM 및 가상 머신 확장 집합의 혼합

가용성 집합에 대한 요구 사항은 더 이상 없습니다. 가용성 집합을 사용하면 다른 이점도 얻을 수 있습니다.

### <a name = "diagnosticinsights"></a>진단 정보

Load Balancer 표준은 공용 및 내부 Load Balancer 구성에 대해 새로운 다차원 진단 기능을 제공합니다. 이러한 새 메트릭은 Azure Monitor(미리 보기)를 통해 제공되며 다운스트림 소비자와 통합할 수 있는 기능을 포함하여 관련된 모든 기능을 활용합니다.

| 메트릭 | 설명 |
| --- | --- |
| VIP 가용성 | Load Balancer 표준은 지역 내에서 Load Balancer 프런트 엔드로, 마지막으로 VM을 지원하는 SDN 스택으로 데이터 경로를 연속적으로 실행합니다. 정상 인스턴스가 남아 있는 한 측정은 응용 프로그램 부하가 분산된 트래픽과 동일한 경로를 따르고 고객이 사용하는 데이터 경로의 유효성도 검사합니다. 측정은 응용 프로그램에 표시되지 않으며 다른 작업을 방해하지 않습니다.|
| DIP 가용성 | Load Balancer 표준은 구성 설정에 따라 응용 프로그램 끝점의 상태를 모니터링하는 분산된 상태 검색 서비스를 사용합니다. 이 메트릭은 Load Balancer 풀에서 각 개별 인스턴스 끝점의 집계 또는 끝점당 필터링된 보기를 제공합니다.  상태 프로브 구성에 표시된 대로 Load Balancer에서 응용 프로그램의 상태를 보는 방법을 확인할 수 있습니다.
| SYN 패킷 | Load Balancer 표준은 TCP 연결을 종료하거나 TCP 또는 UDP 패킷 흐름을 조작하지 않습니다. 흐름 및 해당 핸드셰이크는 항상 원본과 VM 인스턴스 사이에 있습니다. TCP 프로토콜 시나리오의 문제를 잘 해결하기 위해 SYN 패킷 카운터를 사용하여 TCP 연결 시도 횟수를 파악할 수 있습니다. 메트릭은 수신된 TCP SYN 패킷 수를 보고합니다.|
| SNAT 연결 | Load Balancer 표준은 공용 IP 주소 프런트 엔드로 위장되는 아웃바운드 흐름 수를 보고합니다. SNAT 포트는 소모성 리소스입니다. 이 메트릭은 응용 프로그램이 아웃바운드에서 시작된 흐름에 대해 SNAT에 얼마나 의존하는지를 나타낼 수 있습니다.  성공 및 실패한 아웃바운드 SNAT 흐름에 대한 카운터가 보고되고 아웃바운드 흐름의 상태를 이해하고 문제를 해결하는 데 사용할 수 있습니다.|
| 바이트 카운터 | Load Balancer 표준은 프런트 엔드당 처리된 데이터를 보고합니다.|
| 패킷 카운터 | Load Balancer 표준은 프런트 엔드당 처리된 패킷을 보고합니다.|

### <a name = "highreliability"></a>높은 안정성

부하 분산 규칙을 구성하여 응용 프로그램 규모를 매우 안정적으로 만듭니다. 개별 포트에 대한 규칙을 구성하거나 HA 포트를 사용하여 TCP 또는 UDP 포트 번호에 관계없이 모든 트래픽을 부하 분산할 수 있습니다.  

새 HA 포트 기능을 사용하여 내부 NVA에 대한 고가용성 및 규모를 비롯한 다양한 시나리오를 활용할 수 있습니다. 이 기능은 개별 포트 지정이 불가능하거나 바람직하지 않은 다른 시나리오에 유용합니다. HA 포트는 필요한 만큼 많은 인스턴스를 허용하여 중복 및 규모를 제공합니다. 더 이상 활성/수동 시나리오로만 구성할 필요가 없습니다. 상태 프로브 구성은 정상 인스턴스에만 트래픽을 전달하여 서비스를 보호합니다.

NVA 공급업체는 고객에게 공급업체에서 완벽히 지원하는 탄력적인 시나리오를 제공할 수 있습니다. 단일 실패 지점이 제거되고 규모에 대해 여러 활성 인스턴스가 지원됩니다. 어플라이언스의 기능에 따라 둘 이상의 인스턴스로 확장할 수 있습니다. 이러한 시나리오에 대한 추가 지침은 NVA 공급업체에 문의하세요.

### <a name = "availabilityzones"></a>가용성 영역

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

지원되는 지역에서 가용성 영역을 사용하여 응용 프로그램의 복구를 진행합니다. 가용성 영역은 현재 특정 지역에서 미리 보기 상태이며 추가 옵트인이 필요합니다.

### <a name="automatic-zone-redundancy"></a>자동 영역 중복

Load Balancer에서 각 응용 프로그램에 대해 영역 중복 또는 영역 프런트 엔드를 제공해야 하는지 여부를 선택할 수 있습니다. Load Balancer 표준을 사용하여 영역 중복을 만들기 쉽습니다. 단일 프런트 엔드 IP 주소는 자동으로 영역 중복입니다. 영역 중복 프런트 엔드는 지역의 모든 가용성 영역에서 동시에 서비스됩니다. 인바운드 및 아웃바운드 연결에 대한 영역 중복 데이터 경로가 만들어집니다. Azure에서 영역 중복은 여러 IP 주소 및 DNS 레코드가 필요하지 않습니다. 

영역 중복은 공용 또는 내부 프런트 엔드에 사용할 수 있습니다. 내부 Load Balancer의 공용 IP 주소 및 프런트 엔드 개인 IP는 영역 중복될 수 있습니다.

다음 스크립트를 사용하여 내부 Load Balancer의 영역 중복 공용 IP 주소를 만듭니다. 구성에서 기존 Resource Manager 템플릿을 사용하는 경우 이러한 템플릿에 **sku** 섹션을 추가합니다.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

다음 스크립트를 사용하여 내부 Load Balancer의 영영 중복 프런트 엔드 IP를 만듭니다. 구성에서 기존 Resource Manager 템플릿을 사용하는 경우 이러한 템플릿에 **sku** 섹션을 추가합니다.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

공용 IP 프런트 엔드가 영역 중복인 경우 VM 인스턴스에서 만들어진 아웃바운드 연결은 자동으로 영역 중복이 됩니다. 프런트 엔드는 영역 장애로부터 보호됩니다. SNAT 포트 할당은 영역 실패 후에도 유지됩니다.

#### <a name="cross-zone-load-balancing"></a>영역 간 부하 분산

영역 간 부하 분산은 백 엔드 풀에 대한 지역 내에서 사용할 수 있으며 VM 인스턴스에 대해 최대 유연성을 제공합니다. 프런트 엔드는 VM 인스턴스의 가용성 영역에 관계없이 가상 네트워크 내의 모든 VM에 흐름을 제공합니다.

또한 프런트 엔드 및 백 엔드 인스턴스에 대한 특정 영역을 지정하여 특정 영역으로 데이터 경로 및 리소스를 맞출 수도 있습니다.

가상 네트워크 및 서브넷은 영역의 제약을 받지 않습니다. 원하는 VM 인스턴스로 백 엔드 풀을 정의하면 구성이 완료됩니다.

#### <a name="zonal-deployments"></a>영역 배포

필요에 따라 영역 프런트 엔드를 정의하여 특정 영역에 부하 분산 장치 프런트 엔드를 맞출 수 있습니다. 영역 프런트 엔드는 지정된 단일 가용성 영역에서만 서비스됩니다. 프런트 엔드를 영역 VM 인스턴스와 결합하면 리소스를 특정 영역에 맞출 수 있습니다.

특정 영역에 생성된 공용 IP 주소는 항상 해당 영역에만 존재합니다. 공용 IP 주소의 영역을 변경하는 것은 불가능합니다. 여러 영역의 리소스에 연결할 수 있는 공용 IP 주소가 필요한 경우 대신 영역 중복 공용 IP를 만듭니다.

다음 스크립트를 사용하여 가용성 영역 1에서 영역 공용 IP 주소를 만듭니다. 구성에서 기존 Resource Manager 템플릿을 사용하는 경우 이러한 템플릿에 **sku** 섹션을 추가합니다.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

다음 스크립트를 사용하여 가용성 영역 1에 내부 Load Balancer 프런트 엔드를 만듭니다.

구성에서 기존 Resource Manager 템플릿을 사용하는 경우 이러한 템플릿에 **sku** 섹션을 추가합니다. 또한 하위 리소스에 대한 프런트 엔드 IP 구성에서 **zones** 특성을 정의합니다.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

가상 네트워크에 있는 VM 인스턴스를 풀에 넣어 백 엔드 풀에 대한 영역 간 부하 분산을 추가합니다.

Load Balancer 표준 리소스는 항상 가용성 영역이 지원되는 지역 및 영역 중복입니다. 할당된 영역이 없는 공용 IP 주소 또는 Load Balancer 프런트 엔드를 원하는 지역에 배포할 수 있습니다. 가용성 영역에 대한 지원이 배포 기능에 영향을 주지는 않습니다. 나중에 지역이 가용성 영역을 얻는 경우 이미 배포된 공용 IP 또는 내부 Load Balancer 프런트 엔드는 자동으로 영역 중복이 됩니다. 영역 중복 데이터 경로는 0% 패킷 손실을 의미하지 않습니다.

### <a name = "nsg"></a>네트워크 보안 그룹

Load Balancer 표준 및 공용 IP 표준은 가상 네트워크에 완전히 온보드되어 NSG(네트워크 보안 그룹)를 사용해야 합니다. NSG를 사용하면 트래픽 흐름을 허용 목록에 추가할 수 있습니다. NSG를 사용하면 배포의 트래픽에 대한 모든 권한을 얻을 수 있습니다. 더 이상 다른 트래픽 흐름이 완료될 때까지 기다릴 필요가 없습니다.

백 엔드 풀에서 VM 인스턴스의 서브넷 또는 NIC(네트워크 인터페이스)와 NSG를 연결합니다. Load Balancer 표준 및 공용 IP 표준(인스턴스 수준 공용 IP로 사용되는 경우)에서 이 구성을 사용합니다. NSG에서는 허용할 트래픽을 명시적으로 허용 목록에 추가해야만 해당 트래픽이 흐릅니다.

NSG에 대한 개요와 NSG를 시나리오에 적용하는 방법을 자세히 알아보려면 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)을 참조하세요.

### <a name ="outboundconnections"></a>아웃바운드 연결

Load Balancer 표준은 부하 분산 장치가 포트 위장 SNAT을 사용할 경우 가상 네트워크 내에 있는 VM에 대한 아웃바운드 연결을 제공합니다. 포트 위장 SNAT 알고리즘은 향상된 견고성 및 규모를 제공합니다.

공용 Load Balancer 리소스가 VM 인스턴스와 연결된 경우 각 아웃바운드 연결 원본이 다시 작성됩니다. 원본은 가상 네트워크 개인 IP 주소 공간에서 부하 분산 장치의 프런트 엔드 공용 IP 주소로 다시 작성됩니다.

아웃바운드 연결이 영역 중복 프런트 엔드로 사용되는 경우에는 이 연결도 영역 중복이며 SNAT 포트 할당은 영역 실패 후에도 유지됩니다.

Load Balancer 표준의 새 알고리즘은 각 VM의 NIC에 SNAT 포트를 미리 할당합니다. 풀에 NIC가 추가되면 풀 크기를 기준으로 SNAT 포트가 미리 할당됩니다. 다음 표에서는 6개 계층의 백 엔드 풀 크기에 대한 포트 미리 할당을 보여 줍니다.

| 풀 크기(VM 인스턴스) | 미리 할당된 SNAT 포트 수 |
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

SNAT 포트는 아웃바운드 연결 수로 직접 변환하지 않습니다. 여러 고유한 대상에 SNAT 포트를 재사용할 수 있습니다. 자세한 내용은 [아웃 바운드 연결](load-balancer-outbound-connections.md) 문서를 참조하세요.

백 엔드 풀 크기가 늘고 더 높은 계층으로 전환되면 할당된 포트의 절반이 회수됩니다. 회수된 포트와 연결된 연결이 시간 초과되며 다시 설정해야 합니다. 새 연결 시도는 즉시 성공합니다. 백 엔드 풀 크기가 줄고 더 낮은 계층으로 전환되면 사용 가능한 SNAT 포트 수가 증가합니다. 이 경우 기존 연결은 영향을 받지 않습니다.

Load Balancer 표준에는 규칙별로 사용할 수 있는 추가 구성 옵션이 있습니다. 여러 프런트 엔드를 사용할 수 있는 경우 포트 위장 SNAT에 사용되는 프런트 엔드를 제어할 수 있습니다.

Load Balancer 표준만 VM 인스턴스를 제공하는 경우 아웃바운드 SNAT 연결을 사용할 수 없습니다. VM 인스턴스를 공용 부하 분산 장치에도 할당하여 이 기능을 명시적으로 복원할 수 있습니다. 공용 IP를 인스턴스 수준 공용 IP로 각 VM 인스턴스에 직접 할당할 수도 있습니다. 일부 운영 체제와 응용 프로그램 시나리오에 이 구성 옵션이 필요할 수 있습니다. 

### <a name="port-forwarding"></a>포트 전달

기본 및 표준 Load Balancer는 인바운드 NAT 규칙을 구성하는 기능을 제공하여 프런트 엔드 포트를 개별 백 엔드 인스턴스에 매핑합니다. 이러한 규칙을 구성하면 원격 데스크톱 프로토콜 끝점과 SSH 끝점을 노출하거나 다른 응용 프로그램 시나리오를 수행할 수 있습니다.

Load Balancer 표준은 인바운드 NAT 규칙을 통해 포트 전달 기능을 계속해서 제공합니다. 영역 중복 프런트 엔드로 사용되는 경우 인바운드 NAT 규칙은 영역 중복이 되고 영역 실패 후에도 유지됩니다.

### <a name="multiple-front-ends"></a>여러 프런트 엔드

응용 프로그램에 노출될 여러 개별 IP 주소가 필요한 경우 설계 유연성을 위해 여러 프런트 엔드를 구성합니다(예: TLS 웹 사이트 또는 SQL AlwaysOn 가용성 그룹 끝점). 

Load Balancer 표준은 고유 IP 주소에서 특정 응용 프로그램 끝점을 노출해야 하는 경우 여러 프런트 엔드를 계속해서 제공합니다.

여러 프런트 엔드 IP를 구성하는 방법에 대한 자세한 내용은 [다중 IP 구성](load-balancer-multivip-overview.md)을 참조하세요.

## <a name = "sku"></a>SKU 정보

SKU는 Azure Resource Manager 배포 모델에서만 사용할 수 있습니다. 이 미리 보기는 Load Balancer 및 공용 IP 리소스에 대해 두 개의 SKU(기본 및 표준)를 소개합니다. SKU는 기능, 성능 특성, 제한 사항 및 일부 내장 동작에서 다릅니다. 두 SKU에서 Virtual Machines를 사용할 수 있습니다. Load Balancer 및 공용 IP 리소스의 경우 SKU는 선택적 특성으로 유지됩니다. 시나리오 정의에서 SKU를 생략하면 구성에서 기본적으로 기본 SKU가 사용됩니다.

>[!IMPORTANT]
>리소스의 SKU는 변경할 수 없습니다. 기존 리소스의 SKU는 변경할 수 없습니다.  

### <a name="load-balancer"></a>Load Balancer

[기존 Load Balancer 리소스](load-balancer-overview.md)는 기본 SKU가 되며 일반 공급으로 유지되고 변경되지 않습니다.

Load Balancer 표준 SKU는 새 제품이며 현재 미리 보기 상태입니다. Microsoft.Network/loadBalancers의 2017년 8월 1일 API 버전에서는 리소스 정의에 **sku** 속성을 추가합니다.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Load Balancer 표준은 가용성 영역을 제공하는 영역에서 자동으로 영역 중복입니다. Load Balancer에 영역이 선언되면 자동으로 영역 중복이 되지 않습니다.

### <a name="public-ip"></a>공용 IP

[기존 공용 IP 리소스](../virtual-network/virtual-network-ip-addresses-overview-arm.md)는 기본 SKU가 되고 모든 해당 기능, 성능 특성 및 제한 사항과 함께 일반 공급으로 유지됩니다.

공용 IP 표준 SKU는 새 제품이며 현재 미리 보기 상태입니다. Microsoft.Network/publicIPAddresses의 2017년 8월 1일 API 버전에서는 리소스 정의에 **sku** 속성을 추가합니다.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

여러 할당 메서드를 제공하는 공용 IP 기본과 달리 공용 IP 표준은 항상 정적 할당을 사용합니다.

공용 IP 표준은 가용성 영역을 제공하는 영역에서 자동으로 영역 중복입니다. 공용 IP에 영역이 선언되면 자동으로 영역 중복이 되지 않습니다. 영역 공용 IP는 한 영역에서 다른 영역으로 변경할 수 없습니다.

## <a name="migration-between-skus"></a>SKU 간의 마이그레이션

SKU는 변경할 수 없습니다. 이 섹션의 단계에 따라 리소스 SKU 간에 이동합니다.

### <a name="migrate-from-basic-to-standard-sku"></a>기본에서 표준 SKU로 마이그레이션

1. 새 표준 리소스(Load Balancer 및 필요에 따라 공용 IP)를 만듭니다. 규칙 및 프로브 정의를 다시 작성합니다.

2. NIC 또는 서브넷에 새 NSG를 만들거나 기존 NSG를 업데이트하여 허용하려는 다른 트래픽 외에도 부하 분산된 트래픽과 프로브를 허용 목록에 추가합니다.

3. 모든 VM 인스턴스에서 기본 SKU 리소스(Load Balancer 및 해당되는 경우 공용 IP)를 제거합니다. 가용성 집합의 모든 VM 인스턴스도 제거해야 합니다.

4. 새 표준 SKU 리소스에 모든 VM 인스턴스를 연결합니다.

### <a name="migrate-from-standard-to-basic-sku"></a>표준에서 기본 SKU로 마이그레이션

1. 새 기본 리소스(Load Balancer 및 필요에 따라 공용 IP)를 만듭니다. 규칙 및 프로브 정의를 다시 작성합니다. 

2. 모든 VM 인스턴스에서 표준 SKU 리소스(Load Balancer 및 해당되는 경우 공용 IP)를 제거합니다. 가용성 집합의 모든 VM 인스턴스도 제거해야 합니다.

3. 새 기본 SKU 리소스에 모든 VM 인스턴스를 연결합니다.

>[!IMPORTANT]
>
>기본 및 표준 SKU를 사용할 때 다음과 같은 제한 사항이 따릅니다.
>
>HA 포트 및 표준 SKU 진단은 표준 SKU에서만 사용할 수 있습니다. 표준 SKU에서 기본 SKU로 마이그레이션할 수 없으며 이러한 기능을 유지할 수도 없습니다.
>
>SKU 일치는 Load Balancer 및 공용 IP 리소스에 대해 사용되어야 합니다. 기본 SKU 리소스와 표준 SKU 리소스를 함께 사용할 수 없습니다. VM, 가용성 집합의 VM 또는 가상 머신 확장 집합을 두 SKU에 동시에 연결할 수 없습니다.
>

## <a name="region-availability"></a>지역 가용성

Load Balancer 표준은 현재 모든 공용 클라우드 지역에서 사용할 수 있습니다.

>[!IMPORTANT]
> 짧은 기간 동안 초기 실행 지역(미국 동부 2, 미국 중부, 북유럽, 미국 중서부, 유럽 서부, 동남 아시아) 이외의 지역에 액세스하려면 추가 구독 기능(AllowLBPreviewWave2 및 AllowLBPreviewWave3)에 등록해야 합니다.  [다음 단계를 따르세요](#additionalpreviewregions). 이전에 AllowLBPreview에 이미 등록한 경우에도 모든 단계를 실행하시기 바랍니다.
> 이 요구 사항은 몇 주 후에 제거됩니다.

## <a name="sku-service-limits-and-abilities"></a>SKU 서비스 제한 및 기능

[네트워킹에 대한 Azure 서비스 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)은 구독당 지역당 적용됩니다. 

다음 표에서는 Load Balancer 기본 및 표준 SKU에 대한 제한 사항과 기능을 비교합니다.

| Load Balancer | Basic | Standard |
| --- | --- | --- |
| 백 엔드 풀 크기 | 최대 100 | 최대 1,000 |
| 백 엔드 풀 경계 | 가용성 집합 | 가상 네트워크, 지역 |
| 백 엔드 풀 디자인 | 가용성 집합의 VM, 가용성 집합의 가상 머신 확장 집합 | 가상 네트워크의 모든 VM 인스턴스 |
| HA 포트 | 지원되지 않음 | 사용 가능 |
| 진단 | 제한됨, 공용만 해당 | 사용 가능 |
| VIP 가용성  | 지원되지 않음 | 사용 가능 |
| 빠른 IP 이동성 | 지원되지 않음 | 사용 가능 |
|가용성 영역 시나리오 | 영역만 해당 | 영역, 영역 중복, 영역 간 부하 분산 |
| 아웃바운드 SNAT 알고리즘 | 요청 시 | 미리 할당됨 |
| 아웃바운드 SNAT 프런트 엔드 선택 | 구성할 수 없음, 여러 후보 | 후보를 줄이기 위한 옵션 구성 |
| 네트워크 보안 그룹 | NIC/서브넷의 선택 사항 | 필수 |

다음 표에서는 공용 IP 기본 및 표준 SKU에 대한 제한 사항과 기능을 비교합니다.

| 공용 IP | Basic | Standard |
| --- | --- | --- |
| 가용성 영역 시나리오 | 영역만 해당 | 영역 중복(기본값), 영역(선택 사항) | 
| 빠른 IP 이동성 | 지원되지 않음 | 사용 가능 |
| VIP 가용성 | 지원되지 않음 | 사용 가능 |
| Counters | 지원되지 않음 | 사용 가능 |
| 네트워크 보안 그룹 | NIC의 선택 사항 | 필수 |


## <a name="preview-sign-up"></a>미리 보기 등록

Load Balancer 표준 SKU 및 해당 공용 IP 표준 SKU에 대한 미리 보기에 참여하려면 구독을 등록합니다.  구독을 등록하면 PowerShell 또는 Azure CLI 2.0에서 액세스할 수 있습니다. 등록하려면 다음 단계를 수행합니다.

>[!NOTE]
>Load Balancer 표준 기능 등록이 전역으로 적용되는 데 최대 1시간이 소요될 수 있습니다. [가용성 영역](https://aka.ms/availabilityzones)과 함께 Load Balancer 표준을 사용하려는 경우 AZ 미리 보기에 대한 [별도의 등록](https://aka.ms/availabilityzones)이 필요합니다.

<a name="additionalpreviewregions"></a>
>[!IMPORTANT]
> 짧은 기간 동안 초기 실행 지역(미국 동부 2, 미국 중부, 북유럽, 미국 중서부, 유럽 서부, 동남 아시아) 이외의 지역에 액세스하려면 추가 구독 기능(AllowLBPreviewWave2 및 AllowLBPreviewWave3)에 등록해야 합니다.  추가 구독 기능을 사용하도록 설정하기 위해 아래 단계가 수정되었습니다. 이전에 AllowLBPreview에 이미 등록한 경우에도 모든 단계를 실행하시기 바랍니다. 이 요구 사항은 몇 주 후에 제거됩니다.


### <a name="sign-up-by-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 등록

1. 공급자에 기능을 등록합니다.

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave2 --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave3 --namespace Microsoft.Network
    ```
    
2. 이 작업을 완료하려면 최대 10분이 걸릴 수 있습니다. 다음 명령을 사용하여 작업 상태를 확인할 수 있습니다.

    ```cli
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreview']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave2']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave3']" --output json
    ```
    
    위의 각 구독 기능에 대해 기능 등록 상태가 ‘등록됨’인 경우 다음 단계로 진행합니다. 예:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
4. 리소스 공급자에 구독을 다시 등록하여 미리 보기 등록을 완료합니다.

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    

### <a name="sign-up-by-using-powershell"></a>PowerShell을 사용하여 등록

1. 공급자에 기능을 등록합니다.

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```
    
2. 이 작업을 완료하려면 최대 10분이 걸릴 수 있습니다. 다음 명령을 사용하여 작업 상태를 확인할 수 있습니다.

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```

  위의 각 구독 기능에 대해 기능 등록 상태가 ‘등록됨’인 경우 다음 단계로 진행합니다. 예:

    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. 리소스 공급자에 구독을 다시 등록하여 미리 보기 등록을 완료합니다.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>가격

Load Balancer 표준 SKU 요금은 구성한 규칙과 처리된 데이터를 기준으로 청구됩니다. 미리 보기 기간 동안은 요금이 발생하지 않습니다. 자세한 내용은 [Load Balancer](https://aka.ms/lbpreviewpricing) 및 [공용 IP](https://aka.ms/lbpreviewpippricing) 가격 책정 페이지를 참조하세요.

고객은 계속해서 추가 비용 없이 Load Balancer 기본 SKU를 이용합니다.

## <a name="limitations"></a>제한 사항

다음 제한 사항은 미리 보기의 기간에 적용되며 변경될 수 있습니다.

- 현재 Load Balancer 백 엔드 인스턴스는 피어링된 가상 네트워크에서 찾을 수 없습니다. 모든 백 엔드 인스턴스는 동일한 지역에 있어야 합니다.
- SKU는 변경할 수 없습니다. 기존 리소스의 SKU는 변경할 수 없습니다.
- 독립 실행형 VM, 가용성 집합의 VM 인스턴스 또는 가상 머신 확장 집합에 두 SKU를 함께 사용할 수 없습니다. VM 조합을 두 SKU에 동시에 사용할 수 없습니다. 혼합된 SKU를 포함하는 구성은 허용되지 않습니다.
- VM 인스턴스(또는 가용성 집합의 일부)와 함께 내부 Load Balancer 표준을 사용하면 [기본 SNAT 아웃바운드 연결](load-balancer-outbound-connections.md)을 사용할 수 없게 됩니다. 이 기능을 독립 실행형 VM, 가용성 집합의 VM 인스턴스 또는 가상 머신 확장 집합으로 복원할 수 있습니다. 이 기능을 복원하여 아웃바운드 연결을 만들 수도 있습니다. 이러한 기능을 복원하려면 동일한 VM 인스턴스에 공용 Load Balancer 표준 또는 공용 IP 표준을 인스턴스 수준 공용 IP로 동시에 할당합니다. 할당이 완료되면 공용 IP 주소에 대한 포트 위장 SNAT가 다시 제공됩니다.
- 전체 백 엔드 풀 규모를 얻으려면 VM 인스턴스를 가용성 집합으로 그룹화해야 할 수 있습니다. 최대 150개의 가용성 집합 및 독립 실행형 VM을 단일 백 엔드 풀에 배치할 수 있습니다.
- IPv6은 지원되지 않습니다.
- 가용성 영역의 컨텍스트에서 프런트 엔드는 영역에서 영역 중복으로 또는 반대로 변경할 수 없습니다. 프런트 엔드를 영역 중복으로 작성하면 영역 중복으로 유지됩니다. 프런트 엔드를 영역으로 작성하면 영역으로 유지됩니다.
- 가용성 영역의 컨텍스트에서 영역 공용 IP 주소는 한 영역에서 다른 영역으로 이동할 수 없습니다.
- 현재 [Azure Monitor 경고](../monitoring-and-diagnostics/monitoring-overview-alerts.md)는 지원되지 않습니다.
- 포털에서 아직 확장된 미리 보기 지역을 지원하지 않습니다.  템플릿, Azure CLI 2.0 또는 PowerShell 등의 클라이언트 도구를 임시 해결책으로 사용하세요.


## <a name="next-steps"></a>다음 단계

- [Load Balancer 기본](load-balancer-overview.md)에 대해 자세히 알아보세요.
- [가용성 영역](../availability-zones/az-overview.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)에 대해 자세히 알아보세요.
- Azure의 다른 주요 [네트워킹 기능](../networking/networking-overview.md)에 대해 알아보세요.
- [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)에 [표시되는 메트릭](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers)에 대해 알아봅니다.
