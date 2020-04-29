---
title: Azure 진단 확장 개요
description: 클라우드 서비스, 가상 머신 및 서비스 패브릭에서 디버깅, 성능 측정, 모니터링, 트래픽 분석을 위해 Azure 진단 사용
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77672381"
---
# <a name="azure-diagnostics-extension-overview"></a>Azure 진단 확장 개요
Azure 진단 확장은 가상 컴퓨터를 포함 하 여 Azure 계산 리소스의 게스트 운영 체제에서 모니터링 데이터를 수집 하는 [Azure Monitor의 에이전트](agents-overview.md) 입니다. 이 문서에서는 지 원하는 특정 기능 및 설치 및 구성 옵션을 포함 하 여 Azure 진단 확장에 대 한 개요를 제공 합니다. 

> [!NOTE]
> Azure 진단 확장은 계산 리소스의 게스트 운영 체제에서 모니터링 데이터를 수집 하는 데 사용할 수 있는 에이전트 중 하나입니다. 다른 에이전트에 대 한 설명과 요구 사항에 적합 한 에이전트를 선택 하는 방법에 대 한 지침은 [Azure Monitor 에이전트 개요](agents-overview.md) 를 참조 하세요.

## <a name="comparison-to-log-analytics-agent"></a>Log Analytics 에이전트와 비교
Azure Monitor의 Log Analytics 에이전트를 사용 하 여 가상 컴퓨터의 게스트 운영 체제에서 모니터링 데이터를 수집할 수도 있습니다. 요구 사항에 따라 둘 중 하나 또는 둘 모두를 사용 하도록 선택할 수 있습니다. Azure Monitor 에이전트의 자세한 비교는 [Azure Monitor 에이전트 개요](agents-overview.md) 를 참조 하세요. 

고려해 야 할 주요 차이점은 다음과 같습니다.

- Azure 진단 확장은 Azure 가상 컴퓨터에만 사용할 수 있습니다. Log Analytics 에이전트는 Azure, 다른 클라우드 및 온-프레미스의 가상 컴퓨터와 함께 사용할 수 있습니다.
- Azure 진단 확장 Azure Storage, [Azure Monitor 메트릭](data-platform-metrics.md) (Windows에만 해당) 및 Event Hubs에 데이터를 보냅니다. Log Analytics 에이전트는 [Azure Monitor 로그](data-platform-logs.md)에 데이터를 수집 합니다.
- Log Analytics 에이전트는 [솔루션](../monitor-reference.md#insights-and-core-solutions), [VM용 Azure Monitor](../insights/vminsights-overview.md)및 [Azure Security Center](/azure/security-center/)와 같은 기타 서비스에 필요 합니다.

## <a name="costs"></a>비용
Azure 진단 확장에 대 한 비용은 없지만 데이터 수집에 대 한 요금이 발생할 수 있습니다. 데이터를 수집 하는 대상의 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/) 을 확인 하세요.

## <a name="data-collected"></a>수집되는 데이터
다음 표에서는 Windows 및 Linux 진단 확장에서 수집할 수 있는 데이터를 나열 합니다.

### <a name="windows-diagnostics-extension-wad"></a>Windows 진단 확장 (WAD)

