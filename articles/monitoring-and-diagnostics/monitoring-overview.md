---
title: "Microsoft Azure의 모니터링 개요 | Microsoft Docs"
description: "경고, 웹후크, 자동 크기 조정 등을 포함하여 Microsoft Azure에서 모니터링 및 진단의 상위 수준 개요."
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
l: 
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4a9e92330081bcf56b7e3755597f76d3530a823d


---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Microsoft Azure의 모니터링 개요
이 문서에서는 Azure 리소스 모니터링의 개념적 개요를 제공합니다. 특정 유형의 리소스 정보에 대한 포인터를 제공합니다.  비 Azure 관점에서의 응용 프로그램 모니터링에 대한 개략적인 정보는 [모니터링 및 진단 지침](../best-practices-monitoring.md)을 참조하세요.

클라우드 응용 프로그램은 이동하는 부분이 많아 복잡합니다. 모니터링은 응용 프로그램을 유지하고 정상 상태에서 실행할 수 있는 데이터를 제공합니다. 또한 잠재적 문제를 방지하거나 지난 문제를 해결할 수 있습니다. 또한 응용 프로그램에 대해 깊이 이해하는 데 모니터링 데이터를 사용할 수 있습니다. 이러한 정보를 통해 응용 프로그램 성능이나 유지 관리를 개선하거나 그렇지 않으면 수동 개입이 필요한 작업을 자동화하는 데 도움이 될 수 있습니다.

다음 다이어그램에서는 수집할 수 있는 유형의 로그 및 해당 데이터로 수행할 수 있는 작업 Azure 모니터링의 개념 보기를 보여 줍니다.   

