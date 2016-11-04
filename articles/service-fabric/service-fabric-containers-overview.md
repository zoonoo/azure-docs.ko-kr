---
title: Service Fabric 및 컨테이너 개요 | Microsoft Docs
description: Service Fabric 및 마이크로 서비스 응용 프로그램 배포를 위한 컨테이너 사용에 대한 개요. 이 문서는 컨테이너를 사용하는 방법과 Service Fabric에서 사용할 수 있는 기능에 대한 개요를 제공합니다.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: ''
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: msfussell

---
# <a name="preview:-service-fabric-and-containers"></a>미리 보기: Service Fabric 및 컨테이너
> [!NOTE]
> 이 기능은 Linux용 미리 보기 상태이며 Windows Server에서는 현재 사용할 수 없습니다. Windows Server 2016 GA 후 Service Fabric의 다음 릴리스에서 Windows Server용 미리 보기로 제공된 후에 후속 릴리스에서 지원될 예정입니다.
> 
> 

## <a name="introduction"></a>소개
Service Fabric은 컴퓨터 클러스터에 걸쳐있는 서비스 [오케스트레이터](service-fabric-cluster-resource-manager-introduction.md) 입니다. 서비스는 [Service Fabric 프로그래밍 모델](service-fabric-choose-framework.md) 사용부터 [게스트 실행 파일](service-fabric-deploy-existing-app.md) 배포에 이르는 다양한 방식으로 개발될 수 있습니다. 기본적으로 Service Fabric은 이러한 서비스를 프로세스로 배포하고 활성화합니다. 프로세스는 클러스터 내에서 리소스의 최고밀도 사용과 가장 신속한 활성화를 제공합니다. Service Fabric은 서비스를 컨테이너 이미지로 배포할 수도 있으며 프로세스에 포함된 서비스와 컨테이너에 포함된 서비스를 같은 응용 프로그램에서 함께 혼합할 수 있습니다. 시나리오에 따라 두 가지 환경의 장점을 활용할 수 있습니다.

## <a name="what-are-containers?"></a>컨테이너란?
컨테이너는 캡슐화되어 개별적으로 배포가 가능한 구성 요소이며 운영 체제 수준 가상화를 활용하여 동일한 커널에서 격리된 인스턴스로 실행됩니다. 즉, 각 응용 프로그램, 해당 런타임, 종속성, 시스템 라이브러리는 운영 체제 구성물의 자체적으로 격리된 뷰에 대해 전체, 개인 액세스 권한이 있는 컨테이너 내에서 실행됩니다. 이식성과 더불어 이러한 수준의 보안 및 리소스 격리는 서비스를 프로세스에서 실행하지 않고 Service Fabric에서 컨테이너를 사용하는 주요 장점입니다. 

컨테이너는 응용 프로그램에서 기본 OS를 가상화하는 가상화 기술입니다. 이것은 응용 프로그램을 다양한 격리 수준에서 실행하도록 변경이 불가능한 환경을 제공합니다. 컨테이너는 커널 바로 위에서 실행되며 파일 시스템 및 기타 리소스에 대한 격리된 뷰를 갖습니다. VM에 비해 컨테이너는 다음과 같은 이점이 있습니다.

1. **작은 크기**: 컨테이너는 각 계층에 단일 저장소 공간과 작은 델타를 사용하기 때문에 보다 효율적입니다.
2. **빠른 부팅 시간**: 컨테이너는 OS를 부팅할 필요가 없기 때문에 VM보다 훨씬 빠르게, 보통 몇 초 만에 가동하여 사용할 수 있습니다.
3. **이식성**: 컨테이너화된 응용 프로그램 이미지는 클라우드나 온-프레미스에서 VM 내부 또는 실제 컴퓨터에서 바로 실행하도록 이식될 수 있습니다.
4. **리소스 관리를 제공** 합니다.

## <a name="container-types"></a>컨테이너 유형
Service Fabric은 다음과 같은 유형의 컨테이너를 지원합니다.

### <a name="docker-containers-on-linux"></a>Linux의 Docker 컨테이너
Docker는 Linux 커널 컨테이너를 기반으로 컨테이너를 만들고 관리하도록 높은 수준의 API를 제공하고 컨테이너 이미지 저장 및 검색용 중앙 리포지토리로 Docker Hub를 제공합니다. 

### <a name="windows-server-containers"></a>Windows Server 컨테이너
Windows Server 2016은 제공되는 격리 수준이 다른 두 가지 유형의 컨테이너를 제공합니다. Windows Server 컨테이너는 네임스페이스와 파일 시스템이 있다는 점에서 Docker 컨테이너와 유사하지만 컨테이너가 실행되는 호스트와 커널을 공유합니다. Linux에서, 이러한 격리는 일반적으로 cgroups 및 네임스페이스에 의해 제공되었으며 Windows Server 컨테이너도 유사하게 작동합니다. 

