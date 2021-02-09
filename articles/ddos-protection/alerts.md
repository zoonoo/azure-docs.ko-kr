---
title: Azure DDoS Protection Standard에 대 한 DDoS protection 경고 보기 및 구성
description: Azure DDoS Protection Standard에 대 한 DDoS protection 경고를 확인 하 고 구성 하는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 2d72027082ed2b57b28a15a736c35801ba88188c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832524"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>DDoS 보호 경고 보기 및 구성

Azure DDoS Protection 표준은 DDoS 공격 분석을 통해 자세한 공격 인사이트와 시각화를 제공합니다. DDoS 공격으로부터 자신의 가상 네트워크를 보호하는 고객은 공격 완화 보고서 및 완화 흐름 로그를 통해 공격 트래픽 및 공격을 완화하는 데 수행된 작업에 대해 자세히 파악할 수 있습니다. 다양 한 원격 분석은 DDoS 공격 기간 동안 상세 메트릭을 포함 하 여 Azure Monitor를 통해 노출 됩니다. DDoS Protection에서 노출하는 Azure Monitor 메트릭에 대한 경고를 구성할 수 있습니다. 로깅은 Azure Monitor 진단 인터페이스를 통한 고급 분석을 위해 [Azure 센티널](../sentinel/connect-azure-ddos-protection.md), Splunk (azure Event Hubs), OMS Log Analytics 및 Azure Storage와 추가로 통합 될 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Monitor를 통해 경고 구성
> * 포털을 통해 경고 구성
> * Azure Security Center에서 경고 보기
> * 경고 유효성 검사 및 테스트

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 자습서의 단계를 완료 하려면 먼저 [Azure DDoS standard 보호 계획](manage-ddos-protection.md) 을 만들고 가상 네트워크에서 DDoS Protection standard를 사용 하도록 설정 해야 합니다.
- DDoS는 가상 네트워크 내의 리소스에 할당된 공용 IP 주소를 모니터링합니다. 가상 네트워크에 공용 IP 주소가 있는 리소스가 없으면 먼저 공용 IP 주소를 사용하여 리소스를 만들어야 합니다. Azure App Service 환경을 제외 하 고 [azure 서비스](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (가상 네트워크에 있는 Azure 부하 분산 장치를 포함 하는 Azure 부하 분산 장치)에 나열 된 리소스 관리자 (클래식이 아님)를 통해 배포 된 모든 리소스의 공용 IP 주소를 모니터링할 수 있습니다. 이 자습서를 계속 진행하려면 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신을 빠르게 만들면 됩니다.     

## <a name="configure-alerts-through-azure-monitor"></a>Azure Monitor를 통해 경고 구성

이러한 템플릿을 사용 하 여 진단 로깅을 사용 하도록 설정한 모든 공용 IP 주소에 대 한 경고를 구성할 수 있습니다. 따라서 이러한 경고 템플릿을 사용 하려면 먼저 진단 설정이 사용 되는 Log Analytics 작업 영역이 필요 합니다. [DDoS 진단 로깅 보기 및 구성을](diagnostic-logging.md)참조 하세요.

### <a name="azure-monitor-alert-rule"></a>Azure Monitor 경고 규칙

이 [Azure Monitor 경고 규칙](https://aka.ms/DDOSmitigationstatus) 은 활성 DDoS 완화가 발생 한 경우를 검색 하는 간단한 쿼리를 실행 합니다. 이는 잠재적인 공격을 나타냅니다. 작업 그룹은 경고의 결과로 동작을 호출 하는 데 사용할 수 있습니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>논리 앱을 사용 하 여 경고 규칙 Azure Monitor

이 [템플릿은](https://aka.ms/ddosalert) 보강 DDoS 완화 경고 (Azure Monitor 경고 규칙, 작업 그룹 및 논리 앱)의 필수 구성 요소를 배포 합니다. 프로세스의 결과는 IP와 연결 된 리소스에 대 한 정보를 포함 하 여 공격의 IP 주소에 대 한 세부 정보를 포함 하는 전자 메일 경고입니다. 리소스 소유자는 보안 팀과 함께 전자 메일의 받는 사람으로 추가 됩니다. 기본 응용 프로그램 가용성 테스트도 수행 되며 결과가 전자 메일 경고에 포함 됩니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>포털을 통해 경고 구성

Azure Monitor 경고 구성을 사용하면 공격 중에 활성 완화가 있는 경우 알리는 데 사용할 수 있는 DDoS 보호 메트릭 중 하나를 선택할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/) 에 로그인 하 여 DDoS Protection 요금제로 이동 합니다.
2. **모니터링** 아래에서 **메트릭** 을 선택합니다.
3. 회색 탐색 모음에서 **새 경고 규칙** 을 선택 합니다. 
4. 사용자 고유의 값을 입력 또는 선택 하거나 다음 예제 값을 입력 하 고 나머지 기본값을 적용 한 다음 **경고 규칙 만들기** 를 선택 합니다.

    |설정                  |값                                                                                               |
    |---------                |---------                                                                                           |
    | 범위                   | **리소스 선택** 을 선택합니다. </br> 로깅할 공용 IP 주소를 포함 하는 **구독** 을 선택 하 고 **리소스 종류** 에 대 한 **공용 ip 주소** 를 선택한 다음 메트릭을 로깅할 특정 공용 ip 주소를 선택 합니다. </br> **완료** 를 선택합니다. | 
    | 조건 | **조건 선택** 을 선택 합니다. </br> 신호 이름 아래에서 **DDoS 공격** 을 선택 합니다. </br> **연산자** 아래에서 **크거나 같음을** 선택 합니다. </br> **집계 유형** 에서 **최대값** 을 선택 합니다. </br> **임계값** 에 *1* 을 입력 합니다. **DDoS 공격을 받고 있지 않은** 경우 **0** 은 공격을 받고 있지 않으며 **1** 은 공격을 받고 있음을 의미 합니다. </br> **완료** 를 선택합니다. | 
    | 동작 | **작업 그룹 추가** 를 선택 합니다. </br> **작업 그룹 만들기** 를 선택합니다. </br> 알림 **아래의** **알림 유형** 에서 **전자 메일/SMS 메시지/푸시/음성** 을 선택 합니다. </br> **이름** 아래에 _MyUnderAttackEmailAlert_ 을 입력 합니다. </br> 편집 단추를 클릭 하 고 **전자 메일** 및 필요한 다음 옵션을 선택한 다음 **확인** 을 선택 합니다. </br> **검토 + 만들기** 를 선택합니다. | 
    | 경고 규칙 세부 정보 | **경고 규칙 이름** 아래에 _MyDdosAlert_ 을 입력 합니다. |

몇 분 동안 공격 감지 후 다음 그림과 비슷한 Azure Monitor 메트릭에서 전자 메일을 받게 됩니다.

![공격 경고](./media/manage-ddos-protection/ddos-alert.png)

경고를 만들기 위해 [웹후크 구성](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세히 알아볼 수도 있습니다.

## <a name="view-alerts-in-azure-security-center"></a>Azure Security Center에서 경고 보기

Azure Security Center는 문제를 조사 하 고 수정 하는 데 도움이 되는 정보를 제공 하는 [보안 경고](../security-center/security-center-managing-and-responding-alerts.md)의 목록을 제공 합니다. 이 기능을 사용 하면 DDoS 공격 관련 경고 및 가까운 공격을 완화 하기 위해 수행 하는 작업을 포함 하 여 경고에 대 한 통합 된 보기를 얻을 수 있습니다.
DDoS 공격 감지 및 완화에 대해 다음과 같은 두 가지 특정 경고가 표시 됩니다.

- **공용 ip에 대 한 DDoS 공격 검색**:이 경고는 DDoS protection 서비스에서 공용 ip 주소 중 하나가 DDoS 공격의 대상인 것을 감지할 때 생성 됩니다.
- **공용 ip에 대 한 DDoS 공격 완화**:이 경고는 공용 ip 주소에 대 한 공격이 완화 된 경우에 생성 됩니다.
경고를 보려면 Azure Portal에서 **Security Center** 를 엽니다. **위협 방지** 에서 **보안 경고** 를 선택 합니다. 다음 스크린샷은 DDoS 공격 경고의 예를 보여 줍니다.

![Azure Security Center의 DDoS 경고](./media/manage-ddos-protection/ddos-alert-asc.png)

경고에는 공격에 포함 된 공용 IP 주소, 지역 및 위협 인텔리전스 정보 및 재구성 단계에 대 한 일반 정보가 포함 됩니다.

## <a name="validate-and-test"></a>유효성 검사 및 테스트

DDoS 공격을 시뮬레이션 하 여 경고의 유효성을 검사 하려면 [DDoS 검색 유효성 검사](test-through-simulations.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

- Azure Monitor를 통해 경고 구성
- 포털을 통해 경고 구성
- Azure Security Center에서 경고 보기
- 경고 유효성 검사 및 테스트

DDoS 공격을 테스트 하 고 시뮬레이트하는 방법에 대해 알아보려면 시뮬레이션 테스트 가이드를 참조 하세요.

> [!div class="nextstepaction"]
> [시뮬레이션을 통해 테스트](test-through-simulations.md)
