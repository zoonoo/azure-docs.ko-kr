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
ms.date: 09/28/2017
ms.author: kumud
ms.openlocfilehash: 0ed8d3432a988c468260589cfe12090529c403d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Azure Load Balancer 표준 개요(미리 보기)

Azure Load Balancer 표준 SKU 및 공용 IP 표준 SKU를 함께 사용하여 확장성이 매우 크고 신뢰할 수 있는 아키텍처를 만들 수 있습니다.  Load Balancer 표준을 사용하는 응용 프로그램은 모든 TCP 및 UDP 응용 프로그램에 대해 짧은 대기 시간, 높은 처리량 및 수백만의 흐름에 대한 크기 조정뿐만 아니라 새로운 기능을 활용할 수 있습니다.

>[!NOTE]
> Load Balancer 표준 SKU는 현재 미리 보기 상태입니다. 미리 보기 중 이 기능은 일반 공급 릴리스에 있는 기능과 동일한 수준의 가용성 및 안정성을 제공하지 못할 수도 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 Microsoft Azure 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 프로덕션 서비스의 경우 일반 공급 [Load Balancer 기본 SKU](load-balancer-overview.md)를 사용합니다.  이 미리 보기와 연결된 기능([가용성 영역](https://aka.ms/availabilityzones) 및 [HA 포트](https://aka.ms/haports))은 현재 별도의 등록이 필요합니다. Load Balancer [표준 미리 보기](#preview-sign-up)와 등록에 대한 각각의 지침을 따르십시오.

## <a name="why-use-load-balancer-standard"></a>Load Balancer 표준을 사용하는 이유

소규모 배포에서부터 크고 복잡한 다중 영역 아키텍처까지 가상 데이터 센터의 전체 범위에 대해 Load Balancer 표준을 사용하여 다음 기능을 활용합니다.

- Load Balancer 표준을 통해 [엔터프라이즈급](#enterprisescale)을 구현할 수 있습니다.  Virtual Network 내에서 최대 1000개의 VM 인스턴스로 모든 가상 컴퓨터 인스턴스에 사용할 수 있습니다.

- [새로운 진단 정보](#diagnosticinsights)를 사용하여 가상 데이터 센터의 이 중요한 구성 요소를 이해하고 관리하고 문제를 해결할 수 있습니다. 프런트 엔드에서 VM, 끝점당 상태 프로브, TCP 연결 시도, 아웃바운드 연결까지 연속 데이터 경로 상태 측정에 대해 Azure Monitor(미리 보기)를 사용하여 새로운 다차원 메트릭을 표시하고, 필터링하고 그룹화합니다.

- [네트워크 보안 그룹](#nsg)은 이제 Load Balancer 또는 공용 IP의 표준 SKU와 연결된 모든 VM 인스턴스에 필요하며 향상된 보안을 제공합니다.

- HA 포트는 네트워크 가상 어플라이언스 및 기타 응용 프로그램 시나리오에 대해 [높은 신뢰성](#highreliability) 및 규모를 제공합니다. HA 포트는 VM 인스턴스의 풀에 내부 Load Balancer 프런트 엔드의 모든 포트에 대한 부하를 분산합니다.

- [아웃바운드 연결](#outboundconnections)은 이제 큰 복원력 및 규모를 제공하는 새로운 SNAT 포트 할당 모델을 사용합니다.

- [가용성 영역이 있는 Load Balancer 표준](#availabilityzones)은 영역 간 부하 분산으로 영역 중복 및 영역 아키텍처를 만드는 데 사용할 수 있습니다. DNS 레코드에 대한 종속성 없이 영역 중복을 얻을 수 있습니다. 단일 IP 주소는 단순히 기본적으로 영역 중복이며 모든 가용성 영역에 걸쳐 지역의 VNet에서 모든 VM에 도달할 수 있습니다.


다음 기본 시나리오를 계속해서 지원하는 공용 또는 내부 구성에서 Load Balancer 표준을 사용할 수 있습니다.

- 정상 백 엔드 인스턴스에 인바운드 트래픽을 부하 분산합니다.
- 단일 백 엔드 인스턴스에 인바운드 트래픽을 포트 전달합니다.
- VNet 내의 개인 IP 주소의 아웃바운드 트래픽을 공용 IP 주소로 변환합니다.

### <a name = "enterprisescale"></a>엔터프라이즈급 규모

 고성능 가상 데이터 센터를 디자인하는 데 Load Balancer 표준을 사용합니다. 백 엔드 풀에서 독립 실행형 VM 인스턴스 또는 1000개의 인스턴스 가상 컴퓨터 확장 집합을 사용하고 모든 TCP 또는 UDP 응용 프로그램을 지원할 수 있습니다. Load Balancer 표준을 사용하여 응용 프로그램은 완전히 관리되는 Azure 서비스에서 낮은 전달 대기 시간, 높은 처리량 성능 및 수백만의 흐름에 대한 규모를 여전히 활용할 수 있습니다.
 
Load Balancer 표준은 영역의 VNet에서 모든 VM 인스턴스에 트래픽을 전달할 수 있습니다. 백 엔드 풀 크기는 다음의 조합으로 최대 1000개의 인스턴스가 될 수 있습니다.

- 가용성 집합이 없는 독립 실행형 VM
- 가용성 집합이 있는 독립 실행형 VM
- 최대 1000개의 인스턴스가 있는 가상 컴퓨터 확장 집합(가상 컴퓨터 확장 집합)
- 여러 가상 컴퓨터 확장 집합
- VM 및 가상 컴퓨터 확장 집합 혼합

가용성 집합에 대한 요구 사항이 더 이상 없지만 제공되는 다른 이점을 위해 가용성 집합을 사용하도록 선택할 수 있습니다.

### <a name = "diagnosticinsights"></a>진단 정보

Load Balancer 표준은 공용 및 내부 Load Balancer 구성에 대해 새로운 다차원 진단 기능을 제공합니다. 이러한 새 메트릭은 Azure Monitor(미리 보기)를 통해 제공되며 다양한 다운스트림 소비자와의 통합에 대한 기능을 포함하여 관련된 모든 기능을 활용합니다.

| 메트릭 | 설명 |
| --- | --- |
| VIP 가용성 | Load Balancer 표준은 영역 내에서 Load Balancer 프런트 엔드로, 마지막으로 VM을 지원하는 SDN 스택으로 데이터 경로를 연속적으로 실행합니다. 정상 인스턴스가 남아 있는 한 측정은 응용 프로그램 부하가 분산된 트래픽과 동일한 경로를 따르고 고객이 사용할 수 있는 데이터 경로의 유효성을 검사합니다. 측정은 응용 프로그램에 표시되지 않으며 방해하지 않습니다.|
| DIP 가용성 | Load Balancer 표준은 구성한 것에 따라 응용 프로그램 끝점의 상태를 모니터링하는 분산된 상태 검색 서비스를 사용합니다.  이 메트릭은 Load Balancer 풀에서 각 개별 인스턴스 끝점의 집계 또는 끝점당 필터링된 보기를 제공합니다.  상태 프로브 구성에 표시된 대로 Load Balancer에서 응용 프로그램의 상태를 보는 방법을 확인할 수 있습니다.
| SYN 패킷 | Load Balancer 표준은 TCP 연결을 끊거나 TCP 또는 UDP 패킷 흐름과 상호 작용하지 않습니다. 흐름 및 해당 핸드셰이크는 항상 원본과 VM 인스턴스 간입니다. 모든 TCP 프로토콜 시나리오를 보다 잘 해결하기 위해 이 메트릭을 사용하여 만들어지는 TCP 연결 시도의 수를 이해할 수 있습니다. 이 메트릭은 받은 TCP SYN 패킷의 수를 보고하며 이는 서비스에 대한 연결을 설정하는 클라이언트 시도에 영향을 줄 수 있습니다.|
| SNAT 연결 | Load Balancer 표준은 공용 IP 주소 프런트 엔드로 위장되는 아웃바운드 연결 수를 보고합니다.  SNAT 포트는 소모성 리소스이며 이 메트릭은 응용 프로그램이 아웃바운드 시작된 연결에 대해 SNAT에 얼마나 의존하는지에 대한 표시를 제공할 수 있습니다.|
| 바이트 카운터 | Load Balancer 표준은 프런트 엔드당 처리된 데이터를 보고합니다.|
| 패킷 카운터 | Load Balancer 표준은 프런트 엔드당 처리된 패킷을 보고합니다.|

### <a name = "highreliability"></a>높은 안정성

부하 분산 규칙을 구성하여 응용 프로그램 규모를 매우 안정적으로 만듭니다.  개별 포트에 대한 규칙을 구성하거나 새 HA 포트를 사용하여 TCP 또는 UDP 포트 번호에 관계 없이 모든 트래픽을 균등화할 수 있습니다.  

새 HA 포트를 사용하여 개별 포트를 지정하는 데 적절하지 않거나 바람직하지 않은 내부 네트워크 가상 어플라이언스 및 다른 시나리오에 대한 높은 가용성 및 규모를 포함하여 다양한 시나리오를 잠금 해제할 수 있습니다. HA 포트는 활성/수동 시나리오에 제한을 받지 않고 필요한 인스턴스를 허용하여 중복 및 규모를 제공합니다. 상태 프로브 구성은 정상 인스턴스에만 트래픽을 전달하여 서비스를 보호합니다.

네트워크 가상 어플라이언스 공급 업체는 고객에 대한 단일 실패 지점을 제거하고 규모에 대한 여러 활성 인스턴스를 허용하여 완전히 공급 업체 지원되는 복원 시나리오를 제공할 수 있습니다. 어플라이언스가 이러한 구성을 허용하는 경우 두 개 이상의 인스턴스로 확장할 수 있습니다. 네트워크 가상 어플라이언스 공급 업체는 이러한 시나리오에 대한 자세한 지침을 제공해야 합니다.

### <a name = "availabilityzones"></a>가용성 영역

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

지원되는 지역에서 가용성 영역을 사용하여 응용 프로그램의 복구를 진행합니다. 가용성 영역은 현재 특정 지역에서 미리 보기 상태이며 추가 옵트인이 필요합니다.

### <a name="automatic-zone-redundancy"></a>자동 영역 중복

Load Balancer에서 각 응용 프로그램에 대해 영역 중복 또는 영역 프런트 엔드를 제공해야 하는지 여부를 선택할 수 있습니다.  Load Balancer 표준을 사용하여 영역 중복을 만들기 쉽습니다.  단일 프런트 엔드 IP 주소는 자동으로 영역 중복입니다.  영역 중복 프런트 엔드는 지역의 모든 가용성 영역에서 동시에 서비스됩니다. 인바운드 및 아웃바운드 연결에 대한 영역 중복 데이터 경로를 만듭니다. Azure에서 영역 중복은 여러 IP 주소 및 DNS 레코드가 필요하지 않습니다. 

이 영역 중복은 공용 또는 내부 프런트 엔드에 사용할 수 있습니다. 내부 Load Balancer 프런트 엔드의 사설 IP 뿐만 아니라 공용 IP 주소는 영역 중복될 수 있습니다.

다음을 사용하여 영역 중복 공용 IP 주소를 만듭니다(기존 Resource Manager 템플릿에 "sku" 추가).

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

다음을 사용하여 내부 Load Balancer 영역 중복 프런트 엔드 IP 구성을 만듭니다(기존 Resource Manager 템플릿에 "sku" 추가).

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

공용 IP 프런트 엔드가 영역 중복인 경우 VM 인스턴스에서 만들어진 모든 아웃바운드 연결은 자동으로 영역 중복이 되고 영역 장애로부터 보호됩니다.  SNAT 포트 할당은 영역 실패 후에도 유지됩니다.

#### <a name="cross-zone-load-balancing"></a>영역 간 부하 분산

영역 간 부하 분산은 가상 컴퓨터 인스턴스에 대해 최대 유연성을 허용하여 백 엔드 풀에 대한 영역 내에서 사용할 수 있습니다.  프런트 엔드는 VM 인스턴스의 가용성 영역에 관계 없이 VNet의 모든 VM에 흐름을 제공할 수 있습니다.

또한 프런트 엔드 및 백 엔드 인스턴스에 대한 특정 영역을 지정하도록 선택하여 특정 영역으로 데이터 경로 및 리소스를 맞출 수도 있습니다.

VNet 및 서브넷은 영역이 제한되지 않으므로 원하는 VM 인스턴스를 사용하여 백 엔드 풀을 정의하기만 하면 구성이 완료됩니다.

#### <a name="zonal-deployments"></a>영역 배포

필요에 따라 영역 프런트 엔드를 정의하여 프런트 엔드를 특정 영역에 맞출 수도 있습니다.  영역 프런트 엔드는 지정된 단일 가용성 영역에서만 제공되며 영역 VM 인스턴스와 결합할 때 리소스를 특정 영역에 맞출 수 있습니다.

특정 영역에 생성된 공용 IP 주소는 항상 해당 영역에만 존재합니다.  공용 IP 주소의 영역을 변경하는 것은 불가능합니다.  여러 영역의 리소스에 연결할 수 있는 공용 IP 주소가 필요하면 대신 영역 중복 공용 IP를 만들어야 합니다.

다음을 사용하여 가용성 영역 1에 영역 공용 IP 주소를 만듭니다(기존 Resource Manager 템플릿에 "zones" 및 "sku" 추가).

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

다음을 사용하여 가용성 영역 1로 내부 Load Balancer 프런트 엔드를 만듭니다(기존 Resource Manager 템플릿에 "sku" 추가, 프런트 엔드 IP 구성 자식 리소스에 "zones" 배치).

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

또한 백 엔드 풀로 VNet의 VM 인스턴스를 배치하여 백 엔드에 대해 영역 간 부하 분산을 사용할 수 있습니다.

Load Balancer 표준 리소스 자체는 항상 가용성 영역이 지원되는 지역 및 영역 중복입니다. 공용 IP 주소 또는 영역에 할당되지 않은 내부 Load Balancer 프런트 엔드는 가용성 영역 지원에 관계 없이 모든 영역에서 배포될 수 있습니다. 영역이 나중에 가용성 영역을 얻는 경우 이미 배포된 공용 IP 또는 내부 Load Balancer 프런트 엔드는 자동으로 영역 중복이 됩니다. 영역 중복 데이터 경로는 0% 패킷 손실을 의미하지 않습니다.

### <a name = "nsg"></a>네트워크 보안 그룹

VNet 및 NSG(네트워크 보안 그룹)에 완전히 온보드하는 Load Balancer 표준 및 공용 IP 표준이 이제 필요합니다. NSG는 흐름의 허용 목록을 허용하고 고객이 다른 구성이 완료되는 경우 대신 NSG를 통한 배포에 대한 트래픽을 허용하는 경우의 전체 제어에 있는 구성을 허용합니다.

NSG를 서브넷 또는 백 엔드 풀에 있는 VM 인스턴스의 NIC와 연결합니다.  이는 인스턴스 수준 공용 IP로 사용되는 경우 Load Balancer 표준 및 공용 IP 표준에 적용됩니다. 전달하기 위해 NSG로 허용하려는 트래픽을 명시적으로 허용 목록에 추가해야 합니다.

네트워크 보안 그룹 및 시나리오에 적용하는 방법에 대해 자세히 알아보려면 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)을 참조하세요.

### <a name ="outboundconnections"></a>아웃바운드 연결

 Load Balancer 표준은 포트 위장 SNAT(원본 네트워크 주소 변환)을 사용하여 Load Balancer와 연결될 때 VNet 내의 VM에 대한 아웃바운드 연결을 제공합니다.

공용 Load Balancer 리소스가 VM 인스턴스와 연결될 때 각 아웃바운드 연결의 원본은 VNet의 개인 IP 주소 공간에서 Load Balancer 프런트 엔드의 공용 IP 주소에 다시 작성됩니다.  Load Balancer 표준은 증가된 견고성 및 규모를 위해 새 포트 위장 SNAT(원본 네트워크 주소 변환) 알고리즘을 사용합니다.  

또한, 영역 중복 프런트 엔드로 사용되는 경우 아웃바운드 연결도 영역 중복이며 SNAT 포트 할당은 영역 실패 후에도 유지됩니다.

Load Balancer 표준의 새로운 알고리즘은 다음 계층에 따라 풀에 추가된 시간에 각 VM의 네트워크 인터페이스에 SNAT 포트를 사전 할당합니다.

| 백 엔드 풀 크기 | 사전 할당된 SNAT 포트 |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

SNAT 포트는 연결의 수로 직접 변환하지 않습니다. 여러 고유한 대상에 SNAT 포트를 재사용할 수 있습니다.  자세한 내용은 [아웃 바운드 연결](load-balancer-outbound-connections.md) 문서를 참조하세요.

백 엔드 풀이 증가되고 하나의 크기 계층에서 다음 큰 크기로 전환하는 경우 할당된 포트의 절반은 회수됩니다. 회수된 포트와 연결된 모든 연결은 시간이 초과되며 다시 설정되어야 합니다. 모든 새 연결 시도는 즉시 성공합니다. 백 엔드 풀이 하나의 크기 계층에서 다음 작은 크기로 감소되는 경우 SNAT 포트 사용 가능성은 증가하며 기존 연결은 영향을 받지 않습니다.

Load Balancer 표준 역시 규칙 단위당 추가 구성 옵션을 가져 여러 프런트 엔드를 사용할 수 있는 경우 포트 위장 SNAT에 사용될 프런트 엔드에 대한 고객 제어를 제공합니다.

마지막으로, Load Balancer 표준만 VM 인스턴스를 제공하는 경우 아웃바운드 SNAT 연결을 사용할 수 없습니다. VM 인스턴스를 공용 Load Balancer에 할당하거나 인스턴스 수준 공용 IP로 공용 IP를 각 VM 인스턴스에 직접 할당하여 이 기능을 명시적으로 복원할 수 있습니다. 일부 운영 체제 및 응용 프로그램 시나리오에 필요할 수도 있습니다. 

### <a name="port-forwarding"></a>포트 전달

 기본 및 표준 Load Balancer는 인바운드 NAT 규칙을 구성하는 기능을 제공하여 프런트 엔드 포트를 개별 백 엔드 인스턴스에 매핑합니다.  원격 데스크톱 프로토콜 끝점, SSH 끝점 또는 다른 다양한 응용 프로그램 시나리오 노출을 포함하여 이 기능에 대한 많은 용도가 있습니다.

Load Balancer 표준은 인바운드 NAT 규칙을 통해 포트 전달 기능을 계속해서 제공합니다.  영역 중복 프런트 엔드로 사용되는 경우 인바운드 NAT 규칙은 영역 중복이 되고 영역 실패 후에도 유지됩니다.

### <a name="multiple-frontends"></a>여러 프런트 엔드

응용 프로그램에 노출될 여러 개별 IP 주소가 필요한 디자인 유연성에 대해 여러 프런트 엔드를 구성합니다(예: TLS 웹 사이트 또는 SQL AlwaysOn 가용성 그룹 끝점).  자세한 내용은 [여기](load-balancer-multivip-overview.md)에서 찾을 수 있습니다.

Load Balancer 표준은 고유 IP 주소에서 특정 응용 프로그램 끝점을 노출하기 적합한 여러 프런트 엔드를 계속해서 제공합니다.

 자세한 내용은 [여기](load-balancer-multivip-overview.md)에서 찾을 수 있습니다.

## <a name = "sku"></a>SKU 정보

SKU는 Azure Resource Manager 배포 모델에서만 사용할 수 있습니다.  이 미리 보기는 Load Balancer 및 공용 IP 리소스에 대해 두 개의 SKU(기본 및 표준)를 소개합니다.  SKU는 기능, 성능 특성, 제한 사항 및 일부 내장 동작에서 다릅니다. 두 SKU에서 Virtual Machines를 사용할 수 있습니다. Load Balancer 및 공용 IP 리소스의 경우 SKU는 기본으로 기본값을 생략한 경우 선택적 특성으로 유지됩니다.

>[!IMPORTANT]
>리소스의 SKU는 변경할 수 없습니다.  기존 리소스의 SKU는 변경할 수 없습니다.  

### <a name="load-balancer"></a>부하 분산 장치

[기존 Load Balancer 리소스](load-balancer-overview.md)는 기본 SKU가 되며 일반 공급으로 유지되고 변경되지 않습니다.

Load Balancer 표준 SKU는 새로운 제품이며 현재 미리 보기 상태입니다. Microsoft.Network/loadBalancers에 대한 2017-08-01 API 버전은 리소스에 SKU를 도입합니다.

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
또한 가용성 영역을 제공하는 지역에서 사용되는 경우 Load Balancer 표준은 영역이 되도록 선언되지 않은 한 자동으로 영역 복원력이 있습니다.

### <a name="public-ip"></a>공용 IP

[기존 공용 IP 리소스](../virtual-network/virtual-network-ip-addresses-overview-arm.md)는 기본 SKU가 되고 모든 해당 기능, 성능 특성 및 제한 사항과 함께 일반 공급으로 유지됩니다.

공용 IP 표준 SKU는 새로운 제품이며 현재 미리 보기 상태입니다. Microsoft.Network/publicIPAddresses 리소스에 대한 2017-08-01 API 버전은 SKU를 도입합니다.

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

여러 할당 메서드를 제공하는 공용 IP 기본과 달리 공용 IP 표준은 항상 정적 할당입니다.

또한 가용성 영역을 제공하는 지역에서 사용되는 경우 공용 IP 표준은 영역이 되도록 선언되지 않은 한 자동으로 영역 복원력이 있습니다.  영역 공용 IP는 한 영역에서 다른 영역으로 변경할 수 없습니다.

## <a name="migration-between-skus"></a>SKU 간의 마이그레이션

SKU는 변경할 수 없습니다.  하나의 리소스 SKU에서 다른 리소스 SKU로 이동하려는 경우 다음 단계를 따릅니다.

### <a name="migrating-from-basic-to-standard-sku"></a>기본에서 표준 SKU로 마이그레이션

1. 새 표준 리소스(Load Balancer 및 필요에 따라 공용 IP)를 만들고, 규칙을 다시 만들고 정의를 검색합니다.
2. 모든 VM 인스턴스(가용성 집합의 모든 인스턴스 포함)에서 기본 SKU 리소스(공용 IP 및 LB)를 제거합니다.
3. 새 표준 SKU 리소스에 모든 VM 인스턴스를 연결합니다.

### <a name="migrating-from-standard-to-basic-sku"></a>표준에서 기본 SKU로 마이그레이션:

1. 새 기본 리소스(Load Balancer 및 필요에 따라 공용 IP)를 만들고, 규칙을 다시 만들고 정의를 검색합니다. 
2. 모든 VM 인스턴스(가용성 집합의 모든 인스턴스 포함)에서 표준 SKU 리소스(공용 IP 및 LB)를 제거합니다.
3. 새 기본 SKU 리소스에 모든 VM 인스턴스를 연결합니다.

  >[!NOTE]
  >HA 포트, 표준 SKU의 진단은 표준 SKU에서만 사용할 수 있습니다. 표준에서 기본으로 마이그레이션할 수 없으며 이 기능을 유지할 수 없습니다.

SKU 일치는 Load Balancer 및 공용 IP 리소스에 대해 사용되어야 합니다.  기본 및 표준 SKU 리소스를 혼합하거나 VM, 가용성 집합 또는 가상 컴퓨터 확장 집합의 VM을 기본 및 표준 SKU 리소스에 동시에 연결하는 것은 불가능합니다.

## <a name="region-availability"></a>지역 가용성

Load Balancer 표준은 현재 다음 지역에서 사용할 수 있습니다.
- 미국 동부 2
- 미국 중부
- 북유럽
- 미국 중서부
- 서유럽
- 동남아시아

## <a name="service-limits--abilities-comparison"></a>서비스 제한 및 기능 비교

[네트워킹에 대한 Azure 서비스 제한](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits)은 구독당 지역당 적용됩니다. 

다음 표는 Load Balancer에 대한 기본 및 표준 SKU 간의 제한 및 기능 비교를 제공합니다.

| 부하 분산 장치 | Basic | Standard |
| --- | --- | --- |
| 백 엔드 풀 크기 | 최대 100 | 최대 1000 |
| 백 엔드 풀 경계 | 가용성 집합 | VNet, 영역 |
| 백 엔드 풀 디자인 | 가용성 집합의 VM 또는 가용성 집합의 가상 컴퓨터 확장 집합 | VNet의 모든 VM 인스턴스 |
| HA 포트 | 지원되지 않음 | 사용 가능 |
| 진단 | 제한됨, 공용만 해당 | 사용 가능 |
| VIP 가용성  | 지원되지 않음 | 사용 가능 |
| 빠른 IP 이동성 | 지원되지 않음 | 사용 가능 |
|가용성 영역 시나리오 | 영역만 해당 | 영역, 영역 중복, 영역 간 부하 분산 |
| 아웃바운드 SNAT 알고리즘 | 요청 시 | 미리 할당됨 |
| 아웃바운드 SNAT 프런트 엔드 선택 | 구성할 수 없음, 여러 후보 | 후보를 줄이기 위한 옵션 구성 |
| 네트워크 보안 그룹 | NIC/서브넷의 선택 사항 | 필수 |

다음 표는 공용 IP에 대한 기본 및 표준 SKU 간의 제한 및 기능 비교를 제공합니다.

| 공용 IP | Basic | Standard |
| --- | --- | --- |
| 가용성 영역 시나리오 | 영역만 해당 | 영역 중복(기본값), 영역(선택 사항) | 
| 빠른 IP 이동성 | 지원되지 않음 | 사용 가능 |
| VIP 가용성 | 지원되지 않음 | 사용 가능 |
| Counters | 지원되지 않음 | 사용 가능 |
| 네트워크 보안 그룹 | NIC의 선택 사항 | 필수 |


## <a name="preview-sign-up"></a>미리 보기 등록

Load Balancer 표준 SKU 및 해당 도우미 공용 IP 표준 SKU에 대한 미리 보기에 참여하려면 PowerShell 또는 Azure CLI 2.0을 사용하여 액세스 권한을 얻도록 구독을 등록합니다.

- PowerShell을 사용하여 등록

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

- Azure CLI 2.0을 사용하여 등록

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```

>[!NOTE]
>Load Balancer 표준 기능을 등록하는 데는 한 시간까지 소요될 수 있습니다.

>[!NOTE]
>[가용성 영역](https://aka.ms/availabilityzones) 및 [HA 포트](https://aka.ms/haports)가 있는 Load Balancer 표준을 사용하려면 다음 미리 보기에 대한 별도의 등록이 필요합니다.  해당 지침을 따르십시오.

## <a name="pricing"></a>가격

Load Balancer 표준 SKU는 구성된 규칙 및 처리된 데이터에 따라 요금이 청구됩니다.  미리 보기 기간 동안은 요금이 발생하지 않습니다.  자세한 내용은 [Load Balancer](https://aka.ms/lbpreviewpricing) 및 [공용 IP](https://aka.ms/lbpreviewpippricing) 가격 책정 페이지를 참조하세요.

고객은 계속해서 추가 비용 없이 Load Balancer 기본 SKU를 이용합니다.

## <a name="limitations"></a>제한 사항

다음 제한 사항은 미리 보기의 기간에 적용되며 변경될 수 있습니다.

- Load Balancer 백 엔드 인스턴스는 이 때 피어링된 VNet에서 찾을 수 없습니다. 모든 백 엔드 인스턴스는 동일한 지역에 있어야 합니다.
- SKU는 변경할 수 없습니다. 기존 리소스의 SKU는 변경할 수 없습니다.
- 독립 실행형 VM, 가용성 집합 또는 가상 컴퓨터 확장 집합의 모든 VM 인스턴스와 함께 기본 SKU 또는 표준 SKU를 사용할 수 있습니다. 독립 실행형 VM, 가용성 집합 또는 가상 컴퓨터 확장 집합의 모든 VM 인스턴스는 동시에 함께 사용될 수 없습니다. SKU의 혼합은 허용되지 않습니다.
- VM 인스턴스(또는 가용성 집합의 일부)와 함께 내부 Load Balancer 표준을 사용하면 [기본 SNAT 아웃바운드 연결](load-balancer-outbound-connections.md)을 비활성화합니다.  독립 실행형 VM 또는 VM 인스턴스 가용성 집합 또는 가상 컴퓨터 확장 집합에 이 기능을 복원하고 인스턴스 수준 공용 IP로 공용 Load Balancer 표준 또는 공용 IP 표준을 동일한 VM에 동시에 할당하여 아웃바운드 연결을 설정할 수 있습니다. 완료되면 공용 IP 주소에 대한 포트 위장 SNAT은 다시 제공됩니다.
- VM 인스턴스는 전체 백 엔드 풀 규모를 달성하기 위해 가용성 집합으로 그룹화되어야 할 수 있습니다. 최대 150개의 가용성 집합 및 독립 실행형 VM을 단일 백 엔드 풀에 배치할 수 있습니다.
- IPv6은 지원되지 않습니다.
- 가용성 영역의 컨텍스트에서 프런트 엔드는 영역에서 영역 중복으로 또는 반대로 변경할 수 없습니다. 영역 중복으로 만들어지면 항상 영역 중복입니다. 영역으로 만들어지면 항상 영역입니다.
- 가용성 영역의 컨텍스트에서 영역 공용 IP 주소는 한 영역에서 다른 영역으로 이동할 수 없습니다.


## <a name="next-steps"></a>다음 단계

- [기본 Load Balancer](load-balancer-overview.md)에 대해 자세히 알아보기
- [가용성 영역](../availability-zones/az-overview.md)에 대해 자세히 알아보기
- Azure의 다른 주요 [네트워킹 기능](../networking/networking-overview.md)에 대해 알아보기

