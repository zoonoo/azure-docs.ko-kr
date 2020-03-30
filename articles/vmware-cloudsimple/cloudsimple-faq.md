---
title: 자주 묻는 질문 - 클라우드에 의한 Azure VMware 솔루션 간단
description: 클라우드간이별 Azure VMware 솔루션에 대한 자주 묻는 질문
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025064"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>클라우드심플의 VMware 솔루션에 대한 자주 묻는 질문

## <a name="cloudsimple-service"></a>클라우드 심플 서비스

**클라우드에 의한 Azure VMware 솔루션이란 간단합니까?**

CloudSimple의 Azure VMware 솔루션은 몇 분 만에 VMware 워크로드를 Azure의 전용 전용 클라우드로 변환하고 확장합니다. CloudSimple은 온-프레미스와 Azure 간에 프로비저닝, 인프라 관리 및 워크로드를 오케스트레이션합니다. 앱이 온-프레미스와 Azure에서 정확히 동일하게 실행되므로 앱을 다시 설계하는 복잡성 없이 클라우드의 탄력성과 서비스를 활용할 수 있습니다. CloudSimple은 온디맨드 프로비저닝, 사용량에 따라 증가하는 종량제 및 용량 최적화를 제공하는 클라우드 소비 모델을 통해 총 소유 비용을 절감합니다.  기능, 이점 및 시나리오에 대한 [Azure by CloudSimple의 VMware 솔루션이란 무엇입니까?](cloudsimple-vmware-solutions-overview.md)

**클라우드심플 프라이빗 클라우드란?**

CloudSimple 프라이빗 클라우드는 Azure 위치의 Microsoft Azure 인프라(하드웨어 및 데이터 센터 공간)에 배포된 고성능 컴퓨팅, 스토리지 및 네트워킹 환경으로 구성된 전용 전용 클라우드입니다.  프라이빗 클라우드는 네이티브 VMware '서비스로서의 플랫폼'을 제공합니다. VMware 용어로 각 프라이빗 클라우드에는 vCenter 서버의 인스턴스가 정확히 하나 있습니다. vCenter 서버는 해당 가상 SAN(vSAN) 저장소와 함께 하나 이상의 vSphere 클러스터에 포함된 여러 ESXi 노드를 관리합니다. CloudSimple 서비스에는 Azure 구독에 여러 개의 프라이빗 클라우드가 포함될 수 있습니다.  자세한 내용은 [프라이빗 클라우드 개요를](cloudsimple-private-cloud.md)참조하십시오.

**CloudSimple 서비스는 어디에서 사용할 수 있습니까?**

CloudSimple은 미국 동부, 미국 서부 및 서유럽 지역에서 사용할 수 있으며 추가 리전은 곧 출시될 예정입니다.

**CloudSimple에 대한 내 구독을 사용하려면 어떻게 해야 합니까?**

CloudSimple 서비스에 대한 [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) 구독을 활성화하려면 Microsoft 계정 담당자에게 문의할 수 있습니다. CloudSimple 서비스를 사용하도록 설정한 전자 메일에 구독 ID를 제공합니다.  

**CloudSimple 포털에 액세스하려면 어떻게 해야 합니까?**

Azure 포털에서 CloudSimple 포털에 액세스합니다.  자세한 내용은 [Azure 포털에서 CloudSimple 포털별 VMware 솔루션 액세스를](access-cloudsimple-portal.md)참조하십시오.

**프라이빗 클라우드의 용량을 늘리하려면 어떻게 해야 합니까?**

용량을 늘리려면 Azure 포털에서 추가 노드를 구입한 다음 노드를 사용하여 CloudSimple 포털에서 프라이빗 클라우드를 확장합니다.  기존 vSphere 클러스터에 노드를 추가하거나 새 vSphere 클러스터에 추가할 수 있습니다.  자세한 내용은 [클라우드단순 프라이빗 클라우드 확장을](expand-private-cloud.md)참조하십시오.

**유지 보수 중에 프라이빗 클라우드는 어떻게 되나요?**

CloudSimple은 예약된 유지 관리 간격 몇 일 전에 알림을 제공합니다.  유지 관리는 프라이빗 클라우드의 가용성을 보장하기 위해 중단 없이 수행됩니다.  유지 관리는 다음과 같은 유형일 수 있습니다.

