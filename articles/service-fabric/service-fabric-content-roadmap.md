---
title: "Azure Service Fabric 살펴보기 | Microsoft Azure"
description: "응용 프로그램이 여러 마이크로 서비스로 구성되어 확장성과 복원력을 제공하는 Service Fabric의 개요 및 요약 안내입니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 8e7c14d9eeeab43d596eec99cb380f06cf9c39b3


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Service Fabric에 대해 궁금하신가요?
이 학습 로드맵은 Service Fabric에서 확장 가능하고 안정적이며 관리하기 쉬운 응용 프로그램을 개발하는 데 도움이 됩니다.

## <a name="the-five-minute-overview"></a>5분 개요
Azure Service Fabric은 확장성과 안정성이 뛰어난 마이크로 서비스를 간편하게 패키지, 배포 및 관리하게 해주며, 클라우드 응용 프로그램 개발 및 관리에서 중요한 문제를 해결하는 분산 시스템 플랫폼입니다. 서비스 패브릭을 사용하면 개발자와 관리자가 복잡한 인프라 문제를 해결하기 위한 작업에 시간을 소비하는 대신, 업무 수행에 필수적인 까다로운 작업을 확장 가능하고 신뢰할 수 있으며 관리가 가능하도록 구현하는 데 집중할 수 있습니다. 서비스 패브릭은 엔터프라이즈급 계층&1; 클라우드 규모의 응용 프로그램을 빌드 및 관리하기 위한 차세대 미들웨어 플랫폼을 나타냅니다.  

이 짧은 Channel9 비디오에서는 Service Fabric 및 마이크로 서비스를 소개합니다.<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>자세한 개요
Service Fabric을 사용하면 컴퓨터의 공유 풀(클러스터라고 함)에서 매우 높은 밀도로 실행되는 마이크로 서비스로 구성된 확장성 있고 신뢰할 수 있는 응용 프로그램을 빌드하고 관리할 수 있습니다. 분산되고 확장 가능한 상태 비저장 및 상태 저장 마이크로 서비스를 빌드하는 정교한 런타임을 제공합니다. 또한 배포된 응용 프로그램을 프로비전, 배포, 모니터링, 업그레이드/패치 및 삭제하는 포괄적인 응용 프로그램 관리 기능을 제공합니다.  자세한 내용은 [Service Fabric 개요](service-fabric-overview.md)를 참조하세요.

마이크로 서비스 설계 방법이 필요한 이유는 무엇일까요? 모든 응용 프로그램은 시간에 따라 진화합니다. 성공적인 응용 프로그램은 사람들에게 유용하게 되어 진화합니다. 현재의 요구 사항에 대해 얼마나 알고 있으며 그러한 요구 사항이 향후 어떻게 될까요? 응용 프로그램이 향후 재설계될 수 있다는 점을 알고 있는 상황에서 개념 증명의 형태로 간단한 앱을 가지고 있으면 추진 동력이 되기도 합니다. 반면 회사에서 클라우드를 위한 구축을 이야기할 때 기대하는 것은 성장과 사용입니다. 이러한 성장과 확장에서의 문제는 예측할 수 없습니다. 예측할 수 없는 성장 및 사용에 맞게 앱이 조정될 수 있다는 사실을 인지하는 동시에 신속하게 프로토타입을 마련하고자 합니다.  [마이크로 서비스란?](service-fabric-overview-microservices.md)에서는 어떻게 마이크로 서비스 설계 방법이 이러한 과제에 부응하며, 독립적으로 확장/축소, 테스트, 배포 및 관리할 수 있는 마이크로서비스를 만드는 방법에 대해 살펴봅니다.

