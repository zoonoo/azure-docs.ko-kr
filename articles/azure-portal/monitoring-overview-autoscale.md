<properties
	pageTitle="Microsoft Azure Microsoft Azure Virtual Machines, Cloud Services 및 Web Apps에서 자동 크기 조정 개요 | Microsoft Azure"
	description="Microsoft Azure의 자동 크기 조정의 개요입니다. Virtual Machines, Cloud Services 및 Web Apps에 적용됩니다."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="robb"/>

# Microsoft Azure Microsoft Azure Virtual Machines, Cloud Services 및 Web Apps에서 자동 크기 조정 개요

이 문서에서는 Microsoft Azure 자동 크기 조정, 이점을 설명하고 사용하는 경로를 시작합니다.

Azure Insights 자동 크기 조정은 다음에 적용됩니다.

* [가상 컴퓨터 크기 집합](https://azure.microsoft.com/services/virtual-machine-scale-sets/)
* [클라우드 서비스](https://azure.microsoft.com/services/cloud-services/)
* [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)
  
   
>[AZURE.NOTE] Azure에는 두 개의 자동 크기 조정 메서드가 있습니다. 이전 버전의 자동 크기 조정은 Virtual Machines(가용성 집합)에 적용됩니다. 이 기능은 제한적으로 지원하므로 빠르고 안정적인 자동 크기 조정 지원을 위해 VM 크기 조정 설정으로 마이그레이션하는 것이 좋습니다. 이전 기술을 사용하는 방법에 대한 링크는 이 문서에 포함됩니다.


## 자동 크기 조정이란? 

자동 크기 조정을 사용하면 사용되지 않는 리소스로 인해 비용을 낭비하지 않고 실행 중인 적절한 양의 리소스가 응용 프로그램의 부하를 처리하도록 할 수 있습니다. 규칙 집합에 따라 자동으로 계산 리소스를 추가하거나 제거할 수 있습니다. 그림 1에서는 이러한 개념을 보여 줍니다.

![자동 크기 조정 개념 설명](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

**그림 1: 자동 크기 조정 개념 설명**

자동 크기 조정은 수평적으로만 규모를 조정하며 이는 VM 인스턴스 수의 증가("규모 확장") 또는 감소("규모 감축")입니다. 수평적 크기 조정은 부하를 처리하기 위해 잠재적으로 수천 개의 VM을 실행할 수 있으므로 클라우드 상황에서 더 유연합니다. 다른 유형의 크기 조정은 수직적입니다. 수직적 크기 조정은 동일한 수의 VM을 유지하지만 VM을 강력하게("강화") 만들거나 약하게("축소") 만듭니다. 전원은 메모리, CPU 속도, 디스크 공간 등에서 측정됩니다. 수직적 크기 조정에 더 많은 제한이 있습니다. 큰 하드웨어의 가용성에 따라 다르며 지역에 따라 달라지고 신속하게 상한값에 도달할 수 있습니다. 또한 수직적 크기 조정은 VM 중지 및 시작이 필요합니다. 자세한 내용은 [Azure Automation을 사용하여 Azure 가상 컴퓨터를 수직으로 확장](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md)을 참조하세요.


자동 크기 조정에서는 리소스 크기를 조정하는 경우에 대한 규칙을 만들어야 합니다. 크기 조정 작업을 제어하도록 설정할 수 있는 기준은

* 실행할 인스턴스의 **최소** 및 **최대** 개수를 포함합니다. 최소 기준은 응용 프로그램은 항상 실행되도록 하고 최대 기준은 비용을 제어할 수 있습니다.
* 자동 크기 조정에 대한 **규칙 또는 조건**입니다. 메트릭 또는 일정 기반 크기 조정일 수 있습니다.
* **휴지 시간**은 다른 자동 크기 조정 이벤트가 발생하도록 허용하기 전에 자동 크기 조정 이벤트 후에 대기하는 시간입니다. 이 기간의 시간은 "플래핑"이라는 상태로부터 보호하기 위한 것으로 몇 분 동안 VM을 신속하게 추가하고 제거하는 경우 발생합니다. VM을 시작 또는 중지하는 비용이 있습니다. 플래핑은 비용을 절약하지 않으며 시작되고 중지된 VM은 유용한 처리를 수행할 수 없으므로 VM이 실행되도록 두는 것보다 지양해야 합니다.

   
구성 가능한 값의 전체 목록은 [자동 크기 조정 REST API](https://msdn.microsoft.com/library/dn931928.aspx)에서 사용할 수 있습니다.


## 개념적 다이어그램  
그림 2는 다이어그램의 일부에 대해 설명하는 자동 크기 조정에 대한 개념적 개요를 보여 줍니다.

![경고 추가](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)

**그림 2: 자동 크기 조정 프로세스 개요**

## 리소스 메트릭 
리소스는 메트릭을 내보내며 나중에 규칙에 의해 처리됩니다. 메트릭은 다른 메서드를 통해 제공됩니다. Web Apps 및 Cloud Services의 원격 분석은 Azure 인프라에서 직접 제공되는 반면 VM 크기 조정 설정에서는 Azure 진단 에이전트의 원격 분석 데이터를 사용합니다. 일부 자주 사용되는 통계는 CPU 사용량, 메모리 사용량, 스레드 수, 큐 길이 및 디스크 사용량을 포함합니다. 사용할 수 있는 원격 분석 데이터 목록은 [자동 크기 조정 공통 메트릭](insights-autoscale-common-metrics.md)을 참조하세요.

 
## Time
일정 기반 규칙은 UTC 기준으로 합니다. 규칙을 설정할 때 표준 시간대를 올바르게 설정해야 합니다.

## 자동 크기 조정 규칙
다이어그램에서는 자동 크기 조정 규칙을 하나만 표시하지만 여러 개 있을 수도 있습니다. 상황에 필요한 대로 겹치는 복잡한 규칙을 만들 수 있습니다. 규칙 형식은 다음을 포함합니다.
 
 - **메트릭 기반** - 예를 들어 CPU 사용률이 50%보다 높으면 이 작업을 수행합니다.
 - **시간 기반** - 예를 들어 지정된 표준 시간대에 토요일 오전 8시마다 웹후크를 트리거합니다.

 
## 자동 크기 조정 작업 및 자동화

규칙은 하나 이상의 형식을 가진 작업을 트리거할 수 있습니다.

- **크기 조정** - VM을 감축 또는 확장합니다.
- **전자 메일** - 구독의 관리자와 공동 관리자 및 지정한 추가 전자 메일 주소에 전자 메일을 보냅니다.
- **Webhook 통해 자동화** - Azure의 내부 또는 외부에서 여러 복잡한 작업을 트리거할 수 있는 웹후크를 호출합니다. Azure에서 Azure Automation runbook, Azure Function 또는 Azure Logic App을 시작할 수 있습니다. Azure 외부의 타사 예제는 Slack 및 Twilio와 같은 서비스를 포함합니다.


## 리소스 관리자 규칙 세부 정보

자동 크기 조정 규칙은 Azure Resource Manager 템플릿에서 다음과 같은 구조를 포함합니다.

![Azure Resource Manager 자동 크기 조정 규칙 구조](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure2.png)

여러 프로필을 나열할 수 있습니다. 각 프로필에는 여러 규칙이 포함될 수 있습니다. 알림 방법 및 위치는 프로필 뒤에 포함됩니다. 알림 예는 URI가 있는 웹후크 또는 전자 메일 주소가 있는 전자 메일을 포함합니다.

코드 예제는 다음을 참조하세요.

* [Resource Manager 템플릿을 사용하여 VM 크기 집합에 대한 고급 자동 크기 조정 구성](insights-advanced-autoscale-virtual-machine-scale-sets.md)
* [REST API 자동 크기 조정](https://msdn.microsoft.com/library/dn931953.aspx)

## 액세스 방법 
자동 크기 조정 규칙은 다음을 통해 설정할 수 있습니다.

- Azure 포털
- PowerShell
- CLI(Common Library Interface)
- Insights REST API

## 자동 크기 조정 연습

- [클라우드 서비스 크기 조정](../cloud-services/cloud-services-how-to-scale-portal.md)
- [웹앱 크기 조정](insights-how-to-scale.md)
- [클래식 가상 컴퓨터 가용성 집합 크기 조정](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/)
- [Windows에서 VM 규모 설정 크기 조정](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)
- [Linux에서 VM 규모 설정 크기 조정](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md)
- [Resource Manager 템플릿을 사용하여 VM 크기 집합에 대한 고급 자동 크기 조정 구성](insights-advanced-autoscale-virtual-machine-scale-sets.md)

## 다음 단계

자동 크기 조정에 대한 자세한 내용은 앞에 나열된 자동 크기 조정 연습을 사용하거나 다음 리소스를 참조하세요.

- [Azure Insights 자동 크기 조정 공용 메트릭](insights-autoscale-common-metrics.md)
- [Azure Insights 자동 크기 조정에 대한 모범 사례](insights-autoscale-best-practices.md)
- [크기 자동 조정 작업을 사용하여 전자 메일 및 웹후크 경고 알림 보내기](insights-autoscale-to-webhook-email.md)
- [REST API 자동 크기 조정](https://msdn.microsoft.com/library/dn931953.aspx) - 각 필드 및 값의 전체 API 및 의미를 참조하세요.
- [가상 컴퓨터 규모 집합 자동 크기 조정 문제 해결](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

<!---HONumber=AcomDC_0907_2016-->