<properties urlDisplayName="Performance Profiling" pageTitle="Azure에서 성능 카운터 사용(.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Azure 응용 프로그램에서 성능 카운터를 사용하도록 설정하고 성능 카운터에서 데이터를 수집하는 방법에 대해 알아봅니다. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Azure에서 성능 카운터 사용" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Azure에서 성능 카운터 사용

Azure 응용 프로그램에서 성능 카운터를 사용하여
시스템 병목 상태, 미세 조정 시스템, 응용 프로그램 성능을 확인하는 데
도움을 줄 수 있는 데이터를 수집할 수 있습니다. Windows Server 2008, Windows Server 2012, IIS 및 ASP.NET에서 사용할 수 있는 성능 카운터를 수집하여 Azure 응용 프로그램의 상태를 확인하는 데 사용할 수 있습니다.

배포 전이나 런타임에 Azure SDK 2.0 이상 버전을 사용하여 Visual Studio 2012 또는 Visual Studio 2013 내에서 성능 카운터를 구성할 수 있습니다. 자세한 내용은 [Azure 진단 구성][Azure 진단 구성](영문)을 참조하세요. 응용 프로그램에서 수동으로 성능 카운터를 구성하는 방법에 대한 자세한 내용은 [성능 카운터를 구성하는 방법][성능 카운터를 구성하는 방법]을 참조하세요.

로깅 및 추적 전략 만들기 및 진단과 기타 기술을 사용한 문제 해결에 대한 더 많은 세부 지침은 [Azure 응용 프로그램 개발 문제 해결 모범 사례][Azure 응용 프로그램 개발 문제 해결 모범 사례](영문)를 참조하세요.

## <a name="nextsteps"> </a>다음 단계

다음 링크를 따라 좀 더 복잡한 문제 해결 시나리오를 구현하는 방법을 알아보세요.

-   [클라우드 서비스의 성능 테스트][클라우드 서비스의 성능 테스트]
-   [Azure 응용 프로그램 개발 문제 해결 모범 사례(영문)][Azure 응용 프로그램 개발 문제 해결 모범 사례]
-   [클라우드 서비스를 모니터링하는 방법][클라우드 서비스를 모니터링하는 방법]
-   [자동 크기 조정 응용 프로그램 블록을 사용하는 방법][자동 크기 조정 응용 프로그램 블록을 사용하는 방법]
-   [탄력적이고 복원력이 뛰어난 클라우드 앱 빌드(영문)][탄력적이고 복원력이 뛰어난 클라우드 앱 빌드(영문)]

## <a name="additional"> </a>추가 리소스

-   [Azure에서 진단 사용][Azure에서 진단 사용]
-   [Azure 진단을 사용하여 로깅 데이터 수집][Azure 진단을 사용하여 로깅 데이터 수집]
-   [Azure 응용 프로그램 디버깅][Azure 응용 프로그램 디버깅]

  [Azure 진단 구성]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn186185.aspx
  [성능 카운터를 구성하는 방법]: http://msdn.microsoft.com/ko-kr/library/azure/dn535595.aspx
  [Azure 응용 프로그램 개발 문제 해결 모범 사례]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh771389.aspx
  [클라우드 서비스의 성능 테스트]: http://msdn.microsoft.com/ko-kr/library/azure/hh369930.aspx
  [클라우드 서비스를 모니터링하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/cloud-services-how-to-monitor/
  [자동 크기 조정 응용 프로그램 블록을 사용하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/cloud-services-dotnet-autoscaling-application-block/
  [탄력적이고 복원력이 뛰어난 클라우드 앱 빌드(영문)]: http://msdn.microsoft.com/ko-kr/library/hh680949(PandP.50).aspx
  [Azure에서 진단 사용]: http://azure.microsoft.com/ko-kr/documentation/articles/cloud-services-dotnet-diagnostics/
  [Azure 진단을 사용하여 로깅 데이터 수집]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433048.aspx
  [Azure 응용 프로그램 디버깅]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee405479.aspx
