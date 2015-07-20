<properties
   pageTitle="서비스 패브릭 서비스 진단 및 문제 해결"
   description="서비스 패브릭 서비스를 진단, 모니터링, 문제 해결하는 데 도움을 주는 개념 정보 및 자습서"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/21/2015"
   ms.author="ryanwi"/>

# 서비스 패브릭 서비스 진단 및 모니터링
모니터링, 검색, 진단 및 문제 해결은 사용자 환경에 최소화하면서 서비스를 계속할 수 있게 해 줍니다. 자세한 내용은 다음을 참조하십시오.

- [로컬에서 서비스를 모니터링 및 진단하는 방법](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [서비스 패브릭 응용 프로그램에 대한 Application Insights 설정](service-fabric-diagnostics-application-insights-setup.md)
- [응용 프로그램 업그레이드 오류 문제 해결](service-fabric-application-upgrade-troubleshooting.md)
- [실패한 모니터링되는 응용 프로그램 업그레이드 문제 해결](../service-fabric-application-monitored-upgrade-troubleshooting.md)
- [신뢰할 수 있는 행위자에 대한 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)
- [신뢰할 수 있는 서비스에 대한 진단 및 성능 모니터링](service-fabric-reliable-services-diagnostics.md)

## 클러스터 문제 해결
다음 정보는 로컬 개발 클러스터의 문제를 해결하는 데 도움이 됩니다.

- [로컬 개발 클러스터 설정 문제 해결](service-fabric-troubleshoot-local-cluster-setup.md)

## 상태 모델
서비스 패브릭은 서비스 패브릭 엔터티에 대한 풍부하고 유연하며 확장 가능한 기능을 제공하는 상태 모델을 소개합니다. 서비스 패브릭 구성 요소는 모든 엔터티에 대해 즉각적으로 상태를 보고합니다. 사용자 서비스는 자체적으로 및 클러스터의 다른 엔터티에 보고된 상태 데이터를 자체 논리에 지정된 정보로 보강할 수 있습니다. 자세한 내용은 다음을 참조하십시오.

- [서비스 패브릭 상태 모니터링 소개](service-fabric-health-introduction.md)
- [서비스 패브릭 상태 보고 보는 방법](service-fabric-view-entities-aggregated-health.md)
- [문제 해결을 위한 시스템 상태 보고서 사용](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [사용자 지정 서비스 패브릭 상태 보고서 추가](service-fabric-report-health.md)
 

<!---HONumber=July15_HO2-->