* **클라우드 간단한 인프라**.  CloudSimple 인프라는 고가용성으로 설계되었습니다.  이러한 유지 관리 간격 동안 중복 구성 요소는 서비스 중단을 방지하기 위해 한 번에 하나씩 업데이트됩니다. 프라이빗 클라우드 vCenter, 모든 가상 컴퓨터, 프라이빗 클라우드의 인터넷 연결, 온-프레미스 또는 Azure에 대한 연결을 유지관리합니다.
* **클라우드 간단한 포털**. 이러한 유지 관리 간격 동안 CloudSimple 포털의 일부 기능이 비활성화되거나 액세스할 수 없습니다.  유지 관리 간격 이전의 알림에는 유지 관리가 진행되는 동안 기능 제한에 대한 세부 정보가 포함됩니다.

## <a name="connectivity"></a>연결

**CloudSimple 리전 네트워크에 대한 연결 옵션은 무엇입니까?**

CloudSimple은 CloudSimple 지역 네트워크에 연결하는 다음과 같은 연결 옵션을 제공합니다. 여러 옵션을 동시에 사용할 수 있습니다.

* **익스프레스루트 온-프레미스 데이터 센터에서 CloudSimple 지역 네트워크에 연결.** 이 연결은 글로벌 리치를 사용하여 온-프레미스 익스프레스루트 회로를 CloudSimple ExpressRoute 회로에 연결하는 고속, 저지연, 보안 개인 연결입니다. 연결 설정에 대한 지침은 [ExpressRoute를 사용하여 온-프레미스에서 CloudSimple으로 연결](on-premises-connection.md)됨을 참조하십시오.
* **Azure 가상 네트워크에서 CloudSimple 지역 네트워크로의 ExpressRoute 연결.** 이 연결은 가상 네트워크 게이트웨이를 사용하여 Azure의 가상 네트워크를 CloudSimple ExpressRoute 회로에 브리지하는 고속의 낮은 대기 시간, 안전한 개인 연결입니다. 연결 설정에 대한 지침은 [ExpressRoute](azure-expressroute-connection.md)를 사용하여 CloudSimple 프라이빗 클라우드 환경을 Azure 가상 네트워크에 연결참조합니다.
* **온-프레미스 데이터 센터에서 CloudSimple 지역 네트워크로 사이트 간 VPN 연결.** 온-프레미스 VPN 장치에서 CloudSimple 프라이빗 클라우드 리전으로 가는 안전한 가상 사설망입니다.  자세한 내용은 [CloudSimple 네트워크에서 VPN 게이트웨이 설정을](vpn-gateway.md)참조하십시오.

**프라이빗 클라우드에 연결하려면 어떻게 해야 합니까?**

CloudSimple 포털에서 프라이빗 클라우드의 세부 정보를 볼 수 있습니다. 프라이빗 클라우드에 해당하는 vCenter에 연결하려면 먼저 사이트 간 VPN, 사이트 간 VPN 또는 ExpressRoute를 사용하여 네트워크 연결이 설정되었는지 확인합니다. 그런 다음 Azure 포털에서 CloudSimple 포털을 실행하고 홈 페이지 또는 사설 클라우드 세부 정보 페이지에서 **vSphere 클라이언트 를 클릭합니다.**

**익스프레스루트 회로의 장점은 무엇입니까?**

Azure ExpressRoute 회로는 고속, 대기 시간이 짧은 보안 연결입니다.  CloudSimple은 고객당 리전당 전용 익스프레스루트 회로를 제공합니다.  이 회로를 사용하여 온-프레미스 또는 Azure 구독에서 보안 연결을 설정할 수 있습니다.

**CloudSimple에 연결하는 데 드는 네트워크 비용은 어떻게 됩니까?  CloudSimple과 Azure 간에 또는 리전 간에 송신 요금이 적용됩니까?**

네트워크 송신에 대한 CloudSimple 요금은 없습니다.  Azure 표준 요금은 가상 네트워크 또는 온-프레미스 ExpressRoute 회로에서 발생하는 모든 트래픽에 적용됩니다.

## <a name="networking"></a>네트워킹

**프라이빗 클라우드에서 사용할 수 있는 네트워킹 기능은 무엇입니까?**

VLAN(및 해당 서브넷) 및 방화벽 테이블을 프로비전하고 프라이빗 클라우드에서 실행되는 가상 시스템에 매핑되는 공용 IP 주소를 할당할 수 있습니다. 네트워킹 기능에 대한 자세한 내용은 [VLAN 및 서브넷 개요,](cloudsimple-vlans-subnets.md) [방화벽 테이블 개요](cloudsimple-firewall-tables.md)및 공용 IP 주소 [개요를](cloudsimple-public-ip-address.md)참조하십시오.

**프라이빗 클라우드에서 애플리케이션에 대해 다른 서브넷을 설정하려면 어떻게 해야 합니까?**

