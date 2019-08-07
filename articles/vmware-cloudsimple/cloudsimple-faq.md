---
title: FAQ-CloudSimple 별 VMware 솔루션
description: CloudSimple의 Azure VMware 솔루션에 대 한 질문과 대답
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80380a1c33927029e000e59a5834f297340f5be3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816235"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>CloudSimple의 VMware 솔루션에 대 한 질문과 대답

서비스 및 사용 방법에 대해 이해 하는 데 도움이 되는 CloudSimple의 Azure VMware 솔루션에 대 한 질문과 대답입니다. 질문과 대답은 다음과 같은 범주로 정렬 됩니다.

* CloudSimple 서비스
* 연결
* 네트워킹
* 보안
* 컴퓨팅
* 저장 공간
* VMware
* Azure 통합
 
## <a name="cloudsimple-service"></a>CloudSimple 서비스

**CloudSimple의 Azure VMware 솔루션 이란?**

클라우드를 통한 azure VMware 솔루션은 azure의 전용 클라우드 (몇 분 내 전용 클라우드)로 VMware 워크 로드를 확장할 수 있습니다. 이 솔루션은 인프라를 프로 비전 하 고 관리 하며 온-프레미스와 Azure 간의 워크 로드를 오케스트레이션 합니다. 앱이 온-프레미스와 Azure에서 동일 하 게 실행 되기 때문에 앱을 다시 설계 하는 복잡성 없이 클라우드의 탄력성 및 서비스를 활용 하는 것이 좋습니다. CloudSimple은 주문형 프로 비전, 종 량 제 및 용량 최적화를 제공 하는 클라우드 소비 모델을 사용 하 여 총 소유 비용을 낮춥니다. 기능, 이점 및 시나리오는 [Azure VMware Solution By CloudSimple 이란?](cloudsimple-vmware-solutions-overview.md)을 참조 하세요.

**CloudSimple 사설 클라우드 란?**

Azure 위치에서 Microsoft Azure 인프라 (하드웨어 및 데이터 센터 공간)에 배포 된 고성능 계산, 저장소 및 네트워킹 환경으로 구성 된 사설 전용 클라우드를 프로 비전 합니다. 사설 클라우드는 네이티브 VMware platform as a service를 제공 합니다. VMware 용어로 각 사설 클라우드에는 정확히 하나의 vCenter Server 인스턴스가 포함 됩니다. VCenter Server는 해당 Vsphere 저장소와 함께 하나 이상의 vSphere 클러스터에 포함 된 여러 ESXi 노드를 관리 합니다. CloudSimple 서비스는 Azure 구독에 여러 사설 클라우드를 포함할 수 있습니다. 사설 클라우드에 대 한 자세한 내용은 [사설 클라우드 개요](cloudsimple-private-cloud.md)를 참조 하세요.

**CloudSimple 서비스는 어디에서 사용할 수 있나요?**

CloudSimple은 미국 동부 및 미국 서 부 지역에서 사용할 수 있습니다.

**CloudSimple에 대해 내 구독을 사용 하도록 설정 어떻게 할까요??**

Cloudsimple 서비스에 대 [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) 한 구독을 사용 하도록 설정 하려면 Microsoft 계정 담당자에 게 문의 하세요. CloudSimple 서비스를 사용 하도록 설정할 전자 메일에 구독 ID를 제공 합니다. 

**CloudSimple 포털에 액세스할 어떻게 할까요? 있나요?**