| 데이터 원본 | Description |
| --- | --- |
| Windows 이벤트 로그   | Windows 이벤트 로그의 이벤트입니다. |
| 성능 카운터 | 숫자 값은 운영 체제 및 작업의 여러 측면에 대 한 성능을 측정 합니다. |
| IIS 로그             | 게스트 운영 체제에서 실행 되는 IIS 웹 사이트에 대 한 사용 정보입니다. |
| 애플리케이션 로그 전송 사용     | 응용 프로그램에서 작성 한 추적 메시지입니다. |
| .NET EventSource 로그 |.NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 클래스를 사용하여 이벤트를 작성하는 코드 |
| [매니페스트 기반 ETW 로그](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |프로세스에서 생성 된 이벤트를 ETW(Windows용 이벤트 추적) 합니다. |
| 크래시 덤프(로그)   | 응용 프로그램이 충돌 하는 경우 프로세스의 상태에 대 한 정보입니다. |
| 파일 기반 로그    | 애플리케이션 또는 서비스에서 생성된 로그입니다. |
| 에이전트 진단 로그 | Azure 진단 자체에 대 한 정보입니다. |


### <a name="linux-diagnostics-extension-lad"></a>Linux 진단 확장 (꼬마)

| 데이터 원본 | Description |
| --- | --- |
| syslog | Linux 이벤트 로깅 시스템으로 전송 되는 이벤트입니다.   |
| 성능 카운터  | 숫자 값은 운영 체제 및 작업의 여러 측면에 대 한 성능을 측정 합니다. |
| 로그 파일 | 파일 기반 로그에 전송 된 항목입니다.  |

## <a name="data-destinations"></a>데이터 대상
Windows 및 Linux 용 Azure 진단 확장은 항상 데이터를 Azure Storage 계정으로 수집 합니다. [Microsoft Azure 진단 확장 설치 및 구성 (WAD)](diagnostics-extension-windows-install.md) 을 참조 하 고 [Linux 진단 확장을 사용 하](../../virtual-machines/extensions/diagnostics-linux.md) 여이 데이터가 수집 되는 특정 테이블 및 blob의 목록에 대 한 메트릭 및 로그를 모니터링 합니다.

하나 이상의 *데이터 싱크* 를 구성 하 여 다른 추가 대상으로 데이터를 전송 합니다. 다음 섹션에서는 Windows 및 Linux 진단 확장에 사용할 수 있는 싱크를 나열 합니다.

### <a name="windows-diagnostics-extension-wad"></a>Windows 진단 확장 (WAD)

| 대상 | Description |
|:---|:---|
| Azure Monitor 메트릭 | 성능 데이터를 수집 하 여 메트릭을 Azure Monitor 합니다. [Azure Monitor 메트릭 데이터베이스에 게스트 OS 메트릭 보내기를](collect-custom-metrics-guestos-resource-manager-vm.md)참조 하세요.  |
| 이벤트 허브(영문) | Azure Event Hubs을 사용 하 여 Azure 외부에서 데이터를 전송 합니다. [Event Hubs Azure 진단 데이터 스트리밍을](diagnostics-extension-stream-event-hubs.md) 참조 하세요. |
| Azure Storage Blob | 테이블 외에도 Azure Storage의 blob에 데이터를 씁니다. |
| Application Insights | VM에서 실행 되는 응용 프로그램의 데이터를 수집 하 여 다른 응용 프로그램 모니터링과 통합 Application Insights 합니다. [Application Insights에 진단 데이터 보내기를](diagnostics-extension-to-application-insights.md)참조 하세요. |

또한 저장소에서 Log Analytics 작업 영역으로 WAD 데이터를 수집 하 여이 기능에 대해 Log Analytics 에이전트가 일반적으로 사용 되더라도 Azure Monitor 로그를 사용 하 여 분석할 수 있습니다. 데이터를 Log Analytics 작업 영역으로 직접 전송할 수 있으며 추가 기능을 제공 하는 솔루션과 통찰력을 지원 합니다.  [Azure Storage에서 Azure 진단 로그 수집을](diagnostics-extension-logs.md)참조 하세요. 


### <a name="linux-diagnostics-extension-lad"></a>Linux 진단 확장 (꼬마)
Azure Storage의 테이블에 데이터를 씁니다. 다음 표의 싱크를 지원 합니다.

| 대상 | Description |
|:---|:---|
| 이벤트 허브(영문) | Azure Event Hubs을 사용 하 여 Azure 외부에서 데이터를 전송 합니다. |
| Azure Storage Blob | 테이블 외에도 Azure Storage의 blob에 데이터를 씁니다. |
| Azure Monitor 메트릭 | Telegraf 에이전트를 설치 하 고 추가 합니다. [InfluxData Telegraf agent를 사용 하 여 LINUX VM에 대 한 사용자 지정 메트릭 수집](collect-custom-metrics-linux-telegraf.md)을 참조 하세요.


## <a name="installation-and-configuration"></a>설치 및 구성
진단 확장은 Azure에서 [가상 컴퓨터 확장](../../virtual-machines/extensions/overview.md) 으로 구현 되므로 리소스 관리자 템플릿, POWERSHELL 및 CLI를 사용 하 여 동일한 설치 옵션을 지원 합니다. 가상 머신 확장 설치 및 유지 관리에 대 한 일반적인 내용은 Windows 용 가상 [머신 확장 및 기능](../../virtual-machines/extensions/features-windows.md) 및 [Linux 용 가상 머신](../../virtual-machines/extensions/features-linux.md) 확장 및 기능을 참조 하세요.

가상 컴퓨터 메뉴의 **모니터링** 섹션에 있는 **진단 설정** 아래 Azure Portal에 Windows 및 Linux 진단 확장을 모두 설치 하 고 구성할 수도 있습니다.

Windows 및 Linux 용 진단 확장을 설치 및 구성 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Microsoft Azure 진단 확장 설치 및 구성 (WAD)](diagnostics-extension-windows-install.md)
- [Linux 진단 확장을 사용하여 메트릭 및 로그 모니터링](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>기타 설명서

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure Cloud Service (클래식) 웹 및 작업자 역할
- [클라우드 서비스 모니터링 소개](../../cloud-services/cloud-services-how-to-monitor.md)
- [Azure Cloud Services에서 Azure Diagnostics 사용](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Azure cloud services에 대 한 Application Insights](../app/cloudservices.md)<br>[Azure Diagnostics로 Cloud Services 애플리케이션의 흐름 추적](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [로컬 머신 개발 설정에서의 모니터링 및 진단 서비스](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>다음 단계


* [Azure Diagnostics에서 성능 카운터 사용](../../cloud-services/diagnostics-performance-counters.md)에 대해 알아봅니다.
* 진단을 시작하거나 Azure 스토리지 테이블에서 데이터를 찾는 데 문제가 있는 경우 [Azure Diagnostics 문제 해결](diagnostics-extension-troubleshooting.md)을 참조하세요.

