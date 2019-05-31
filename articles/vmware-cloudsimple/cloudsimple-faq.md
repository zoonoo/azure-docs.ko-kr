---
title: FAQ-CloudSimple VMware 솔루션
description: VMware 솔루션 CloudSimple Azure에 대 한 질문과 대답
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358530"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>CloudSimple 하 여 VMware 솔루션에 대 한 질문과 대답

질문과 대답 및 도움이 되는 VMware CloudSimple 솔루션에 대 한 답변에는 서비스 및 사용 하는 방법을 이해 합니다.  질문 및 답변은 다음과 같은 범주로 정렬 됩니다.

* CloudSimple 서비스
* 연결
* 네트워킹
* 보안
* 컴퓨팅
* Storage
* VMware
* Azure 통합
  
## <a name="cloudsimple-service"></a>CloudSimple 서비스

**CloudSimple 하 여 VMware 솔루션 이란?**

**VMware 솔루션 CloudSimple** 변환 하 고 몇 분 안에 Azure에서 전용 클라우드에 VMware 워크 로드를 확장 합니다. 에서는 프로 비전, 인프라를 관리 하 고 온-프레미스와 Azure 간에 워크 로드 오케스트레이션 주의 해야 합니다. Azure에서 및을 정확 하 게 동일한 온-프레미스 앱이 실행 하므로 탄력성 및 복잡 한 앱의 아키텍처를 변경 하지 않고 클라우드 서비스에서 활용 합니다. CloudSimple 용량 최적화를 주문형으로 프로 비전, 지불으로-성장를 제공 하는 클라우드 소비 모델을 사용 하 여 소유 비용을 낮춥니다.  참조 [CloudSimple 하 여 Azure에서 VMware 솔루션 이란](cloudsimple-vmware-solutions-overview.md) 기능, 이점 및 시나리오에 대 한 합니다.

**CloudSimple 사설 클라우드 란?**

고성능 계산, 저장소, 구성 및 Azure 위치에서 Microsoft Azure 인프라 (하드웨어 및 데이터 센터 공간)에 배포 된 환경 네트워킹 전용 클라우드를 프로 비전 합니다.  사설 클라우드 ' as a service' 네이티브 VMware 플랫폼을 제공합니다. VMware 측면에서 각 사설 클라우드는 vCenter Server의 인스턴스 중 하나만 포함합니다. VCenter 서버에서 해당 가상 SAN (vSAN) 저장소와 함께 하나 이상의 vSphere 클러스터에 포함 된 여러 ESXi 노드를 관리 합니다. CloudSimple 서비스는 Azure 구독에서 여러 사설 클라우드를 포함할 수 있습니다.  사설 클라우드에 대 한 자세한 내용은 참조 하세요. [Private Cloud 개요](cloudsimple-private-cloud.md)합니다.

**CloudSimple 서비스는 사용할 수 있는 어디 입니까?**

CloudSimple는 미국 동부 및 미국 서 부 지역에서 사용할 수 있습니다.

**CloudSimple 내 구독 사용 하려면 어떻게 하나요?**

Microsoft 계정 담당자에 문의 [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) CloudSimple 서비스에 대 한 구독을 사용 하도록 설정 합니다. 원하는 CloudSimple 서비스를 사용할 수 있는 전자 메일에 구독 ID를 제공 합니다.  

**CloudSimple 포털에 액세스 하려면 어떻게 해야 합니까?**

