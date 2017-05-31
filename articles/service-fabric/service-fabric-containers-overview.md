---
title: "Service Fabric 및 컨테이너 개요 | Microsoft Docs"
description: "Service Fabric 및 마이크로 서비스 응용 프로그램 배포를 위한 컨테이너 사용에 대한 개요. 이 문서는 컨테이너를 사용하는 방법과 Service Fabric에서 사용할 수 있는 기능에 대한 개요를 제공합니다."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/16/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: b8334d0ca0d1460edad9b0ef399e9b3428b1ef8d
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017


---
# <a name="service-fabric-and-containers"></a>Service Fabric 및 컨테이너
> [!NOTE]
> 이 기능은 Linux에서 미리 보기로 제공됩니다. 
>   

## <a name="introduction"></a>소개
Azure Service Fabric은 컴퓨터 클러스터에서 사용되는 여러 서비스의 [Orchestrator](service-fabric-cluster-resource-manager-introduction.md)로, Microsoft의 방대한 서비스에서 수년 동안 사용되면서 최적화 과정을 거친 기능입니다. 서비스는 [Service Fabric 프로그래밍 모델](service-fabric-choose-framework.md) 사용부터 [게스트 실행 파일](service-fabric-deploy-existing-app.md) 배포에 이르는 다양한 방식으로 개발될 수 있습니다. 기본적으로 Service Fabric은 이러한 서비스를 프로세스로 배포하고 활성화합니다. 프로세스는 클러스터 내에서 리소스의 최고밀도 사용과 가장 신속한 활성화를 제공합니다. Service Fabric도 컨테이너 이미지에 서비스를 배포할 수 있습니다. 중요한 점은 프로세스의 서비스와 동일한 응용 프로그램의 컨테이너의 서비스를 혼합할 수 있습니다. 

## <a name="containers-and-service-fabric-roadmap"></a>컨테이너 및 Service Fabric 로드맵
향후 제공될 몇 번의 릴리스에서 Service Fabric은 네트워킹, 리소스 제약 조건, 보안, 진단, 볼륨 드라이버 및 도구 지원(특히 Visual Studio) 향상을 비롯하여 Windows 및 Linux 둘 다에서 포괄적인 컨테이너 지원을 지속적으로 추가하여 컨테이너 이미지를 사용하여 서비스를 배포하는 환경을 최고 수준까지 끌어올릴 에정입니다. 이를 통해 컨테이너를 사용하여 기존 코드를 패키지하는 방식(예: IIS MVC 앱) 또는 Service Fabric 프로그래밍 모델 중에서 선택할 수 있습니다. 또한 Service Fabric에서는 이러한 두 가지 방식을 동일하게 취급하므로 응용 프로그램에서 이러한 방식을 혼합하여 코드를 보다 유연하게 배포할 수 있게 됩니다. 시나리오에 따라 두 가지 환경의 장점을 활용할 수 있습니다.

## <a name="what-are-containers"></a>컨테이너란?
컨테이너는 캡슐화되어 개별적으로 배포가 가능한 구성 요소로, 운영 체제가 제공하는 가상화를 활용하기 위해 동일한 커널에서 격리된 인스턴스로 실행됩니다. 즉, 각 응용 프로그램, 해당 런타임, 종속성, 시스템 라이브러리는 운영 체제 구성물의 자체적으로 격리된 뷰에 대해 전체, 개인 액세스 권한이 있는 컨테이너 내에서 실행된다는 의미입니다. 이식성과 더불어 이러한 수준의 보안 및 리소스 격리는 서비스를 프로세스에서 실행하지 않고 Service Fabric에서 컨테이너를 사용하는 주요 장점입니다.

컨테이너는 응용 프로그램에서 기본 운영 체제를 가상화하는 가상화 기술입니다. 컨테이너는 응용 프로그램을 다양한 격리 수준에서 실행하도록 변경이 불가능한 환경을 제공합니다. 컨테이너는 커널 바로 위에서 실행되며 파일 시스템 및 기타 리소스에 대한 격리된 뷰를 갖습니다. 가상 컴퓨터에 비해 컨테이너는 다음과 같은 이점이 있습니다.

* **작은 크기**: 컨테이너는 각 계층에 단일 저장소 공간과 작은 델타를 사용하기 때문에 효율성이 높습니다.
* **빠른 부팅 시간**: 컨테이너는 운영 체제를 부팅할 필요가 없기 때문에 가상 컴퓨터보다 훨씬 빠르게, 보통 몇 초 만에 가동하여 사용할 수 있습니다.
* **이식성**: 컨테이너화된 응용 프로그램 이미지는 클라우드 내, 온-프레미스에서, 가상 컴퓨터 내부 또는 실제 컴퓨터에서 바로 실행하도록 이식될 수 있습니다.
* **리소스 관리**: 컨테이너는 호스트에서 사용할 수 있는 물리적 리소스를 제한할 수 있습니다.

## <a name="container-types"></a>컨테이너 유형
Service Fabric은 다음과 같은 유형의 컨테이너를 지원합니다.

### <a name="docker-containers-on-linux"></a>Linux의 Docker 컨테이너
Docker는 고급 수준의 API를 제공하여 Linux 커널 컨테이너 위에서 컨테이너를 만들고 관리합니다. Docker 허브는 컨테이너 이미지를 저장하고 검색하기 위한 중앙 리포지토리입니다.

이 작업을 수행하는 방법에 대한 연습은 [Docker 컨테이너를 Service Fabric에 배포](service-fabric-deploy-container-linux.md)를 참조하세요.