Windows Hyper-V 컨테이너는 각 컨테이너가 서로간에 또는 호스트와 OS 커널을 공유하지 않기 때문에 높은 수준의 격리 및 보안을 제공합니다. 이렇게 보안 격리 수준이 높은 Hyper-V 컨테이너는 특히 적대적인 다중 테넌트 시나리오를 대상으로 합니다.

다음 그림은 OS에서 사용할 수 있는 다양한 유형의 가상화 및 격리 수준을 보여줍니다.
![Service Fabric 플랫폼][Image1]

## <a name="scenarios-for-using-containers"></a>컨테이너 사용 시나리오
컨테이너를 사용해야 하는 일반적인 사례는 다음과 같습니다.

1. **IIS 전환**. 기존 ASP.NET MVC 앱을 ASP.NET 코어로 마이그레이션하지 않고 계속 사용하려는 경우, 컨테이너에 넣습니다. 이러한 ASP.NET MVC 앱은 IIS에 종속됩니다. 이것을 미리 만든 IIS 이미지의 컨테이너 이미지로 패키징하고 Service Fabric을 사용하여 배포할 수 있습니다. IIS 이미지를 만드는 방법은 [Windows Server의 컨테이너 이미지](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) 를 참조하세요.
2. **컨테이너 및 Service Fabric 마이크로 서비스 혼합**. 기존 컨테이너 이미지를 응용 프로그램의 일부에 사용합니다. 예를 들어, 응용 프로그램의 웹 프런트 엔드에 [NGINX 컨테이너](https://hub.docker.com/_/nginx/) 를 사용하고 Reliable Services를 사용하여 빌드한 상태 저장 서비스를 보다 집약적인 백 엔드 계산에 사용합니다. 이런 시나리오의 사례로는 게임 응용 프로그램이 있습니다.
3. **"노이즈가 많은 주변 환경" 서비스의 영향 줄이기**. 컨테이너의 리소스 관리 기능은 서비스가 호스트에서 사용하는 리소스를 제한할 수 있도록 합니다. 다수의 리소스를 소비하기 때문에 다른 서비스(예: 연산과 같은 장기 실행 쿼리)의 성능에 영향을 미칠 수 있는 서비스가 있다면, 리소스 관리를 사용하여 이 서비스를 컨테이너에 넣는 것이 좋습니다.

## <a name="service-fabric-support-for-containers"></a>컨테이너에 대한 Service Fabric 지원
Service Fabric은 현재 Linux에서 Docker 컨테이너의 배포를 지원하고 Windows Server 2016에서 Windows Server 컨테이너의 배포를 지원합니다. Hyper-V 컨테이너에 대한 지원은 향후 릴리스에 추가될 예정입니다. 

Service Fabric [응용 프로그램 모델](service-fabric-application-model.md)에서 컨테이너는 다수의 서비스 복제본이 배치되는 응용 프로그램 호스트를 나타냅니다. 다음 시나리오가 컨테이너에 대해 지원됩니다.

1. **게스트 컨테이너**: 컨테이너의 기존 응용프로그램을 배포할 수 있는 [게스트 실행 파일 시나리오](service-fabric-deploy-existing-app.md) 와 동일합니다. 예를 들어, nodejs, javascript 또는 코드(EXE)가 있습니다.
2. **컨테이너 내 상태 비저장 서비스**: Reliable Services와 Reliable Actors 프로그래밍 모델을 사용하는 상태 비저장 서비스입니다. 현재 Linux에서만 지원됩니다. Windows 컨테이너의 상태 비저장 서비스에 대한 지원은 향후 릴리스에 추가될 예정입니다.
3. **컨테이너 내 상태 저장 서비스**: Linux에서 Reliable Actors 프로그래밍 모델을 사용하는 상태 저장 서비스입니다. Reliable Services는 현재 Linux에서 지원되지 않습니다.  Windows 컨테이너의 상태 저장 서비스에 대한 지원은 향후 릴리스에 추가될 예정입니다.

Service Fabric에는 컨테이너화된 마이크로 서비스로 구성된 응용 프로그램을 빌드하는 데 도움을 주는 몇 가지 컨테이너 기능이 있습니다. 이것을 컨테이너화된 서비스라고 합니다. 기능은 다음과 같습니다.

* 컨테이너 이미지 배포 및 활성화
* 리소스 관리
* 리포지토리 인증
* 포트 매핑을 호스트하는 컨테이너 포트
* 컨테이너 간 검색 및 통신
* 환경 변수를 구성하고 설정할 수 있는 기능

## <a name="next-steps"></a>다음 단계
이 문서에서는 Service Fabric이 컨테이너 오케스트레이터인 컨테이너와 Service Fabric이 컨테이너를 지원하기 위해 제공하는 기능에 대해 알아보았습니다. 다음 단계에서는 이것을 사용하는 방법을 보여주는 각 기능의 사례를 살펴보겠습니다. 

[컨테이너를 Service Fabric에 배포](service-fabric-deploy-container.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png




<!--HONumber=Oct16_HO2-->


