<properties
 pageTitle="A8, A9, A10 및 A11 인스턴스 정보 | Microsoft Azure"
 description="가상 컴퓨터 및 클라우드 서비스에 대한 Azure A8, A9, A10 및 A11 계산 집약적 크기 사용에 관한 배경 정보와 고려사항을 얻습니다."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# A8, A9, A10 및 A11 계산 집약적 인스턴스 정보

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


이 문서는 *계산 집약적* 인스턴스로 알려진 Azure A8, A9, A10 및 A11 인스턴스에 대한 고려사항과 배경 정보를 제공합니다. 이러한 인스턴스의 주요 기능은 다음과 같습니다.

* **고성능 하드웨어** – 이러한 인스턴스를 실행하는 Azure 데이터 센터 하드웨어는 고성능 컴퓨팅(HPC) 클러스터 응용 프로그램, 모델링 및 시뮬레이션을 포함하는 계산 집약적 및 네트워크 집약적 응용 프로그램을 위해 디자인되고 최적화되었습니다.

* **MPI 응용 프로그램에 대한 RDMA 네트워크 연결** - A8 및 A9 인스턴스를 설정하여 RDMA(원격 직접 메모리 액세스) 기술을 기반으로 하는 Azure에서 대기 시간이 짧고 처리량이 많은 네트워크를 통해 다른 A8 및 A9 인스턴스와 통신할 수 있습니다. 이 기능은 특정 Linux 및 Windows MPI(메시지 전달 인터페이스) 응용 프로그램의 성능을 높일 수 있습니다.

* **Linux 및 Windows HPC 클러스터 지원** - Azure의 A8, A9, A10 및 A11 인스턴스에서 클러스터 관리 및 작업 예약 소프트웨어를 배포하여 독립 실행형 HPC 클러스터를 만들거나 온-프레미스 클러스터에 용량을 추가합니다.

>[AZURE.NOTE]A10 및 A11 인스턴스는 A8 및 A9 인스턴스와 성능 최적화 및 사양이 동일합니다. 하지만 Azure의 RDMA 네트워크에 대한 액세스는 포함하지 않습니다. 노드 간에 지속적이고 대기 시간이 짧은 통신을 필요로 하지 않는 HPC 응용 프로그램을 위해 설계되었으며, 매개 변수 또는 병렬 처리 응용 프로그램이라고도 합니다.


## 사양

### CPU 및 메모리

Azure A8, A9, A10 및 A11 계산 집약적 인스턴스는 다음 표와 같이 고속 멀티 코어 CPU 및 대량 메모리를 특징으로 합니다.

크기 | CPU | 메모리
------------- | ----------- | ----------------
A8 및 A10 | Intel Xeon E5-2670<br/>8 코어 @ 2.6 GHz | DDR3-1600MHz<br/>56GB
A9 A11 | Intel Xeon E5-2670<br/>16 코어 @ 2.6 GHz | DDR3-1600MHz<br/>112GB


>[AZURE.NOTE]지원되는 명령어 집합 확장을 비롯한 추가 프로세서 세부 정보는 Intel.com 웹 사이트에 있습니다. VM 저장소 용량 및 디스크 세부 정보는 [가상 컴퓨터 크기](virtual-machines-size-specs.md)를 참조하세요.

### 네트워크 어댑터

A8 및 A9 인스턴스에는 다음 두 백엔드 AZure 네트워크에 연결하는 두 개의 네트워크 어댑터가 있습니다.


네트워크 | 설명
-------- | -----------
10-Gbps 이더넷 | Azure 서비스(예: Azure 저장소, Azure 가상 네트워크) 및 인터넷에 연결합니다.
32-Gbps 백엔드, RDMA 지원 | 단일 클라우드 서비스 또는 가용성 집합 내의 인스턴스 사이에서 대기 시간이 짧고 처리량이 많은 응용 프로그램 통신을 사용할 수 있습니다. MPI 트래픽 전용으로 예약됩니다.


