---
title: Azure(대규모 인스턴스)에서 SAP HANA 네트워크 아키텍처 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA를 배포하는 방법에 대한 네트워크 아키텍처입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8794a93cecb50774f30746c22931db31a9fa9194
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239607"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA(대규모 인스턴스) 네트워크 아키텍처

Azure 네트워크 서비스의 아키텍처는 SAP 애플리케이션을 HANA 대규모 인스턴스에 성공적으로 배포하는 핵심 구성 요소입니다. 일반적으로 Azure(큰 인스턴스)에서 SAP HANA 배포에는 다양한 데이터베이스 크기, CPU 리소스 사용량 및 메모리 사용률이 있는 서로 다른 여러 SAP 솔루션을 갖춘 더 큰 SAP 환경이 포함됩니다. 일부 IT 시스템 Azure에 이미 있는 것입니다. SAP 지형에는 지점 DBMS 및 SAP 응용 프로그램 관점 NetWeaver S/4HANA 및 SAP HANA 및 다른 DBMS의 혼합을 사용 하 여에서 하이브리드 경우가 많습니다. Azure는 Azure에서 다양 한 DBMS, NetWeaver 및 S/4HANA 시스템을 실행할 수 있는 다양 한 서비스를 제공 합니다. Azure도 제품 기술을 확인 하는 azure 네트워크와 같은 온-프레미스 소프트웨어 배포는 가상 데이터 센터

않으면 전체 IT 시스템은 Azure에서 호스트 됩니다. Azure 네트워킹 기능 하는 Azure 가상 데이터 센터를 유사 하 여 Azure 자산을 사용 하 여 온-프레미스 세계를 연결할 사용 됩니다. 사용 되는 Azure 네트워크 기능을 다음과 같습니다. 

