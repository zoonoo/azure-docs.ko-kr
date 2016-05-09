<properties
   pageTitle="서비스 패브릭 프로그래밍 모델 개요 | Microsoft Azure"
   description="서비스 패브릭은 서비스 빌드에 대해 행위자 프레임워크 및 서비스 프레임워크라는 두 가지 프레임워크를 제공합니다. 이 두 프레임 간에는 단순성과 제어 면에서 고유의 장단점이 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="seanmck"/>

# 서비스 패브릭 프로그래밍 모델 개요

서비스 패브릭은 서비스의 작성 및 관리를 위한 여러 방법을 제공합니다. 서비스는 플랫폼의 기능과 응용 프로그램 프레임워크를 완전히 활용하기 위해 사용할 서비스 패브릭 API를 선택할 수 있습니다. 또는 단순히 어떤 언어로 작성되어 컴파일된 실행 파일 프로그램이 서비스가 되고 서비스 패브릭 클러스터에 호스팅될 수 있습니다.

## 게스트 실행 파일

게스트 실행 파일은 어떤 언어로 작성되어 기존 응용 프로그램으로 가져가 서비스 패브릭 클러스터에서 호스팅할 수 있는 임의의 실행 파일입니다. 게스트 실행 파일은 응용 프로그램 안에 패키지하여 다른 서비스와 함께 호스팅할 수 있습니다. 서비스 패브릭은 실행 파일의 오케스트레이션과 간단한 실행 관리를 처리하여 서비스 설명에 따라 유지 및 실행될 수 있게 합니다. 그러나 게스트 실행 파일은 서비스 패브릭 API와 직접 통합되지 않으므로 사용자 지정 상태 및 로드 보고, 서비스 끝점 등록, 상태 저장 계산 등, 플랫폼이 제공하는 전체 기능을 활용하지는 않습니다.

첫 번째 [게스트 실행 파일 응용 프로그램](service-fabric-deploy-existing-app.md)을 배포하여 게스트 실행 파일을 시작합니다.

## Reliable Services

Reliable Services는 서비스 패브릭 플랫폼과 통합하여 전체 플랫폼 기능을 활용하는 서비스 작성을 위한 간단한 프레임워크입니다. Reliable Services는 서비스 패브릭 런타임이 서비스의 수명 주기를 관리하고 서비스가 런타임과 상호 작용할 수 있도록 하는 최소한의 API 집합을 제공합니다. 응용 프로그램 프레임워크는 최소한의 부담으로 완전한 설계 및 구현 제어를 제공하며, 이를 통해 ASP.NET MVC 또는 Web API 등의 다른 응용 프로그램 프레임워크를 호스팅할 수 있습니다.

Reliable Services는 웹 서버나 Azure 클라우드 서비스의 작업자 역할 등, 대부분의 서비스 플랫폼과 유사하게 상태 비저장이 될 수 있습니다. 여기서는 서비스의 각 인스턴스가 동등하게 생성되고 상태가 Azure DB나 Azure 테이블 저장소 같은 외부 솔루션에 유지됩니다.

Reliable Services는 서비스 패브릭 단독으로 상태를 저장할 수도 있습니다. 여기서는 Reliable Collections를 사용하여 서비스 자체에 상태가 직접적으로 유지됩니다. 상태는 복제를 통해 고가용성이 유지되고 분할을 통해 배포되며, 모두 서비스 패브릭에서 자동으로 관리합니다.

[Reliable Actors에 대해 자세히 알아보거나](service-fabric-reliable-services-introduction) [첫 번째 Reliable Services를 작성해 보세요.](service-fabric-reliable-services-quick-start.md)

## Reliable Actors

Reliable Services의 최상위에 구축되는 Reliable Actor 프레임워크는 행위자 설계 패턴을 기준으로 가상 행위자 패턴을 구현하는 응용 프로그램 프레임워크입니다. Reliable Actor 프레임워크는 행위자라고 하는 단일 스레드 실행을 통해 독립적인 계산 단위 및 상태를 사용합니다. Reliable Actor 프레임워크는 행위자와 사전 설정 상태 지속성 및 확장 구성에 대해 기본 포함된 통신을 제공합니다.

Reliable Actors 자체는 Reliable Services에 구축된 응용 프로그램 프레임워크이므로 서비스 패브릭 플랫폼과 완전히 통합되며 플랫폼이 제공하는 모든 기능을 완벽히 활용할 수 있습니다.

## 다음 단계
[Reliable Actors에 대해 자세히 알아보거나](service-fabric-reliable-actors-introduction.md)[첫 번째 Reliable Actor 서비스 작성](service-fabric-reliable-actors-get-started.md)

<!---HONumber=AcomDC_0427_2016-->