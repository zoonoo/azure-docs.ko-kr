<properties
 pageTitle="A8, A9, A10 및 A11 인스턴스 정보 | Microsoft Azure"
 description="Azure A8, A9, A10 및 A11 계산 집약적 인스턴스를 사용하기 위한 배경 정보 및 고려 사항을 제공합니다."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="07/08/2015"
 ms.author="danlep"/>

# A8, A9, A10 및 A11 계산 집약적 인스턴스 정보

이 문서에서는 *계산 집약적* 인스턴스라고도 하는 Azure A8, A9, A10 및 A11 인스턴스를 사용하기 위한 배경 정보 및 고려 사항을 제공합니다. 이러한 인스턴스의 주요 기능은 다음과 같습니다.

* **고성능 하드웨어** - 이러한 인스턴스를 실행하는 Azure 데이터센터 하드웨어는 HPC(고성능 컴퓨팅) 클러스터 응용 프로그램, 모델링 및 시뮬레이션을 비롯한 네트워크 집약적 응용 프로그램 및 계산을 위해 디자인되고 최적화되었습니다.

* **MPI 응용 프로그램에 대한 RDMA 네트워크 연결** - 필요한 네트워크 드라이버를 사용하여 구성할 때, A8 및 A9 인스턴스는 RDMA(원격 직접 메모리 액세스) 기술을 기반으로 하는 Azure에서 대기 시간이 짧고 처리량이 많은 네트워크를 통해 다른 A8 및 A9 인스턴스와 통신할 수 있습니다. 이 기능은 지원되는 Linux 또는 Windows MPI(메시지 전달 인터페이스) 구현을 사용하는 응용 프로그램의 성능을 강화할 수 있습니다.

* **Linux 및 Windows HPC 클러스터 지원** - Azure의 A8, A9, A10 및 A11 인스턴스에서 클러스터 관리 및 작업 예약 소프트웨어를 배포하여 독립 실행형 HPC 클러스터를 만들거나 온-프레미스 클러스터에 용량을 추가합니다. 다른 Azure VM 크기와 같이 A8, A9, A10 및 A11 인스턴스도 표준 또는 사용자 지정 Windows Server 및 Linux 운영 체제 이미지 또는 Azure VM에서(laaS) 리소스 관리자 템플릿 또는 클라우드 서비스에서 Azure 게스트 OS 릴리스(PaaS, Windows Server 전용)를 지원합니다.

>[AZURE.NOTE]A10 및 A11 인스턴스는 A8 및 A9 인스턴스와 성능 최적화 및 사양이 동일합니다. 하지만 Azure의 RDMA 네트워크에 대한 액세스는 포함하지 않습니다. 노드 간에 지속적이고 대기 시간이 짧은 통신을 필요로 하지 않는 HPC 응용 프로그램을 위해 설계되었으며, 매개 변수 또는 병렬 처리 응용 프로그램이라고도 합니다. 그리고 MPI 응용 프로그램이 아닌 워크로드를 실행할 때 A8 및 A9 인스턴스는 RDMA 네트워크에 액세스하지 않으며 기능적으로 A10 및 A11 인스턴스와 동일합니다.


## 사양

### CPU 및 메모리

Azure A8, A9, A10 및 A11 계산 집약적 인스턴스는 다음 표와 같이 고속 멀티 코어 CPU 및 대량 메모리를 특징으로 합니다.

크기 | CPU | 메모리
------------- | ----------- | ----------------
A8 및 A10 | Intel® Xeon® E5-2670<br/>8코어 @ 2.6GHz | DDR3-1600MHz<br/>56GB
A9 A11 | Intel® Xeon® E5-2670<br/>16코어 @ 2.6GHz | DDR3-1600MHz<br/>112GB


>[AZURE.NOTE]지원되는 명령어 집합 확장을 비롯한 추가 프로세서 세부 정보는 Intel.com 웹 사이트에 있습니다. VM 저장소 용량 및 디스크 세부 정보는 [가상 컴퓨터 크기](virtual-machines-size-specs.md)를 참조하세요.

### 네트워크 어댑터

A8 및 A9 인스턴스에는 다음 두 백 엔드 Azure 네트워크에 연결되는 두 네트워크 어댑터가 있습니다.


네트워크 | 설명
-------- | -----------
10Gbps 이더넷 | Azure 서비스(예: 저장소 및 가상 네트워크) 및 인터넷에 연결
32Gbps 백 엔드, RDMA 지원 | 단일 클라우드 서비스 또는 가용성 집합 내의 인스턴스 사이에서 대기 시간이 짧고 처리량이 많은 응용 프로그램 통신을 사용할 수 있습니다. MPI 트래픽 전용으로 예약됩니다.


