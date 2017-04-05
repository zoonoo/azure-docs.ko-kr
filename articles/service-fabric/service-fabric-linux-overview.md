---
title: "Linux의 Azure Service Fabric | Microsoft Docs"
description: "Service Fabric 클러스터는 Linux 및 Java를 지원하므로 Linux에서 Java 및 C#으로 작성된 Service Fabric 응용 프로그램을 배포 및 호스트할 수 있습니다."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 459afade-145d-4ee6-b72b-ddf380ccd1bf
ms.service: service-fabric
ms.devlang: Java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: SubramaR
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 62d7e0088c66a538dff7f3b99882ebef4949211b
ms.lasthandoff: 03/29/2017


---
# <a name="service-fabric-on-linux"></a>Linux의 서비스 패브릭
Linux의 서비스 패브릭 미리 보기를 사용하면 Windows에서와 마찬가지로 Linux에서도 서비스 패브릭을 통해 해당 환경에서 가용성 및 확장성이 뛰어난 응용 프로그램을 빌드, 배포 및 관리할 수 있습니다. Service Fabric 프레임워크(Reliable Services 및 Reliable Actors) 는 C#(.NET Core) 뿐만 아니라 Linux의 Java로 사용할 수 있습니다.  어떤 언어 또는 프레임워크에서도 [게스트 실행 서비스](service-fabric-deploy-existing-app.md) 를 빌드할 수 있습니다. 또한, 미리 보기는 Docker 컨테이너 오케스트레이션도 지원합니다. Docker 컨테이너는 게스트 실행 파일 또는 Service Fabric 프레임워크를 사용하는 네이티브 Service Fabric 서비스를 실행할 수 있습니다.

Linux의 서비스 패브릭은 Windows의 서비스 패브릭과 개념적으로 동일합니다(OS 사양 및 프로그래밍 언어 지원 제외). 따라서 대부분의 [기존 설명서](http://aka.ms/servicefabricdocs) 를 통해 이 기술을 익힐 수 있습니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Service-Fabric-Linux-Preview/player]
>
>

## <a name="supported-operating-systems-and-programming-languages"></a>지원되는 운영 체제 및 프로그래밍 언어
제한된 미리 보기에서는 Ubuntu Server 16.04를 실행하는 Azure에서 통합 개발 클러스터 및 다중 컴퓨터 클러스터 만들기를 지원합니다. 미리 보기는 게스트 실행 파일뿐만 아니라 Java 및 C#으로 작성된 Reliable Actors 및 Reliable Stateless Services 프레임워크를 지원하며 Docker 컨테이너 오케스트레이션을 지원합니다.  

> [!NOTE]
> 신뢰할 수 있는 컬렉션은 Linux에서 아직 지원되지 않습니다. 독립 실행형 클러스터 역시 지원 되지 않습니다 미리 보기에서는 one box 및 Azure Linux 다중 컴퓨터 클러스터만 지원됩니다.
>
>


## <a name="supported-tooling"></a>지원되는 도구
미리 보기는 Azure CLI를 통해 클러스터와의 상호 작용을 지원합니다. Java 개발자를 위해 Linux 및 OSX에서 지원되는 Eclipse에 Eclipse 및 Yeoman과의 통합이 제공됩니다. OSX 통합은 Vagrant를 통해 내부에서 Linux VM을 사용합니다. C# 개발자를 위해 Yeoman과의 통합이 응용 프로그램 템플릿을 생성하도록 제공됩니다.

## <a name="next-steps"></a>다음 단계
1. [Reliable Actors](service-fabric-reliable-actors-introduction.md) 및 [Reliable Services](service-fabric-reliable-services-introduction.md) 프로그래밍 프레임워크에 대해 알아봅니다.
2. [Linux에서 개발 환경 준비](service-fabric-get-started-linux.md)
3. [OSX에서 개발 환경 준비](service-fabric-get-started-mac.md)
4. [Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기](service-fabric-create-your-first-linux-application-with-java.md)
5. [Jenkins 및 GitHub로 Setup Service Fabric 연속 통합 및 배포 설정](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
6. [Service Fabric Windows/Linux 간 차이점](service-fabric-linux-windows-differences.md)

