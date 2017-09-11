---
title: "Azure(큰 인스턴스)에서 SAP HANA의 개요 및 아키텍처 | Microsoft Docs"
description: "Azure(큰 인스턴스)에서 SAP HANA를 배포하는 방법에 대한 아키텍처 개요입니다."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: bcdcbd9e781dc9686f4be18e16bf046de6981a9d
ms.contentlocale: ko-kr
ms.lasthandoff: 08/19/2017

---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처

## <a name="what-is-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure(대규모 인스턴스)란?

SAP HANA on Azure(대규모 인스턴스)는 Azure의 고유한 솔루션입니다. Azure는 SAP HANA를 배포 및 실행하기 위한 Azure Virtual Machines를 제공하는 것 외에 고객 전용 베어 메탈 서버에서 SAP HANA를 실행 및 배포할 수 있는 가능성을 제공합니다. SAP HANA on Azure(대규모 인스턴스) 솔루션은 고객에게 할당되는 비공유 호스트/서버 베어 메탈 하드웨어를 기반으로 구축됩니다. 서버 하드웨어는 계산/서버, 네트워킹 및 스토리지 인프라가 포함된 큰 스탬프 안에 내장됩니다. 이는 하나의 조합으로 HANA TDI 인증을 받았습니다. SAP HANA on Azure(대규모 인스턴스) 서비스 제품은 72개의 CPU와 768GB 메모리 단위부터 시작하여 960개의 CPU와 20TB 메모리 단위까지 다양한 서버 SKU 또는 규모를 제공합니다.

인프라 스탬프 내에서 고객 격리는 테넌트에서 수해오디며 세부적인 형태는 다음과 같습니다.

- 네트워킹: 고객 할당 테넌트별로 가상 네트워크를 통해 인프라 스택 내의 고객 격리. 테넌트는 단일 고객에게 할당됩니다. 고객은 여러 개의 테넌트를 둘 수 있습니다. 테넌트의 네트워크 격리는 인프라 스탬프 수준에서 테넌트 간 네트워크 통신을 차단합니다. 테넌트가 동일한 고객에게 속한 경우에도 마찬가지입니다.
- 저장소 구성 요소: 저장소 볼륨이 할당된 저장소 가상 컴퓨터를 통해 격리. 저장소 볼륨은 하나의 스토리지 가상 컴퓨터에만 할당될 수 있습니다. 저장소 가상 컴퓨터는 SAP HANA TDI 인증 인프라 스택에서 단일 테넌트에만 독점적으로 할당됩니다. 따라서 저장소 가상 컴퓨터에 할당되는 저장소 볼륨은 관련된 단일 특정 테넌트에서만 액세스할 수 있습니다. 다른 배포된 테넌트 간에는 보이지 않습니다.
- 서버 또는 호스트: 서버 또는 호스트 단위는 고객 또는 테넌트 간 공유되지 않습니다. 고객에게 배포되는 서버 또는 호스트는 단일 테넌트에 할당되는 원자성 베어 메탈 계산 단위입니다. 고객이 호스트 또는 서버를 다른 고객과 공유하는 결과를 초래할 수 있는 하드웨어 파티션 또는 소프트 파티션은 사용되지 **않습니다**. 특정 테넌트의 저장소 가상 컴퓨터에 할당되는 저장소 볼륨은 그러한 서버에 탑재됩니다. 테넌트에는 다양한 SKU의 하나 또는 다수의 서버 단위가 할당될 수 있습니다.
- SAP HANA on Azure(대규모 인스턴스) 인프라 스탬프 내에서 많은 수의 다양한 테넌트가 배포되며 네트워킹, 저장소 및 계산 수준에 대한 테넌트 개념을 통해 상호 격리됩니다. 


이러한 베어 메탈 서버 단위는 SAP HANA 실행만 지원합니다. SAP 응용 프로그램 계층 또는 워크로드 미들웨어 계층은 Microsoft Azure Virtual Machines에서 실행됩니다. SAP HANA on Azure(대규모 인스턴스) 단위를 실행하는 인프라 스탬프는 Azure 네트워크 백본에 연결되며 따라서 SAP HANA on Azure(대규모 인스턴스) 단위와 Azure Virtual Machines 간 연결의 지연은 낮습니다.

이 문서는 SAP HANA on Azure(대규모 인스턴스)를 다루는 5개 문서 중 하나입니다. 이 문서에서는 솔루션의 기본 아키텍처, 책임, 제공되는 서비스와 전반적인 기능에 대해 살펴봅니다. 네트워킹 및 연결과 같은 영역의 경우 다른 4개의 문서에서 세부 사항 및 구체적인 내용을 다룹니다. SAP HANA on Azure(대규모 인스턴스) 문서에서는 SAP NetWeaver 설치 또는 Azure VM에 SAP NetWeaver를 배포하는 부분에 대해서는 다루지 않습니다. 이 항목은 동일한 문서 컨테이너에 있는 별도의 문서에서 다룹니다. 


다섯 부분으로 구성된 이 가이드는 다음 항목에 대해 설명합니다.