>[AZURE.IMPORTANT]laaS에서 Linux를 실행하는 A8 및 A9 VM의 경우, RDMA 네트워크에는 현재 SUSE Linux Enterprise Server 12에서 Azure Linux RDMA 및 Intel MPI Library 5.0을 사용하는 응용 프로그램을 통해서만 액세스할 수 있습니다. IaaS 또는 PaaS에서 Windows Server를 실행하는 A8 및 A9 인스턴스의 경우, RDMA 네트워크에는 현재 Microsoft MPI(MS-MPI)를 통해 Microsoft Network Direct 인터페이스를 사용하는 응용 프로그램을 통해서만 액세스할 수 있습니다. 추가 요구 사항은 이 문서에 있는 [RDMA 네트워크에 액세스](#access-the-rdma-network)를 참조하세요.

A10 및 A11 인스턴스에는 Azure 서비스 및 인터넷에 연결하는 한 개의 10Gbps 이더넷 네트워크 어댑터가 있습니다.

## Azure 구독에 대한 고려 사항

* **Azure 계정** - 소량의 계산 집약적 인스턴스를 배포하려는 경우 종량제 구독 또는 기타 구매 옵션을 고려합니다. MSDN 구독을 이용할 수도 있습니다. [MSDN 구독자에 대한 Azure 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세. [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 사용하는 경우 제한된 수의 Azure 계산 코어만 사용할 수 있습니다.

* **코어 할당량** - 8코어 또는 16코더 인스턴스로는 많은 시나리오의 경우 충분하지 않기 때문에 기본 20코어에서 Azure 구독의 코어 할당량을 높여야 할 수 있습니다. 초기 테스트의 경우 할당량을 100코어로 높이도록 요청해볼 수 있습니다. 이를 위해 [Azure 제한 및 증가 이해](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)에서 볼 수 있듯이 무료 지원 티켓을 엽니다.

>[AZURE.NOTE]Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 사용하는 코어에만 요금이 부과됩니다.

* **선호도 그룹** - 현재 선호도 그룹은 대부분의 새 배포에 대해서는 권장되지 않습니다. 하지만, A8~A11을 제외한 크기의 인스턴스를 포함하는 선호도 그룹을 사용 중인 경우 A8~A11 인스턴스에 대해서도 이를 사용할 수 없으며 반대의 경우도 마찬가지입니다.

* **가상 네트워크** - Azure 가상 네트워크는 계산 집약적 인스턴스를 사용할 필요가 없습니다. 하지만 응용 프로그램 라이선스 서버와 같은 온-프레미스 리소스에 액세스해야 하는 경우 많은 IaaS 시나리오 또는 사이트 간 연결을 위해 클라우드 기반 Azure 가상 네트워크가 필요할 수 있습니다. 인스턴스를 배포하기 전에 새(지역) 가상 네트워크를 만들어야 합니다. 선호도 그룹에서 가상 네트워크에 A8, A9, A10 또는 A11 VM을 추가하는 것은 지원되지 않습니다. 자세한 내용은 [가상 네트워크 만들기](https://msdn.microsoft.com/library/azure/dn631643.aspx) 및 [사이트 간 VPN 연결로 가상 네트워크 구성](../vpn-gateway/vpn-gateway-site-to-site-create.md)을 참조하세요.

* **클라우드 서비스 또는 가용성 집합** – RDMA 네트워크를 통해 연결하기 위해 A8 및 A9 인스턴스는 동일한 클라우드(Azure 서비스 관리에서 Linux VM 또는 Windows VM을 사용하는 laaS 시나리오 또는 Windows Server를 사용하는 PaaS 시나리오) 또는 동일한 가용성 집합(Azure 리소스 관리자에서 Linux 또는 Windows VM)에서 배포되어야 합니다.

## HPC 팩 사용에 대한 고려 사항

### HPC 팩 및 Linux에 대한 고려 사항

[HPC 팩](https://technet.microsoft.com/library/jj899572.aspx)은 Windows용 Microsoft의 무료 HPC 클러스터 및 작업 관리 솔루션입니다. HPC Pack 2012 R2 Update 2부터 HPC 팩은 여러 Linux 배포를 지원하여 Windows Server 헤드 노드를 통해 관리되는 Azure VM에서 배포된 계산 노드에서 실행할 수 있습니다. HPC 팩의 최신 릴리스에서 Azure의 RDMA 네트워크에 액세스하는 MPI 응용 프로그램을 실행할 수 있는 Linux 기반 클러스터를 배포할 수 있습니다. 자세한 내용은 [HPC 팩 설명서](http://go.microsoft.com/fwlink/?LinkId=617894)를 참조하세요.

### HPC 팩 및 Windows에 대한 고려 사항

HPC 팩은 Windows 서버와 함께 A8, A9, A10 및 A11 인스턴스를 사용하는 데에는 필요하지 않지만, Azure에서 Windows HPC 클러스터를 만들기 위한 권장 도구입니다. A8 및 A9 인스턴스의 경우 HPC 팩은 Azure의 RDMA 네트워크에 액세스하는 Windows MPI 응용 프로그램을 실행하는 가장 효율적인 방법입니다. HPC 팩에 Windows용 메시지 전달 인터페이스의 Microsoft 구현에 대한 런타임 환경을 포함합니다.

Windows Server에서 HPC 팩을 사용하여 IaaS 및 PaaS 시나리오에서 계산 집약적 인스턴스를 배포 및 사용하기 위한 자세한 정보 및 확인 목록은 [A8 및 A9 계산 집약적 인스턴스: HPC 팩을 사용한 빠른 시작](https://msdn.microsoft.com/library/azure/dn594431.aspx)을 참조하세요.

## RDMA 네트워크에 액세스

### Linux A8 및 A9 VM에서 액세스

단일 클라우드 서비스 또는 가용성 집합 내에서 인스턴스 간에 통신을 위해 Linux RDMA 드라이버를 사용하는 MPI 응용 프로그램을 실행할 때 A8 및 A9 인스턴스는 Azure의 RDMA 네트워크에 액세스할 수 있습니다. 현재 Azure Linux RDMA는 [Intel MPI Library 5.0](https://software.intel.com/ko-kr/intel-mpi-library/)만 지원합니다.

>[AZURE.NOTE]현재 Azure Linux RDMA 드라이버는 드라이버 확장을 통해 설치에 사용할 수 없습니다. Azure Marketplace의 RDMA 지원 SLES 12 이미지를 사용하는 경우에만 사용할 수 있습니다.

계산 노드의 클러스터(laaS)에서 RDMA 네트워크에 액세스하기 위해 Linux MPI 응용 프로그램에 대한 필수 구성 요소는 다음 표를 참조하세요.

필수 요소 | 가상 컴퓨터(IaaS)
------------ | -------------
운영 체제 | Azure Marketplace의 SUSE Linux Enterprise Server 12(SLES 12) HPC 이미지
MPI | Intel MPI Library 5.0

### Windows A8 및 A9 인스턴스에서 액세스

단일 클라우드 서비스 또는 가용성 집합 내에서 Microsoft Network Direct 인터페이스를 사용하여 인스턴스 간에 통신하는 MPI 응용 프로그램을 실행할 때 A8 및 A9 인스턴스는 Azure의 RDMA 네트워크에 액세스할 수 있습니다. 이 때 Network Direct는 Microsoft의 Windows용 MS-MPI를 통해서만 지원됩니다. A10 및 A11 인스턴스는 RDMA 네트워크에 대한 액세스는 포함하지 않습니다.

A8 또는 A9 인스턴스의 가상 컴퓨터(IaaS) 및 클라우드 서비스(PaaS) 배포에서 RDMA 네트워크에 액세스하기 위한 MPI 응용 프로그램의 필수 요소는 다음 표를 참조하세요. 일반적인 배포 시나리오는 [A8 및 A9 계산 집약적 인스턴스: HPC 팩을 사용한 빠른 시작](https://msdn.microsoft.com/library/azure/dn594431.aspx)을 참조하세요.


필수 요소 | 가상 컴퓨터(IaaS) | 클라우드 서비스(PaaS)
---------- | ------------ | -------------
운영 체제 | Windows Server 2012 R2 또는 Windows Server 2012 VM | Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2 게스트 OS 제품군
MPI | MS-MPI 2012 R2 이상, 독립 실행형 또는 HPC Pack 2012 R2 이상을 통한 설치 | MS-MPI 2012 R2 이상, HPC Pack 2012 R2 이상을 통한 설치


>[AZURE.NOTE]IaaS 시나리오의 경우 RDMA 연결에 필요한 Windows 드라이버를 설치하려면 [HpcVmDrivers 확장](https://msdn.microsoft.com/library/azure/dn690126.aspx)이 VM에 추가되어야 합니다.


## 알아야할 추가 사항

* A8~A11 VM 크기는 표준 가격 책정 계층에서만 사용할 수 있습니다.

* A8, A9, A10 또는 A11 크기로 기존 Azure VM의 크기를 조정할 수 없습니다.

* A8, A9, A10 및 A11 인스턴스는 현재 기존 선호도 그룹의 일부인 클라우드 서비스를 사용하여 배포할 수 없습니다. 마찬가지로, A8, A9, A10 및 A11 인스턴스를 포함하는 클라우드 서비스가 있는 선호도 그룹은 다른 인스턴스 크기의 배포에 사용할 수 없습니다. 이러한 배포를 시도하는 경우 `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`와(과) 유사한 오류 메시지를 보게 됩니다.


## 다음 단계

* A8, A9, A10 및 A11 인스턴스의 가용성 및 가격에 대한 세부 정보는 [가상 컴퓨터 가격](http://azure.microsoft.com/pricing/details/virtual-machines/) 및 [클라우드 서비스 가격](http://azure.microsoft.com/pricing/details/cloud-services/)을 참조하세요.
* Windows에서 HPC 팩을 사용하여 A8 및 A9 인스턴스 배포 및 사용을 시작하려면 [A8 및 A9 계산 집약적 인스턴스: HPC 팩을 사용한 빠른 시작](https://msdn.microsoft.com/library/azure/dn594431.aspx) 및 [A8 및 A9 인스턴스에서 MPI 응용 프로그램 실행](https://msdn.microsoft.com/library/azure/dn592104.aspx)을 참조하세요.

<!---HONumber=July15_HO2-->