Service Fabric은 다양한 유형의 비즈니스 응용 프로그램 및 서비스를 쓰고 실행할 수 있도록 하는 안정적이고 유연한 플랫폼을 제공합니다.  또한 기존 응용 프로그램(모든 언어로 작성된)도 실행할 수 있습니다.  이러한 응용 프로그램 및 마이크로 서비스는 상태를 저장하지 않을 수도 있고 상태를 저장할 수도 있으며, 가상 컴퓨터 간에 리소스를 분산하여 효율성을 극대화합니다. 서비스 패브릭의 고유한 아키텍처는 응용 프로그램에서 거의 실시간으로 데이터 분석, 메모리 내 계산, 병렬 트랜잭션 및 이벤트 처리가 가능합니다. 리소스 요구 사항의 변화에 따라 응용 프로그램을 간단하게 확장 또는 축소(실제 내부 또는 외부)할 수 있습니다. [응용 프로그램 시나리오](service-fabric-application-scenarios.md)에서 만들 수 있는 응용 프로그램 및 서비스의 범주와 고객 사례 연구에 대해 확인해 보세요.

이 긴 Microsoft Virtual Academy 비디오에서는 Service Fabric의 핵심 개념을 설명합니다.<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>첫 번째 앱 시작하기 및 만들기 
Service Fabric SDK 및 도구를 사용하여 Windows, Linux 또는 MacOS 환경에서 앱을 개발하고, 개발한 앱을 Windows 또는 Linux 기반 클러스터에 배포할 수 있습니다.  다음 가이드에서는 몇 분 안에 앱을 배포합니다.  첫 번째 응용 프로그램을 실행한 후 일부 [샘플 앱](http://aka.ms/servicefabricsamples)을 다운로드하여 실행합니다.

### <a name="on-windows"></a>Windows 
서비스 패브릭 SDK는 서비스 패브릭 응용 프로그램을 만들고, 배포하고, 디버그하는 도구를 제공하는 Visual Studio용 추가 기능을 포함합니다. 이 항목에서는 Visual Studio에서 최초 응용 프로그램을 만들어 개발 컴퓨터에서 실행하는 과정을 안내합니다.

[개발 환경 설정](service-fabric-get-started.md)
[첫 번째 앱 만들기(C#)](service-fabric-create-your-first-application-in-visual-studio.md)

이 광범위한 [실습 랩](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx)을 통해 포괄적인 Service Fabric 개발 흐름을 익힙니다.  상태 비저장 서비스를 만들고 모니터링 및 상태 보고서를 구성하고 응용 프로그램 업그레이드를 수행하는 방법을 학습합니다. 

다음 Channel9 비디오에서는 Visual Studio에서 C# 앱을 만드는 과정을 안내합니다.  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>Linux
Service Fabric은 .NET Core 및 Java 모두에서 Linux에 대한 서비스를 구축하는 SDK를 제공합니다. 이 항목에서는 Linux에서 첫 번째 Java 또는 C# 응용 프로그램을 만들어 개발 컴퓨터에서 실행하는 과정을 안내합니다.
[개발 환경 설정](service-fabric-get-started-linux.md)
[첫 번째 앱 만들기(Java)](service-fabric-create-your-first-linux-application-with-java.md)
[첫 번째 앱 만들기(C#)](service-fabric-create-your-first-linux-application-with-csharp.md)

다음 Microsoft Virtual Academy 비디오는 Linux에서 Java 앱을 만드는 과정을 안내합니다.  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>MacOS 
Linux 클러스터에서 실행되도록 MacOS X에서 Service Fabric 응용 프로그램을 빌드할 수 있습니다. 이 문서에서는 개발을 위해 Mac을 설정하는 방법을 설명하고 MacOS에서 첫 번째 Java 응용 프로그램을 만들어 Ubuntu 가상 컴퓨터에서 실행하는 방법을 안내합니다.
[개발 환경 설정](service-fabric-get-started-mac.md)
[첫 번째 앱 만들기(Java)](service-fabric-create-your-first-linux-application-with-java.md)

## <a name="core-concepts"></a>핵심 개념
[Service Fabric 용어](service-fabric-technical-overview.md), [응용 프로그램 모델](service-fabric-application-model.md) 및 [지원되는 프로그래밍 모델](service-fabric-choose-framework.md)은 기초 수준에서 자세한 개념과 설명을 제공합니다.

<table><tr><th>핵심 개념</th><th>설계 시간</th><th>실행 시간</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-app-type-service-type-app-package-and-manifest-service-package-and-manifest"></a>설계 시간: 앱 프로그램 형식, 서비스 형식, 앱 패키지 및 매니페스트, 서비스 패키지 및 매니페스트
응용 프로그램 형식은 서비스 형식 컬렉션에 할당된 이름/버전입니다. ApplicationManifest.xml 파일에 정의되고 응용 프로그램 패키지 디렉터리에 포함된 후 Service Fabric 클러스터의 이미지 저장소에 복사됩니다. 그런 다음 클러스터 내에서 실행되는 이 응용 프로그램 형식으로부터 명명된 응용 프로그램을 만들 수 있습니다. 

서비스 형식은 서비스의 코드 패키지, 데이터 패키지 및 구성 패키지에 할당된 이름/버전입니다. ServiceManifest.xml 파일에 정의되고, 서비스 패키지 디렉터리에 포함되며, 서비스 패키지 디렉터리는 응용 프로그램 패키지의 ApplicationManifest.xml 파일에서 참조됩니다. 클러스터 내에서 명명된 응용 프로그램을 만든 후에 응용 프로그램 형식의 서비스 형식 중 하나에서 명명된 서비스를 만들 수 있습니다. 서비스 형식은 ServiceManifest.xml 파일로 설명되며, 런타임에 로드되는 실행 가능한 코드 서비스 구성 설정과 서비스가 소비하는 정적 데이터로 구성됩니다

![서비스 패브릭 응용 프로그램 유형 및 서비스 유형][cluster-imagestore-apptypes]

응용 프로그램 패키지는 응용 프로그램 형식의 ApplicationManifest.xml 파일이 들어 있는 디스크 디렉터리로, 응용 프로그램 형식을 구성하는 각 서비스 형식에 대해 서비스 패키지를 참조합니다. 예를 들어 전자 메일 응용 프로그램 형식에 대한 응용 프로그램 패키지는 큐 서비스 패키지, 프런트엔드 서비스 패키지 및 데이터베이스 서비스 패키지에 대한 참조를 포함할 수 있습니다. 응용 프로그램 패키지 디렉터리에 있는 파일은 Service Fabric 클러스터의 이미지 저장소에 복사됩니다. 

서비스 패키지는 서비스 형식의 ServiceManifest.xml 파일이 들어 있는 디스크 디렉터리로, 서비스 형식에 대한 코드, 정적 데이터 및 구성 패키지를 참조합니다. 서비스 패키지 디렉터리의 파일은 응용 프로그램 형식의 ApplicationManifest.xml 파일에서 참조합니다. 예를 들어, 서비스 패키지는 데이터베이스 서비스를 구성하는 코드, 정적 데이터 및 구성 패키지를 참조할 수 있습니다.

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>런타임: 클러스터 및 노드, 명명된 앱, 명명된 서비스, 파티션 및 복제본
[Service Fabric 클러스터](service-fabric-deploy-anywhere.md): 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 컴퓨터 집합입니다. 클러스터의 규모를 컴퓨터 수천 대로 확장할 수 있습니다.

클러스터의 일부인 컴퓨터나 VM을 노드라고 합니다. 각 노드는 노드 이름(문자열)에 할당됩니다. 노드는 배치 속성과 같은 특징이 있습니다. 각 컴퓨터 또는 VM은 자동 시작 Windows 서비스인 FabricHost.exe를 갖습니다. 이 서비스는 부팅 시 실행된 다음 Fabric.exe 및 FabricGateway.exe 등의 두 실행 파일을 시작합니다. 이러한 두 실행 파일이 노드를 구성합니다. 개발 또는 테스트 시나리오에서는 Fabric.exe 및 FabricGateway.exe의 여러 인스턴스를 실행하여 단일 컴퓨터나 VM에 여러 노드를 호스팅할 수 있습니다. 

명명된 응용 프로그램은 하나 이상의 특정 기능을 수행하는 명명된 서비스 컬렉션입니다. 서비스는 완전한 독립 실행형 기능을 수행하며(서비스는 다른 서비스와 독립적으로 시작 및 실행 가능) 코드, 구성 및 데이터로 이루어집니다. 응용 프로그램 패키지가 이미지 저장소에 복사된 후에 응용 프로그램 패키지의 응용 프로그램 형식을 지정하여 클러스터 내에서 응용 프로그램의 인스턴스를 만듭니다(이름/버전 사용). 각 응용 프로그램 형식 인스턴스는 *fabric:/MyNamedApp*와 같은 URI 이름이 할당됩니다. 클러스터 내에서 단일 응용 프로그램 형식에서 여러 명명된 응용 프로그램을 만들 수 있습니다. 또한 다른 형식의 응용 프로그램에서 명명된 응용 프로그램을 만들 수 있습니다. 명명된 응용 프로그램은 각각 독립적으로 관리되고 버전이 지정됩니다.

명명된 응용 프로그램을 만든 후에 서비스 형식을 지정하여(이름/버전 사용) 클러스터 내에서 해당 서비스 형식(명명된 서비스)의 인스턴스를 만들 수 있습니다. 각 서비스 형식 인스턴스는 명명된 응용 프로그램의 URI로 범위가 지정된 URI 이름이 할당됩니다. 예를 들어 "MyNamedApp"이라는 응용 프로그램 내에서 "MyDatabase"라는 서비스를 만드는 경우 URI는 *fabric:/MyNamedApp/MyDatabase*와 같습니다. 명명된 응용 프로그램 내에서 하나 이상의 명명된 서비스를 만들 수 있습니다. 명명된 각 서비스는 고유한 파티션 구성표 및 복제본/인스턴스 수를 가질 수 있습니다. 

여기에는 상태 비저장과 상태 저장 등의 두 가지 서비스 형식이 있습니다.  Azure 저장소, Azure SQL Database 또는 Azure DocumentDB와 같은 외부 저장소 서비스에 상태 비저장 서비스의 영구적 상태를 저장합니다. 서비스에 영구 저장소가 없는 경우 상태 비저장 서비스를 사용합니다. Reliable Collection 또는 Reliable Actors 프로그래밍 모델을 통해 서비스의 상태를 관리하기 위해 상태 저장 서비스가 Service Fabric을 사용합니다.  

명명된 서비스를 만들 때 파티션 구성표를 지정합니다. 많은 양의 상태가 있는 서비스는 클러스터의 노드에 분산하는 파티션에 데이터를 분할합니다. 이렇게 하면 명명된 서비스의 상태를 조정할 수 있습니다. 상태 저장 서비스에 복제본이 있는 반면 파티션 내에서 명명된 상태 비저장 서비스에는 인스턴스가 있습니다. 일반적으로 명명된 상태 비저장 서비스는 내부 상태가 없기 때문에 하나의 파티션만을 가질 수 있습니다. 파티션 인스턴스를 가용성에 제공합니다. 인스턴스가 실패하면 다른 인스턴스는 계속 정상적으로 작동하고 서비스 패브릭은 새 인스턴스를 만듭니다. 명명된 상태 저장 서비스는 복제본 내에서 해당 상태를 유지하고 각 파티션은 동기화 상태로 유지된 모든 상태를 가진 고유한 복제를 설정합니다. 복제가 실패하면 서비스 패브릭은 기존 복제본에서 새 복제본을 작성해야 합니다.

다음 다이어그램에서는 응용 프로그램 및 서비스 인스턴스, 파티션, 복제본 간의 관계를 보여 줍니다.

![서비스 내의 파티션 및 복제본][cluster-application-instances]

## <a name="supported-programming-models"></a>지원되는 프로그래밍 모델
서비스 패브릭은 서비스의 작성 및 관리를 위한 여러 방법을 제공합니다. 서비스는 플랫폼의 기능과 응용 프로그램 프레임워크를 완전히 활용하기 위해 사용할 서비스 패브릭 API를 선택할 수 있습니다. 또는 단순히 어떤 언어로 작성되어 컴파일된 실행 파일 프로그램이 서비스가 되고 서비스 패브릭 클러스터에 호스팅될 수 있습니다. 자세한 내용은 [지원되는 프로그래밍 모델](service-fabric-choose-framework.md)을 참조하세요.

### <a name="guest-executables"></a>게스트 실행 파일
[게스트 실행 파일](service-fabric-deploy-existing-app.md)은 어떤 언어로 작성되어 기존 응용 프로그램으로 가져가 Service Fabric 클러스터에서 다른 서비스와 함께 호스팅할 수 있는 임의의 실행 파일입니다. 그러나 게스트 실행 파일은 Service Fabric API와 직접 통합되지 않으므로 사용자 지정 상태 및 로드 보고, 서비스 끝점 등록, 상태 저장 계산 등, 플랫폼이 제공하는 전체 기능을 활용하지는 않습니다.

### <a name="containers"></a>컨테이너
기본적으로 Service Fabric은 이러한 서비스를 프로세스로 배포하고 활성화합니다. Service Fabric도 [컨테이너 이미지](service-fabric-containers-overview.md)에 서비스를 배포할 수 있습니다. 중요한 점은 프로세스의 서비스와 동일한 응용 프로그램의 컨테이너의 서비스를 혼합할 수 있습니다.  Service Fabric은 현재 Linux에서 Docker 컨테이너의 배포를 지원하고 Windows Server 2016에서 Windows Server 컨테이너의 배포를 지원합니다. Service Fabric 응용 프로그램 모델에서 컨테이너는 다수의 서비스 복제본이 배치되는 응용 프로그램 호스트를 나타냅니다.  Service Fabric을 사용하여 컨테이너에서 기존 응용 프로그램, 상태 비저장 서비스 또는 상태 저장 서비스를 배포할 수 있습니다.  

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md)는 Service Fabric 플랫폼과 통합하여 전체 플랫폼 기능을 활용하는 서비스 작성을 위한 간단한 프레임워크입니다. Reliable Services는 웹 서버나 Azure Cloud Services의 작업자 역할 등, 대부분의 서비스 플랫폼과 유사하게 상태 비저장이 될 수 있습니다. 여기서는 상태가 Azure DB나 Azure Table Storage 같은 외부 솔루션에서 유지됩니다. Reliable Services는 Reliable Collections를 사용하여 서비스 자체에 직접 유지되게 상태를 저장할 수도 있습니다. 상태는 복제를 통해 고가용성이 유지되고 분할을 통해 배포되며, 모두 서비스 패브릭에서 자동으로 관리합니다.

### <a name="reliable-actors"></a>Reliable Actors
Reliable Services의 최상위에 구축되는 [Reliable Actor](service-fabric-reliable-actors-introduction.md) 프레임워크는 행위자 설계 패턴을 기준으로 가상 행위자 패턴을 구현하는 응용 프로그램 프레임워크입니다. Reliable Actor 프레임워크는 행위자라고 하는 단일 스레드 실행을 통해 독립적인 계산 단위 및 상태를 사용합니다. Reliable Actor 프레임워크는 행위자와 사전 설정 상태 지속성 및 확장 구성에 대해 기본 포함된 통신을 제공합니다.

## <a name="next-steps"></a>다음 단계
* [Azure에서 클러스터](service-fabric-cluster-creation-via-portal.md)를 만들거나 [Windows에서 독립 실행형 클러스터](service-fabric-cluster-creation-for-windows-server.md)를 만드는 방법을 알아봅니다.
* [Reliable Services](service-fabric-reliable-services-quick-start.md) 또는 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 프로그래밍 모델을 사용하여 서비스를 만들어 봅니다.
* [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에 대해 알아봅니다.
* [응용 프로그램 및 클러스터 상태 검사](service-fabric-health-introduction.md)에 대해 알아봅니다.
* [서비스 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)에 대해 알아봅니다. 
* [Service Fabric 지원 옵션](service-fabric-support.md)에 대해 알아봅니다.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png


<!--HONumber=Jan17_HO4-->


