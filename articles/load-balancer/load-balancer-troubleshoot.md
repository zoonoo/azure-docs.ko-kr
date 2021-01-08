---
title: 일반적인 문제를 해결 Azure Load Balancer
description: Azure Load Balancer와 관련 된 일반적인 문제를 해결 하는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028814"
---
# <a name="troubleshoot-azure-load-balancer"></a>Azure Load Balancer 문제 해결

이 페이지에서는 기본 및 표준 공통 Azure Load Balancer 질문에 대한 문제 해결 정보를 제공합니다. 표준 Load Balancer에 대한 자세한 내용은 [표준 Load Balancer 개요](load-balancer-standard-diagnostics.md)를 참조하세요.

Load Balancer 연결을 사용할 수 없을 때 가장 일반적인 증상은 다음과 같습니다.

- Load Balancer 뒤의 Vm이 상태 프로브에 응답 하지 않습니다. 
- Load Balancer 뒤의 Vm이 구성 된 포트의 트래픽에 응답 하지 않습니다.

백 엔드 VM에 대한 외부 클라이언트가 부하 분산 장치를 통과하면 클라이언트의 IP 주소가 통신에 사용됩니다. 클라이언트의 IP 주소가 NSG 허용 목록에 추가되어 있는지 확인합니다.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>표준 내부 부하 분산 장치 (ILB)에서 아웃 바운드 연결이 없습니다.

**유효성 검사 및 해결**

표준 ILBs는 **기본적으로 안전** 합니다. 기본 ILBs는 *숨겨진* 공용 IP 주소를 통해 인터넷에 연결할 수 있습니다. IP 주소가 정적이 아니고 사용자가 소유 하는 NSGs를 통해 잠기지 않으므로 프로덕션 워크 로드에는이 방법이 권장 되지 않습니다. 최근에 기본 ILB에서 표준 ILB로 이동한 경우에는 NSGs를 통해 IP를 잠그는 [아웃 바운드 전용](egress-only.md) 구성을 통해 명시적으로 공용 IP를 만들어야 합니다. 서브넷에서 [NAT 게이트웨이](../virtual-network/nat-overview.md) 를 사용할 수도 있습니다.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>백 엔드 풀에 배포 된 가상 머신 확장 집합을 포함 하는 부하 분산 장치의 기존 LB 규칙에 대해 백 엔드 포트를 변경할 수 없습니다.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>원인: 가상 머신 확장 집합에서 참조 하는 부하 분산 장치에 대 한 상태 프로브에서 사용 하는 부하 분산 규칙에 대해 백 엔드 포트를 수정할 수 없습니다.

**해결 방법** 포트를 변경 하려면 가상 머신 확장 집합을 업데이트 하 고, 포트를 업데이트 한 후 상태 프로브를 다시 구성 하 여 상태 프로브를 제거할 수 있습니다.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>부하 분산 장치의 백 엔드 풀에서 Vm을 제거한 후에도 작은 트래픽이 부하 분산 장치를 통해 진행 됩니다.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>원인: 백 엔드 풀에서 제거 되는 Vm은 더 이상 트래픽을 수신 하지 않아야 합니다. 소량의 네트워크 트래픽은 Azure 내의 스토리지, DNS 및 기타 기능과 관련될 수 있습니다.

확인하려면 네트워크 추적을 수행하면 됩니다. Blob 스토리지 계정에 사용되는 FQDN은 각 스토리지 계정의 속성 내에 나열됩니다.  Azure 구독 내의 가상 머신에서 nslookup을 수행 하 여 해당 저장소 계정에 할당 된 Azure IP를 확인할 수 있습니다.

## <a name="additional-network-captures"></a>추가 네트워크 캡처

지원 사례를 열기로 결정한 경우 더 빠른 해결을 위해 다음 정보를 수집합니다. 단일 백 엔드 VM을 선택하여 다음과 같은 테스트를 수행합니다.

- VNet 내의 백 엔드 Vm 중 하나에서 ps ping을 사용 하 여 프로브 포트 응답을 테스트 하 고 (예: ps ping 10.0.0.4:3389) 결과를 기록 합니다. 
- 이러한 ping 테스트에서 응답이 수신되지 않으면 PsPing을 실행하는 동안 백 엔드 VM 및 VNet 테스트 VM에 대해 동시 Netsh 추적을 실행한 후 Netsh 추적을 중지합니다.

## <a name="load-balancer-in-failed-state"></a>실패 상태 Load Balancer

**해결 방법**

- 실패 상태에 있는 리소스를 확인 한 후 [Azure Resource Explorer](https://resources.azure.com/) 으로 이동 하 여이 상태에서 리소스를 식별 합니다.
- 오른쪽 위 모퉁이의 설정/해제를 읽기/쓰기로 업데이트 합니다.
- 실패 상태에서 리소스에 대 한 편집을 클릭 합니다.
- PUT, GET을 차례로 클릭 하 여 프로 비전 상태가 성공으로 업데이트 되었는지 확인 합니다.
- 그런 다음 리소스가 실패 상태를 초과 하 여 다른 작업을 진행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

위의 단계를 수행 해도 문제가 해결 되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.