![비 계산 리소스의 모니터링 및 진단에 대한 논리 모델](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

그림 1: 비 계산 리소스의 모니터링 및 진단에 개념 모델

<br/>

![계산 리소스의 모니터링 및 진단에 대한 논리 모델](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

그림 2: 계산 리소스의 모니터링 및 진단을 위한 개념 모델

## <a name="monitoring-sources"></a>모니터링 소스
### <a name="activity-logs"></a>활동 로그
Azure 인프라에 표시되는 대로 리소스에 대한 자세한 내용은 활동 로그(이전에 작업 또는 감사 로그라고도 함)를 검색할 수 있습니다. 이러한 로그는 리소스가 생성되거나 소멸된 시간과 같은 정보를 포함합니다.  

### <a name="host-vm"></a>호스트 VM
**계산만**

Cloud Services, Virtual Machines 및 Service Fabric과 같은 일부 계산 리소스에는 상호 작용하는 전용 호스트 VM이 있습니다. 호스트 VM은 Hyper-V 하이퍼바이저 모델의 루트 VM과 같습니다. 이 경우 게스트 OS 외에도 호스트 VM에서 메트릭을 수집할 수 있습니다.  

다른 Azure 서비스의 경우 리소스와 특정 호스트 VM이 반드시 일대일로 매핑되는 것은 아니므로 호스트 VM 메트릭은 사용할 수 없습니다.

### <a name="resource---metrics-and-diagnostics-logs"></a>리소스 - 메트릭 및 진단 로그
수집 가능한 메트릭은 리소스 종류에 따라 다릅니다. 예를 들어 Virtual Machines는 디스크 IO 및 % CPU에 대한 통계를 제공합니다. 하지만 이러한 통계는 큐 크기와 메시지 처리량과 같은 메트릭을 대신 제공하는 Service Bus 큐에 대해 존재하지 않습니다.

계산 리소스의 경우 게스트 OS에 대한 메트릭과 Azure Diagnostics과 같은 진단 모듈에 대한 메트릭을 가져올 수 있습니다. Azure Diagnostics은 수집 진단 데이터를 수집하고 다른 위치(예: Azure Storage)로의 라우팅할 수 있게 합니다.

현재 수집 가능한 메트릭의 목록은 [지원되는 메트릭](monitoring-supported-metrics.md)에서 제공됩니다.

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>응용 프로그램 - 진단 로그, 응용 프로그램 로그 및 메트릭
**계산만**

응용 프로그램은 계산 모델의 게스트 OS에서 실행될 수 있으며, 고유한 로그 및 메트릭 집합을 제공합니다.

메트릭 유형은 다음과 같습니다.

* 성능 카운터
* 응용 프로그램 로그
* Windows 이벤트 로그
* .NET 이벤트 원본
* IIS 로그
* 매니페스트 기반 ETW
* 크래시 덤프
* 고객 오류 로그

## <a name="uses-for-monitoring-data"></a>모니터링 데이터 사용
### <a name="visualize"></a>시각화
모니터링 데이터를 그래프 및 차트로 시각화하면 데이터 자체를 살펴보는 것보다 훨씬 빠르게 추세를 파악할 수 있습니다.  

몇 가지 시각화 방법은 다음과 같습니다.

* Azure 포털 사용
* Azure Application Insights로 데이터 라우팅
* Microsoft PowerBI로 데이터 라우팅
* 라이브 스트리밍 또는 Azure 저장소의 보관 파일에서 데이터를 읽는 도구를 사용하여 타사 시각화 도구로 데이터 라우팅

### <a name="archive"></a>보관
모니터링 데이터는 일반적으로 Azure Storage에 기록되며 사용자가 삭제할 때까지 유지됩니다.

이 데이터를 사용하는 몇 가지 방법은 다음과 같습니다.

* 기록된 경우 Azure 내부 또는 외부의 다른 도구를 사용하여 데이터를 읽고 처리할 수 있습니다.
* 로컬 보관을 위해 데이터를 로컬로 다운로드하거나, 연장된 기간 동안 데이터를 유지하기 위해 클라우드의 보존 정책을 변경할 수 있습니다.  
* <a name="you-leave-the-data-in-azure-storage-indefinitely-though-you-have-to-pay-for-azure-storage-based-on-the-amount-of-data-you-keep"></a>보관하는 데이터 양에 따라 Azure 저장소 요금을 지불해야 하지만, Azure 저장소에 데이터를 무기한으로 보관합니다.
  -

### <a name="query"></a>쿼리
Azure Monitor REST API, 플랫폼 간 CLI(명령줄 인터페이스), PowerShell cmdlet 또는 .NET SDK를 사용하여 시스템 또는 Azure 저장소의 데이터에 액세스할 수 있습니다.

이러한 예로 다음이 포함됩니다.

* 작성한 사용자 지정 모니터링 응용 프로그램에 대한 데이터 가져오기
* 사용자 지정 쿼리를 만들고 이 데이터를 타사 응용 프로그램으로 보내기

### <a name="route"></a>라우팅
모니터링 데이터를 다른 위치에 실시간으로 스트리밍할 수 있습니다.

예를 들면 다음과 같습니다.

* 시각화 도구를 사용할 수 있도록 Application Insights로 보내기
* 타사 도구로 라우팅하여 실시간 분석을 수행할 수 있도록 Event Hubs로 보내기

### <a name="automate"></a>자동화
모니터링 데이터를 사용하여 이벤트를 트리거하거나 전체 프로세스를 트리거할 수 있습니다. 예를 들면 다음과 같습니다.

* 데이터를 사용하여 응용 프로그램 부하에 따라 계산 인스턴스 크기를 자동으로 조정
* 메트릭이 미리 결정된 임계값을 초과하는 경우 전자 메일 보내기
* 웹 URL(웹후크)을 호출하여 Azure 외부 시스템에서 동작 실행
* Azure 자동화에서 runbook을 시작하여 다양한 태스크 수행

## <a name="methods-of-use"></a>사용 방법
일반적으로 다음 방법 중 하나를 사용하여 데이터 추적, 라우팅 및 검색을 조작할 수 있습니다. 일부 방법은 일부 동작 및 데이터 형식에 사용할 수 없습니다.

* [Azure 포털](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [CLI(플랫폼 간 명령줄 인터페이스)](insights-cli-samples.md)
* [REST API](https://msdn.microsoft.com/library/dn931943.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Azure의 모니터링 제품
Azure에는 운영 체제 미설치 인프라부터 응용 프로그램 원격 분석까지 서비스를 모니터링하는 데 사용할 수 있는 제품이 있습니다. 최상의 모니터링 전략은 세 가지 모두를 사용하여 서비스 상태에 대한 포괄적이고 세부적인 통찰력을 얻는 것입니다.

* [Azure Monitor](http://aka.ms/azmondocs) – Azure 인프라(작업 로그)와 개별 Azure 리소스(진단 로그)의 데이터에 대한 시각화, 쿼리, 라우팅, 경고, 자동 크기 조정 및 자동화를 제공합니다. 이 문서는 Azure Monitor 설명서의 일부입니다. Azure Monitor 이름은 Ignite 2016에서 9월 25일 릴리스되었습니다.  이전 이름은 "Azure Insights"입니다.  
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) – Azure 모니터링의 데이터에 원활히 통합된 사용자 서비스의 응용 프로그램 계층에서 발생하는 문제에 대한 다양한 검색 및 진단을 제공합니다. App Service Web Apps에 대한 기본 진단 플랫폼입니다.  다른 서비스에서 데이터를 라우팅할 수 있습니다.  
* [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)의 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 부분 – Azure 리소스의 온-프레미스 및 타사 클라우드 기반 인프라(예: AWS)에 대한 전체적인 IT 관리 솔루션을 제공합니다.  Azure Monitor의 데이터를 Log Analytics로 직접 라우팅할 수 있으므로 전체 환경에 대한 메트릭 및 로그를 한 곳에서 볼 수 있습니다.     

## <a name="next-steps"></a>다음 단계
자세한 정보

* [Ignite 2016의 비디오에서 Azure Monitor](https://myignite.microsoft.com/videos/4977)
* [Azure Monitor 시작](monitoring-get-started.md)
* [Azure 진단](../azure-diagnostics.md) - Cloud Service, Virtual Machine 또는 Service Fabric 응용 프로그램에서 문제를 진단하려는 경우
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) - 앱 서비스 웹앱에서 문제를 진단하려는 경우
* [Azure Storage 문제 해결](../storage/storage-e2e-troubleshooting.md) - 저장소 Blob, 테이블 및 큐를 사용하는 경우
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 및 [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)




<!--HONumber=Nov16_HO3-->