Azure portal에서 CloudSimple 포털에 액세스할 수 있습니다.  참조 [Azure portal에서 CloudSimple 포털에서 VMware 솔루션에 액세스](https://docs.azure.cloudsimple.com/access-cloudsimple-portal) 문서를 CloudSimple 포털에 액세스 하세요. 

**사설 클라우드 용량을 늘리려면 어떻게 해야 합니까?**

Azure portal에서 노드를 구입 하 고 CloudSimple 포털에서 사설 클라우드를 확장 합니다.  기존 vSphere 클러스터에 다른 노드를 추가 하거나 새 vSphere 클러스터를 만들어 사설 클라우드를 확장할 수 있습니다.  참조 [CloudSimple 사설 클라우드 확장](https://docs.azure.cloudsimple.com/expand-private-cloud) 절차 문서입니다.

**유지 관리 동안 사설 클라우드 내에 어떻게 되나요?**

CloudSimple 일 예약 된 유지 관리 전에 정기 알림을 제공합니다.  유지 관리는 사설 클라우드의 가용성을 보장 하는 무중단 방법에서 수행 됩니다.  유지 관리는 다음 형식일 수 있습니다.

1. **CloudSimple 인프라:**  CloudSimple 인프라는 항상 사용 가능 하도록 설계 되었습니다.  유지 관리 하는 동안 영향을 주지 않고 한 번에 하나씩 중복 구성 요소를 업데이트 하 여 사설 클라우드의 연결과 가용성이 보장 됩니다.  사설 클라우드 vCenter, 모든 가상 컴퓨터, 사설 클라우드에서 인터넷 연결 및 온-프레미스 또는 Azure에 대 한 연결에 액세스를 해야 합니다.
2. **CloudSimple 포털:** 유지 관리 하는 동안 액세스할 수 없거나 사용 안 함 CloudSimple 포털에서 일부 기능 수 없습니다 수 있습니다.  유지 관리 알림 포털에서 수행할 수 있는 작업의 세부 정보가 포함 됩니다.

## <a name="connectivity"></a>연결

**CloudSimple 지역 네트워크에 연결 하나요?**

CloudSimple는 CloudSimple 지역 네트워크에 연결 하려면 세 가지 서로 다른 연결 옵션을 제공 합니다.  세 가지 모두를 함께 사용할 수 있습니다.

1. ExpressRoute 연결은 온-프레미스 데이터 센터에서 CloudSimple 지역 네트워크 대기 시간이 짧은 고속 보안 개인 연결 글로벌 환경 사용 하 여 CloudSimple ExpressRoute 회로 사용 하 여 온-프레미스 ExpressRoute 회로 브리징입니다. 참조 [CloudSimple ExpressRoute를 사용 하 여 온-프레미스에서 연결할](https://docs.azure.cloudsimple.com/on-premises-connection) 연결 설정에 대 한 문서.
2. CloudSimple 지역 네트워크-가상 네트워크 게이트웨이 사용 하 여 CloudSimple ExpressRoute 회로 사용 하 여 Azure에서 가상 네트워크를 브리징 고속 대기 시간이 짧은 안전한 개인 연결에 Azure 가상 네트워크에서 ExpressRoute 연결.  참조 [CloudSimple 사설 클라우드 환경의 ExpressRoute를 사용 하 여 Azure 가상 네트워크에 연결](https://docs.azure.cloudsimple.com/azure-expressroute-connection) 연결 설정에 대 한 문서.
3. 온-프레미스 데이터 센터에서 CloudSimple 지역 네트워크로의 사이트 간 VPN 연결 CloudSimple 사설 클라우드 지역으로 온-프레미스 VPN 장치에서 개인 가상 네트워크를 보호 합니다.  VPN 연결을 설정 하는 것에 대 한 [CloudSimple 사설 클라우드 확인 하 고 온-프레미스 네트워크 간의 VPN 연결 설정] 참조 문서입니다.

**사설 클라우드를 연결 하는 방법**

CloudSimple 포털에서 사설 클라우드의 세부 정보를 볼 수 있습니다. 사설 클라우드를 해당 vCenter에 연결 하려면 ExpressRoute 또는 사이트 간, 지점 및 사이트에는 설정 된를 사용 하 여 네트워크 연결을 확인 합니다. 다음을 클릭 하 고 Azure portal에서 CloudSimple 포털을 시작 *vSphere 클라이언트를 시작* 홈페이지 또는 사설 클라우드 세부 정보 페이지입니다.

**ExpressRoute 회로의 장점은 무엇입니까?**

Azure ExpressRoute 회로 대기 시간이 짧은 고속 보안 연결을 제공합니다.  CloudSimple 고객 당 지역당 전용된 ExpressRoute 회로 제공합니다.  이 회로 사용 하 여 온-프레미스 및/또는 Azure 구독에서 보안 연결을 설정할 수 있습니다.

**이란 CloudSimple에서 연결할 네트워크 비용입니다. Azure에 CloudSimple에서 송신 요금이? 지역?**

모든 네트워크 송신에 대 한 요금은 없습니다.  표준 요금으로 azure 가상 네트워크 또는 온-프레미스 ExpressRoute 회로에서 모든 송신 트래픽에 적용 됩니다.

## <a name="networking"></a>네트워킹

**사설 클라우드 내에 사용할 수 있는 네트워킹 기능은 무엇입니까?**

Vlan (및 해당 서브넷)를 프로 비전, 테이블, 방화벽 및 공용 IP 주소 할당을 사설 클라우드에서 실행 중인 가상 컴퓨터에 매핑합니다.  자세한 내용은 [Vlan/서브넷 개요](cloudsimple-vlans-subnets.md)를 [방화벽 테이블 개요](cloudsimple-firewall-tables.md), 및 [공용 IP 주소 개요](cloudsimple-public-ip-address.md) 문서입니다.

**설정 하려면 어떻게 합니까 서로 다른 서브넷 내 응용 프로그램에 대 한 사설 클라우드 내에서?**

CloudSimple 포털에서 개인 클라우드를 사용 하 여 Vlan을 만들 수 있습니다.  VLAN을 만든 후 VLAN을 사용 하 여 사설 클라우드 vCenter에는 분산된 포트 그룹을 만들 수 있으며 분산된 포트 그룹에 연결 된 가상 컴퓨터를 만듭니다.  VLAN/서브넷에 대 한 방화벽 테이블을 사용 하도록 설정 하 고 네트워크 트래픽을 보호 하는 방화벽 규칙을 정의할 수 있습니다.

**사설 클라우드 내에 사용할 수 있는 방화벽 설정은 어떤?**

북-남 및 동-서 트래픽에 대 한 규칙을 구성할 수 있습니다.  규칙은 방화벽 테이블에 정의 됩니다.  방화벽은 VLAN(s) 사설 클라우드에 연결할 수 있습니다.  참조 [사설 클라우드에 대 한 방화벽 테이블 및 규칙 설정](https://docs.azure.cloudsimple.com/firewall) 설정 절차에 대 한 문서.

**할당할 수 있습니까 공용 IP 주소 Vm에 대 한 사설 클라우드 환경 내에서?**

CloudSimple 포털에서 새 공용 IP 주소를 할당 하 고 가상 컴퓨터 또는 어플라이언스의 개인 IP 주소를 사용 하 여 연결 쉽게 수 있습니다.  새 방화벽 규칙을 만들 하거나 트래픽을 특정 포트 및/또는 특정 일련의 포털에서 IP 주소를 허용 하는 기존 방화벽 규칙을 적용할 수도 있습니다. 참조 [사설 클라우드 환경에 대 한 공용 IP 주소를 할당할](https://docs.azure.cloudsimple.com/public-ips) 설정 절차에 대 한 합니다.

## <a name="security"></a>보안

**CloudSimple에서 내 보안 옵션은 무엇입니까?**

CloudSimple 사설 클라우드 사설 클라우드 환경의 보안에 대 한 다음과 같은 보안 기능을 제공 합니다.

1. **데이터 암호화 미사용**: 사설 클라우드에서 vSAN 저장소에 있는 미사용 데이터를 암호화할 수 있습니다. vSAN은 Azure vNet 또는 온-프레미스 환경에서 배포할 수 있는 외부 키 관리 서버를 지원 합니다.  참조 [CloudSimple 사설 클라우드를 위한 vSAN 암호화 구성](https://docs.azure.cloudsimple.com/vsan-encryption) 대 한 자세한 내용은 합니다.
2. **네트워크 보안**: 네트워크 트래픽 흐름을 제어 간에 인터넷, 온-프레미스에서 방화벽 규칙을 사용 하 여 사설 클라우드의 서브넷 내에서 사설 클라우드입니다.
3. **보안, 개인 연결**: 온-프레미스 네트워크와 Azure 구독 간에 개인 연결을 보호 합니다.

## <a name="compute"></a>컴퓨팅

**호스트의 종류를 사용할 수 있습니까?**

CloudSimple는 두 호스트 형식을 제공합니다.

* **CS28 노드:** CPU:2 2.2 GHz 총 28 개 코어, 48 x HT 합니다.  RAM: 256GB입니다.  스토리지: 1600 GB NVMe 캐시 5760 GB 데이터 (모든 플래시). 네트워크: 2x25Gbe NIC
* **CS36 노드:** CPU 2 x 2.3 GHz, 총 36 개 코어, 72 HT 합니다.  RAM: 512 GB입니다.  스토리지: 3200 GB NVMe 캐시 11,520 GB 데이터 (모든 플래시)입니다.  네트워크: 2x25Gbe NIC

**하드웨어 오류는 어떻게 처리 되나요?**

모든 CloudSimple 인프라 CloudSimple 플랫폼 및 서비스 작업 팀에서 지속적으로 모니터링 됩니다.  하드웨어 오류가 감지 되 면, 새 노드가 사설 클라우드에 추가 되 고 실패 한 노드의 사설 클라우드의 고가용성을 보장이 제거 됩니다.

## <a name="storage"></a>Storage

**어떤 유형의 저장소는 사설 클라우드에서 지원 되나요?**

CloudSimple 제공 **모든 플래시 VMware vSAN 저장소** 모든 사설 클라우드를 사용 하 여 합니다.  각 vSphere 자체 vSAN 데이터 저장소를 사용 하 여 만들어집니다.  참조 [사설 클라우드 VMware 구성 요소-vSAN 저장소](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage) 을 참조 하십시오.

**지원 되는 데이터의 암호화 기능**
예.  설정할 수 있습니다 vSAN 저장소 배포 된 온-프레미스 키 관리 서버 (KMS)를 사용 하 여 사설 클라우드 또는 Azure에서 vSAN에 저장 된 데이터를 암호화 하기 위한

**실패 한 디스크는 어떻게 처리 되나요?**

CloudSimple 모니터링 사설 클라우드의 모든 하드웨어 구성 요소를 지속적으로 모니터링 합니다.  모든 디스크 오류가 감지 될 경우 모든 디스크 실패 (추론에 따라)로 식별 됩니다 새 노드를 사설 클라우드로 자동으로 추가 됩니다.  노드 실패 또는 실패 한 디스크를 사용 하 여 사설 클라우드에서 제거 됩니다.

## <a name="vmware"></a>VMware

**대규모 업로드/마이그레이션 응용 프로그램 및 데이터 온-프레미스에서 수행 하려면 어떻게 해야 합니까?**

CloudSimple 네이티브 VMware vSphere 솔루션을 제공합니다.  CloudSimple 사설 클라우드를 사용 하 여 대량 데이터 마이그레이션에 사용 되는 도구를 사용할 수 있습니다.  사용 가능한 옵션은 다음과 같습니다.

1. 대량 데이터 마이그레이션에 대 한 VMware HCX 합니다.
2. 콜드 CloudSimple 온-프레미스에서 Storage vMotion을 사용 하 여 데이터 마이그레이션의 합니다.

**VMware 도구를 설치할 수 있습니까?**

CloudSimple 네이티브 VMware vSphere 솔루션을 제공합니다.  CloudSimple에 vSphere 환경 온-프레미스 관리에 사용 되는 도구를 사용할 수 있습니다.  CloudSimple는 VMware 도구 설치에 대 한 Bring Your-라이선스 (BYOL) 모델을 지원 합니다.

**업데이트 및 업그레이드 관리 하는 방법?**

CloudSimple를 관리 하 고 원활한 무중단 방식 사설 클라우드의 모든 인프라 구성 요소를 업데이트 합니다.  VMware 또는 인프라 공급 업체에서 릴리스된 모든 업데이트 또는 보안 패치 CloudSimple로 한정 됩니다 하는 즉시 업데이트에 대 한 예약 됩니다.

CloudSimple는 업그레이드 또는 사설 클라우드에 설치 하는 응용 프로그램의 업데이트를 수행 하지 않습니다.

## <a name="azure-integration"></a>Azure 통합

**어떤 Azure 서비스가 지원 됩니까?**

CloudSimple는 Azure에서 구독에 Azure ExpressRoute 연결을 제공합니다.  구독에서 실행 되는 모든 서비스는 사설 클라우드를 네트워크에 연결 되어 및 사설 클라우드를 연결할 수 있습니다.  예제:

1. **Azure Active Directory** CloudSimple vCenter에 대 한 identity 원본으로
2. **Azure storage** 백업, 이미지 및 사설 클라우드를 다른 데이터를 저장 합니다.
3. **하이브리드 응용 프로그램** -공용 및 사설 클라우드를 포괄 하는 응용 프로그램 아키텍처를 만들 수 있습니다.  예를 들어, 응용 프로그램 및 데이터베이스 서버의 CloudSimple 사설 클라우드를 액세스 하는 Azure에서 webservers를 만들 수 있습니다.
4. **Azure monitor** 하 고 **Azure security center** -VMware에서 실행 중인 작업 로깅, 성능 메트릭 및 보안 관리 하는 데 사용할 수 있습니다.

**내 VMware 테 넌 트를 Azure에 매핑하려면 어떻게 하나요?**

CloudSimple Azure portal에서 사설 클라우드에서 VMware Vm을 관리 하는 고유한 기능을 제공 합니다.  VCenter 리소스 풀 (원하는 리소스 제약 조건으로 구성 됨)는 전역 관리자가 구독에 매핑할 수 있습니다.  

**어떤 라이선스 혜택을 Azure를 사용 하나요?**

CloudSimple를 사용 하 여 Azure 하이브리드 사용 혜택을 활용할 수 있으며 다른 클라우드에 비해 TCO를 낮추고 Microsoft 라이선스에 대 한 투자를 유지 하는 라이선스에서 최대 90%를 절약할 수 있습니다. 또한 Microsoft SQL Server 2008 및 Windows Server 2008에 대 한 보안 업데이트를 확장 가져오기.  Veeam, 프로그램인 Zerto 등과 같은 일반적인 앱에 대 한 클라우드 비용을 사용 하 여 라이선스 (BYOL) 낮게 유지 합니다.  