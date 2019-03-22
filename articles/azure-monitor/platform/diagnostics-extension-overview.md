---
title: Azure 진단 확장 개요
description: 클라우드 서비스, 가상 머신 및 서비스 패브릭에서 디버깅, 성능 측정, 모니터링, 트래픽 분석을 위해 Azure 진단 사용
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 8a287f118c126967d2cf8cad77a434cfecc098eb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078542"
---
# <a name="what-is-azure-diagnostics-extension"></a>Azure 진단 확장이란?
Azure 진단 확장은 배포된 애플리케이션에서 진단 데이터를 수집할 수 있도록 하는 Azure 내의 에이전트입니다. 다양한 원본에서 진단 확장을 사용할 수 있습니다. 현재 Azure Cloud Service(클래식) 웹 및 작업자 역할, Virtual Machines, Virtual Machine Scale Sets 및 Service Fabric이 지원됩니다. 다른 Azure 서비스에는 여러 진단 메서드가 있습니다. [Azure의 모니터링 개요](../../azure-monitor/overview.md)를 참조하세요.

## <a name="linux-agent"></a>Linux 에이전트
[Linux 버전의 확장](../../virtual-machines/extensions/diagnostics-linux.md)은 Linux를 실행하는 Virtual Machines에 사용할 수 있습니다. 수집되는 통계 및 동작은 Windows 버전에 따라 다릅니다.

## <a name="data-you-can-collect"></a>수집할 수 있는 데이터
Azure 진단 확장은 다음과 같은 유형의 데이터를 수집할 수 있습니다.

| 데이터 원본 | 설명 |
| --- | --- |
| 성능 카운터 메트릭 |운영 체제 및 사용자 지정 성능 카운터 |
| 애플리케이션 로그 |애플리케이션에서 작성한 메시지 추적 |
| Windows 이벤트 로그 |Windows 이벤트 로깅 시스템으로 전송된 정보 |
| .NET EventSource 로그 |.NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 클래스를 사용하여 이벤트를 작성하는 코드 |
| IIS 로그 |IIS 웹 사이트에 대한 정보 |
| [매니페스트 기반 ETW 로그](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |모든 프로세스에서 생성된 ETW(Windows용 이벤트 추적) 이벤트.(1) |
| 크래시 덤프(로그) |애플리케이션 작동이 중단된 경우 프로세스 상태에 대한 정보 |
| 사용자 지정 오류 로그 |애플리케이션 또는 서비스에서 생성한 로그 |
| Azure 진단 인프라 로그 |Azure 진단 자체에 대한 정보 |

(1) ETW 공급자 목록을 가져오려면 정보를 수집할 머신의 콘솔 창에서 `c:\Windows\System32\logman.exe query providers`를 실행합니다.

## <a name="data-storage"></a>데이터 저장소
확장은 해당 데이터를 지정한 [Azure Storage 계정](diagnostics-extension-to-storage.md)에 저장합니다.

이 데이터를 [Application Insights](../../azure-monitor/app/cloudservices.md)에 보낼 수도 있습니다. 

또 다른 옵션은 [이벤트 허브](../../event-hubs/event-hubs-about.md)로 스트리밍하는 것입니다. 그런 다음, 이벤트 허브를 통해 데이터를 비 Azure 모니터링 서비스에 보낼 수 있습니다.

Azure Monitor 메트릭 시계열 데이터베이스에 데이터를 보낼 수도 있습니다. 지금은 성능 카운터에만 이 싱크가 적용됩니다. 싱크를 통해 성능 카운터를 사용자 지정 메트릭으로 보낼 수 있습니다. 이 기능은 미리 보기 상태입니다. Azure Monitor 싱크는 다음을 지원합니다.
* [Azure Monitor 메트릭 API](https://docs.microsoft.com/rest/api/monitor/)를 통해 Azure Monitor로 보낸 모든 성능 카운터 검색
* Azure Monitor의 [메트릭 경고](../../azure-monitor/platform/alerts-overview.md)를 통해 Azure Monitor로 보낸 모든 성능 카운터에 대해 경고
* 성능 카운터의 와일드카드 연산자를 메트릭에 대한 “인스턴스” 차원으로 처리.  예를 들어 “LogicalDisk(\*)/DiskWrites/sec” 카운터를 수집한 경우 “인스턴스” 차원을 기준으로 필터링 및 분할하여 VM의 각 논리 디스크(C:)에 대해 Disk Writes/sec를 그리거나 경고할 수 있습니다.

이 싱크를 구성하는 방법을 자세히 알아보려면 [Azure 진단 스키마 문서](diagnostics-extension-schema-1dot3.md)를 참조하세요.

## <a name="costs"></a>비용
위의 옵션 중 각 비용이 발생할 수 있습니다. 예기치 않은 청구서가 발생 하지 않도록 조사 해야 합니다.  Application Insights에서 이벤트 허브와 Azure Storage가 수집을 사용 하 여 연결 된 별도 비용 및 시간으로 저장 합니다. 특히 Azure Storage는 데이터가 영구적으로 비용을 절감 하려면 특정 시간 기간 후 오래 된 데이터를 제거 해야 할 수 있습니다.    

## <a name="versioning-and-configuration-schema"></a>버전 관리 및 구성 스키마
[Azure 진단 버전 기록 및 스키마](diagnostics-extension-schema.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계
진단을 수집할 서비스를 선택하고 다음 문서를 사용하여 시작합니다. 특정 작업에 대한 참조로 일반 Azure 진단 링크를 사용합니다.

## <a name="cloud-services-using-azure-diagnostics"></a>Azure 진단을 사용하는 Cloud Services
* Visual Studio를 사용하는 경우 시작하려면 [Visual Studio를 사용하여 Cloud Services 애플리케이션 추적](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)을 참조하세요. 그렇지 않은 경우 다음을 참조하세요.
* [Azure 진단을 사용하여 클라우드 서비스를 모니터링하는 방법](../../cloud-services/cloud-services-how-to-monitor.md)
* [Cloud Services 애플리케이션에서 Azure 진단 설정](../../cloud-services/cloud-services-dotnet-diagnostics.md)

고급 항목의 경우 다음을 참조하세요.

* [Cloud Services용 Application Insights에서 Azure 진단 사용](../../azure-monitor/app/cloudservices.md)
* [Azure 진단으로 Cloud Services 애플리케이션의 흐름 추적](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [PowerShell을 사용하여 Cloud Services에 진단 설정](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Visual Studio를 사용하는 경우 시작하려면 [Visual Studio를 사용하여 Azure Virtual Machines 추적](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)을 참조하세요. 그렇지 않은 경우 다음을 참조하세요.
* [Azure Virtual Machine에서 Azure 진단 설정](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

고급 항목의 경우 다음을 참조하세요.

* [PowerShell을 사용하여 Azure Virtual Machines에서 진단 설정](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단 기능으로 Windows 가상 머신 만들기](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
[서비스 패브릭 애플리케이션 모니터링](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)에서 시작합니다. 이 문서를 사용하면 왼쪽의 탐색 트리에서 다른 여러 서비스 패브릭 진단 문서를 사용할 수 있습니다.

## <a name="general-articles"></a>일반 문서
* [Azure 진단에서 성능 카운터 사용](../../cloud-services/diagnostics-performance-counters.md)에 대해 알아봅니다.
* 진단을 시작하거나 Azure Storage 테이블에서 데이터를 찾는 데 문제가 있는 경우 [Azure 진단 문제 해결](diagnostics-extension-troubleshooting.md)을 참조하세요.