Azure Portal에서 CloudSimple 포털에 액세스 합니다. CloudSimple 포털에 액세스 하는 방법에 대 한 자세한 내용은 [Azure Portal에서 CloudSimple 포털에의 한 VMware 솔루션 액세스](https://docs.azure.cloudsimple.com/access-cloudsimple-portal)를 참조 하세요.

**사설 클라우드의 용량을 늘릴 어떻게 할까요? 있나요?**

Azure Portal에서 노드를 프로 비전 하 고 CloudSimple 포털에서 사설 클라우드를 확장 합니다. 기존 vSphere 클러스터에 노드를 추가 하거나 새 vSphere 클러스터를 만들어 사설 클라우드를 확장할 수 있습니다. 절차에 대 한 자세한 내용은 [CloudSimple 사설 클라우드 확장](https://docs.azure.cloudsimple.com/expand-private-cloud)을 참조 하세요.

**유지 관리 중 사설 클라우드는 어떻게 되나요?**

CloudSimple은 예약 된 유지 관리 전에 정기적으로 알림을 제공 합니다. 유지 관리는 사설 클라우드의 가용성을 보장 하기 위해 무중단 된 방식으로 수행 됩니다. 유지 관리의 유형은 다음 중 하나일 수 있습니다.

- **Cloudsimple 인프라**: CloudSimple 인프라는 항상 사용 가능 하도록 설계 되었습니다. 유지 관리 중에는 영향을 주지 않고 한 번에 하나씩 중복 구성 요소를 업데이트 하 여 사설 클라우드의 연결과 가용성을 보장 합니다. 사설 클라우드 vCenter, 모든 가상 머신, 사설 클라우드의 인터넷 연결, 온-프레미스 또는 Azure에 대 한 연결에 액세스할 수 있습니다.
- **Cloudsimple 포털**: 유지 관리 중에 CloudSimple 포털의 일부 기능에 액세스 하지 못할 수도 있고 사용 하지 못할 수도 있습니다. 유지 관리 알림에는 포털에서 수행할 수 있는 작업에 대 한 정보가 포함 되어 있습니다.

## <a name="connectivity"></a>연결

**CloudSimple 영역 네트워크에 대 한 연결 옵션은 무엇 인가요?**

CloudSimple은 CloudSimple 영역 네트워크에 연결 하기 위한 세 가지 연결 옵션을 제공 합니다. 세 가지 옵션을 모두 함께 사용할 수 있습니다.

- 온-프레미스 데이터 센터에서 CloudSimple 영역 네트워크로 Azure Express 경로 연결: Global Reach를 사용 하 여 CloudSimple Express 경로 회로와 온-프레미스 Express 경로 회로를 연결 하는 고속 대기 시간이 짧은 보안 개인 연결입니다. 연결을 설정 하려면 Express 경로를 [사용 하 여 온-프레미스에서 CloudSimple로 연결](https://docs.azure.cloudsimple.com/on-premises-connection)을 참조 하세요.
- Azure 가상 네트워크에서 CloudSimple 영역 네트워크로의 Express 경로 연결: 가상 네트워크 게이트웨이를 사용 하 여 Azure의 가상 네트워크를 CloudSimple Express 경로 회로와 연결 하는 대기 시간이 짧은 고속 보안 개인 연결입니다. 연결을 설정 하려면 Express 경로를 [사용 하 여 CloudSimple 사설 클라우드 환경을 Azure virtual network에 연결](https://docs.azure.cloudsimple.com/azure-expressroute-connection)을 참조 하세요.
- 온-프레미스 데이터 센터에서 CloudSimple 영역 네트워크로 사이트 간 VPN 연결: 온-프레미스 VPN 장치에서 CloudSimple 사설 클라우드 지역으로의 보안 가상 개인 네트워크 연결을 설정 하려면 [온-프레미스 네트워크와 CloudSimple 사설 클라우드 간에 VPN 연결 설정](https://docs.azure.cloudsimple.com/set-up-vpn)을 참조 하세요.

**사설 클라우드에 연결 어떻게 할까요??**

CloudSimple 포털에서 사설 클라우드의 세부 정보를 볼 수 있습니다. 사설 클라우드에 해당 하는 vCenter에 연결 하려면 사이트 간, 지점 및 사이트 간 또는 Express 경로를 사용 하 여 네트워크 연결이 설정 되어 있는지 확인 합니다. 그런 다음 Azure Portal에서 CloudSimple 포털을 시작 합니다. 홈 페이지 또는 사설 클라우드 세부 정보 페이지에서 **vSphere 클라이언트 시작** 을 선택 합니다.

**Express 경로 회로의 이점은 무엇 인가요?**

Azure Express 경로 회로는 고속 및 대기 시간이 짧은 보안 연결을 제공 합니다. CloudSimple은 고객 당 지역 당 전용 Express 경로 회로를 제공 합니다. 이 회로를 사용 하 여 온-프레미스와 Azure 구독에서 보안 연결을 설정할 수 있습니다.

**CloudSimple에 연결 하는 데 드는 네트워크 비용은 무엇 인가요? CloudSimple에서 Azure로의 송신 요금이 있나요? 지역 간에 송신 요금이 있나요?**

네트워크 송신에 대 한 요금이 부과 되지 않습니다. Azure 표준 요금은 가상 네트워크 또는 온-프레미스 Express 경로 회로에서 송신 트래픽에 적용 됩니다.

## <a name="networking"></a>네트워킹

**사설 클라우드에 사용할 수 있는 네트워킹 기능은 무엇 인가요?**

Vlan 및 서브넷 및 방화벽 테이블을 프로 비전 할 수 있습니다. 공용 IP 주소를 할당 하 고 사설 클라우드에서 실행 되는 가상 컴퓨터에 매핑할 수 있습니다. 자세한 내용은 [vlan 및 서브넷 개요](cloudsimple-vlans-subnets.md), [방화벽 테이블 개요](cloudsimple-firewall-tables.md)및 [공용 IP 주소 개요](cloudsimple-public-ip-address.md)를 참조 하세요.

**사설 클라우드의 내 응용 프로그램에 대해 다른 서브넷을 설정 어떻게 할까요??**

CloudSimple 포털에서 사설 클라우드에 Vlan을 만들 수 있습니다. VLAN을 만든 후에는 VLAN을 사용 하 여 사설 클라우드 vCenter에 분산 포트 그룹을 만들고 분산 포트 그룹에 연결 된 가상 컴퓨터를 만들 수 있습니다. VLAN 또는 서브넷에 대 한 방화벽 테이블을 사용 하도록 설정 하 고 네트워크 트래픽을 보호 하는 방화벽 규칙을 정의할 수 있습니다.

**사설 클라우드에 사용할 수 있는 방화벽 설정은 무엇 인가요?**

북-남 및 동-서 트래픽에 대 한 규칙을 구성할 수 있습니다. 규칙은 방화벽 테이블에 정의 됩니다. 방화벽 테이블은 사설 클라우드의 Vlan에 연결할 수 있습니다. 설치 절차는 [사설 클라우드에 대 한 방화벽 테이블 및 규칙 설정](https://docs.azure.cloudsimple.com/firewall)을 참조 하세요.

**사설 클라우드 환경에서 Vm에 대 한 공용 IP 주소를 할당할 수 있나요?**

CloudSimple 포털에서 쉽게 새 공용 IP 주소를 할당 하 고 가상 머신 또는 어플라이언스의 개인 IP 주소와 연결할 수 있습니다. 또한 포털에서 새 방화벽 규칙을 만들거나 기존 방화벽 규칙을 적용 하 여 특정 포트 및 특정 IP 주소 집합의 트래픽을 허용할 수 있습니다. 설치 절차는 [사설 클라우드 환경에 대 한 공용 IP 주소 할당](https://docs.azure.cloudsimple.com/public-ips)을 참조 하세요.

## <a name="security"></a>보안

**CloudSimple의 내 보안 옵션은 무엇 인가요?**

CloudSimple 사설 클라우드는 사설 클라우드 환경을 보호 하기 위해 다음과 같은 보안 기능을 제공 합니다.

- **미사용 데이터 암호화:** 사설 클라우드의 vSAN 저장소에 있는 미사용 데이터를 암호화할 수 있습니다. vSAN은 Azure virtual network 또는 온-프레미스 환경에 배포할 수 있는 외부 키 관리 서버를 지원 합니다. 자세한 내용은 [CloudSimple 사설 클라우드에 대 한 vSAN 암호화 구성](https://docs.azure.cloudsimple.com/vsan-encryption)을 참조 하세요.
- **네트워크 보안:** 방화벽 규칙을 사용 하 여 인터넷, 온-프레미스 및 사설 클라우드의 서브넷 내에서 사설 클라우드로의 네트워크 트래픽 흐름을 제어 합니다.
- **보안, 개인 연결:** 온-프레미스 네트워크와 Azure 구독 간의 안전 하 고 개인 연결입니다.

## <a name="compute"></a>컴퓨팅

**어떤 종류의 호스트를 사용할 수 있나요?**

CloudSimple은 다음 두 가지 호스트 유형을 제공 합니다.

* **CS28 노드**: CPU: 2, 2.2 g h z, 총 28 코어, 48 HT. RAM: 256 GB 스토리지: 1600-GB NVMe 캐시, 5760-GB 데이터 (전체-플래시). 네트워크: 2x25Gbe NIC.
* **CS36 노드**: CPU 2x 2.3 g h z, 총 36 코어, 72 HT. RAM: 512 GB 스토리지: 3200-GB NVMe Cache 11520-GB 데이터 (모두-플래시). 네트워크: 2x25Gbe NIC.

**하드웨어 오류는 어떻게 처리 되나요?**

모든 CloudSimple 인프라는 CloudSimple 플랫폼과 해당 서비스 운영 팀에서 지속적으로 모니터링 합니다. 하드웨어 오류가 검색 되 면 사설 클라우드에 새 노드가 추가 됩니다. 사설 클라우드의 고가용성을 보장 하기 위해 실패 한 노드가 제거 됩니다.

## <a name="storage"></a>저장 공간

**사설 클라우드에서 지원 되는 저장소 유형은 무엇 인가요?**

CloudSimple **은 모든 사설 클라우드에서 모든 플래시 VMware vSAN 저장소** 를 제공 합니다. 각 vSphere 자체 Vsphere 데이터 저장소를 사용 하 여 생성 됩니다. 자세한 내용은 [사설 클라우드 VMware 구성 요소-vSAN 저장소](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage)를 참조 하세요.

**데이터 암호화가 지원 되나요?**
예. VSAN에 저장 된 데이터를 암호화 하기 위해 온-프레미스 또는 Azure에 배포 된 KMS (키 관리 서버)를 사용 하도록 사설 클라우드에서 vSAN 저장소를 설정할 수 있습니다.

**실패 한 디스크는 어떻게 처리 되나요?**

CloudSimple 모니터링은 사설 클라우드의 모든 하드웨어 구성 요소를 지속적으로 모니터링 합니다. 디스크 오류가 감지 되거나 추론을 기반으로 하는 디스크가 실패 한 것으로 확인 되 면 새 노드가 자동으로 사설 클라우드에 추가 됩니다. 장애 또는 실패 한 디스크를 포함 하는 노드는 사설 클라우드에서 제거 됩니다.

## <a name="vmware"></a>VMware

**온-프레미스에서 응용 프로그램 및 데이터의 대규모 업로드 및 마이그레이션을 수행 하는 어떻게 할까요??**

CloudSimple은 네이티브 VMware vSphere 솔루션을 제공 합니다. 대량 데이터 마이그레이션에 사용 되는 모든 도구는 CloudSimple 사설 클라우드에서 사용할 수 있습니다. 사용할 수 있는 옵션은 다음과 같습니다.

- 데이터를 대량으로 마이그레이션하기 위한 VMware HCX
- 온-프레미스에서 CloudSimple로 저장소 vMotion를 사용 하 여 데이터의 콜드 마이그레이션.

**VMware 도구를 설치할 수 있나요?**

CloudSimple은 네이티브 VMware vSphere 솔루션을 제공 합니다. 온-프레미스에서 vSphere 환경을 관리 하는 데 사용 되는 모든 도구는 CloudSimple에서 사용할 수 있습니다. CloudSimple은 VMware 도구를 설치 하기 위한 BYOL (사용자 라이선스 가져오기) 모델을 지원 합니다.

**업데이트 및 업그레이드는 어떻게 관리 되나요?**

CloudSimple은 원활한 무중단 방식으로 사설 클라우드의 모든 인프라 구성 요소를 관리 하 고 업데이트 합니다. VMware 또는 인프라 공급 업체에서 릴리스된 모든 업데이트 또는 보안 패치는 CloudSimple로 한정 되는 즉시 업데이트 되도록 예약 됩니다.

CloudSimple은 사설 클라우드에 설치 된 응용 프로그램의 업그레이드 또는 업데이트를 수행 하지 않습니다.

## <a name="azure-integration"></a>Azure 통합

**지원 되는 Azure 서비스는 무엇 인가요?**

CloudSimple은 azure에서 구독에 대 한 Azure Express 경로 연결을 제공 합니다. 구독에서 실행 되는 모든 서비스는 사설 클라우드에 네트워크로 연결 되어 있으며 사설 클라우드에 연결할 수 있습니다. 예를 들면 다음과 같습니다.

- **Azure Active Directory**: CloudSimple vCenter의 id 원본으로 Azure Active Directory를 사용 합니다.
- **Azure Storage**: 저장소를 사용 하 여 사설 클라우드의 백업, 이미지 및 기타 데이터를 저장 합니다.
- **하이브리드 응용 프로그램**: 공용 및 사설 클라우드에 걸쳐 있는 응용 프로그램 아키텍처를 만들 수 있습니다. 예를 들어 Azure에서 CloudSimple 사설 클라우드의 응용 프로그램 및 데이터베이스 서버에 액세스 하는 웹 서버를 만들 수 있습니다.
- **Azure Monitor** 및 **Azure Security Center**: VMware에서 실행 되는 작업은 로깅, 성능 메트릭 및 보안 관리를 위해 모니터와 Security Center를 사용할 수 있습니다.

**VMware 테 넌 트를 Azure에 매핑할 어떻게 할까요? 있나요?**

CloudSimple은 Azure Portal에서 사설 클라우드의 VMware Vm을 관리 하는 고유한 기능을 제공 합니다. 원하는 리소스 제약 조건으로 구성 된 vCenter 리소스 풀을 전역 관리자가 구독에 매핑할 수 있습니다. 

**Azure에서 제공 하는 라이선싱 혜택은 무엇 인가요?**

CloudSimple을 사용 하면 Azure 하이브리드 혜택를 활용 하 고 라이선스에 대 한 최대 90%까지 비용을 절감 하 여 Microsoft 라이선스에 대 한 투자를 유지 하 고 다른 클라우드와 비교 하 여 총 소유 비용을 절감할 수 있습니다. Windows Server 2008 및 Microsoft SQL Server 2008에 대 한 확장 된 보안 업데이트도 제공 됩니다. Veeam, Zerto 등의 일반적인 앱에 대 한 클라우드는 BYOL을 사용 하 여 비용을 낮게 유지 하세요. 