- Azure 가상 네트워크에 연결 되는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 온-프레미스 네트워크 자산에 연결 하는 회로입니다.
- Azure에 온-프레미스를 연결 하는 ExpressRoute 회로의 최소 대역폭 있어야 [1gbps 이상](https://azure.microsoft.com/pricing/details/expressroute/)합니다. 이 최소 대역폭을 통해 온-프레미스 시스템과 VM에서 실행되는 시스템 간의 데이터 전송에 적합한 대역폭이 허용됩니다. 또한 온-프레미스 사용자의 Azure 시스템에 연결할 수 있는 적절한 대역폭도 허용됩니다.
- Azure에서 모든 SAP 시스템이 서로 통신 하도록 가상 네트워크에서 설정 됩니다.
- 호스팅된 온-프레미스 active Directory 및 DNS 온-프레미스에서 ExpressRoute 통해 Azure로 확장 됩니다 또는 Azure에서 전체 실행 됩니다.

HANA 큰 인스턴스를 Azure 데이터 센터 네트워크 패브릭 통합의 특정 경우 Azure ExpressRoute 기술도 사용 됩니다.


> [!NOTE] 
> 하나의 Azure 구독은 특정 Azure 지역의 HANA 큰 인스턴스 스탬프에 하나의 테 넌 트에 연결할 수 있습니다. 반대로, 단일 HANA 큰 인스턴스 스탬프 테 넌 트를 하나의 Azure 구독에 연결할 수 있습니다. 이 요구 사항은 Azure에서 청구 가능한 다른 개체와 일치합니다.

SAP HANA on Azure (큰 인스턴스)를 여러 다른 Azure 지역에 배포 하는 경우 별도 테 넌 트의 HANA 큰 인스턴스 스탬프에 배포 됩니다. 이러한 인스턴스가 동일한 SAP 자산에 포함되면 둘 다 동일한 Azure 구독에서 실행할 수 있습니다. 

> [!IMPORTANT] 
> SAP HANA on Azure (큰 인스턴스)를 사용 하 여 Azure Resource Manager 배포 방법만 사용할 수 있습니다.

 

## <a name="additional-virtual-network-information"></a>추가 가상 네트워크 정보

ExpressRoute 가상 네트워크에 연결 하려면 Azure ExpressRoute 게이트웨이 만들어야 합니다. 자세한 내용은 [ExpressRoute에 대 한에 대 한 Expressroute 게이트웨이](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)합니다. 

Azure ExpressRoute 게이트웨이 Azure 외부의 인프라 또는 Azure 큰 인스턴스 스탬프에 ExpressRoute를 사용 하 여 사용 됩니다. 다른 Microsoft enterprise edge 라우터에서 제공 되는 이러한 연결을 그대로 Azure ExpressRoute 게이트웨이 최대 4 개의 ExpressRoute 회로를 연결할 수 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 

> [!NOTE] 
> ExpressRoute 게이트웨이 달성할 수 있는 최대 처리량을 10gbps ExpressRoute 연결을 사용 합니다. 가상 네트워크에 있는 VM과 온-프레미스 시스템 간에 파일을 복사하는 경우(단일 복사 스트림으로), 다양한 게이트웨이 SKU의 전체 처리량을 달성할 수 없습니다. ExpressRoute 게이트웨이의 전체 대역폭을 활용 하려면 여러 스트림을 사용 합니다. 또는 단일 파일의 병렬 스트림에서 다른 파일을 복사해야 합니다.


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 대규모 인스턴스에 대한 네트워킹 아키텍처
HANA 대규모 인스턴스에 대한 네트워킹 아키텍처는 다음 네 가지 부분으로 구분할 수 있습니다.

- Azure에 대한 온-프레미스 네트워킹 및 ExpressRoute 연결. 이 부분은 고객의 도메인이며 ExpressRoute 통해 Azure에 연결됩니다. 이 Expressroute 회로 완벽 하 게 고객으로 서 사용자가 지불 됩니다. 대역폭에 대 한 연결 하는 Azure 지역과 온-프레미스 자산 간의 네트워크 트래픽을 처리 하기에 충분 해야 합니다. 다음 그림의 오른쪽 아래 부분을 참조하세요.
- 앞에서 설명한 대로 다시 추가 하는 ExpressRoute 게이트웨이 해야 하는 가상 네트워크를 사용 하 여 azure 네트워크 서비스를 제공 합니다. 이 부분은 애플리케이션 요구 사항, 보안 및 규정 준수 요구 사항에 적합한 디자인을 찾아야 하는 영역입니다. HANA 대규모 인스턴스를 사용하는지 여부는 선택할 가상 네트워크와 Azure 게이트웨이 SKU의 수 측면에서 고려해야 할 또 다른 요소입니다. 그림의 오른쪽 위 부분을 참조하세요.
- Azure에 ExpressRoute 기술을 통해 HANA 대규모 인스턴스 연결. 이 부분은 Microsoft에서 배포하고 처리합니다. HANA 대규모 인스턴스에 자산을 배포한 후 ExpressRoute 회로를 가상 네트워크에 연결하기 위한 일부 IP 주소 범위를 제공하기만 하면 됩니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. Azure 데이터 센터 네트워크 패브릭 및 HANA 큰 인스턴스 단위 간의 연결에 대 한 고객 수에 대 한 추가 요금 없이 있습니다.
- HANA 큰 인스턴스 스탬프 내에서 네트워킹, 즉 대부분 투명 한입니다.

![SAP HANA on Azure(대규모 인스턴스) 및 온-프레미스에 연결된 가상 네트워크](./media/hana-overview-architecture/image1-architecture.png)

HANA 대규모 인스턴스를 사용하기 때문에 온-프레미스 자산에 있는 사용자가 ExpressRoute를 통해 Azure에 연결해야 한다는 요구 사항은 변경되지 않습니다. HANA 대규모 인스턴스 장치에서 호스팅되는 HANA 인스턴스에 연결하는 애플리케이션 계층을 호스팅하는 VM을 실행하는 하나 이상의 가상 네트워크가 있어야 한다는 요구 사항도 변경되지 않습니다. 

Azure에서 SAP 배포의 차이점은 다음과 같습니다.

- 고객 테넌트의 HANA 대규모 인스턴스 장치는 또 다른 ExpressRoute 회로 통해 가상 네트워크에 연결됩니다. 로드 조건을 분리 하는 온-프레미스와 Azure 가상 네트워크 ExpressRoute 회로 및 Azure 가상 네트워크와 HANA 대규모 인스턴스 간의 회로 동일한 라우터를 공유 하지 마세요.
- SAP 애플리케이션 계층과 HANA 대규모 인스턴스 간의 워크로드 프로필은 SAP HANA에서 애플리케이션 계층으로의 데이터 전송(결과 집합)과 같이 많은 작은 요청과 버스트와는 다른 특성을 가지고 있습니다.
- SAP 애플리케이션 아키텍처는 온-프레미스와 Azure 간에 데이터가 교환되는 일반적인 시나리오보다 네트워크 대기 시간에 더 민감합니다.
- Azure ExpressRoute 게이트웨이 두 개 이상의 ExpressRoute 연결이 있습니다. 온-프레미스에서 HANA 큰 인스턴스에 연결 된 하나에서 연결 된 하나의 회로입니다. 이 둡니다만 다른 두 개의 추가 회로에 ExpressRoute 게이트웨이를 연결할 다른 Msee에서. 이 제한은 ExpressRoute 빠른 경로의 사용량 독립적입니다. 연결 된 모든 회로 ExpressRoute 게이트웨이의 들어오는 데이터에 대 한 최대 대역폭을 공유합니다.

VM과 HANA 대규모 인스턴스 장치 간에 발생하는 네트워크 대기 시간은 일반적인 VM 간 네트워크 왕복 대기 시간보다 길 수 있습니다. Azure 지역에 따라 측정 된 값을 초과할 수에서 평균 미달로 분류 된 0.7ms 왕복 대기 시간 [SAP Note #1100926-FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)합니다. Azure VM 및 HANA 대규모 인스턴스 단위 간의 네트워크 왕복 대기 시간을 측정하는 Azure 지역 및 도구에 따라 측정된 대기 시간은 최대 약 2밀리초일 수 있습니다. 그럼에도 불구하고 고객은 SAP HANA 기반 프로덕션 SAP 애플리케이션을 SAP HANA 대규모 인스턴스에 성공적으로 배포합니다. Azure HANA 대규모 인스턴스에서 자신의 비즈니스 프로세스를 철저하게 테스트해야 합니다. ExpressRoute 빠른 경로 라는 새로운 기능에는 HANA 큰 인스턴스 및 응용 프로그램 계층 Azure에서 Vm 간의 네트워크 대기 시간을 크게 줄일 수 (아래 참조). 

Vm 및 HANA 대규모 인스턴스를 ExpressRoute 게이트웨이 선택 간에 결정적인 네트워크 대기 시간을 제공 하려면 SKU는 필수입니다. 온-프레미스와 VM 간의 트래픽 패턴과는 달리, VM과 HANA 대규모 인스턴스 간의 트래픽 패턴은 작지만 높은 버스트의 요청 및 데이터 볼륨을 전송하도록 개발할 수 있습니다. 이러한 버스트를 잘 처리하려면 UltraPerformance 게이트웨이 SKU를 사용하는 것이 좋습니다. HANA 대규모 인스턴스 Sku의 Type II 클래스에 대 한 인스턴스용 게이트웨이로 UltraPerformance 게이트웨이 SKU 사용은 필수입니다.

> [!IMPORTANT] 
> SAP 애플리케이션과 데이터베이스 계층 간의 전체 네트워크 트래픽을 고려할 때, SAP HANA on Azure(대규모 인스턴스) 연결에는 가상 네트워크용 HighPerformance 또는 UltraPerformance 게이트웨이 SKU만 지원됩니다. HANA 대규모 인스턴스 유형 II Sku, ExpressRoute 게이트웨이로 UltraPerformance 게이트웨이 SKU만 지원 됩니다. ExpressRoute 빠른 경로 (아래 참조)를 사용 하는 경우 예외가 적용

### <a name="expressroute-fast-path"></a>ExpressRoute 빠른 경로
대기 시간을 낮게, ExpressRoute 빠른 경로 도입 및 SAP 응용 프로그램 Vm을 호스팅하는 Azure 가상 네트워크를 HANA 큰 인스턴스의 특정 연결에 대 한 2019 년 5 월에에서 릴리스된입니다. Vm 및 HANA 큰 인스턴스 간의 데이터 흐름에서 ExpressRoute 게이트웨이 통해 더 이상 라우팅되지는 지금 롤아웃 솔루션에 주요 차이점은입니다. 대신 Azure 가상 네트워크의 서브넷에 할당 된 Vm 전용된 엔터프라이즈에 지 라우터가와 직접 통신 합니다. 

> [!IMPORTANT] 
> ExpressRoute 빠른 경로 기능을 실행 중인 SAP 응용 프로그램 Vm 서브넷에서 가져온 HANA 큰 인스턴스에 연결 하는 동일한 Azure 가상 네트워크에 있어야 합니다. Azure 가상 네트워크 피어 링 되는 HANA 큰 인스턴스 단위에 직접 연결 된 Azure 가상 네트워크에에서 있는 Vm은 ExpressRoute 빠른 경로에서 이익이 없습니다. 결과적으로 일반적인 허브 및 스포크 가상 네트워크 설계를 허브 가상 네트워크에 대 한 ExpressRoute 회로 연결 하는 SAP 응용 프로그램 계층 (스포크)를 포함 하는 가상 네트워크 피어 링 가져오기는 않았고, ExpressRoute 빠른에서 최적화 경로 작동 하지 않습니다. 또한 ExpressRoute 빠른 경로 지원 하지 않습니다 (UDR) 사용자 정의 라우팅 규칙 지금. 자세한 내용은 [ExpressRoute 가상 네트워크 게이트웨이 및 FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)합니다. 


ExpressRoute 빠른 경로 구성 하는 방법에 대 한 자세한 내용은 문서를 읽어보세요 [가상 네트워크를 HANA 큰 인스턴스에 연결할](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)합니다.    

> [!NOTE]
> UltraPerformance ExpressRoute 게이트웨이 ExpressRoute 빠른 경로 사용할 필요가


## <a name="single-sap-system"></a>단일 SAP 시스템

앞에서 보여 준 온-프레미스 인프라는 ExpressRoute를 통해 Azure에 연결됩니다. ExpressRoute 회로 Microsoft enterprise edge 라우터 (MSEE)에 연결합니다. 자세한 내용은 [ExpressRoute 기술 개요](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 경로 설정 되 면 Azure 백본에 연결 합니다.

> [!NOTE] 
> Azure에서 SAP 자산을 실행하려면 SAP 자산에서 Azure 지역과 가장 가까운 엔터프라이즈 에지 라우터에 연결합니다. HANA 큰 인스턴스 스탬프는 Azure iaas에서 Vm 및 HANA 큰 인스턴스 스탬프 간에 네트워크 대기 시간을 최소화 하기 위해 전용된 엔터프라이즈에 지 라우터 장치를 통해 연결 됩니다.

SAP 응용 프로그램 인스턴스를 호스팅하는 Vm에 대 한 ExpressRoute 게이트웨이 온-프레미스에 연결 하는 하나의 ExpressRoute 회로에 연결 됩니다. 동일한 가상 네트워크는 대규모 인스턴스 스탬프에 전용으로 연결하기 위해 별도의 엔터프라이즈 에지 라우터에 연결됩니다. ExpressRoute 빠른 경로 HANA 큰 인스턴스에서 SAP 응용 프로그램 계층을 데이터 흐름을 사용 하 여 Vm 라우팅되지 않는 ExpressRoute 게이트웨이 통해 더 이상 하 고는 사용 하 여 네트워크 왕복 대기 시간을 줄입니다.

이 시스템은 단일 SAP 시스템의 간단한 예입니다. SAP 애플리케이션 계층은 Azure에서 호스팅됩니다. SAP HANA 데이터베이스는 SAP HANA on Azure(대규모 인스턴스)에서 실행됩니다. 2gbps 또는 10gbps 처리량의 ExpressRoute 게이트웨이 대역폭에서 병목 현상이 나타나지는으로 가정 합니다.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>여러 SAP 시스템 또는 큰 SAP 시스템

여러 SAP 시스템 또는 큰 SAP 시스템에 배포 하는 SAP HANA on Azure (큰 인스턴스)에 연결 하는 경우 ExpressRoute 게이트웨이 처리량에 병목 상태가 될 수 있습니다. 또는 프로덕션 및 비프로덕션 시스템 서로 다른 Azure 가상 네트워크에서 격리 하려고 합니다. 이 경우 애플리케이션 계층을 여러 가상 네트워크로 분할합니다. 또한 다음과 같은 경우 HANA 대규모 인스턴스에 연결하는 특수 가상 네트워크를 만들 수도 있습니다.

- HANA 대규모 인스턴스의 HANA 인스턴스에서 NFS 공유를 호스팅하는 Azure의 VM으로 백업을 직접 수행합니다.
- 큰 백업 또는 HANA 큰 인스턴스 단위의 기타 파일을 Azure에서 관리하는 디스크 공간으로 복사합니다.

HANA 큰 인스턴스 및 Azure 간에 대용량 데이터 전송에 대 한 저장소를 관리 하는 Vm 호스트에 별도 가상 네트워크를 사용 합니다. 이 정렬 큰 파일이 나 데이터 전송을 HANA 대규모 인스턴스로부터 Azure에 ExpressRoute 게이트웨이에서 SAP 응용 프로그램 계층을 실행 하는 Vm을 제공 하는 효과 방지할 수 있습니다. 

확장성 있는 네트워크 아키텍처를 위해서는 다음과 같이 합니다.

- 더 큰 단일 SAP 애플리케이션 계층에 대해 여러 가상 네트워크를 활용합니다.
- 동일한 가상 네트워크 하에서 별도의 서브넷에 있는 이러한 SAP 시스템을 결합하는 것과 비교하여 배포된 각 SAP 시스템에 대해 별도의 가상 네트워크를 하나씩 배포합니다.

  Azure(큰 인스턴스)에서 SAP HANA에 대한 보다 확장성 있는 네트워킹 아키텍처

![여러 가상 네트워크를 통해 SAP 애플리케이션 계층 배포](./media/hana-overview-architecture/image4-networking-architecture.png)

규칙 및 제한 사항에 따라, 적용 하려는 다른 SAP 시스템의 Vm을 호스팅하는 다른 가상 네트워크 간에, 해당 가상 네트워크를 피어 링 해야 합니다. 가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)을 참조하세요.


## <a name="routing-in-azure"></a>Azure에서 라우팅

기본 배포에서 세 가지 네트워크 라우팅 고려 사항이 SAP HANA on Azure (큰 인스턴스)에 대 한 중요 한 있습니다.

* SAP HANA on Azure (큰 인스턴스)는 Azure Vm 및 온-프레미스에서 직접 전용된 ExpressRoute 연결을 통해서만 액세스할 수 있습니다. Microsoft에서 제공하는 것처럼 온-프레미스에서 HANA 대규모 인스턴스 장치에 직접적으로 즉시 액세스할 수 없습니다. 전이적 라우팅 제한은 SAP HANA 대규모 인스턴스에 사용되는 현재 Azure 네트워크 아키텍처로 인해 발생합니다. 온-프레미스에서 실행되는 SAP 솔루션 관리자와 같이 직접 액세스가 필요한 관리 클라이언트 및 애플리케이션 일부는 SAP HANA 데이터베이스에 연결할 수 없습니다. 예외에 대 한 ' HANA 큰 인스턴스에 대 한 직접 라우팅을 ' 섹션을 확인 합니다.

* 서로 다른 두 Azure 지역 재해 복구를 위해 이전에 적용 되는 동일한 일시적 라우팅 제한이에 배포 된 HANA 큰 인스턴스 단위에 있으면 합니다. 즉, IP 주소 (예: 미국 서 부) 지역에서 HANA 큰 인스턴스 단위의 된 다른 지역 (예: 미국 동부)에 배포 된 HANA 대규모 인스턴스 장치로 라우팅되지 않습니다. 이 제한 된 Azure 네트워크를 지역에서 피어 링 또는 가상 네트워크에 연결 하는 HANA 큰 인스턴스 단위는 ExpressRoute 회로 간 연결의 사용과 별개입니다. 그래픽 표현은 "여러 지역에서 HANA 대규모 인스턴스 장치 사용" 섹션의 그림을 참조하세요. 배포 된 아키텍처와 함께 제공에이 제한은 HANA 시스템 복제를 재해 복구 기능으로 즉시 사용할 수 없습니다. 최근 변경 내용에 대 한 '여러 지역에서 사용 하 여 HANA 큰 인스턴스 단위' 섹션을 조회 합니다. 

* SAP HANA on Azure (큰 인스턴스) 장치는 HANA 큰 인스턴스 배포를 요청할 때 사용자가 제출한 서버 IP 풀 주소 범위에서 할당 된 IP 주소가 있으며 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 이 IP 주소는 Azure 구독 및 Azure 가상 네트워크를 HANA 큰 인스턴스에 연결 하는 회로 통해 액세스할 수 있습니다. 해당 서버 IP 풀 주소 범위 이외의 할당된 IP 주소는 하드웨어 장치에 직접 할당됩니다. 이 솔루션을 처음 배포하는 경우와 같이 더 이상 NAT를 통해 *할당되지 않습니다*. 

### <a name="direct-routing-to-hana-large-instances"></a>직접 HANA 큰 인스턴스에 라우팅
기본적으로 HANA 큰 인스턴스 단위와 온-프레미스 또는 HANA 큰 인스턴스 라우팅 배포 되는 서로 다른 두 지역에서 간에 전이적 라우팅이 작동 하지 않습니다. 일부의 가능성 이러한는 전이적 라우팅이 사용 하도록 설정 합니다.

- 데이터를 라우팅하는 역방향 프록시를 사용합니다. 예를 들어 F5 BIG-IP, NGINX Azure 가상 네트워크에 배포 된 Traffic Manager와 함께 연결 하는 HANA 큰 인스턴스를 온-프레미스 가상 방화벽/트래픽 라우팅 솔루션으로 합니다.
- Linux VM에서 [IPTables 규칙](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)을 사용하여 온-프레미스 위치와 HANA 대규모 인스턴스 장치 간 또는 다른 지역의 HANA 대규모 인스턴스 장치 간에 라우팅을 사용하도록 설정합니다. IPTables를 실행 하는 VM 온-프레미스를 HANA 큰 인스턴스에 연결 하는 Azure 가상 네트워크에 배포 해야 합니다. VM 요구를 적절 하 게 조정에 있도록, VM의 네트워크 처리량이 예상된 된 네트워크 트래픽에 대해 부족 합니다. VM에 대 한 내용은 네트워크 대역폭에 대 한 문서를 체크 [크기의 Linux 가상 머신에 Azure에서](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)합니다.
- [Azure 방화벽](https://azure.microsoft.com/services/azure-firewall/) 솔루션을 온-프레미스 및 HANA 큰 인스턴스 단위 간의 직접 트래픽을 사용 하도록 설정 됩니다. 

이러한 솔루션의 모든 트래픽을 Azure virtual network를 통해 라우트됩니다와 같이 트래픽에 사용 되는 소프트 어플라이언스를 통해 또는 Azure 네트워크 보안 그룹, 따라서에서 특정 IP 주소 또는 IP 주소 범위는 또한 제한 된 수 온-프레미스를 차단 하거나 명시적으로 HANA 큰 인스턴스에 액세스를 허용 될 수 없습니다. 

> [!NOTE]  
> Microsoft에서는 타사 네트워크 어플라이언스 또는 IPTables와 관련된 사용자 지정 솔루션에 대한 구현 및 지원을 제공하지 않습니다. 지원은 사용되는 구성 요소의 공급업체 또는 통합자에서 제공해야 합니다. 

#### <a name="express-route-global-reach"></a>Express 경로 대 한 글로벌 정보
라는 새 기능이 도입 되었으며 [ExpressRoute 글로벌 환경](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)합니다. 글로벌은 HANA 큰 인스턴스에 대 한 두 가지 시나리오에서 사용할 수 있습니다.

- 다른 지역에 배포 된 HANA 큰 인스턴스 단위 사용 하 여 온-프레미스에서 직접 액세스할 수 있도록
- 다른 지역에 배포 된 HANA 큰 인스턴스 단위 사용 하 여 간의 직접 통신을 사용 하도록 설정


##### <a name="direct-access-from-on-premise"></a>온-프레미스에서 직접 액세스
글로벌 환경 제공 되는 Azure 지역에 온-프레미스 네트워크에 HANA 큰 인스턴스 단위에 연결 하는 Azure 가상 네트워크에 연결 하는 ExpressRoute 회로 대 한 글로벌 환경에서 기능 활성화를 요청할 수 있습니다. ExpressRoute 회로의 온-프레미스 측에 대 한 비용 영향 몇 가지 있습니다. 가격에 대 한 가격을 확인 [도달 하는 추가 기능을 전역](https://azure.microsoft.com/pricing/details/expressroute/)합니다. 관련 회로 HANA 큰 인스턴스 단위를 Azure에 연결 하 여에 대 한 추가 비용은 없습니다. 

> [!IMPORTANT]  
> 네트워크 데이터 및 제어 흐름은 사용 글로벌 환경에 HANA 큰 인스턴스 단위와 온-프레미스 자산 간의 직접 액세스를 사용 하도록 설정 하는 경우 **Azure 가상 네트워크를 통해 라우팅되지 않습니다**, Microsoft 간 직접 엔터프라이즈 exchange 라우터입니다. 결과적으로 모든 ASG 또는 NSG 규칙 또는 방화벽, NVA를 또는 Azure 가상 네트워크에 배포 하는 프록시 형식의 가져오기는 작업 하지 않습니다. **글로벌 환경 ExpressRoute를 사용 하 여 HANA 큰 인스턴스 단위 제한에 온-프레미스에서 직접 액세스할 수 있도록 하 고 HANA 큰 인스턴스 단위에 액세스 권한을 온-프레미스 쪽에서 방화벽에서 정의 해야 하는 경우** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>다른 Azure 지역의 HANA 큰 인스턴스에 연결
마찬가지로, HANA 큰 인스턴스 단위에 온-프레미스를 연결 하는 데 사용할 수 있는 ExpressRoute 글로벌 환경 처럼 것 사용할 수으로 HANA 큰 인스턴스 테 넌 트를 서로 다른 두 지역에 배포 되는 연결. 격리는 두 지역 모두에서 Azure에 연결할 HANA 큰 인스턴스 테 넌 트가 사용 하는 ExpressRoute 회로. 두 개의 다른 지역에 배포 된 두 명의 HANA 큰 인스턴스 테 넌 트를 연결 하는 것에 대 한 추가 요금은 없습니다. 

> [!IMPORTANT]  
> 데이터 흐름 및 제어는 네트워크 간의 트래픽 흐름을 다른 HANA 큰 인스턴스 테 넌 트 azure 네트워크를 통해 라우팅되지 것입니다. 결과적으로 사용할 수 없습니다 Azure 기능이 나 Nva에 두 명의 HANA 큰 인스턴스 테 넌 트 간 통신 제한 사항을 적용 합니다. 

ExpressRoute 글로벌 환경 사용을 가져오는 방법에 대 한 자세한 내용은 문서를 읽어 [가상 네트워크를 HANA 큰 인스턴스에 연결할](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)합니다.


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 대규모 인스턴스의 인터넷 연결
HANA 대규모 인스턴스는 인터넷에 직접 *연결되지 않습니다*. 예를 들어 이 제한으로 인해 OS 공급업체에 OS 이미지를 직접 등록하는 기능이 제한될 수 있습니다. 로컬 SUSE Linux Enterprise Server 구독 관리 도구 서버 또는 Red Hat Enterprise Linux 구독 관리자를 사용해야 할 수도 있습니다.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 및 HANA 대규모 인스턴스 간 데이터 암호화
HANA 대규모 인스턴스 및 VM 간에 전송되는 데이터는 암호화되지 않습니다. 그러나 HANA DBMS 쪽과 JDBC/ODBC 기반 애플리케이션 간의 교환을 위해서만 트래픽 암호화를 사용할 수 있습니다. 자세한 내용은 [이 SAP 설명서](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)를 참조하세요.

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>여러 지역에서 HANA 대규모 인스턴스 장치 사용

재해 복구 설정 ups를 실현 하려면 여러 Azure 지역에 새 해 큰 인스턴스 단위를 포함 해야 합니다. Azure [글로벌 Vnet 피어 링]를 사용 하 여를 사용 하더라도 기본적으로 전이적 라우팅이 작동 하지 않습니다 두 개의 다른 지역의 HANA 큰 인스턴스 테 넌 트 간에. 그러나 글로벌 환경 다른 두 지역에 프로 비전 하면 HANA 큰 인스턴스 단위 간의 통신 경로를 엽니다. ExpressRoute 글로벌 환경 사용 시나리오는 다음 작업을 사용 하도록 설정 합니다.

 - 추가 프록시 또는 방화벽 없이 HANA 시스템 복제
 - 시스템 복사본 또는 시스템 새로 고침을 수행 하려면 두 개의 다른 지역의 HANA 큰 인스턴스 단위 간의 복사 백업


![다른 Azure 지역의 Azure 대규모 인스턴스 스탬프에 연결된 가상 네트워크](./media/hana-overview-architecture/image8-multiple-regions.png)

그림 두 Azure 지역 (회색 선)에서 SAP HANA on Azure (큰 인스턴스)에 연결 하는 데 사용 되는 두 개의 서로 다른 ExpressRoute 회로에 두 지역 모두에서 서로 다른 가상 네트워크는 연결 하는 방법을 보여줍니다. 이 두 가지 교차 연결에 대 한 이유 양쪽 Msee의 가동 중단에서 보호 하는 것입니다. 두 Azure 지역에서 두 가상 네트워크 간의 통신 흐름을 통해 처리 해야 합니다.는 [전역 피어 링](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) 의 두 가상 네트워크는 서로 다른 두 지역 (파란색 점선). 굵은 빨간색 선이 서로 통신 하도록 두 개의 다른 지역의 테 넌 트의 HANA 큰 인스턴스 단위를 허용 하는 글로벌 환경 ExpressRoute 연결을 설명 합니다. 

> [!IMPORTANT] 
> 여러 개의 ExpressRoute 회로를 사용한 경우 트래픽을 적절하게 라우팅할 수 있도록 선행 AS 경로 추가 및 로컬 기본 BGP 설정을 사용해야 합니다.

**다음 단계**
- [SAP HANA(대규모 인스턴스) 저장소 아키텍처](hana-storage-architecture.md) 참조