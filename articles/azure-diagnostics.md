<properties
	pageTitle="Azure 진단 개요"
	description="클라우드 서비스, 가상 컴퓨터 및 서비스 패브릭에서 디버깅, 성능 측정, 모니터링, 트래픽 분석을 위해 Azure 진단 사용"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/02/2016"
	ms.author="robb"/>


# Microsoft Azure 진단이란?


Azure 진단은 배포된 응용 프로그램에서 진단 데이터를 수집할 수 있도록 하는 Azure 내 기능입니다. 다양한 원본에서 진단 확장을 사용할 수 있습니다. Azure 클라우드 서비스 웹 및 작업자 역할, Microsoft Windows 및 서비스 패브릭을 실행하는 Azure 가상 컴퓨터에서 현재 지원되고 있습니다. 다른 Azure 서비스에는 별도의 자체 진단이 있습니다.

## 수집할 수 있는 데이터

Azure 진단에서는 다음과 같은 유형의 데이터를 수집할 수 있습니다.

데이터 원본|설명
---|---
성능 카운터 | 운영 체제 및 사용자 지정 성능 카운터
응용 프로그램 로그 | 응용 프로그램에서 작성한 메시지 추적
Windows 이벤트 로그 | Windows 이벤트 로깅 시스템으로 전송된 정보
.NET 이벤트 원본 | .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 클래스를 사용하여 이벤트를 작성하는 코드
IIS 로그 | IIS 웹 사이트에 대한 정보
매니페스트 기반 ETW | 모든 프로세스에서 생성된 Windows 이벤트용 이벤트 추적
크래시 덤프 | 응용 프로그램 크래시가 발생할 경우의 프로세스 상태에 대한 정보
사용자 지정 오류 로그 | 응용 프로그램 또는 서비스에서 생성한 로그
Azure 진단 인프라 로그|진단 자체에 대한 정보입니다.

Azure 진단 확장은 이 데이터를 Azure 저장소 계정에 전송하거나 [Application Insights](./application-insights/app-insights-cloudservices.md)와 같은 서비스에 보낼 수 있습니다. 디버깅 및 문제 해결, 성능 측정, 리소스 사용 모니터링, 트래픽 분석 및 용량 계획, 감사 등에 데이터를 사용할 수 있습니다.


## 버전 관리
[Azure Diagnostics Versioning History(Azure 진단 버전 관리 기록)](azure-diagnostics-versioning-history.md)를 참조하세요.

## 다음 단계
진단을 수집할 서비스를 선택하고 다음 문서를 사용하여 시작합니다. 특정 작업에 대한 참조로 일반 Azure 진단 링크를 사용합니다.

## 웹앱
웹앱에서는 Azure 진단을 사용하지 마세요. [웹앱](./app-service-web/web-sites-enable-diagnostic-log.md)에서 동일한 정보 찾기

## Azure 진단을 사용하는 클라우드 서비스
- Visual Studio를 사용하는 경우 시작하려면 [Visual Studio를 사용하여 클라우드 서비스 응용 프로그램 추적](./vs-azure-tools-debug-cloud-services-virtual-machines.md)을 참조하세요. 그렇지 않은 경우 다음을 참조하세요.
- [Azure 진단을 사용하여 클라우드 서비스를 모니터링하는 방법](./cloud-services/cloud-services-how-to-monitor.md)
- [클라우드 서비스 응용 프로그램에서 Azure 진단 설정](./cloud-services/cloud-services-dotnet-diagnostics.md)

고급 항목의 경우 다음을 참조하세요.

- [클라우드 서비스용 Application Insights에서 Azure 진단 사용](./application-insights/app-insights-cloudservices.md)
- [Azure 진단으로 클라우드 서비스 응용 프로그램의 흐름 추적](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [PowerShell을 사용하여 클라우드 서비스에 진단 설정](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## Azure 진단을 사용하는 가상 컴퓨터
- Visual Studio를 사용하는 경우 시작하려면 [Visual Studio를 사용하여 Azure 가상 컴퓨터 추적](./vs-azure-tools-debug-cloud-services-virtual-machines.md)을 참조하세요. 그렇지 않은 경우 다음을 참조하세요.
- [Azure 가상 컴퓨터에서 Azure 진단 설정](./virtual-machines-dotnet-diagnostics.md)

고급 항목의 경우 다음을 참조하세요.

- [PowerShell을 사용하여 Azure 가상 컴퓨터에서 진단 설정](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Azure 리소스 관리자를 사용하여 Windows 가상 컴퓨터와 모니터링 및 진단 기능 만들기](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## Azure 진단을 사용하는 서비스 패브릭
[서비스 패브릭 응용 프로그램 모니터링](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)에서 시작합니다. 이 문서를 사용하면 왼쪽의 탐색 트리에서 다른 여러 서비스 패브릭 진단 문서를 사용할 수 있습니다.

## 일반 Azure 진단 문서
- [Azure 진단 스키마 구성](https://msdn.microsoft.com/library/azure/mt634524.aspx) - 스키마 파일을 변경하여 진단 데이터를 수집하고 경로를 지정하는 방법에 대해 알아봅니다. Visual Studio를 사용하여 스키마 파일을 변경할 수도 있습니다.
- [Azure 저장소에 Azure 진단 데이터가 저장되는 방법](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - 진단 데이터가 작성되는 Blob 및 테이블의 이름을 알아봅니다.
- [Azure 진단에서 성능 카운터 사용](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md)에 대해 알아봅니다.
- [Azure 진단 정보를 Application Insights에 라우트](./azure-diagnostics-configure-applicationinsights.md)하는 방법을 알아봅니다.
- 진단을 시작하거나 Azure 저장소 테이블에서 데이터를 찾는 데 문제가 있는 경우 [Azure 진단 문제 해결](./azure-diagnostics-troubleshooting.md)을 참조하세요.

<!---HONumber=AcomDC_0608_2016-->