CloudSimple 포털에서 프라이빗 클라우드에 VLAN을 만듭니다.  VLAN을 만든 후 VLAN을 사용하여 프라이빗 클라우드 vCenter에서 분산 포트 그룹을 만들고 분산 포트 그룹에 연결된 가상 컴퓨터를 만들 수 있습니다.  VLAN/서브넷에 대한 방화벽 테이블을 활성화하고 방화벽 규칙을 정의하여 네트워크 트래픽을 보호할 수 있습니다.

**내 프라이빗 클라우드에 사용할 수 있는 방화벽 설정은 무엇입니까?**

남북 및 동서 트래픽에 대한 규칙을 구성할 수 있습니다.  규칙은 방화벽 테이블에 정의되어 있습니다.  방화벽 테이블은 프라이빗 클라우드의 VLAN에 연결할 수 있습니다.  자세한 내용은 [사설 클라우드에 대한 방화벽 테이블 및 규칙 설정을](firewall.md)참조하십시오.

**프라이빗 클라우드 환경에서 VM에 공용 IP 주소를 할당할 수 있습니까?**

CloudSimple 포털에서 새 공용 IP 주소를 할당하고 가상 컴퓨터 또는 어플라이언스의 개인 IP 주소와 연결할 수 있습니다.  새 방화벽 규칙을 만들거나 기존 방화벽 규칙을 적용하여 포털의 특정 포트 및 IP 주소의 트래픽을 허용할 수도 있습니다. 자세한 내용은 [프라이빗 클라우드 환경에 대한 공용 IP 주소 할당을](public-ips.md)참조하십시오.

## <a name="security"></a>보안

**CloudSimple의 보안 옵션은 무엇입니까?**

CloudSimple은 프라이빗 클라우드 환경을 보호하기 위한 다음과 같은 보안 기능을 제공합니다.

* **미사용 데이터 암호화**. 프라이빗 클라우드의 vSAN 저장소에 있는 미사용 데이터를 암호화할 수 있습니다. vSAN은 Azure vNet 또는 온-프레미스 환경에 배포할 수 있는 외부 키 관리 서버를 지원합니다.  자세한 내용은 [CloudSimple 프라이빗 클라우드에 대한 vSAN 암호화 구성을](vsan-encryption.md)참조하십시오.
* **네트워크 보안**. 프라이빗 클라우드와 인터넷, 프라이빗 클라우드 및 온-프레미스 환경 또는 프라이빗 클라우드의 서브넷 내에 적용되는 방화벽 규칙으로 네트워크 트래픽 흐름을 제어합니다.
* **안전한 개인 연결**. 온-프레미스 네트워크와 Azure 구독 간에 안전한 개인 연결이 설정됩니다.

## <a name="compute"></a>컴퓨팅

**어떤 종류의 호스트를 사용할 수 있습니까?**

CloudSimple은 다음과 같은 호스트 유형을 제공합니다.

* **CS28 노드:** CPU : 2x 2.2 GHz, 총 28 코어, 48 HT.  RAM: 256GB.  스토리지: 1600GB NVMe 캐시, 5760GB 데이터(올플래시). 네트워크 : 4x25Gbe NIC
* **CS36 노드:** CPU 2x 2.3 GHz, 총 36 코어, 72 HT.  RAM: 512GB.  스토리지: 3200GB NVMe 캐시 11520GB 데이터(올플래시).  네트워크 : 4x25Gbe NIC
* **CS36m 노드:** CPU 2x 2.3 GHz, 총 36 코어, 72 HT.  RAM: 576GB.  스토리지: 3200GB NVMe 캐시 13360GB 데이터(올플래시).  네트워크 : 4x25Gbe NIC

**하드웨어 오류는 어떻게 처리됩니까?**

모든 CloudSimple 인프라는 CloudSimple 플랫폼과 서비스 운영 팀에 의해 지속적으로 모니터링됩니다.  하드웨어 오류가 감지되면 새 노드가 프라이빗 클라우드에 추가되고 실패한 노드가 제거됩니다.

## <a name="storage"></a>스토리지

**프라이빗 클라우드에서 어떤 유형의 스토리지가 지원됩니까?**

