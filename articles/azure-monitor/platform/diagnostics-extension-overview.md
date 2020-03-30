---
title: Azure 진단 확장 개요
description: 클라우드 서비스, 가상 머신 및 서비스 패브릭에서 디버깅, 성능 측정, 모니터링, 트래픽 분석을 위해 Azure 진단 사용
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672381"
---
# <a name="azure-diagnostics-extension-overview"></a>Azure 진단 확장 개요
Azure 진단 확장은 [Azure 모니터의 에이전트로](agents-overview.md) 가상 컴퓨터를 포함한 Azure 컴퓨팅 리소스의 게스트 운영 체제에서 모니터링 데이터를 수집합니다. 이 문서에서는 지원하는 특정 기능및 설치 및 구성 옵션을 포함하여 Azure 진단 확장에 대한 개요를 제공합니다. 

> [!NOTE]
> Azure 진단 확장은 컴퓨팅 리소스의 게스트 운영 체제에서 모니터링 데이터를 수집하는 데 사용할 수 있는 에이전트 중 하나입니다. 다양한 에이전트에 대한 설명과 요구 사항에 적합한 에이전트 선택에 대한 지침은 [Azure Monitor 에이전트 개요를](agents-overview.md) 참조하십시오.

## <a name="comparison-to-log-analytics-agent"></a>로그 분석 에이전트와 비교
Azure Monitor의 로그 분석 에이전트를 사용하여 가상 시스템의 게스트 운영 체제에서 모니터링 데이터를 수집할 수도 있습니다. 요구 사항에 따라 둘 중 하나 또는 둘 다를 사용하도록 선택할 수 있습니다. Azure Monitor 에이전트에 대한 자세한 비교는 [Azure Monitor 에이전트 개요를](agents-overview.md) 참조하십시오. 

고려해야 할 주요 차이점은 다음과 같습니다.

