---
title: DDoS 보호 원격 분석 보기 및 구성
description: DDoS protection 원격 분석을 보고 구성 하는 방법에 대해 알아봅니다.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c80e5e611c275c2a2262963aa0759075fca836b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989397"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS 보호 원격 분석 보기 및 구성

Azure DDoS Protection 표준은 DDoS 공격 분석을 통해 자세한 공격 인사이트와 시각화를 제공합니다. DDoS 공격으로부터 자신의 가상 네트워크를 보호하는 고객은 공격 완화 보고서 및 완화 흐름 로그를 통해 공격 트래픽 및 공격을 완화하는 데 수행된 작업에 대해 자세히 파악할 수 있습니다. 다양 한 원격 분석은 DDoS 공격 기간 동안 상세 메트릭을 포함 하 여 Azure Monitor를 통해 노출 됩니다. DDoS Protection에서 노출하는 Azure Monitor 메트릭에 대한 경고를 구성할 수 있습니다. 로깅은 Azure Monitor 진단 인터페이스를 통한 고급 분석을 위해 [Azure 센티널](../sentinel/connect-azure-ddos-protection.md), Splunk (azure Event Hubs), OMS Log Analytics 및 Azure Storage와 추가로 통합 될 수 있습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * DDoS 보호 메트릭에 대한 경고 구성
> * DDoS 보호 원격 분석 사용
> * DDoS 완화 정책 보기
> * Azure Security Center에서 DDoS protection 경고 보기

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 자습서의 단계를 완료 하기 전에 먼저 [Azure DDoS Standard 보호 계획](manage-ddos-protection.md)을 만들어야 합니다.

## <a name="configure-alerts-for-ddos-protection-metrics"></a>DDoS 보호 메트릭에 대한 경고 구성

Azure Monitor 경고 구성을 사용하면 공격 중에 활성 완화가 있는 경우 알리는 데 사용할 수 있는 DDoS 보호 메트릭 중 하나를 선택할 수 있습니다. 조건이 충족되면 지정된 주소에서 경고 메일을 받습니다.

1. 포털의 왼쪽 위에서 **모든 서비스** 를 선택합니다.
2. **필터** 상자에서 *모니터* 를 입력합니다. 결과에 **모니터** 가 표시되면 해당 모니터를 선택합니다.
3. **공유 서비스** 아래에서 **메트릭** 을 선택합니다.
4. 값을 직접 입력 또는 선택하거나 다음 예의 값을 입력하고, 나머지 기본값을 적용한 다음, **확인** 을 선택합니다.

    |설정                  |값                                                                                               |
    |---------                |---------                                                                                           |
    |Name                     | _MyDdosAlert_ 를 입력 합니다.                                                                                |
    |Subscription             | 경고를 받으려는 공용 IP 주소가 포함된 구독을 선택합니다.        |
    |Resource group           | 경고를 받으려는 공용 IP 주소가 포함된 리소스 그룹을 선택합니다.      |
    |리소스                 | 경고를 받으려는 공용 IP 주소가 포함된 공용 IP 주소를 선택합니다. DDoS는 가상 네트워크 내의 리소스에 할당된 공용 IP 주소를 모니터링합니다. 가상 네트워크에 공용 IP 주소가 있는 리소스가 없으면 먼저 공용 IP 주소를 사용하여 리소스를 만들어야 합니다. Azure App Service 환경 및 Azure VPN Gateway를 제외하고 [Azure 서비스에 대한 가상 네트워크](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)에 나열된 Resource Manager(클래식이 아님)를 통해 배포된 모든 리소스의 공용 IP 주소를 모니터링할 수 있습니다. 이 자습서를 계속 진행하려면 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신을 빠르게 만들면 됩니다.                   |
    |메트릭                   | **DDoS 공격을** 선택 합니다.                                                                |
    |임계값                | 1 - **1** 은 공격을 받고 있음을 나타냅니다. **0** 은 공격을 받고 있지 않음을 나타냅니다.                         |
    |기간                   | 선택한 값을 선택합니다.                                                                   |
    |이메일을 통해 알림         | 확인란을 선택 합니다.                                                                                 |
    |추가 관리자 | 구독에 대한 이메일 소유자, 기여자 또는 읽기 권한자가 아닌 경우 이메일 주소를 입력합니다. |

    공격 탐지 후 몇 분 내에 다음 그림과 비슷한 Azure Monitor 메트릭에서 이메일을 받게 됩니다.

    ![공격 경고](./media/manage-ddos-protection/ddos-alert.png)


DDoS 공격을 시뮬레이션하여 경고의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](test-through-simulations.md)를 참조하세요.

