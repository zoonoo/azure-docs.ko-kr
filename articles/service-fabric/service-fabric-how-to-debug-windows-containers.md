---
title: Service Fabric 및 VS를 사용하여 Windows 컨테이너 디버깅 | Microsoft Docs
description: Visual Studio 2017을 사용하여 Azure Service Fabric에서 Windows 컨테이너를 디버그하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/14/2018
ms.author: twhitney, mikhegn
ms.openlocfilehash: b38946f813185a4821520b8591b7fd72a5f0cce0
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300067"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>방법: Visual Studio 2017을 사용하여 Azure Service Fabric에서 Windows 컨테이너 디버깅

Visual Studio 2017 업데이트 7(15.7)에서는 컨테이너의 .NET 애플리케이션을 Service Fabric 서비스로 디버그할 수 있습니다. 이 문서에서는 환경을 구성한 다음, 로컬 Service Fabric 클러스터에서 실행되는 컨테이너의 .NET 애플리케이션을 디버그하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

* Windows 10에서 이 빠른 시작에 따라 [Windows 컨테이너를 실행하도록 Windows 10을 구성](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)합니다.
* Windows Server 2016에서 이 빠른 시작에 따라 [Windows 컨테이너를 실행하도록 Windows 2016을 구성](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)합니다.
* [Windows에서 개발 환경 준비](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)에 따라 로컬 Service Fabric 환경을 설정합니다.

## <a name="configure-your-developer-environment-to-debug-containers"></a>컨테이너를 디버그하도록 개발자 환경 구성

1. 다음 단계로 계속 진행하기 전에 Window용 Docker 서비스가 실행되고 있는지 확인합니다.

1. 컨테이너 간에 DNS 확인을 지원하려면 컴퓨터 이름을 사용하여 로컬 개발 클러스터를 설정해야 합니다. 또한 이러한 단계는 역방향 프록시를 통해 서비스에 주소를 지정하려는 경우 필요합니다.
    1. PowerShell을 관리자 권한으로 엽니다.
    2. SDK 클러스터 설치 폴더(일반적으로 `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`)로 이동합니다.
    3. `-UseMachineName` 매개 변수를 사용하여 `DevClusterSetup.ps1` 스크립트를 실행합니다.

       ``` PowerShell
         C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -UseMachineName
       ```

    > [!NOTE]
    > `-CreateOneNodeCluster`를 사용하여 단일 노드 클러스터를 설정할 수 있습니다. 기본적으로 5개 로컬 노드 클러스터를 만듭니다.
    >

    Service Fabric의 DNS 서비스에 대한 자세한 내용은 [Azure Service Fabric의 DNS 서비스](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice)를 참조하세요. 컨테이너에서 실행되는 서비스에서 Service Fabric 역방향 프록시 사용 방법에 대한 자세한 정보는 [컨테이너에서 실행되는 서비스에 대한 역방향 프록시 특별 처리](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)를 참조하세요.

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Service Fabric에서 컨테이너를 디버그할 때 알려진 제한 사항

Service Fabric의 컨테이너 디버깅에 대해 알려진 제한 사항과 가능한 해결 방법은 다음과 같습니다.

* ClusterFQDNorIP에 localhost를 사용하면 컨테이너에서 DNS 확인을 지원하지 않습니다.
    * 해결 방법: 컴퓨터 이름을 사용하여 로컬 클러스터를 설정합니다(위 참조).
* Virtual Machine에서 Windows10을 실행하면 DNS 회신이 컨테이너에 반환되지 않습니다.
    * 해결 방법: Virtual Machines NIC에서 IPv4에 대한 UDP 체크섬 오프로드를 사용하지 않도록 설정합니다.
    * 이 경우 컴퓨터의 네트워킹 성능이 저하됩니다.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Docker Compose를 사용하여 애플리케이션을 배포한 경우 DNS 서비스 이름을 사용하여 동일한 애플리케이션에서 서비스를 확인하는 작업이 Windows10에서 작동하지 않습니다.
    * 해결 방법: servicename.applicationname을 사용하여 서비스 엔드포인트를 확인합니다.
    * https://github.com/Azure/service-fabric-issues/issues/1062
* ClusterFQDNorIP에 IP-address를 사용하는 경우 호스트에서 주 IP를 변경하면 DNS 기능이 중단됩니다.
    * 해결 방법: 호스트에서 새 주 IP를 사용하여 클러스터를 다시 만들거나 컴퓨터 이름을 사용합니다. 의도적인 작동입니다.
* 클러스터에서 만든 FQDN을 네트워크에서 확인할 수 없는 경우 DNS가 실패합니다.
    * 해결 방법: 호스트의 주 IP를 사용하여 로컬 클러스터를 다시 만듭니다. 의도적인 작동입니다.
* 컨테이너를 디버그하는 경우 Docker 로그는 Visual Studio 출력 창에서만 사용할 수 있으며 Service Fabric API(Service Fabric Explorer 포함)를 통해서는 사용할 수 없습니다

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Service Fabric을 통해 Docker 컨테이너에서 실행되는 .NET 애플리케이션 디버깅

1. Visual Studio를 관리자 권한으로 실행합니다.

1. 기존 .NET 애플리케이션을 열거나 새로 만듭니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고, **추가 -> 컨테이너 오케스트레이터 지원 -> Service Fabric**을 차례로 선택합니다.

1. **F5** 키를 눌러 응용 프로그램 디버깅을 시작합니다.

    Visual Studio는 .NET 및 .NET Core용 콘솔 및 ASP.NET 프로젝트 형식을 지원합니다.

## <a name="next-steps"></a>다음 단계
Service Fabric 및 컨테이너의 기능에 대한 자세한 내용은 [Service Fabric 컨테이너 개요](service-fabric-containers-overview.md) 링크를 따르세요.