- [Azure에서 SAP HANA(큰 인스턴스) 개요 및 아키텍처](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure의 SAP HANA(큰 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure에서 SAP HANA(큰 인스턴스)를 설치하고 구성하는 방법](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure의 SAP HANA(큰 인스턴스) 문제 해결 및 모니터링](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>정의

아키텍처 및 기술적 배포 가이드에는 여러 가지 일반적인 정의가 광범위하게 사용됩니다. 다음 용어와 의미에 유의합니다.

- **IaaS:** Infrastructure as a Service
- **PaaS:** Platform as a Service
- **SaaS:** Software as a Service
- **SAP 구성 요소**: ECC, BW, Solution Manager 또는 EP 등의 개별 SAP 응용 프로그램. SAP 구성 요소는 기존의 ABAP 또는 Java 기술을 기반으로 하거나 비즈니스 개체와 같은 비NetWeaver 기반 응용 프로그램을 기반으로 사용할 수 있습니다.
- **SAP 환경:** 하나 이상의 SAP 구성 요소가 논리적으로 그룹화되어 개발, QAS, 훈련, DR 또는 프로덕션과 같은 비즈니스 기능을 수행합니다.
- **SAP 배경:** IT 환경의 전체 SAP 자산입니다. SAP 지형에는 모든 프로덕션 및 비프로덕션 환경이 포함됩니다.
- **SAP 시스템:** SAP ERP 개발 시스템, SAP BW 테스트 시스템, SAP CRM 프로덕션 시스템 등의 응용 프로그램 계층과 DBMS 계층의 조합입니다. Azure 배포에서는 온-프레미스와 Azure 간에 이러한 두 계층 분할을 지원하지 않습니다. 즉, SAP 시스템은 온-프레미스에 배포되거나 Azure에 배포됨을 의미합니다. 그러나 Azure 또는 온-프레미스에는 SAP 지형의 서로 다른 시스템을 배포할 수 있습니다. 예를 들어 Azure에는 SAP CRM 개발 및 테스트 시스템을 배포할 수 있지만 온-프레미스에는 SAP CRM 프로덕션 시스템을 배포할 수 있습니다. Azure(큰 인스턴스)에 있는 SAP HANA의 경우 Azure VM에서 SAP 시스템의 SAP 응용 프로그램 계층을 호스트하고 HANA 큰 인스턴스 스탬프에서 관련된 SAP HANA 인스턴스를 한 단위로 호스트할 수 있습니다.
- **큰 인스턴스 스탬프:** SAP HANA TDI 인증을 받았고 Azure 내에서 SAP HANA 인스턴스를 전용으로 실행하는 하드웨어 인프라 스택입니다.
- **Azure(큰 인스턴스)에서 SAP HANA:** 다양한 Azure 지역에서 큰 인스턴스 스탬프로 배포된 SAP HANA TDI 인증 하드웨어에서 HANA 인스턴스를 실행하는 Azure 제품에 대한 공식적인 이름입니다. 관련 용어인 **HANA 큰 인스턴스**는 Azure(큰 인스턴스)에서 SAP HANA의 약어이며 기술적 배포 가이드에 널리 사용됩니다.
- **프레미스 간:** VM이 온-프레미스 데이터 센터와 Azure 간에 사이트-사이트, 다중 사이트 또는 ExpressRoute 방식으로 연결되는 Azure 구독에 배포되는 시나리오를 설명합니다. 공통 Azure 설명서에서 이러한 종류의 배포를 프레미스 간 시나리오라고도 합니다. 연결하는 이유는 온-프레미스 도메인, 온-프레미스 Active Directory/OpenLDAP 및 온-프레미스 DNS를 Azure로 확장하기 위한 것입니다. 온-프레미스 배경은 Azure 구독의 Azure 자산으로 확장됩니다. 이렇게 확장된 VM은 온-프레미스 도메인에 속할 수 있습니다. 온-프레미스 도메인의 도메인 사용자는 서버에 액세스하고 이러한 VM에서 서비스(예: DBMS 서비스)를 실행할 수 있습니다. 온-프레미스에 배포된 VM과 Azure에 배포된 VM 간의 통신 및 이름 확인이 가능합니다. 이러한 것이 대부분의 SAP 자산이 배포될 것으로 예상되는 일반적인 시나리오입니다. 자세한 내용은 [VPN Gateway 계획 및 설계](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 가이드 및 [Azure Portal을 사용해 사이트 간 연결로 VNet 만들기](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
- **테넌트:** HANA 대규모 인스턴스 스탬프에 배포되는 고객은 “테넌트"로 격리됩니다. 테넌트는 네트워킹, 저장소 및 계산 계층에서 다른 테넌트로부터 격리됩니다. 따라서 서로 다른 테넌트에 할당된 저장소 및 계산 단위는 HANA 대규모 인스턴스 스탬프 수준에서 상호 보거나 통신할 수 없습니다. 고객은 다양한 테넌트에 배포하도록 선택할 수 있습니다. 그러한 경우에도 HANA 대규모 인스턴스 스탬프 수준에서 테넌트 간 통신은 없습니다.

Microsoft Azure 공용 클라우드에서 SAP 워크로드를 배포하는 토픽에 게시된 여러 가지의 추가 리소스가 있습니다. Azure에서 SAP HANA 배포를 계획하고 실행하는 사용자라면 누구든지 Azure IaaS의 보안 주체와 Azure IaaS에서 SAP 워크로드 배포를 경험하고 잘 이해하는 것이 좋습니다. 다음 리소스를 통해 더 많은 정보를 얻고 진행하기 전에 참조합니다.


- [Microsoft Azure Virtual Machines에서 SAP 솔루션 사용](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>인증

NetWeaver 인증 외에도, SAP에서는 Azure IaaS와 같은 특정 인프라에서 SAP HANA를 지원하기 위해 SAP HANA에 대한 특수한 인증을 요구합니다.

SAP HANA 인증과 더불어 NetWeaver에서 핵심 SAP Note는 [SAP Note #1928533 – Azure의 SAP 응용 프로그램: 지원 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533)입니다.

이 [SAP Note #2316233 - Microsoft Azure(큰 인스턴스)에서 SAP HANA](https://launchpad.support.sap.com/#/notes/2316233/E)도 중요합니다. 이 가이드에 설명된 솔루션에 대해 다룹니다. 또한 Azure의 GS5 VM 유형에서 SAP HANA를 실행할 수 있습니다. [이 사례에 대한 정보는 SAP 웹 사이트에 게시됩니다](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

SAP Note #2316233에 나와 있는 Azure(큰 인스턴스)에서 SAP HANA 솔루션을 통해 Microsoft 및 SAP 고객은 Azure에서 큰 SAP Business Suite, SAP Business Warehouse(BW), S/4 HANA, BW/4HANA 또는 기타 SAP HANA 워크로드를 배포할 수 있습니다. 이 솔루션은 SAP-HANA 인증된 전용 하드웨어 스탬프([SAP HANA 맞춤형 데이터 센터 통합 – TDI](https://scn.sap.com/docs/DOC-63140))를 기반으로 합니다. SAP HANA TDI 구성 솔루션으로 실행하면 모든 SAP HANA 기반 응용 프로그램(SAP Business Suite on SAP HANA, SAP Business Warehouse(BW) on SAP HANA, S4/HANA 및 BW4/HANA)이 하드웨어 인프라에서 작동한다는 사실에 안심할 수 있습니다.

Azure Virtual Machines에서 SAP HANA를 실행하는 것과 비교했을 때 훨씬 큰 메모리 볼륨을 제공한다는 이점이 있습니다. 이 솔루션을 사용하도록 설정하려면 몇 가지 주요 측면을 이해해야 합니다.

- SAP 응용 프로그램 계층 및 SAP 이외의 응용 프로그램은 일반적인 Azure 하드웨어 스탬프에서 호스트되는 Azure Virtual Machines(VM)에서 실행됩니다.
- 고객 온-프레미스 인프라, 데이터 센터 및 응용 프로그램 배포는 Azure ExpressRoute(권장) 또는 VPN(가상 사설망)을 통해 Microsoft Azure 클라우드 플랫폼에 연결됩니다. Active Directory(AD) 및 DNS도 Azure로 확장됩니다.
- HANA 워크로드에 대한 SAP HANA 데이터베이스 인스턴스는 Azure(큰 인스턴스)의 SAP HANA에서 실행됩니다. 큰 인스턴스 스탬프는 Azure 네트워킹에 연결되므로 Azure VM에서 실행되는 소프트웨어는 HANA 큰 인스턴스에서 실행되는 HANA 인스턴스와 상호 작용할 수 있습니다.
- Azure(큰 인스턴스)에서 SAP HANA의 하드웨어는 SUSE Linux Enterprise Server 또는 Red Hat Enterprise Linux가 미리 설치되어 IaaS(Infrastructure as a Service)로 제공되는 전용 하드웨어입니다. Azure Virtual Machines와 마찬가지로 운영 체제에 대한 추가 업데이트 및 유지 관리를 사용자의 책임입니다.
- HANA 또는 HANA 큰 인스턴스 단위에서 SAP HANA를 실행하는 데 필요한 추가 구성 요소를 설치하는 것은 Azure에서 SAP HANA에 대해 진행 중인 모든 각 작업 및 관리와 마찬가지로 사용자의 책임입니다.
- 여기에 설명된 솔루션 외에도 Azure(큰 인스턴스)에서 SAP HANA에 연결되는 Azure 구독에 다른 구성 요소를 설치할 수 있습니다.  예를 들어 SAP HANA 데이터베이스와 통신이 가능하거나 직접 연결되는 구성 요소를 설치할 수 있습니다(점프 서버, RDP 서버, SAP HANA Studio, SAP Data Services for SAP BI 시나리오, 네트워크 모니터링 솔루션).
- Azure에서와 같이 HANA 큰 인스턴스는 고가용성 및 재해 복구 기능을 제공합니다.

## <a name="architecture"></a>건축

상위 수준에서 Azure(큰 인스턴스)의 SAP HANA 솔루션에는 Azure VM에 있는 SAP 응용 프로그램 계층과 Azure IaaS에 연결된 동일한 Azure 지역의 큰 인스턴스 스탬프에 위치한 SAP TDI 구성 하드웨어에 있는 데이터베이스 계층이 포함됩니다.

> [!NOTE]
> SAP 응용 프로그램 계층은 SAP DBMS 계층과 동일한 Azure 지역에 배포해야 합니다. 이 규칙은 Azure에서 SAP 워크로드에 대해 게시된 정보에 잘 문서화되어 있습니다. 

Azure(큰 인스턴스)에서 SAP HANA의 전체적인 아키텍처는 SAP TDI 인증 하드웨어 구성(SAP HANA 데이터베이스용으로 가상화되지 않은, 완전 복구된 고성능 서버)을 제공하며 사용자 요구에 따라 SAP 응용 프로그램 계층의 리소스를 조정하도록 하는 Azure 기능 및 유연성을 제공합니다.

![Azure(큰 인스턴스)에서 SAP HANA의 아키텍처 개요](./media/hana-overview-architecture/image1-architecture.png)

표시된 아키텍처는 다음과 같은 세 가지 섹션으로 구분됩니다.

- **오른쪽:** 최종 사용자가 LOB 응용 프로그램(예: SAP)에 액세스하는 데이터 센터에서 서로 다른 응용 프로그램을 실행하는 온-프레미스 인프라. 이상적으로는 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 통해 이 온-프레미스 인프라가 Azure에 연결됩니다.

- **가운데:** Azure IaaS를 보여 주고 이 경우, Azure VM을 사용하여 DBMS 시스템으로 SAP HANA를 사용하는 SAP 또는 기타 응용 프로그램을 호스트합니다. Azure VM에서 제공하는 메모리에서 작동하는 소규모 HANA 인스턴스는 응용 프로그램 계층과 함께 Azure VM에 배포됩니다. [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)에 대해 자세히 알아보세요.
<br />SAP 시스템을 다른 응용 프로그램과 함께 Azure Virtual Networks(VNet)로 그룹화하는 데 Azure 네트워킹이 사용됩니다. 이러한 VNet은 온-프레미스 시스템 및 Azure(큰 인스턴스)에서 SAP HANA에 연결됩니다.
<br />Microsoft Azure에서 실행이 지원되는 SAP NetWeaver 응용 프로그램 및 데이터베이스의 경우 [SAP Support Note #1928533 – Azure의 SAP 응용 프로그램: 지원 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533)을 참조하세요. Azure에서 SAP 솔루션 배포에 대한 설명서는 다음을 검토합니다.

  -  [Windows VM(가상 컴퓨터)에서 SAP 사용](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Microsoft Azure Virtual Machines에서 SAP 솔루션 사용](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **왼쪽:** Azure 큰 인스턴스 스탬프에서 SAP HANA TDI 인증 하드웨어를 표시합니다. HANA 큰 인스턴스 단위는 온-프레미스에서 Azure로 연결 시와 동일한 기술을 사용하여 사용자 구독의 Azure VNet에 연결됩니다.

Azure 큰 인스턴스 스탬프 자체는 다음 구성 요소를 결합합니다.

- **컴퓨팅:** 필요한 컴퓨팅 기능을 제공하고 SAP HANA 인증된 Intel Xeon E7-8890v3 또는 Intel Xeon E7-8890v4 프로세서 기반의 서버입니다.
- **네트워크:** 컴퓨팅, 저장소 및 LAN 구성 요소를 상호 연결하는 통합된 고속 네트워크 패브릭입니다.
- **저장소:** 통합된 네트워크 패브릭을 통해 액세스되는 저장소 인프라입니다. 배포 중인 Azure(큰 인스턴스)에서 특정 SAP HANA 구성에 따라 특정 저장소 용량이 제공됩니다(매달 부가되는 요금으로 더 많은 저장소 용량 사용 가능).

큰 인스턴스 스탬프의 다중 테넌트 인프라 내에서 고객이 격리된 테넌트로 배포됩니다. 테넌트 배포 시 Azure 등록 내의 Azure 구독에 이름을 붙여야 합니다. 이것이 HANA 큰 인스턴스가 청구될 Azure 구독이 될 것입니다. 이러한 테넌트는 Azure 구독과 1:1 관계를 포함합니다. 네트워크 측면에서 각기 다른 Azure 구독에 속하는 다른 Azure VNet으로부터 한 Azure 지역에 있는 한 테넌트에 배포된 HANA 대규모 인스턴스 단위에 액세스하는 것은 가능합니다. 하지만 해당 Azure 구독이 동일한 Azure 등록에 포함되어야만 합니다. 

Azure VM과 마찬가지로, Azure(큰 인스턴스)에서 SAP HANA이 여러 Azure 지역에 제공됩니다. 재해 복구 기능을 제공하려면 옵트인하도록 선택할 수 있습니다. 하나의 지정학적 지역의 서로 다른 큰 인스턴스 스탬프가 서로 연결됩니다. 예를 들어 미국 서부 및 미국 동부에 있는 HANA 대형 인스턴스 스탬프가 DR 복제 목적을 위해 전용 네트워크 링크를 통해 연결됩니다. 

Azure Virtual Machines로 서로 다른 VM 유형 간에 선택할 수 있는 것처럼 SAP HANA의 다양한 워크로드 유형에 맞게 조정된 HANA 큰 인스턴스의 다양한 SKU 중에서 선택할 수 있습니다. SAP는 Intel 프로세서 세대(네 가지 SKU 유형 제공됨)에 따라 메모리를 다양한 워크로드에 맞게 프로세서 소켓 비율에 적용합니다.

2017년 7월 현재 Azure(대형 인스턴스)의 SAP HANA는 미국 서부, 미국 동부, 오스트레일리아 동부, 오스트레일리아 남동부, 유럽 서부 및 북유럽의 Azure 지역에서 여러 구성으로 사용하도록 제공됩니다.

| SAP 솔루션 | CPU | 메모리 | 저장소 | Availability |
| --- | --- | --- | --- | --- |
| OLAP에 대해 최적화됨: SAP BW, BW/4HANA<br /> 또는 SAP HANA(일반 OLAP 워크로드용) | Azure S72에서 SAP HANA<br /> – 2 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 36 CPU 코어 및 72 CPU 스레드 |  768 GB |  3 TB | 사용 가능 |
| --- | Azure S144에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 72 CPU 코어 및 144 CPU 스레드 |  1.5 TB |  6 TB | 더 이상 제공되지 않음 |
| --- | Azure S192에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 96 CPU 코어 및 192 CPU 스레드 |  2.0 TB |  8 TB | 사용 가능 |
| --- | Azure S384에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  4.0 TB |  16 TB | 주문 준비됨 |
| OLTP에 대해 최적화됨: SAP Business Suite<br /> SAP HANA 또는 S/4HANA(OLTP)에서,<br /> 일반 OLTP | Azure S72m에서 SAP HANA<br /> – 2 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 36 CPU 코어 및 72 CPU 스레드 |  1.5 TB |  6 TB | 사용 가능 |
|---| Azure S144m에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 72 CPU 코어 및 144 CPU 스레드 |  3.0 TB |  12 TB | 더 이상 제공되지 않음 |
|---| Azure S192m에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 96 CPU 코어 및 192 CPU 스레드  |  4.0 TB |  16 TB | 사용 가능 |
|---| Azure S384m에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  6.0 TB |  18 TB | 주문 준비됨 |
|---| Azure S384xm에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  8.0 TB |  22 TB |  주문 준비됨 |
|---| Azure S576에서 SAP HANA<br /> – 12 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 288 CPU 코어 및 576 CPU 스레드 |  12.0 TB |  28 TB | 주문 준비됨 |
|---| Azure S768에서 SAP HANA<br /> – 16 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 384 CPU 코어 및 768 CPU 스레드 |  16.0 TB |  36 TB | 주문 준비됨 |
|---| Azure S960에서 SAP HANA<br /> – 20 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 480 CPU 코어 및 960 CPU 스레드 |  20.0 TB |  46 TB | 주문 준비됨 |

- CPU 코어 = 서버 단위 프로세서 합계의 비하이퍼 스레드 CPU 코어 합계
- CPU 스레드 = 서버 단위 프로세서 합계의 하이퍼 스레드된 CPU 코어에서 제공하는 계산 스레드 합계 모든 단위는 기본적으로 하이퍼 스레드를 사용하도록 구성됩니다.


사용할 수 있거나 '더 이상 제공되지 않는’ 위의 다양한 구성은 [SAP Support Note #2316233 – Microsoft Azure(큰 인스턴스)에서 SAP HANA](https://launchpad.support.sap.com/#/notes/2316233/E)에 나와 있습니다. '주문 준비됨'으로 표시된 구성은 항목이 곧바로 SAP Note에 나타납니다. 하지만 이러한 인스턴스 SKU는 HANA 큰 인스턴스 서비스를 사용할 수 있는 Azure 지역 여섯 곳에서 이미 주문할 수 있습니다.

선택한 특정 구성은 워크로드, CPU 리소스 및 원하는 메모리에 따라 달라집니다. OLTP 워크로드에 대해 OLAP 워크로드용으로 최적화된 SKU를 활용할 수 있습니다. 

모든 제안의 하드웨어 기반은 SAP HANA TDI 인증입니다. 그러나, 하드웨어는 두 가지 클래스로 구별하며, 그에 따라 SKU는 다음과 같이 구분됩니다.

- SKU의 'Type I 클래스'인 S72, S72m, S144, S144m, S192 및 S192m.
- SKU의 'Type II 클래스'인 S384, S384m, S384xm, S576, S768, 및 S960.

중요한 점은 전체 HANA 대규모 인스턴스 스탬프가 단일 고객 용도로 단독으로 할당되지 않는다는 것입니다. 이 사실은 Azure에 배포된 네트워크 패브릭을 통해 연결된 계산 및 저장소 리소스 랙에도 적용됩니다. HANA 대규모 인스턴스 인프라는 Azure와 마찬가지로 다음과 같은 세 가지 수준에서 서로 격리된 서로 다른 고객 &quot;테넌트&quot;를 배포합니다.

- 네트워크: HANA 대규모 인스턴스 스탬프 내의 가상 네트워크를 통해 격리.
- 저장소: 저장소 볼륨이 할당되고 테넌트 간 저장소 볼륨을 격리하는 저장소 가상 컴퓨터를 통해 격리.
- 계산: 서버 단위를 단일 테넌트에 전용으로 할당 서버 단위의 하드 또는 소프트 파티션 없음. 테넌트 간 단일 서버 또는 호스트 단위의 공유 없음. 

따라서 서로 다른 테넌트 간 HANA 대규모 인스턴스 단위 배포는 상호 보이지 않습니다. 또한 서로 다른 테넌트에 배포된 HANA 대규모 인스턴스 단위가 HANA 대규모 인스턴스 스탬프 수준에서 상호 직접 통신하지도 않습니다. 한 테넌트 내의 HANA 대규모 인스턴스 단위만 HANA 대규모 인스턴스 스탬프 수준에서 상호 통신할 수 있습니다.
청구 측면에서 큰 인스턴스 스탬프에 배포된 테넌트가 하나의 Azure 구독에 할당됩니다. 그러나 네트워크 측면에서 그것은 동일한 Azure 등록 내에서 다른 Azure 구독의 Azure Vnet에서 액세스할 수 있습니다. 또한 동일한 Azure 지역의 또 다른 Azure 구독을 사용하여 배포할 경우 별도의 HANA 대규모 인스턴스 테넌트를 요청하기로 선택할 수 있습니다.

HANA 대규모 인스턴스에서 SAP HANA를 실행할 때와 Azure에 배포된 Azure VM에서 SAP HANA를 실행할 때는 상당한 차이가 있습니다.

- Azure(큰 인스턴스)의 SAP HANA의 경우 가상화 계층이 없습니다. 기본적인 완전 복구 하드웨어의 성능을 얻을 수 있습니다.
- Azure와 달리 Azure(큰 인스턴스) 서버에서 SAP HANA는 특정 고객이 전용으로 사용할 수 있습니다. 서버 단위 또는 호스트가 하드 또는 소프트 파티션될 가능성은 없습니다. 따라서 HANA 대규모 인스턴스 단위는 테넌트에, 즉 고객에게 전체가 할당되는 것으로 사용됩니다. 서버를 다시 부팅하거나 종료해도 운영 체제 및 SAP HANA가 다른 서버에 자동으로 배포되지 않습니다. (Type I 클래스 SKU의 경우 유일한 예외 사항은 서버에서 문제가 발생할 경우 다른 서버에서 재배포를 수행해야 한다는 점입니다.)
- Azure와 달리 최고의 가성비를 위해 호스트 프로세서 유형을 선택한 경우 Azure(큰 인스턴스)에서 SAP HANA에 대해 선택한 프로세서 유형은 Intel E7v3 및 E7v4 프로세서 제품군에서 최고의 성능을 보입니다.


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>여러 SAP HANA 인스턴스를 하나의 HANA 큰 인스턴스 단위에서 실행함
둘 이상의 활성 SAP HANA 인스턴스를 HANA 큰 인스턴스 단위에 호스팅하는 것이 가능합니다. 저장소 스냅숏 및 재해 복구 기능을 계속 제공하기 위해서는 그런 구성을 위해 인스턴스당 볼륨 집합이 필요합니다. 현재로서는 HANA 큰 인스턴스 단위를 다음과 같이 세분화할 수 있습니다.

- S72, S72m, S144, S192: 256GB를 가장 작은 시작 단위로 하여 256GB씩 증분. 256GB, 512 GB 등과 같은 다른 증분을 단위의 최대 메모리값과 결합할 수 있습니다.
- S144m and S192m: 512GB를 가장 작은 단위로 하여 256GB씩 증분. 512 GB, 768 GB 등과 같은 다른 증분을 단위의 메모리 최대값과 결합할 수 있습니다.
- Type II 클래스: 2TB를 가장 작은 시작 단위로 하여 512GB씩 증분. 512 GB, 1 TB, 1.5 TB 등과 같은 다른 증분을 단위의 최대 메모리값과 결합할 수 있습니다.

다음은 여러 SAP HANA 인스턴스 실행의 몇 가지 예입니다.

| SKU | 메모리 크기 | 저장소 크기 | 여러 데이터베이스가 장착된 크기 |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768 GB HANA 인스턴스<br /> 또는 1x512 GB 인스턴스 + 1x256 GB 인스턴스<br /> 또는 3x256 GB 인스턴스 | 
| S72m | 768 GB | 3 TB | 3x512GB HANA 인스턴스<br />또는 1x512 GB 인스턴스 + 1x1 TB 인스턴스<br />또는 6x256 GB 인스턴스<br />또는 1x1.5 TB 인스턴스 | 
| S192m | 4 TB | 16 TB | 8x512 GB 인스턴스<br />또는 4x1 TB 인스턴스<br />또는 4x512 GB 인스턴스 + 2x1 TB 인스턴스<br />또는 4x768 GB 인스턴스 + 2x512 GB 인스턴스<br />또는 1x4 TB 인스턴스 |
| S384xm | 8 TB | 22 TB | 4x2 TB 인스턴스<br />또는 2x4 TB 인스턴스<br />또는 2x3 TB 인스턴스 + 1x2 TB 인스턴스<br />또는 2x2.5 TB 인스턴스 + 1x3 TB 인스턴스<br />또는 1x8 TB 인스턴스 |


이해했으리라고 생각합니다. 물론 다른 변형도 있습니다. 


## <a name="operations-model-and-responsibilities"></a>작업 모델 및 책임

Azure(큰 인스턴스)에서 SAP HANA가 제공하는 서비스는 Azure IaaS 서비스에 맞춰 조정됩니다. SAP HANA용으로 최적화된 운영 체제가 설치된 HANA 큰 인스턴스가 생성됩니다. Azure IaaS VM과 마찬가지로 OS를 강화하고 필요한 추가 소프트웨어와 HANA를 설치하며, OS 및 HANA를 작동하고 OS 및 HANA를 업데이트하는 대부분의 작업은 사용자 책임입니다. Microsoft는 사용자에게 OS 업데이트 또는 HANA 업데이트를 강요하지 않습니다.

![Azure(큰 인스턴스)에서 SAP HANA의 책임](./media/hana-overview-architecture/image2-responsibilities.png)

위의 다이어그램에서 볼 수 있듯이 Azure(큰 인스턴스)에서 SAP HANA는 다중 테넌트 Infrastructure as a Service 제품입니다. 결과적으로 대부분의 부분에 대해 책임의 분담은 OS 인프라 경계에서 이루어집니다. Microsoft는 운영 체제 줄 아래 서비스의 모든 측면을 담당하며 사용자는 운영 체제를 포함한 줄 위를 담당합니다. 따라서 규정 준수, 보안, 응용 프로그램 관리를 위해 최신 온-프레미스 메서드를 도입하고 기본 및 OS 관리를 계속 사용할 수 있습니다. 시스템은 모든 측면의 네트워크에 있는 것처럼 표시됩니다.

그러나 이 서비스는 SAP HANA용으로 최적화되므로 기본 인프라 기능을 사용하여 최상의 결과를 얻기 위해 사용자가 Microsoft가 협력해야 하는 부분이 있습니다.

다음 목록에는 각 레이어와 책임에 대한 자세한 정보를 제공합니다.

**네트워킹:** SAP HANA를 실행하는 큰 인스턴스 스탬프에서 모든 내부 네트워크, 저장소에 대한 액세스, 인스턴스 간 연결(규모 확장 및 기타 기능), 환경에 대한 연결, Azure Virtual Machines에서 SAP 응용 프로그램 계층이 호스트되는 Azure로의 연결. 재해 복구 용도 복제를 위해 Azure 데이터 센서 간 WAN 연결도 포함합니다. 모든 네트워크는 테넌트에 의해 분할되며 QOS가 적용되어 있습니다.

**저장소:** SAP HANA 서버에 의해 필요한 모든 볼륨 및 스냅숏에 대해 가상화되고 분할된 저장소. 

**서버:** 테넌트에 할당된 SAP HANA DB를 실행할 전용 물리적 서버. SKU의 Type I 클래스 서버는 추상화된 하드웨어입니다. 이런 유형의 서버에서는 서버 구성이 프로필에 수집되고 유지 관리되며, 이런 프로필은 하나의 실제 하드웨어에서 다른 실제 하드웨어에 이동할 수 있습니다. 작업에 의한 그런 프로필 (수동) 이동은 조금은 Azure 서비스 복구와 비교될 수 있습니다. Type II 클래스 SKU 서버는 그런 기능을 제공하지 않습니다.

**SDDC:** 소프트웨어 정의 엔터티로 데이터 센터를 관리하는 데 사용되는 관리 소프트웨어. Microsoft를 확장성, 가용성 및 성능상의 이유로 리소스를 풀(pool)할 수 있습니다.

**O/S:** 서버에서 실행 중이고 사용자가 선택한 OS(SUSE Linux 또는 Red Hat Linux). 제공되는 OS 이미지는 SAP HANA 실행을 위해 개별 Linux 공급 업체에서 Microsoft에 제공하는 이미지입니다. 특정 SAP HANA에 최적화된 이미지에 대해 Linux 공급 업체에서 제공한 구독을 보유해야 합니다. 사용자는 OS 공급 업체에서 제공한 이미지를 등록할 책임이 있습니다. 또한 Microsoft가 인계하는 지점에서 Linux 운영 체제의 추가 패치를 수행할 책임도 있습니다. 이 패치는 또한 SAP HANA 설치(SAP의 HANA 설치 설명서 및 SAP 참고 사항 참조)에 성공하기 위해 필요하고 특정 Linux 공급 업체가 해당 SAP HANA에 최적화된 OS 이미지에 포함시키지 않은 추가 패키지를 포함합니다. 고객은 OS의 고장/최적화와 관련된 OS 패치 및 특정 서버 드라이버와 관련된 해당 하드웨어 패치를 수행할 책임도 있습니다. 또는 OS의 보안이나 기능 패치도 포함됩니다. 고객은 다음 항목의 모니터링 및 용량 계획도 책임집니다.

- CPU 리소스 사용량
- 메모리 사용량
- 여유 공간, IOPS 및 대기 시간과 관련된 디스크 볼륨
- HANA 큰 인스턴스 및 SAP 응용 프로그램 계층 간의 네트워크 볼륨 트래픽

HANA 큰 인스턴스의 기본 인프라는 OS 볼륨의 백업 및 복원에 대한 기능을 제공합니다. 이 기능을 사용하는 것도 사용자의 책임입니다.

**미들웨어:** 주로 SAP HANA 인스턴스. 관리, 작업 및 모니터링은 사용자의 책임입니다. 백업/복원 및 재해 복구 용도로 저장소 스냅숏을 사용할 수 있도록 제공된 기능이 있습니다. 이러한 기능은 인프라에 의해 제공됩니다. 그러나 이러한 기능으로 고가용성 또는 재해 복구를 설계하고 활용하며 저장소 스냅숏이 성공적으로 실행되었는지 모니터링하는 것도 사용자의 책임에 포함됩니다.

**데이터:** SAP HANA에서 관리하는 데이터 및 볼륨 또는 파일 공유에 있는 백업 파일과 같은 기타 데이터. 사용자 책임에는 사용 가능한 디스크 공간 모니터링 및 볼륨에서 콘텐츠 관리, 디스크 볼륨 및 저장소 스냅숏의 백업이 성공적으로 실행되었는지 모니터링이 포함됩니다. 그러나 DR 사이트로의 데이터 복제를 성공적으로 실행하는 것은 Microsoft의 책임입니다.

**응용 프로그램:** SAP 응용 프로그램 인스턴스 또는 SAP 이외의 응용 프로그램의 경우 해당 응용 프로그램의 응용 프로그램 계층. 사용자 책임에는 CPU 리소스 사용량, 메모리 사용량, Azure Storage 사용량 및 Azure VNet 내에서와 Azure VNet에서 Azure(큰 인스턴스)의 SAP HANA로의 네트워크 대역폭 사용량의 용량 계획과 관련된 응용 프로그램의 배포, 관리, 작업 및 모니터링이 포함됩니다.

**WAN:** 워크로드에 대해 온-프레미스에서 Azure 배포로 설정되는 연결. HANA 큰 인스턴스를 장착한 모든 고객은 연결을 위해 Azure ExpressRoute를 사용합니다. 이 연결은 Azure(큰 인스턴스)의 SAP HANA 솔루션에 포함되지 않으므로 이 연결을 설정하는 것은 사용자 책임입니다.

**보관:** 저장소 계정에서 사용자 고유의 메서드를 사용하여 데이터 복사본을 보관하는 것을 선호할 수 있습니다. 보관하려면 관리, 규정 준수, 비용 및 작업이 필요합니다. 사용자는 Azure에 보관 복사본과 백업을 생성하고 호환되는 방식으로 이를 저장합니다.

[Azure(큰 인스턴스)의 SAP HANA에 SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)를 참조하세요.

## <a name="sizing"></a>크기 조정

HANA 큰 인스턴스에 대한 크기 조정은 일반적인 HANA에 대한 크기 조정과 다르지 않습니다. 기존 및 배포된 시스템의 경우 사용자가 다른 RDBMS에서 HANA로 이동을 원하고 SAP가 기존 SAP 시스템에서 실행되는 다양한 보고서를 제공합니다. 데이터베이스가 HANA로 이동된 경우 이러한 보고서는 데이터를 확인하고 HANA 인스턴스에 대한 메모리 요구 사항을 계산합니다. 다음 SAP Note에서 이러한 보고서를 실행하는 방법 및 가장 최근의 패치/버전을 구하는 방법에 대해 자세히 알아보세요.

- [SAP Note #1793345 - HANA에서 SAP Suite에 대한 크기 조정](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA 및 S/4 HANA 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ: SAP BW on HANA 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - BW on HANA에 대한 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - BW on HANA에 대한 새 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/2296290)

녹색 필드 구현을 위해 SAP Quick Sizer를 사용하여 HANA 상단에서 SAP 소프트웨어 구현에 대한 메모리 요구 사항을 계산할 수 있습니다.

데이터 볼륨이 증가함에 따라 HANA에 대한 메모리 요구 사항이 늘어나므로 현재 메모리 사용량을 알고 향후 사용량을 예측할 수 있습니다. 그런 다음 메모리 요구 사항에 따라 요구량을 HANA 큰 인스턴스 SKU 중 하나로 매핑할 수 있습니다.

## <a name="requirements"></a>요구 사항

이 목록은 Azure(더 큰 인스턴스)에서 SAP HANA를 실행하기 위한 요구 사항을 모읍니다.

**Microsoft Azure:**

- Azure(큰 인스턴스)에서 SAP HANA로 연결할 수 있는 Azure 구독입니다.
- Microsoft 프리미어 지원 계약입니다. Azure에서 SAP 실행에 대한 자세한 정보는 [SAP Support Note #2015553 – Microsoft Azure의 SAP: 지원 필수 조건](https://launchpad.support.sap.com/#/notes/2015553)을 참조하세요. 384 및 더 많은 CPU가 장착된 HANA 큰 인스턴스 단위를 사용할 경우 Azure 신속한 응답(ARR)을 포함하도록 프리미어 지원 계약을 확장할 필요가 있습니다.
- SAP로 크기 조정 연습을 수행한 후 필요한 HANA 큰 인스턴스 SKU 인식

**네트워크 연결:**

- 온-프레미스와 Azure 간의 Azure ExpressRoute: 온-프레미스 데이터 센터를 Azure에 연결하려면 ISP로부터 1GBPS 이상의 연결을 요청해야 합니다. 

**운영 체제:**

- SAP 응용 프로그램용 SUSE Linux Enterprise Server 12에 대한 라이선스

> [!NOTE] 
> Microsoft에서 제공하는 운영 체제는 SUSE로 등록되지 않고 SMT 인스턴스와 연결되지도 않습니다.

- Azure VM에서 Azure에 배포된 SUSE Linux Subscription Management Tool(SMT). Azure(큰 인스턴스)에서 SAP HANA가 SUSE에 의해 등록되고 각각 업데이트될 수 있는 기능을 제공합니다(HANA 큰 인스턴스 데이터 센터 내에서는 인터넷 액세스가 불가능하므로). 
- SAP HANA용 Red Hat Enterprise Linux 6.7 또는 7.2에 대한 라이선스.

> [!NOTE]
> Microsoft에서 제공하는 운영 체제는 Red Hat으로 등록되지 않고 Red Hat 구독 관리자 인스턴스에 연결되지도 않습니다.

- Azure VM에서 Azure에 배포된 Red Hat Subscription Manager. Red Hat Subscription Manager는.Azure(큰 인스턴스)에서 SAP HANA가 Red Hat에 의해 등록되고 각각 업데이트될 수 있는 기능을 제공합니다(Azure 큰 인스턴스 스탬프에 배포된 테넌트 내에서는 직접적인 인터넷 액세스가 불가능하므로).
- SAP를 사용하려면 Linux 공급자와도 지원 계약을 체결해야 합니다. 이 요구 사항은 HANA 큰 인스턴스 솔루션이나 Azure에서 Linux를 실행한다고 해도 적용됩니다. 일부 Linux Azure 갤러리 이미지와 달리 HANA 큰 인스턴스의 솔루션 제품에는 서비스 요금이 포함되지 않습니다. Linux 배포자와 지원 계약과 관련하여 SAP의 요구 사항을 충족시키는 것은 고객의 책임입니다.   
   - SUSE Linux의 경우 [SAP Note #1984787 - SUSE LINUX Enterprise Server 12: 설치 참고](https://launchpad.support.sap.com/#/notes/1984787) 및 [SAP Note #1056161 - SAP 응용 프로그램에 대한 SUSE 우선 지원](https://launchpad.support.sap.com/#/notes/1056161)에서 지원 계약의 요구 사항을 살펴봅니다.
   - Red Hat Linux의 경우 지원 및 서비스(HANA 큰 인스턴스의 운영 체제 업데이트)를 포함하는 올바른 구독 수준을 마련해야 합니다. Red Hat은 "RHEL for SAP Business Applications"를 구독할 것을 권장합니다. 지원 및 서비스와 관련하여 자세한 내용은 [SAP Note #2002167 - Red Hat Enterprise Linux 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/2002167) 및 [SAP Note #1496410 - Red Hat Enterprise Linux 6.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/1496410)를 확인하세요.

**데이터베이스:**

- SAP HANA에 대한 라이선스 및 소프트웨어 설치 구성 요소(플랫폼 및 Enterprise 버전).

**응용 프로그램:**

- SAP HANA에 연결되는 모든 SAP 응용 프로그램 및 관련 SAP 지원 계약에 대한 라이선스 및 소프트웨어 설치 구성 요소.
- Azure(큰 인스턴스)에서 SAP HANA 환경과 관련하여 사용되는 모든 SAP 이외의 응용 프로그램 및 관련 SAP 지원 계약에 대한 라이선스 및 소프트웨어 설치 구성 요소.

**기술:**

- Azure IaaS 및 해당 구성 요소에 대한 경험 및 지식
- Azure에서 SAP 워크로드 배포에 대한 경험 및 지식
- SAP HANA 설치 공인 전문가
- SAP HANA에 대한 고가용성 및 재해 복구 설계를 위한 SAP 설계 기술

**SAP:**

- SAP와의 지원 계약을 맺은 SAP 고객이기를 기대합니다.
- 특히 HANA 큰 인스턴스 SKU의 Type II 클래스에 구현을 위해, SAP와 SAP HANA 버전 및 큰 크기 정률 증가 하드웨어 상의 최종 구성에 대해 문의하는 것이 좋습니다.


## <a name="storage"></a>저장소

Azure(큰 인스턴스)에서 SAP HANA에 대한 저장소 레이아웃은 [SAP HANA 저장소 요구 사항](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 백서에 문서화된 SAP 권장 지침을 통해 Azure Service Management의 SAP HANA에 의해 구성됩니다.

Type I 클래스의 HANA 큰 인스턴스는 저장소 볼륨의 4배의 메모리 볼륨을 제공합니다. HANA 큰 인스턴스 단위의 Type II 클래스 형식인 경우, 저장소는 4배가 되지는 않을 것입니다. 단위에는 HANA 트랜잭션 로그 백업 저장을 위한 볼륨이 함께 제공됩니다. [Azure에서 SAP HANA(큰 인스턴스)를 설치하고 구성하는 방법](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 자세한 내용을 확인하세요.

저장소 할당이라는 면에서 다음 표를 참조하세요. 서로 다른 HANA 큰 인스턴스 단위와 함께 제공되는 서로 다른 볼륨에 대한 용량이 테이블에 대략적으로 나열됩니다.

| HANA 큰 인스턴스 SKU | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384 | 11,520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12,000 GB | 2050 GB | 2050 GB | 2040GB |
| S384xm | 16,000 GB | 2050 GB | 2050 GB | 2040GB |
| S576 | 20,000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768 | 28,000 GB | 3100 GB | 2050 GB | 3100 GB |
| S960 | 36,000 GB | 4100 GB | 2050 GB | 4100 GB |


실제 배포된 볼륨은 볼륨 크기를 표시하는 데 사용되는 배포 및 도구에 따라 약간 달라질 수도 있습니다.

HANA 큰 인스턴스 SKU를 세분화하면 가능한 분할 조각들의 몇 가지 예입니다.

| 메모리 파티션(GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


이러한 크기는 볼륨을 보는 데 사용된 배포 및 도구에 따라 약간 다를 수 있는 대략적인 볼륨 숫자입니다. 또한 2.5 TB와 같은 생각할 수 있는 다른 파티션 크기가 있습니다. 이러한 저장소 크기는 위의 파티션에 대해 사용 된 것과 비슷한 수식으로 계산 될 것입니다. 용어 '파티션'은 운영 체제, 메모리 또는 CPU 리소스가 어떤 방식으로 분할되었는지 나타내지 않습니다. 그것은 단지 하나의 단일 HANA 큰 인스턴스 단위로 배포하려는 다른 HANA 인스턴스에 대한 저장소 파티션을 나타냅니다. 

고객이 더 많은 저장소를 필요로 할 수도 있으므로 1TB 단위로 추가 저장소를 구매하여 저장소를 추가할 가능성이 있습니다. 이 추가 저장소는 추가 볼륨으로 추가되거나 하나 이상의 기존 볼륨을 확장하는 데 사용할 수 있습니다. 위의 테이블에 의해 원래 배포되고 대부분 문서화된 볼륨의 크기를 줄이는 것이 불가능합니다. 또한 볼륨의 이름을 변경하거나 이름을 탑재할 수 없습니다. 저장소 볼륨은 위에서 설명한 대로 NFS4 볼륨으로 HANA 큰 인스턴스 단위에 연결됩니다.

고객은 백업/복원 및 재해 복구 용도로 저장소 스냅숏을 사용하도록 선택할 수 있습니다. 이 항목에 대한 자세한 내용은 [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 설명되어 있습니다.

### <a name="encryption-of-data-at-rest"></a>미사용 데이터 암호화
HANA 큰 인스턴스에 사용된 저장소를 통해 디스크에 저장된 대로 데이터의 투명한 암호화가 가능합니다. HANA 큰 인스턴스 단위 배포 시 이러한 종류의 암호화를 설정하는 옵션이 제공됩니다. 또한 이미 배포 후에는 암호화된 볼륨으로 변경하도록 선택할 수 있습니다. 암호화되지 않은 볼륨에서 암호화된 볼륨으로의 이동은 투명하며 가동 중지 시간이 필요하지 않습니다. 

Type I 클래스 SKU와 함께 부팅 LUN이 저장된 볼륨은 암호화됩니다. HANA 큰 인스턴스의 Type II 클래스 SKU인 경우 OS 메서드로 부팅 LUN을 암호화해야 합니다. 


## <a name="networking"></a>네트워킹

Azure 네트워킹의 아키텍처는 SAP 응용 프로그램을 HANA 큰 인스턴스에 성공적으로 배포하기 위한 핵심 구성 요소입니다. 일반적으로 Azure(큰 인스턴스)에서 SAP HANA 배포에는 다양한 데이터베이스 크기, CPU 리소스 사용량 및 메모리 사용률이 있는 서로 다른 여러 SAP 솔루션을 갖춘 더 큰 SAP 환경이 포함됩니다. 이러한 SAP 시스템 전부가 SAP HANA를 기반으로 할 가능성은 없으므로 사용자의 SAP 환경은 다음을 사용하는 하이브리드 형태일 것입니다.

- 온-프레미스에 배포된 SAP 시스템 이들 시스템은 그 크기 때문에 현재 Azure에서 호스트할 수 없습니다. 전형적인 예로는 Azure VM 이 제공할 수 있는 더 많은 CPU 또는 메모리 리소스를 요구하는 Microsoft SQL Server (데이터베이스로)에서 실행 중인 프로덕션 SAP ERP 시스템을 들 수 있습니다.
- 온-프레미스에 배포된 SAP HANA 기반 SAP 시스템.
- Azure VM에 배포된 SAP 시스템. 이러한 시스템은 리소스 사용량 및 메모리 요구량에 따라 Azure(VM에서)에 성공적으로 배포할 수 있는 SAP NetWeaver 기반 응용 프로그램에 대한 배포, 테스팅, 샌드박스 또는 프로덕션 인스턴스일 수 있습니다. 이들 시스템은 또한 SQL Server([SAP Support Note #1928533 – Azure의 SAP 응용 프로그램: 지원 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533/E) 참조) 또는 SAP HANA([SAP HANA 인증 IaaS 플랫폼](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) 참조)와 같은 데이터베이스를 기반으로 할 수도 있습니다.
- Azure 큰 인스턴스 스탬프로 Azure(큰 인스턴스)에서 SAP HANA를 활용하는 Azure(VM에서)에 배포된 SAP 응용 프로그램 서버.

하이브리드 SAP 환경(4개 이상의 다양한 배포 시나리오 포함)은 일반적인 반면에 Azure에서 실행되는 전체 SAP 환경은 고객에 맞춰진 경우가 많습니다. Microsoft Azure VM이 더 강력해짐에 따라, 모든 SAP 솔루션을 Azure로 옮기는 고객의 수가 증가하고 있습니다.

Azure에 배포된 SAP 시스템 맥락에서 Azure 네트워킹은 복잡하지 않습니다. 다음을 기준합니다.

- 온-프레미스 네트워크에 연결되는 Azure ExpressRoute 회로에 연결해야 하는 Azure Virtual Network(VNet).
- 온-프레미스를 Azure에 연결하는 ExpressRoute 회로는 일반적으로 1GBPS 이상의 대역폭을 포함합니다. 이 최소 대역폭은 온-프레미스 시스템과 Azure VM에서 실행되는 시스템 간에 데이터를 전송하고 최종 사용자 온-프레미스에서 Azure 시스템으로 연결하기에 적합한 대역폭을 허용합니다.
- Azure에서 모든 SAP 시스템은 서로 통신하도록 Azure VNet에서 설정해야 합니다.
- 온-프레미스에서 호스트되는 Active Directory 및 DNS는 ExpressRoute를 통해 온-프레미스에서 Azure로 확장됩니다.


> [!NOTE] 
> 청구라는 관점에서 단일 Azure 구독 만이 특정 Azure 지역의 큰 인스턴스 스탬프에서 하나의 단일 테넌트에만 연결할 수 있으며 단일 큰 인스턴스 스탬프 테넌트는 Azure 구독 하나에만 연결할 수 있습니다. Azure의 청구 가능한 다른 개체에도 이 사실은 다르지 않습니다.

서로 다른 여러 Azure 지역에 Azure(큰 인스턴스)의 SAP HANA를 배포하면 큰 인스턴스 스탬프에 별도의 테넌트가 배포되는 결과를 초래합니다. 그러나 이러한 인스턴스가 동일한 SAP 환경에 포함된다면 둘 다 동일한 Azure 구독에서 실행할 수 있습니다. 

> [!IMPORTANT] 
> Azure(큰 인스턴스)의 SAP HANA에서는 Azure Resource Management 배포만 지원됩니다.

 

### <a name="additional-azure-vnet-information"></a>Azure VNet 추가 정보

Azure VNet을 ExpressRoute에 연결하려면 Azure 게이트웨이를 만들어야 합니다([ExpressRoute에 대한 가상 네트워크 게이트웨이 정보](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조). Azure 외부의 인프라(또는 Azure 큰 인스턴스 스탬프로)에 대한 ExpressRoute로 Azure 게이트웨이를 사용하거나 Azure VNet 간에 연결하는 데 Azure 게이트웨이를 사용할 수 있습니다([PowerShell을 사용하여 Resource Manager의 VNet 간 연결 구성](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조). 그러한 연결이 서로 다른 MS Enterprise Edges(MSEE) 라우터에서 오는 경우 Azure 게이트웨이를 최대 4개의 서로 다른 ExpressRoute 연결로 연결할 수 있습니다.  자세한 내용은 [Azure의 SAP HANA(큰 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 

> [!NOTE] 
> Azure 게이트웨이에서 제공하는 처리량은 두 사용 사례마다 다릅니다([VPN Gateway 정보](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조). VNet 게이트웨이로 달성할 수 있는 최대 처리량은 ExpressRoute 연결을 사용할 경우 10GBPS입니다. Azure VNet에 있는 Azure VM과 시스템 온-프레미스 간에 파일을 복사할 경우(단일 복사 스트림으로) 다양한 게이트웨이 SKU의 전체 처리량을 달성할 수 없습니다. VNet 게이트웨이의 전체 대역폭을 활용하려면 여러 스트림을 사용하거나 서로 다른 파일을 단일 파일의 병렬 스트림으로 복사해야 합니다.


### <a name="networking-architecture-for-hana-large-instances"></a>HANA 큰 인스턴스에 대한 네트워킹 아키텍처
HANA 큰 인스턴스에 대한 네트워킹 아키텍처는 아래와 같이 4개의 서로 다른 부분으로 분리될 수 있습니다.

- 온-프레미스 네트워킹 및 Azure로의 ExpressRoute 연결. 이 부분은 고객 도메인이며 ExpressRoute 통해 Azure에 연결됩니다. 아래 그래픽의 오른쪽 아래에 있는 부분입니다.
- Azure 네트워킹은 위에서 간단히 설명한 것처럼 Azure Vnet과 함께 다시 게이트웨이를 갖습니다. 이 영역에서 응용 프로그램 요구 사항, 보안 및 규정 준수 요구 사항에 대한 적절한 디자인을 찾아야 합니다. HANA 큰 인스턴스 사용은 선택할 VNet 및 Azure 게이트웨이 SKU 수라는 관점에서 고려해야 할 또 다른 점입니다. 그래픽의 오른쪽 위에 있는 부분입니다.
- HANA 큰 인스턴스를 ExpressRoute 기술을 통해 Azure에 연결. 이 부분은 Microsoft에서 배포하고 처리합니다. 고객이 해야 할 일이라고는 몇 가지 IP 주소 범위를 제공하고 그런 후 ExpressRoute 회로를 Azure Vnet(s)에 연결하는 HANA 큰 인스턴스에서 자산을 배포하는 것입니다([Azure의 SAP HANA(큰 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조). 
- 고객에게 대부분 투명한 HANA 큰 인스턴스의 네트워킹.

![Azure(큰 인스턴스)에서 SAP HANA 및 온-프레미스로 연결된 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

HANA 큰 인스턴스를 사용한다는 사실이 ExpressRoute를 통해 Azure에 연결된 온-프레미스 자산을 얻기 위한 요구 사항을 변경하지 않습니다. 또한 HANA 큰 인스턴스 단위에 호스트된 HANA 인스턴스에 연결하는 응용 프로그램 계층을 호스트하는 Azure VM을 실행하는 하나 이상의 VNet이 있어야 한다는 요구 사항을 변경하지 않습니다. 

순수 Azure 내에서 SAP 배포의 차이는 다음과 같은 사실에 기인합니다.

- 고객 테넌트의 HANA 큰 인스턴스 단위는 또 다른 ExpressRoute 회로 통해 Azure VNet(s)에 연결됩니다. 부하 조건을 분리하기 위해 온-프레미스와 Azure VNets ExpressRoute 간의 링크와 Azure VNet과 HANA 큰 인스턴스 간의 링크는 동일한 라우터를 공유하지 않습니다.
- SAP 응용 프로그램 계층 및 HANA 인스턴스 간의 워크로드 프로필은 많은 작은 요청의 다른 특성이며 SAP HANA에서 응용 프로그램 계층으로의 데이터 전송(결과 집합)과 같이 버스트합니다.
- SAP 응용 프로그램 아키텍처는 온-프레미스와 Azure 간에 데이터가 교환되는 일반적인 시나리오보다 네트워크 대기 시간에 더 민감합니다.
- VNet 게이트웨이에는 두 개 이상의 ExpressRoute 연결이 있으며 두 연결이 함께 VNet 게이트웨이의 들어오는 데이터에 대한 최대 대역폭을 공유합니다.

Azure VM과 HANA 큰 인스턴스 단위 간의 네트워크 대기 시간은 일반적인 VM간 네트워크 왕복 대기 시간보다 클 수 있습니다. Azure 지역에 따라, 측정된 값이 [SAP Note #1100926 - FAQ: 네트워크 성능을](https://launchpad.support.sap.com/#/notes/1100926/E)에서 평균 미달로 분류된 0.7ms 라운드 트립 대기 시간을 초과할 수 있습니다. 그럼에도 고객들은 SAP HANA 기반 프로덕션 SAP 응용 프로그램을 SAP HANA 큰 인스턴스에서 매우 성공적으로 배포했습니다. 배포한 고객들이 HANA 큰 인스턴스 단위를 사용하여 SAP 응용 프로그램을 SAP HANA에서 실행해 보고 크게 향상되었음을 보고했습니다. 그렇더라도 사용자는 자신의 비지니스 프로세스를 Azure HANA 큰 인스턴스에서 철저하게 테스트해야 합니다.
 
Azure VM 및 HANA 큰 인스턴스 간에 결정적인 네트워크 대기 시간을 제공하기 위해 Azure VNet 게이트웨이 SKU를 선택하는 것이 중요합니다. 온-프레미스와 Azure VM 간의 트래픽 패턴과는 달리, Azure VM과 HANA 큰 인스턴스 단위 간의 트래픽 패턴은 작지만 높은 요청 버스트 및 전송될 데이터 볼륨을 개발할 수 있습니다. 그러한 버스트가 잘 처리되도록 하기 위해 UltraPerformance 게이트웨이 SKU를 사용하는 것이 좋습니다. HANA 큰 인스턴스 단위의 Type II 클래스인 경우, Azure VNet 게이트웨이로 UltraPerformance 게이트웨이 SKU 사용은 필수적입니다.  

> [!IMPORTANT] 
> SAP 응용 프로그램과 데이터베이스 계층 간의 전체 네트워크 트래픽이 정해졌다면 Azure(큰 인스턴스)에서 SAP HANA 연결에는 VNet에 대한 HighPerformance 또는 UltraPerformance 게이트웨이 SKU만 지원됩니다. HANA 큰 인스턴스 Type II SKU인 경우, UltraPerformance 게이트웨이 SKU만이 Azure VNet 게이트웨이로 지원됩니다.



### <a name="single-sap-system"></a>단일 SAP 시스템

위에 표시된 온-프레미스 인프라는 ExpressRoute를 통해 Azure에 연결되며 ExpressRoute 회로는 Microsoft Enterprise Edge Router(MSEE)에 연결됩니다([ExpressRoute 기술적 개요](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조). 설정되면 해당 경로가 Microsoft Azure 백본에 연결되고 모든 Azure 지역에 액세스할 수 있습니다.

> [!NOTE] 
> Azure에서 SAP 환경을 실행하려면 SAP 환경에서 Azure 지역에 가장 가까운 MSEE에 연결합니다. Azure 큰 인스턴스 스탬프는 전용 MSEE 장치를 통해 연결되어 Azure IaaS 및 큰 인스턴스 스탬프에서 Azure VM 간에 네트워크 대기 시간을 최소화합니다.

SAP 응용 프로그램 인스턴스를 호스팅하는 Azure VM용 VNet 게이트웨이는 ExpressRoute 회로에 연결되며 동일한 VNet이 큰 인스턴스 스탬프에 전용으로 연결되는 별도의 MSEE 라우터에 연결됩니다

단일 SAP 시스템에 대한 간단한 예입니다. 여기서는 SAP 응용 프로그램 계층이 Azure에 호스트되고 SAP HANA 데이터베이스가 Azure(큰 인스턴스)의 SAP HANA에서 실행됩니다. 2GBPS 또는 10GBPS 처리량의 VNet 게이트웨이 대역폭에서 병목 현상이 나타나지 않는다고 가정합니다.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>여러 SAP 시스템 또는 큰 SAP 시스템

Azure(큰 인스턴스)에서 SAP HANA에 연결되는 여러 SAP 시스템 또는 큰 SAP 시스템이 배포된 경우 VNet 게이트웨이의 처리량으로 병목 현상이 발생할 수 있다고 가정하는 것이 합리적입니다. 이 경우 응용 프로그램 계층을 여러 Azure VNet으로 분할해야 합니다. 또한 다음과 같은 경우 HANA 큰 인스턴스에 연결하는 특수 VNet을 만드는 것이 좋습니다.

- HANA 큰 인스턴스의 HANA 인스턴스에서 NFS 공유를 호스트하는 Azure의 VM으로 직접 백업을 수행합니다.
- 큰 백업 또는 HANA 큰 인스턴스 단위의 기타 파일을 Azure에서 관리하는 디스크 공간으로 복사합니다.

저장소를 관리하는 VM을 호스트하는 별도의 VNet를 사용하면 HANA 큰 인스턴스에서, SAP 응용 프로그램 계층을 실행하는 VM을 제공하는 VNet 게이트웨이의 Azure로, 대규모 파일 또는 데이터 전송으로 인한 영향을 피할 수 있습니다. 

확장성 있는 네트워크 아키텍처를 위해서는 다음과 같이 합니다.

- 단일의 더 큰 SAP 응용 프로그램 계층에 여러 Azure VNet을 활용합니다.
- 동일한 VNet 하에서 별도의 서브넷에 있는 SAP 시스템을 결합하는 것과 달리, 배포된 각 SAP 시스템에 대해 별도의 Azure VNet을 하나 배포합니다.

 Azure(큰 인스턴스)에서 SAP HANA에 대한 보다 확장성 있는 네트워킹 아키텍처

![여러 Azure VNet에 걸쳐 SAP 응용 프로그램 계층 배포](./media/hana-overview-architecture/image4-networking-architecture.png)

위에 표시된 대로 여러 Azure VNet에 걸쳐 SAP 응용 프로그램 계층 또는 구성 요소를 배포하면 해당 Azure VNet에서 호스트되는 응용 프로그램 간 통신 중에 불가피한 대기 시간 오버헤드가 발생했습니다. 기본적으로 서로 다른 VNet에 있는 Azure VM 간에 네트워크 트래픽은 이 구성에서 MSEE 라우터를 통해 라우팅합니다. 그러나 2016년 9월부터 이 라우팅은 최적화될 수 있습니다. 동일한 지역 내에서 Azure VNet을 피어링하여 두 VNet 간의 통신을 최적화하고 대기 시간을 줄일 수 있습니다. 해당 VNet이 다른 구독에 있더라도 가능합니다. Azure VNet 피어링을 사용하면 서로 다른 두 개의 Azure VNet에서 VM 간의 통신은 Azure 네트워크 백본을 사용하여 서로 직접 통신할 수 있습니다. 따라서 VM이 동일한 VNet에 있는 경우 대기 시간이 유사하게 표시됩니다. 반면 Azure VNet 게이트웨이를 통해 연결된 IP 주소 범위를 지정하는 트래픽은 VNet의 개별 VNet 게이트웨이를 통해 라우팅됩니다. [VNet 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 문서에서 Azure VNet 피어링에 대한 자세한 내용을 확인할 수 있습니다.


### <a name="routing-in-azure"></a>Azure에서 라우팅

Azure(큰 인스턴스)의 SAP HANA에 대한 두 가지 중요한 네트워크 라우팅 고려 사항이 있습니다.

1. Azure(큰 인스턴스)에서 SAP HANA는 전용 ExpressRoute 연결의 Azure VM에서만 액세스할 수 있으며 온-프레미스에서 직접 연결은 불가능합니다. 따라서 온-프레미스에서 실행되는 SAP Solution Manager처럼 직접 액세스가 필요한 관리 클라이언트 및 응용 프로그램은 SAP HANA 데이터베이스에 액세스할 수 없습니다.

2. Azure(큰 인스턴스) 단위의 SAP HANA에는 고객이 제출한 서버 IP 풀 주소 범위의 IP 주소가 할당되어 있습니다(자세한 내용은 [Azure의 SAP HANA(큰 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조).  이 IP 주소는 Azure(큰 인스턴스)에서 Azure VNet을 HANA에 연결하는 Azure 구독 및 ExpressRoute를 통해 액세스할 수 있습니다. 해당 서버 IP 풀 주소 범위를 벗어나 할당된 IP 주소는 하드웨어 장치에 직접 할당되며 이 솔루션의 첫 번째 배포에서처럼 더 이상 NAT되지 않습니다. 

> [!NOTE] 
> _데이터 웨어하우스_ 시나리오에서 Azure(큰 인스턴스)의 SAP HANA에 연결해야 하는 경우 응용 프로그램 및/또는 최종 사용자는 SAP HANA 데이터베이스(직접 실행)에 연결해야 하며 다른 네트워킹 구성 요소(데이터를 앞뒤로 라우팅하는 역방향 프록시)를 사용해야 합니다. 예를 들어 F5 BIG-IP, NGINX(Traffic Manager 포함)가 가상 방화벽/트래픽 라우팅 솔루션으로 Azure에 배포되어 있습니다.

### <a name="internet-connectivity-of-hana-large-instances"></a>HANA 큰 인스턴스의 인터넷 연결
HANA 큰 인스턴스에는 직접 인터넷 연결이 없습니다. 예를 들어 OS 공급업체에 OS 이미지 직접 등록처럼 이로 인해 사용자의 기능이 제한됩니다. 따라서 로컬 SLES SMT 서버 또는 RHEL Subscription Manager로 작업해야 할 수 있습니다

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Azure VM 및 HANA 큰 인스턴스 간의 데이터 암호화
HANA 큰 인스턴스 및 Azure VM 간에 전송된 데이터는 암호화되지 않습니다. 그러나 HANA DBMS 쪽과 JDBC/ODBC 기반 응용 프로그램 간의 교환을 위해서만 트래픽 암호화를 사용할 수 있습니다. [SAP에서 제공하는 이 설명서](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)를 참조하세요.

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>여러 지역에서 HANA 큰 인스턴스 단위 사용하기

재해 복구 외에도 여러 Azure 지역에 Azure(큰 인스턴스)의 SAP HANA를 배포하는 다른 이유가 있을 수 있습니다. 해당 지역의 다른 VNet에 배포된 각 VM에서 HANA 큰 인스턴스에 액세스하려고 할 수 있습니다. 서로 다른 HANA 큰 인스턴스 단위에 할당된 IP 주소는 Azure VNet을 벗어나 전파되지 않으므로(게이트웨이를 통해 인스턴스로 직접 연결됨) 위에 소개된 VNet 설계에 약간의 변경 사항이 있습니다. Azure VNet 게이트웨이는 서로 다른 MSEE에서 나오는 4가지 서로 다른 ExpressRoute 회로를 처리할 수 있으며 큰 인스턴스 스탬프 중 하나에 연결된 각 VNet은 다른 Azure 지역의 큰 인스턴스 스탬프에 연결될 수 있습니다.

![다양한 Azure 지역에서 Azure 큰 인스턴스 스탬프에 연결된 Azure VNet](./media/hana-overview-architecture/image8-multiple-regions.png)

위의 그림에서는 두 지역에 있는 서로 다른 Azure VNet이 두 Azure 지역에 있는 Azure(큰 인스턴스)의 SAP HANA에 연결하는 데 사용되는 서로 다른 두 ExpressRoute 회로에 연결되는 모습을 보여 줍니다. 새로 소개된 연결은 빨간색 사각형 선입니다. Azure VNet에서 나오는 이러한 연결을 통해 VNet 중 하나에서 실행되는 VM은 두 지역에 배포된 서로 다른 HANA 큰 인스턴스 단위 각각에 액세스할 수 있습니다. 위의 그림에서 볼 수 있듯이 온-프레미스에서 두 Azure 지역으로 두 개의 ExpressRoute 연결이 있다고 가정하는데, 이는 재해 복구 이유로 권장됩니다.

> [!IMPORTANT] 
> 여러 ExpressRoute 회로가 사용되는 경우 트래픽을 제대로 라우팅하기 위해서는 AS Path 앞에 추가 및 로컬 기본 BGP 설정을 사용해야 합니다.