### <a name="windows-server-containers"></a>Windows Server 컨테이너
Windows Server 2016은 제공되는 격리 수준이 다른 두 가지 유형의 컨테이너를 제공합니다. Windows Server 컨테이너와 Docker 컨테이너는 둘다 네임스페이스와 파일 시스템이 있다는 점에서 유사하지만 이들 컨테이너가 실행되는 호스트가 있는 커널을 공유합니다. Linux에서, 이러한 격리는 일반적으로 cgroups 및 네임스페이스에 의해 제공되었으며 Windows Server 컨테이너도 유사하게 작동합니다.

Windows Hyper-V 컨테이너는 각 컨테이너가 다른 컨테이너 또는 호스트가 있는 운영 체제 커널을 공유하지 않기 때문에 더 많은 격리 및 보안을 제공합니다. 이렇게 보안 격리 수준이 높은 Hyper-V 컨테이너는 적대적인 다중 테넌트 시나리오를 대상으로 합니다.

이 작업을 수행하는 방법에 대한 연습은 [Windows 컨테이너를 Service Fabric에 배포](service-fabric-deploy-container.md)를 참조하세요.

다음 그림은 운영 체제에서 사용할 수 있는 다양한 유형의 가상화 및 격리 수준을 보여줍니다.
![Service Fabric 플랫폼][Image1]

## <a name="scenarios-for-using-containers"></a>컨테이너 사용 시나리오
다음은 컨테이너가 적합한 일반적인 예입니다.

* **IIS 리프트 및 이동**: 기존의 [ASP.NET MVC](https://www.asp.net/mvc) 앱을 계속 사용하려는 경우 앱을 ASP.NET 코어로 마이그레이션하는 대신 컨테이너에 둡니다. 이러한 ASP.NET MVC 앱은 IIS(인터넷 정보 서비스)에 다릅니다. 이것을 미리 만든 IIS 이미지의 컨테이너 이미지로 패키징하고 Service Fabric을 사용하여 배포할 수 있습니다. IIS 이미지를 만드는 방법에 대한 자세한 내용은 [Windows Server의 컨테이너 이미지](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images)를 참조하세요.
* **컨테이너와 Service Fabric 마이크로 서비스의 혼합**: 기존 컨테이너 이미지를 응용 프로그램의 일부에 사용합니다. 예를 들어, 응용 프로그램의 웹 프런트 엔드에 [NGINX 컨테이너](https://hub.docker.com/_/nginx/) 를 사용하고 상태 저장 서비스를 보다 집약적인 백 엔드 계산에 사용합니다.
* **"시끄러운 이웃" 서비스의 영향 감소**: 컨테이너의 리소스 관리 기능을 사용하여 호스트에 서비스가 사용하는 리소스를 제한합니다. 서비스는 많은 리소스를 소비하고 다른 서비스의 성능에 영향을 미칠 수 있는 경우(예: 장기적으로 실행되는 쿼리 같은 연산), 리소스 관리를 사용하는 컨테이너에 이 서비스를 넣는 것이 좋습니다.

## <a name="service-fabric-support-for-containers"></a>컨테이너에 대한 Service Fabric 지원
Service Fabric은 현재 Linux에서 Docker 컨테이너의 배포를 지원하고 Windows Server 2016에서 Windows Server 컨테이너의 배포를 지원합니다. Hyper-V 컨테이너에 대한 지원은 향후 릴리스에 추가될 예정입니다.

Service Fabric [응용 프로그램 모델](service-fabric-application-model.md)에서 컨테이너는 다수의 서비스 복제본이 배치되는 응용 프로그램 호스트를 나타냅니다. 다음 시나리오가 컨테이너에 대해 지원됩니다.

* **게스트 컨테이너**: 이 시나리오는 컨테이너의 기존 응용프로그램을 배포할 수 있는 [게스트 실행 파일 시나리오](service-fabric-deploy-existing-app.md)와 동일합니다. Node.js, JavaScript 또는 코드(실행 파일)을 예로 들 수 있습니다.
* **컨테이너 내 상태 비저장 서비스**: Reliable Services와 Reliable Actors 프로그래밍 모델을 사용하는 상태 비저장 서비스입니다. 현재 Linux에서만 지원됩니다. Windows 컨테이너의 상태 비저장 서비스에 대한 지원은 향후 릴리스에 추가될 예정입니다.
* **컨테이너 내 상태 저장 서비스**: Linux에서 Reliable Actors 프로그래밍 모델을 사용하는 상태 저장 서비스입니다. Reliable Services는 현재 Linux에서 지원되지 않습니다.  Windows 컨테이너의 상태 저장 서비스에 대한 지원은 향후 릴리스에 추가될 예정입니다.

Service Fabric에는 컨테이너화된 마이크로 서비스로 구성된 응용 프로그램을 빌드하는 데 도움을 주는 몇 가지 컨테이너 기능이 있습니다. 이것을 컨테이너화된 서비스라고 합니다. 기능은 다음과 같습니다.

* 컨테이너 이미지 배포 및 활성화
* 리소스 관리
* 리포지토리 인증
* 컨테이너 포트와 호스트 포트 간 매핑
* 컨테이너 간 검색 및 통신
* 환경 변수를 구성하고 설정할 수 있는 기능

## <a name="next-steps"></a>다음 단계
이 문서에서는 Service Fabric이 컨테이너 오케스트레이터인 컨테이너와 Service Fabric이 컨테이너를 지원하기 위해 제공하는 기능에 대해 알아보았습니다. 다음 단계에서는 이것을 사용하는 방법을 보여주는 각 기능의 사례를 살펴보겠습니다.

[Windows Server 2016에서 Windows 컨테이너를 Service Fabric에 배포](service-fabric-deploy-container.md)

[Linux에서 Docker 컨테이너를 Service Fabric에 배포](service-fabric-deploy-container-linux.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