>[AZURE.IMPORTANT]Linux 및 Windows MPI 응용 프로그램에서 RDMA 네트워크에 액세스하기 위한 추가 요구 사항은 이 문서의 [RDMA 네트워크에 대한 액세스](#access-the-rdma-network)를 참조하세요.

A10 및 A11 인스턴스에는 Azure 서비스 및 인터넷에 연결하는 한 개의 10-Gbps 이더넷 네트워크 어댑터가 있습니다.

## 배포 고려 사항

* **Azure 계정** - 소량의 계산 집약적 인스턴스를 배포하려는 경우 종량제 구독 또는 기타 구매 옵션을 고려합니다. MSDN 구독을 이용할 수도 있습니다. [MSDN 구독자에 대한 Azure 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 사용하는 경우, 제한된 수의 Azure 계산 코어만 사용할 수 있습니다.

* **코어 할당량** – Azure 구독의 코어 할당량의 기본값은 구독당 20개의 코어(클래식 배포 모델을 사용하는 경우) 또는 지역당 20개의 코어(Azure 리소스 관리자 배포 모델을 사용하는 경우)이며 이를 더 늘려야 할 수도 있습니다. 할당량 증가를 요청하려면 [Azure 제한 및 증가 이해](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)에 표시된 대로 무료 지원 티켓을 엽니다.

    >[AZURE.NOTE]Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 사용하는 코어에 대해서만 요금이 청구됩니다.

* **가상 네트워크** - Azure [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)는 계산 집약적 인스턴스를 사용할 필요가 없습니다. 하지만 응용 프로그램 라이선스 서버와 같은 온-프레미스 리소스에 액세스해야 하는 경우 많은 IaaS 시나리오 또는 사이트 간 연결을 위해 클라우드 기반 Azure 가상 네트워크가 필요할 수 있습니다. 인스턴스를 배포하기 위해 새 가상 네트워크를 만들어야 합니다. 선호도 그룹에서 가상 네트워크에 A8, A9, A10 또는 A11 VM을 추가하는 것은 지원되지 않습니다.

* **클라우드 서비스 또는 가용성 집합** – RDMA 네트워크를 통해 연결하려면 A8 및 A9 인스턴스가 동일한 클라우드 서비스(클래식 배포 모델을 사용하는 경우) 또는 동일한 가용성 집합(Azure 리소스 관리자 배포 모델을 사용하는 경우)에서 배포되어야 합니다.

## HPC 팩 사용에 대한 고려 사항

### HPC 팩 및 Linux에 대한 고려 사항

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)은 Windows용 Microsoft의 무료 HPC 클러스터 및 작업 관리 솔루션입니다. HPC Pack 2012 R2의 최신 릴리스는 여러 Linux 배포를 지원하여 Windows Server 헤드 노드를 통해 관리되는 Azure VM에서 배포된 계산 노드에서 실행할 수 있습니다. 자세한 내용은 [Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작](virtual-machines-linux-cluster-hpcpack.md)을 참조하세요.

### HPC 팩 및 Windows에 대한 고려 사항

HPC 팩은 Windows Server를 사용하는 A8, A9, A10 및 A11 인스턴스를 사용할 때는 필요하지 않지만, Azure의 Windows HPC Server 기반 클러스터를 만드는 데 유용한 도구입니다. A8 및 A9 인스턴스의 경우 HPC 팩은 Azure의 RDMA 네트워크에 액세스하는 Windows 기반 MPI 응용 프로그램을 실행하는 가장 효율적인 방법입니다. HPC 팩에 Windows용 MS-MPI의 Microsoft 구현(메시지 전달 인터페이스)에 대한 런타임 환경을 포함합니다.

Windows Server에서 HPC 팩을 사용하여 계산 집약적 인스턴스를 사용하는 방법에 대한 자세한 내용 및 검사 목록은 [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](virtual-machines-windows-hpcpack-cluster-rdma.md)을 참조하세요.

## RDMA 네트워크에 액세스

### Linux A8 및 A9 VM에서 액세스

단일 클라우드 서비스 또는 가용성 집합 내에서 A8 및 A9 인스턴스가 Linux MPI 응용 프로그램을 실행하기 위해 Azure의 RDMA 네트워크에 액세스할 수 있습니다. 이때 Azure Linux RDMA는 [Intel MPI Library 5](https://software.intel.com/ko-KR/intel-mpi-library/)에서만 지원됩니다.

>[AZURE.NOTE] 현재 Azure Linux RDMA 드라이버는 드라이버 확장을 통한 설치에 사용할 수 없습니다. 오직 Azure Marketplace의 RDMA-enabled SLES 12 이미지를 사용하는 경우에만 사용 가능합니다.

다음 표에서 계산 노드의 클러스터(laaS)에서 RDMA 네트워크에 액세스하기 위해 Linux MPI 응용 프로그램에 대한 필수 조건을 요약합니다. 배포 옵션 및 구성 단계는 [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](virtual-machines-linux-cluster-rdma.md)을 참조하세요.

필수 요소 | 가상 컴퓨터(IaaS)
------------ | -------------
운영 체제 | Azure Marketplace의 SLES 12 HPC 이미지
MPI | Intel MPI Library 5

### Windows A8 및 A9 인스턴스에서 액세스

단일 클라우드 서비스 또는 가용성 집합 내에서 A8 및 A9 인스턴스는 인스턴스 간 통신을 위한 Microsoft Network Direct 인터페이스를 사용하는 Windows MPI 응용 프로그램을 실행하도록 Azure의 RDMA 네트워크에 액세스할 수 있습니다.

A8 또는 A9 인스턴스의 가상 컴퓨터(IaaS) 및 클라우드 서비스(PaaS) 배포에서 RDMA 네트워크에 액세스하기 위한 MPI 응용 프로그램의 필수 요소는 다음 표를 참조하세요. 일반적 배포 시나리오는 [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](virtual-machines-windows-hpcpack-cluster-rdma.md)를 참조하세요.


필수 요소 | 가상 컴퓨터(IaaS) | 클라우드 서비스(PaaS)
---------- | ------------ | -------------
운영 체제 | Windows Server 2012 R2 또는 Windows Server 2012 | Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2 게스트 OS 제품군
MPI | MS-MPI 2012 R2 이상, 독립 실행형 또는 HPC Pack 2012 R2 이상을 통한 설치<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 이상, HPC Pack 2012 R2 이상을 통한 설치<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]IaaS 시나리오의 경우, RDMA 연결에 필요한 Windows 네트워크 장치 드라이버를 설치하는 HpcVmDrivers 확장을 VM에 추가해야 합니다. 배포 방법에 따라 A8 또는 A9 크기의 VM에 HpcVmDrivers 확장이 자동으로 추가되거나 직접 추가해야 할 수도 있습니다. 확장을 추가하려면 [VM 확장 관리](virtual-machines-extensions-install.md)를 참조하세요.


## 알아야할 추가 사항

* **가격 책정** - A8~A11 VM 크기는 표준 가격 책정 계층에서만 사용할 수 있습니다.

* **크기 조정** – A8–A11이 아닌 크기의 인스턴스를 계산 집약적 인스턴스 크기(A8-11) 중 하나로 조정할 수 없으며 계산 집약적 인스턴스를 그렇지 않은 크기로 조정할 수 없습니다. 이것은 계산 집약적 인스턴스에 특정한 성능 최적화 및 특수 하드웨어 때문에 그렇습니다.

* **RDMA 네트워크 주소 공간** - Azure의 RDMA 네트워크는 주소 공간 172.16.0.0/12를 예약합니다. Azure 가상 네트워크의 A8 및 A9 인스턴스에서 MPI 응용 프로그램을 실행하려면 가상 네트워크 주소 공간이 RDMA 네트워크와 겹치지 않도록 해야 합니다.

## 다음 단계

* A8, A9, A10 및 A11 인스턴스의 가용성 및 가격에 대한 세부 정보는 [가상 컴퓨터 가격](https://azure.microsoft.com/pricing/details/virtual-machines/) 및 [클라우드 서비스 가격](https://azure.microsoft.com/pricing/details/cloud-services/)을 참조하세요.
* Azure RDMA 네트워크에 액세스할 수 있는 A8 및 A9 인스턴스를 포함한 Linux 기반 클러스터를 배포 및 구성하려면 [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](virtual-machines-linux-cluster-rdma.md)을 참조하세요.
* Windows에서 HPC 팩을 사용하여 A8 및 A9 인스턴스의 배포 및 사용을 시작하려면 [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](virtual-machines-windows-hpcpack-cluster-rdma.md)을 참조하세요.

<!---HONumber=AcomDC_0204_2016-->