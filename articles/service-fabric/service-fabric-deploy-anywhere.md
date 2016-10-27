<properties
   pageTitle="Windows Server 및 Linux에서 Azure 서비스 패브릭 클러스터 만들기 | Microsoft Azure"
   description="서비스 패브릭 클러스터는 Windows Server 및 Linux에서 실행됩니다. 즉, Windows Server 또는 Linux를 실행할 수 있는 모든 위치에 서비스 패브릭 응용 프로그램을 배포 및 호스트할 수 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="chackdan"/>


# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Windows Server 또는 Linux에서 Service Fabric 클러스터 만들기

Azure Service Fabric을 사용하면 Windows Server 또는 Linux를 실행하는 VM 또는 컴퓨터에서 Service Fabric 클러스터를 만들 수 있습니다. 다시 말해 온-프레미스든 Microsoft Azure든 클라우드 공급자든 Windows Server 또는 Linux 컴퓨터가 서로 연결된 모든 환경에 Service Fabric 응용 프로그램을 배포하고 실행할 수 있습니다.

##<a name="create-service-fabric-clusters-on-azure"></a>Azure에서 Service Fabric 클러스터 만들기

Azure에서 클러스터 만들기는 리소스 모델 템플릿 또는 Azure Portal을 통해 수행됩니다. 자세한 내용은 [Resource Manager 템플릿을 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md) 또는 [Azure Portal에서 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)를 참조하세요.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Azure에서 클러스터에 지원되는 운영 체제

다음 운영 체제를 실행하는 VM에서 클러스터를 만들 수 있습니다.

* Windows Server 2012 R2
* Windows Server 2016(일반 공급으로 발표된 후)
* Linux Ubuntu 16.04(공개 미리 보기 상태) 


##<a name="create-service-fabric-standalone-clusters-on-premise-or-with-any-cloud-provider"></a>온-프레미스에서 또는 클라우드 공급자를 사용하여 Service Fabric 독립 실행형 클러스터 만들기

Service Fabric은 온-프레미스 또는 클라우드 공급자에서 독립 실행형 Service Fabric 클러스터를 만들 수 있는 설치 패키지를 제공합니다.

Windows Server에서 독립 실행형 Service Fabric 클러스터 설치에 자세한 내용은 [Windows Server용 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)

### <a name="any-cloud-deployments-vs.-on-premises-deployments"></a>클라우드 배포와 온-프레미스 배포 비교
서비스 패브릭 클러스터 온-프레미스를 만드는 프로세스는 VM 집합이 있는 클라우드를 선택하여 거기에 클러스터를 만드는 프로세스와 비슷합니다. VM을 프로비전하는 초기 단계는 사용하는 클라우드 공급자 또는 온-프레미스 환경에 의해 제어됩니다. VM을 준비하고 VM 간에 네트워크 연결을 설정한 후에는 동일한 방법으로 서비스 패브릭 패키지를 설정하고, 클러스터 설정을 편집하고, 클러스터 만들기 및 관리 스크립트를 실행합니다. 이렇게 하면 새 호스팅 환경을 대상으로 선택할 때 Service Fabric 클러스터 운영 및 관리에 대한 지식과 경험이 전달됩니다.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>독립 실행형 서비스 패브릭 클러스터를 만들 경우의 이점
* 클러스터를 호스트할 클라우드 공급자를 자유롭게 선택할 수 있습니다.
* 서비스 패브릭 응용 프로그램을 한 번 작성한 다음 변경 없이 또는 최소한의 변경으로 여러 호스팅 환경에서 실행할 수 있습니다.
* 호스팅 환경 간에 서비스 패브릭 응용 프로그램 빌드에 대한 지식이 전달됩니다.
* 환경 간에 서비스 패브릭 클러스터 실행 및 관리에 대한 운영 경험이 전달됩니다.
* 호스팅 환경 제약을 받지 않아 고객 도달 범위가 넓습니다.
* 데이터 센터 또는 클라우드에 블랙아웃이 발생하면 서비스를 다른 배포 환경으로 이동할 수 있으므로 안정성이 강화되고 대규모로 가동이 중단되는 일이 없습니다.

## <a name="supported-operating-systems-for-standalone-clusters"></a>독립 실행형 클러스터에 지원되는 운영 체제
다음 운영 체제를 실행하는 컴퓨터 또는 VM에서 클러스터를 만들 수 있습니다.

* Windows Server 2012 R2
* Windows Server 2016(일반 공급으로 발표된 후)
* Linux(서비스 예정)

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>온-프레미스에서 만든 독립 실행형 서비스 패브릭 클러스터와 비교한 Azure 서비스 패브릭 클러스터의 장점

Azure에서 서비스 패브릭 클러스터를 실행하면 온-프레미스 옵션을 사용할 때보다 몇 가지 이점이 더 있습니다. 따라서 클러스터 실행 위치가 크게 중요하지 않은 경우 Azure에서 클러스터를 실행하는 것이 좋습니다. Azure에서는 클러스터의 작업 및 관리를 더 용이하고 안정적으로 해주는 다음과 같은 다른 Azure 기능 및 서비스와의 통합을 제공합니다.

* **Azure 포털:** Azure 포털을 사용하면 쉽게 클러스터를 만들고 관리할 수 있습니다.

* **Azure 리소스 관리자:** Azure 리소스 관리자를 사용하면 클러스터에서 사용하는 모든 리소스를 하나의 단위로 쉽게 관리할 수 있기 때문에 비용 추적 및 청구가 간소화됩니다.
* **Azure 리소스로서의 서비스 패브릭 클러스터** 서비스 패브릭 클러스터는 ARM 리소스이므로 Azure의 다른 ARM 리소스와 같이 모델링할 수 있습니다.
* **Azure 인프라와의 통합** 서비스 패브릭은 OS, 네트워크 및 그 밖에 응용 프로그램의 가용성 및 안정성을 개선하기 위한 업그레이드에 맞게 기본 Azure 인프라를 조정합니다.  
* **진단:** Azure에서는 Azure 진단과 Log Analytics가 통합됩니다.
* **자동 크기 조정:** Azure의 클러스터는 가상 컴퓨터 크기 조정 집합으로 인해 자동 크기 조정 기능이 기본적으로 제공됩니다. 온-프레미스 및 다른 클라우드 환경에서는 사용자 고유 자동 크기 조정 기능을 빌드하거나 서비스 패브릭에서 클러스터 크기 조정을 위해 제공하는 API를 사용하여 수동으로 조정해야 합니다.

## <a name="next-steps"></a>다음 단계
Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)

Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux의 서비스 패브릭](service-fabric-linux-overview.md)



<!--HONumber=Oct16_HO2-->