경고를 만들기 위해 [웹후크 구성](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세히 알아볼 수도 있습니다.

## <a name="use-ddos-protection-telemetry"></a>DDoS 보호 원격 분석 사용

공격에 대한 원격 분석이 Azure Monitor를 통해 실시간으로 제공됩니다. 이러한 원격 분석 기능은 공용 IP 주소의 위험이 완화되는 동안에만 사용할 수 있습니다. 공격이 완화되기 전 또는 후에는 원격 분석이 표시되지 않습니다.

1. 포털의 왼쪽 위에서 **모든 서비스** 를 선택합니다.
2. **필터** 상자에서 *모니터* 를 입력합니다. 결과에 **모니터** 가 표시되면 해당 모니터를 선택합니다.
3. **공유 서비스** 아래에서 **메트릭** 을 선택 합니다.
4. 원격 분석하려는 공용 IP 주소가 포함된 **구독** 및 **리소스 그룹** 을 선택합니다.
5. **리소스 종류** 에 대해 **공용 IP 주소** 를 선택한 다음, 원격 분석하려는 특정 공용 IP 주소를 선택합니다.
6. 일련의 **사용 가능한 메트릭** 이 화면 왼쪽에 표시됩니다. 이러한 메트릭을 선택할 경우 개요 화면의 **Azure Monitor 메트릭 차트** 에 그래프로 표시됩니다.
7. **집계** 유형을 **Max** 로 선택 합니다.

메트릭 이름은 다양한 패킷 유형과 바이트 및 패킷을 나타내며, 각 메트릭에서 태그 이름의 기본 구조는 다음과 같습니다.

- **Dropped 태그 이름**(예: **Inbound Packets Dropped DDoS**): DDoS 보호 시스템에서 삭제/스크럽한 패킷의 수입니다.
- **Forwarded 태그 이름**(예: **Inbound Packets Forwarded DDoS**): DDoS 시스템에서 대상 VIP로 전달한 패킷의 수입니다(필터링되지 않은 트래픽).
- **태그 이름 없음**(예: **Inbound Packets DDoS**): 스크럽 시스템으로 들어온 패킷의 총 수입니다(삭제 및 전달된 패킷의 합계를 나타냄).

이 [Azure Monitor 경고 규칙](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) 은 활성 DDoS 완화가 발생 한 경우를 검색 하는 간단한 쿼리를 실행 합니다. DDoS 공격을 시뮬레이션하여 원격 분석의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](test-through-simulations.md)를 참조하세요. 

## <a name="view-ddos-mitigation-policies"></a>DDoS 완화 정책 보기

DDoS 보호 표준은 DDoS를 사용하도록 설정된 가상 네트워크에서 보호되는 리소스의 각 공용 IP 주소에 대해 자동 조정된 세 가지 완화 정책(TCP SYN, TCP 및 UDP)을 적용합니다. 다음 그림에 표시 된 것 처럼 DDoS 완화 및 **인바운드 UDP 패킷을** 트리거하여 **집계** 유형을 포함 하는 DDoS 완화 메트릭을 ' 최대 '로 트리거할 **인바운드 TCP 패킷을** 선택 하 여 정책 임계값을 볼 수 있습니다.

![완화 정책 보기](./media/manage-ddos-protection/view-mitigation-policies.png)

정책 임계값은 Azure 기계 학습 기반의 네트워크 트래픽 프로파일링을 통해 자동으로 구성됩니다. 정책 임계값을 위반한 경우에만 공격을 받고 있는 IP 주소에 대해 DDoS 완화가 발생합니다.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Azure Security Center에서 DDoS protection 경고 보기

Azure Security Center는 문제를 조사 하 고 수정 하는 데 도움이 되는 정보를 제공 하는 [보안 경고](../security-center/security-center-managing-and-responding-alerts.md)의 목록을 제공 합니다. 이 기능을 사용 하면 DDoS 공격 관련 경고 및 가까운 공격을 완화 하기 위해 수행 하는 작업을 포함 하 여 경고에 대 한 통합 된 보기를 얻을 수 있습니다.
DDoS 공격 감지 및 완화에 대해 다음과 같은 두 가지 특정 경고가 표시 됩니다.

- **공용 ip에 대 한 DDoS 공격 검색**:이 경고는 DDoS protection 서비스에서 공용 ip 주소 중 하나가 DDoS 공격의 대상인 것을 감지할 때 생성 됩니다.
- **공용 ip에 대 한 DDoS 공격 완화**:이 경고는 공용 ip 주소에 대 한 공격이 완화 된 경우에 생성 됩니다.
경고를 보려면 Azure Portal에서 **Security Center** 를 엽니다. **위협 방지** 에서 **보안 경고** 를 선택 합니다. 다음 스크린샷은 DDoS 공격 경고의 예를 보여 줍니다.

![Azure Security Center의 DDoS 경고](./media/manage-ddos-protection/ddos-alert-asc.png)

경고에는 공격에 포함 된 공용 IP 주소, 지역 및 위협 인텔리전스 정보 및 재구성 단계에 대 한 일반 정보가 포함 됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

- DDoS 보호 메트릭에 대한 경고 구성
- DDoS 보호 원격 분석 사용
- DDoS 완화 정책 보기
- Azure Security Center에서 DDoS protection 경고 보기

공격 완화 보고서 및 흐름 로그를 구성 하는 방법을 알아보려면 다음 자습서를 계속 진행 합니다.

> [!div class="nextstepaction"]
> [DDoS 공격 완화 보고서 및 흐름 로그 구성](reports-and-flow-logs.md)