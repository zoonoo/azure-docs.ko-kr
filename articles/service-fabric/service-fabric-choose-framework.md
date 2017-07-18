---
title: "Service Fabric 프로그래밍 모델 개요 | Microsoft Docs"
description: "서비스 패브릭은 서비스 빌드에 대해 행위자 프레임워크 및 서비스 프레임워크라는 두 가지 프레임워크를 제공합니다. 이 두 프레임 간에는 단순성과 제어 면에서 고유의 장단점이 있습니다."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 102ede8d2aafaf485a5212faad47de6781d84578
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017


---
# <a name="service-fabric-programming-model-overview"></a>서비스 패브릭 프로그래밍 모델 개요
서비스 패브릭은 서비스의 작성 및 관리를 위한 여러 방법을 제공합니다. 서비스는 플랫폼의 기능과 응용 프로그램 프레임워크를 최대한 활용하기 위해 Service Fabric API를 사용할 수 있습니다. 서비스는 Service Fabric 클러스터에서 호스트되는 컨테이너에서 실행 중인 모든 언어 또는 코드로 작성된 컴파일된 실행 프로그램일 수도 있습니다.

### <a name="guest-executables"></a>게스트 실행 파일
[게스트 실행 파일](service-fabric-deploy-existing-app.md)은 Service Fabric 클러스터에서 다른 서비스와 함께 호스트된 임의의 기존 실행 파일입니다. 게스트 실행 파일은 Service Fabric API와 직접 통합되지 않습니다. 그러나 사용자 지정 상태 및 로드 보고, REST API 호출에 의한 서비스 검색 가능성과 같이 플랫폼에서 제공하는 기능을 계속 활용합니다. 또한 전체 응용 프로그램 수명 주기 지원도 포함합니다.

첫 번째 [게스트 실행 파일 응용 프로그램](service-fabric-deploy-existing-app.md)을 배포하여 게스트 실행 파일을 시작합니다.

### <a name="containers"></a>컨테이너
기본적으로 Service Fabric은 이러한 서비스를 프로세스로 배포하고 활성화합니다. Service Fabric도 [컨테이너](service-fabric-containers-overview.md)에 서비스를 배포할 수 있습니다. Service Fabric은 Windows Server 2016에서 Linux 컨테이너 및 Windows 컨테이너의 배포를 지원합니다. 기존 응용 프로그램, 상태 비저장 서비스 또는 컨테이너의 상태 저장 서비스를 배포할 수 있으며, 프로세스의 서비스와 컨테이너의 서비스를 동일한 응용 프로그램에서 함께 사용할 수 있습니다.

## <a name="reliable-services"></a>Reliable Services
Reliable Services는 서비스 패브릭 플랫폼과 통합하여 전체 플랫폼 기능을 활용하는 서비스 작성을 위한 간단한 프레임워크입니다. Reliable Services는 서비스 패브릭 런타임이 서비스의 수명 주기를 관리하고 서비스가 런타임과 상호 작용할 수 있도록 하는 최소한의 API 집합을 제공합니다. 응용 프로그램 프레임워크는 최소한의 부담으로 완전한 설계 및 구현 제어를 제공하며, 이를 통해 ASP.NET Core 등의 다른 응용 프로그램 프레임워크를 호스팅할 수 있습니다.

Reliable Services는 웹 서버나 등, 대부분의 서비스 플랫폼과 유사하게 상태 비저장이 될 수 있습니다. 여기서는 서비스의 각 인스턴스가 동등하게 생성되고 상태가 Azure DB나 Azure Table Storage 같은 외부 솔루션에 유지됩니다.

Reliable Services는 서비스 패브릭 단독으로 상태를 저장할 수도 있습니다. 여기서는 Reliable Collections를 사용하여 서비스 자체에 상태가 직접적으로 유지됩니다. 상태는 복제를 통해 고가용성이 유지되고 분할을 통해 배포되며, 모두 서비스 패브릭에서 자동으로 관리합니다.

[Reliable Actors에 대해 자세히 알아보거나](service-fabric-reliable-services-introduction.md) [첫 번째 Reliable Services를 작성해 보세요.](service-fabric-reliable-services-quick-start.md)

## <a name="reliable-actors"></a>Reliable Actors
Reliable Services의 최상위에 구축되는 Reliable Actor 프레임워크는 행위자 설계 패턴을 기준으로 가상 행위자 패턴을 구현하는 응용 프로그램 프레임워크입니다. Reliable Actor 프레임워크는 행위자라고 하는 단일 스레드 실행을 통해 독립적인 계산 단위 및 상태를 사용합니다. Reliable Actor 프레임워크는 행위자와 사전 설정 상태 지속성 및 확장 구성에 대해 기본 포함된 통신을 제공합니다.

Reliable Actors 자체는 Reliable Services에 구축된 응용 프로그램 프레임워크이므로 서비스 패브릭 플랫폼과 완전히 통합되며 플랫폼이 제공하는 모든 기능을 완벽히 활용할 수 있습니다.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric은 웹 및 API 응용 프로그램 빌드를 위해 [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)와 통합됩니다. 

## <a name="next-steps"></a>다음 단계
[Reliable Actors에 대해 자세히 알아보기](service-fabric-reliable-actors-introduction.md) 또는 [첫 번째 Reliable Actor 서비스를 작성](service-fabric-reliable-actors-get-started.md)하여 시작
[Windows 또는 Linux에서 서비스를 컨테이너화하는 방법에 대해 자세히 알아보기](service-fabric-deploy-container.md)
[ASP.NET Core를 사용하여 프런트 엔드 서비스 빌드](service-fabric-add-a-web-frontend.md)