CloudSimple은 모든 프라이빗 클라우드와 함께 올플래시 VMware vSAN 스토리지를 제공합니다.  각 vSphere는 자체 vSAN 데이터 스토어로 만들어집니다.  자세한 내용은 [프라이빗 클라우드 VMware 구성 요소- vSAN 스토리지를](vmware-components.md#vsan-storage)참조하십시오.

**데이터 암호화가 지원됩니까?**
예.  프라이빗 클라우드에서 vSAN 저장소를 설정하여 온-프레미스 또는 Azure에 배포된 KMS(키 관리 서버)를 사용하여 vSAN에 저장된 데이터를 암호화할 수 있습니다.

**실패한 디스크는 어떻게 처리됩니까?**

CloudSimple은 프라이빗 클라우드의 모든 하드웨어 구성 요소를 지속적으로 모니터링합니다.  디스크 오류가 감지되거나 디스크가 실패로 식별되면(휴리스틱에 따라) 새 노드가 프라이빗 클라우드에 자동으로 추가됩니다.  장애가 있거나 실패한 디스크가 있는 노드가 사설 클라우드에서 제거됩니다.

## <a name="vmware"></a>VMware

**온-프레미스에서 응용 프로그램 및 데이터의 대규모 업로드 또는 마이그레이션을 수행하려면 어떻게 해야 합니까?**

CloudSimple은 네이티브 VMware vSphere 솔루션을 제공합니다.  대량 데이터 마이그레이션을 위한 모든 VMware 도구를 프라이빗 클라우드와 함께 사용할 수 있습니다.  옵션은 다음과 같습니다.

* 데이터의 대량 마이그레이션을 위한 VMware HCX.
* 온-프레미스에서 CloudSimple으로 스토리지 vMotion을 사용하여 데이터의 콜드 마이그레이션.

**VMware 도구를 설치할 수 있습니까?**

CloudSimple은 네이티브 VMware vSphere 솔루션을 제공합니다.  온-프레미스 vSphere 환경을 관리하는 데 사용되는 모든 VMware 도구는 CloudSimple에서 사용할 수 있습니다.  CloudSimple은 VMware 도구를 설치하기 위한 BYOL(사용자 라이선스 가져오기) 모델을 지원합니다.

**업데이트 및 업그레이드는 어떻게 관리됩니까?**

CloudSimple은 원활한 무중단 방식으로 프라이빗 클라우드의 모든 인프라 구성 요소를 관리하고 업데이트합니다.  VMware 또는 인프라 공급업체에서 릴리스한 모든 업데이트 및 보안 패치는 CloudSimple에 의해 인증되는 즉시 업데이트될 예정입니다.

CloudSimple은 프라이빗 클라우드에 설치된 애플리케이션의 업그레이드 또는 업데이트를 수행하지 않습니다.

## <a name="azure-integration"></a>Azure 통합

**어떤 Azure 서비스가 지원됩니까?**

CloudSimple은 Azure에서 구독에 대한 Azure 익스프레스라우팅 연결을 제공합니다.  구독에서 실행되는 모든 서비스는 프라이빗 클라우드에 연결할 수 있습니다.  다음은 이러한 템플릿의 예입니다.

* 클라우드심플 vCenter의 ID 원본으로 **Azure Active Directory.**
* 프라이빗 클라우드에서 백업, 이미지 및 기타 데이터를 저장하기 위한 **Azure 저장소입니다.**
* 퍼블릭 및 프라이빗 클라우드에 걸쳐 있는 응용 프로그램 아키텍처를 갖춘 **하이브리드 응용** 프로그램입니다.  예를 들어 Azure에서 사설 클라우드의 응용 프로그램 및 데이터베이스 서버에 액세스하는 웹 서버를 만들 수 있습니다.
* VMware에서 실행되는 워크로드에 대한 **Azure 모니터** 및 Azure **보안 센터는** 로깅, 성능 메트릭 및 보안 관리를 지원합니다.

**VMware 테넌을 Azure에 매핑하려면 어떻게 해야 합니까?**

CloudSimple은 Azure 포털에서 프라이빗 클라우드에서 VMware VM을 관리할 수 있는 고유한 기능을 제공합니다.  원하는 리소스 제약 조건으로 구성된 vCenter 리소스 풀은 전역 관리자가 구독에 매핑할 수 있습니다.  

**Azure는 어떤 라이선스 혜택을 받을 수 있나요?**

CloudSimple을 사용하면 Azure 하이브리드 사용 혜택을 활용하고 라이선스를 최대 90%까지 절약할 수 있습니다. 이 이점은 Microsoft 라이선스에 대한 투자를 보존하고 다른 클라우드 솔루션에 비해 TCO를 낮춥니다. 또한 Windows Server 2008 및 Microsoft SQL Server 2008에 대한 확장 된 보안 업데이트를 받을 수 있습니다.  BYOL(사용자 라이선스 가져오기) 모델을 사용하면 Veeam 및 Zerto와 같은 일반적인 앱의 비용을 낮게 유지할 수 있습니다.  