- Azure 진단 확장은 Azure 가상 컴퓨터에서만 사용할 수 있습니다. 로그 분석 에이전트는 Azure, 다른 클라우드 및 온-프레미스의 가상 컴퓨터에서 사용할 수 있습니다.
- Azure 진단 확장은 Azure 저장소, Azure 모니터 메트릭(Windows만) 및 이벤트 허브로 데이터를 보냅니다. [Azure Monitor Metrics](data-platform-metrics.md) 로그 분석 에이전트는 [Azure 모니터 로그에 데이터를 수집합니다.](data-platform-logs.md)
- 로그 분석 에이전트는 [솔루션,](../monitor-reference.md#insights-and-core-solutions) [VM용 Azure 모니터](../insights/vminsights-overview.md)및 Azure 보안 [센터와](/azure/security-center/)같은 기타 서비스에 필요합니다.

## <a name="costs"></a>비용
Azure 진단 확장에 대한 비용은 없지만 수집된 데이터에 대한 요금이 발생할 수 있습니다. 데이터를 수집하는 대상에 대한 [Azure 모니터 가격을](https://azure.microsoft.com/pricing/details/monitor/) 확인합니다.

## <a name="data-collected"></a>수집되는 데이터
다음 표에는 Windows 및 Linux 진단 확장에서 수집할 수 있는 데이터가 나열되어 있습니다.

### <a name="windows-diagnostics-extension-wad"></a>윈도우 진단 확장 (WAD)

| 데이터 원본 | 설명 |
| --- | --- |
| Windows 이벤트 로그   | Windows 이벤트 로그에서 발생 합니다. |
| 성능 카운터 | 운영 체제 및 워크로드의 다양한 측면의 성능을 측정하는 수치 값입니다. |
| IIS 로그             | 게스트 운영 체제에서 실행되는 IIS 웹 사이트의 사용 정보입니다. |
| 애플리케이션 로그 전송 사용     | 응용 프로그램에서 작성한 메시지를 추적합니다. |
| .NET EventSource 로그 |.NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 클래스를 사용하여 이벤트를 작성하는 코드 |
| [매니페스트 기반 ETW 로그](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |모든 프로세스에서 생성된 Windows 이벤트에 대한 이벤트 추적. |
| 크래시 덤프(로그)   | 응용 프로그램이 충돌하는 경우 프로세스 상태에 대한 정보입니다. |
| 파일 기반 로그    | 애플리케이션 또는 서비스에서 생성된 로그입니다. |
| 에이전트 진단 로그 | Azure 진단 자체에 대한 정보입니다. |


### <a name="linux-diagnostics-extension-lad"></a>리눅스 진단 확장 (LAD)

| 데이터 원본 | 설명 |
| --- | --- |
| syslog | Linux 이벤트 로깅 시스템으로 전송된 이벤트입니다.   |
| 성능 카운터  | 운영 체제 및 워크로드의 다양한 측면의 성능을 측정하는 수치 값입니다. |
| 로그 파일 | 파일 기반 로그로 전송된 항목입니다.  |

## <a name="data-destinations"></a>데이터 대상
Windows 및 Linux 모두에 대한 Azure 진단 확장은 항상 Azure 저장소 계정으로 데이터를 수집합니다. [설치 및 Windows Azure 진단 확장(WAD) 및](diagnostics-extension-windows-install.md) Linux 진단 확장 을 사용하여 이 데이터가 수집되는 특정 테이블 및 Blob 목록에 대한 메트릭 및 [로그를 모니터링합니다.](../../virtual-machines/extensions/diagnostics-linux.md)

다른 추가 대상으로 데이터를 보내도록 하나 이상의 *데이터 싱크를* 구성합니다. 다음 섹션에서는 Windows 및 Linux 진단 확장에 사용할 수 있는 싱크를 나열합니다.

### <a name="windows-diagnostics-extension-wad"></a>윈도우 진단 확장 (WAD)

| 대상 | 설명 |
|:---|:---|
| Azure 모니터 메트릭 | 성능 데이터를 Azure 모니터 메트릭으로 수집합니다. [Azure 모니터 메트릭 데이터베이스로 게스트 OS 메트릭 보내기](collect-custom-metrics-guestos-resource-manager-vm.md)를 참조하십시오.  |
| 이벤트 허브(영문) | Azure 이벤트 허브를 사용하여 Azure 외부에서 데이터를 보냅니다. [이벤트 허브로 Azure 진단 데이터 스트리밍](diagnostics-extension-stream-event-hubs.md) 참조 |
| Azure Storage Blob | 테이블 외에 Azure 저장소의 Blob에 데이터를 씁니다. |
| 애플리케이션 정보 | VM에서 실행 중인 응용 프로그램에서 응용 프로그램 인사이트까지 데이터를 수집하여 다른 애플리케이션 모니터링과 통합합니다. [응용 프로그램 인사이트로 진단 데이터 보내기](diagnostics-extension-to-application-insights.md)를 참조하십시오. |

Log Analytics 에이전트는 일반적으로 이 기능에 사용되지만 저장소에서 로그 분석 작업 영역으로 WAD 데이터를 수집하여 Azure Monitor Logs로 분석할 수도 있습니다. Log Analytics 작업 영역으로 직접 데이터를 전송할 수 있으며 추가 기능을 제공하는 솔루션 및 통찰력을 지원합니다.  [Azure 저장소에서 Azure 진단 로그 수집을](diagnostics-extension-logs.md)참조하십시오. 


### <a name="linux-diagnostics-extension-lad"></a>리눅스 진단 확장 (LAD)
LAD는 Azure 저장소의 테이블에 데이터를 씁니다. 다음 표의 싱크를 지원합니다.

| 대상 | 설명 |
|:---|:---|
| 이벤트 허브(영문) | Azure 이벤트 허브를 사용하여 Azure 외부에서 데이터를 보냅니다. |
| Azure Storage Blob | 테이블 외에 Azure 저장소의 Blob에 데이터를 씁니다. |
| Azure 모니터 메트릭 | LAD 이외에 텔레그라프 에이전트를 설치합니다. [InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집을](collect-custom-metrics-linux-telegraf.md)참조하십시오.


## <a name="installation-and-configuration"></a>설치 및 구성
진단 확장은 Azure에서 [가상 시스템 확장으로](../../virtual-machines/extensions/overview.md) 구현되므로 리소스 관리자 템플릿, PowerShell 및 CLI를 사용하여 동일한 설치 옵션을 지원합니다. 가상 컴퓨터 확장 설치 및 유지 관리에 대한 일반적인 세부 정보는 Windows 및 가상 컴퓨터 [확장 및 Linux용 가상 컴퓨터 확장 및 기능에 대한](../../virtual-machines/extensions/features-linux.md) 가상 컴퓨터 확장 및 [기능을](../../virtual-machines/extensions/features-windows.md) 참조하십시오.

가상 컴퓨터 메뉴의 **모니터링** 섹션에서 **진단 설정에서** Azure 포털에서 Windows 및 Linux 진단 확장을 모두 설치하고 구성할 수도 있습니다.

Windows 및 Linux용 진단 확장 의 설치 및 구성에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [설치 및 Windows Azure 진단 확장 (WAD) 구성](diagnostics-extension-windows-install.md)
- [Linux 진단 확장을 사용하여 메트릭 및 로그 모니터링](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>기타 설명서

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure 클라우드 서비스(클래식) 웹 및 작업자 역할
- [클라우드 서비스 모니터링 소개](../../cloud-services/cloud-services-how-to-monitor.md)
- [Azure Cloud Services에서 Azure Diagnostics 사용](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Azure 클라우드 서비스에 대한 애플리케이션 인사이트](../app/cloudservices.md)<br>[Azure Diagnostics로 Cloud Services 애플리케이션의 흐름 추적](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [로컬 머신 개발 설정에서의 모니터링 및 진단 서비스](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>다음 단계


* [Azure Diagnostics에서 성능 카운터 사용](../../cloud-services/diagnostics-performance-counters.md)에 대해 알아봅니다.
* 진단을 시작하거나 Azure 스토리지 테이블에서 데이터를 찾는 데 문제가 있는 경우 [Azure Diagnostics 문제 해결](diagnostics-extension-troubleshooting.md)을 참조하세요.

