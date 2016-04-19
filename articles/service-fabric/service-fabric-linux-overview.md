<properties
   pageTitle="Linux의 Azure 서비스 패브릭 | Microsoft Azure"
   description="서비스 패브릭 클러스터는 Linux 및 Java를 지원하므로 Linux에서 Java로 작성된 서비스 패브릭 응용 프로그램을 배포 및 호스트할 수 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/29/2016"
   ms.author="SubramaR"/>

# Linux의 서비스 패브릭

서비스 패브릭은 현재 Linux에서 제한된 미리 보기로 제공됩니다. Linux에서는 Windows에서와 마찬가지로 서비스 패브릭을 통해 해당 환경에서 가용성 및 확장성이 뛰어난 응용 프로그램을 빌드, 배포 및 관리할 수 있습니다. 또한 이제 뛰어난 수준의 서비스 패브릭 프레임 워크(Reliable Services 및 Reliable Actors)를 Java에서 빌드할 수 있습니다.

## 지원되는 운영 체제 및 프로그래밍 언어

제한된 미리 보기에서는 Ubuntu Server 15.10을 실행하는 Azure에서 통합 개발 클러스터 및 다중 컴퓨터 클러스터 만들기를 지원합니다.

모든 언어나 프레임워크를 사용하여 [게스트 실행 서비스](service-fabric-deploy-existing-app.md)를 빌드할 수 있습니다. 또한 Java를 사용하여 Reliable Services 및 Reliable Actor 프레임워크를 기반으로 서비스를 빌드할 수 있습니다.

>[AZURE.NOTE] 신뢰할 수 있는 컬렉션은 Java에서 아직 지원되지 않습니다.

## 미리 보기에 참여하기

제한된 미리 보기 프로그램에 관심이 있는 경우 해당 시나리오 및 요구 사항을 보다 잘 이해할 수 있도록 [설문 조사를 작성](http://aka.ms/sflinuxsurvey)해 주시기 바랍니다. 미리 보기는 처음에는 기능이 매우 적지만 점차 확장될 예정입니다.

Linux의 서비스 패브릭은 OS 관련 기능 및 프로그래밍 언어 지원을 제외하고는 Windows에서 사용할 수 있는 것과 개념적으로 같으므로 대부분의 [기존 설명서](http://aka.ms/servicefabricdocs)가 적용되며 이를 통해 기술을 익힐 수 있습니다.

## 다음 단계

[Reliable Actors](service-fabric-reliable-actors-introduction.md) 및 [Reliable Services](service-fabric-reliable-services-introduction.md) 프로그래밍 프레임워크에 대해 알아봅니다.

<!---HONumber=AcomDC_0406_2016-->