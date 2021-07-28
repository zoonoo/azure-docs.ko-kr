---
title: 프라이빗 클라우드 VMware 구성 요소
titleSuffix: Azure VMware Solution by CloudSimple
description: CloudSimple 서비스를 사용하여 Azure 위치에 VMware를 기본적으로 배포하는 방법을 알아봅니다. 프라이빗 클라우드는 Azure Cloud의 나머지 서비스와 통합되었습니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88142505"
---
# <a name="private-cloud-vmware-components"></a>프라이빗 클라우드 VMware 구성 요소

프라이빗 클라우드는 관리 도메인에서 vCenter Server가 관리하는 격리된 VMware 스택(ESXi 호스트, vCenter, vSAN, NSX) 환경입니다.  CloudSimple 서비스를 사용하면 Azure 위치의 Azure 완전 메탈 인프라에 VMware를 기본적으로 배포할 수 있습니다.  프라이빗 클라우드는 Azure Cloud의 나머지 서비스와 통합되었습니다.  프라이빗 클라우드는 다음 VMware 스택 구성 요소와 함께 배포됩니다.

* **VMware ESXi** - Azure 전용 노드의 하이퍼바이저
* **VMware vCenter** - 프라이빗 클라우드 vSphere 환경을 중앙에서 관리하기 위한 어플라이언스
* **VMware vSAN** - 하이퍼 컨버지드 인프라 솔루션
* **VMware NSX Data Center** - 네트워크 가상화 및 보안 소프트웨어  

## <a name="vmware-component-versions"></a>VMware 구성 요소 버전

프라이빗 클라우드 VMware 스택은 다음 소프트웨어 버전과 함께 배포됩니다.

| 구성 요소 | 버전 | 라이선스 버전 |
|-----------|---------|------------------|
| ESXi | 6.7U2 | Enterprise Plus |
| vCenter | 6.7U2 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.4.1 | 고급 |

## <a name="esxi"></a>ESXi

VMware ESXi는 프라이빗 클라우드를 만들 때 프로비저닝된 CloudSimple 노드에 설치됩니다.  ESXi는 워크로드 VM(가상 머신)을 배포하기 위한 하이퍼바이저를 제공합니다.  노드는 프라이빗 클라우드에서 하이퍼 컨버지드 인프라(컴퓨팅 및 스토리지)를 제공합니다.  노드는 프라이빗 클라우드의 vSphere 클러스터에 포함됩니다.  각 노드에는 언더레이 네트워크에 연결된 물리적 네트워크 인터페이스 4개가 있습니다.  물리적 네트워크 인터페이스 2개는 vCenter에서 **VDS(vSphere 분산 스위치)** 를 만드는 데 사용되고, 나머지 2개는 **N-VDS(NSX 관리형 가상 분산 스위치)** 를 만드는 데 사용됩니다.  네트워크 인터페이스는 고가용성을 위해 활성-활성 모드로 구성됩니다.

VMware ESXi에 대한 자세한 정보

## <a name="vcenter-server-appliance"></a>vCenter 서버 어플라이언스

VCSA(vCenter 서버 어플라이언스)는 VMware Solution by CloudSimple에 대한 인증, 관리, 오케스트레이션 기능을 제공합니다. PSC(Platform Services Controller)가 포함된 VCSA는 프라이빗 클라우드를 만들 때 배포됩니다.  VCSA는 프라이빗 클라우드를 배포할 때 생성된 vSphere 클러스터에 배포됩니다.  각 프라이빗 클라우드에 자체 VCSA가 있습니다.  프라이빗 클라우드를 확장하면 프라이빗 클라우드의 VCSA에 노드가 추가됩니다.

### <a name="vcenter-single-sign-on"></a>vCenter Single Sign-On

VCSA에 포함된 Platform Services Controller는 **vCenter Single Sign-On 도메인** 과 연결됩니다.  도메인 이름은 **cloudsimple.local** 입니다.  vCenter에 액세스할 수 있도록 기본 사용자 **CloudOwner@cloudsimple.com** 이 자동으로 생성됩니다.  온-프레미스/Azure Active Directory [vCenter ID 원본](set-vcenter-identity.md)을 추가할 수 있습니다.

## <a name="vsan-storage"></a>vSAN 스토리지

프라이빗 클라우드는 클러스터에 로컬인, 완전히 구성된 올 플래시 vSAN 스토리지를 사용하여 생성됩니다.  vSAN 데이터 저장소를 사용하여 vSphere 클러스터를 만들려면 동일한 SKU의 노드가 3개 이상 필요합니다.  중복 제거 및 압축은 vSAN 데이터 저장소에서 기본적으로 사용할 수 있습니다.  vSphere 클러스터의 각 노드에 두 개의 디스크 그룹이 생성됩니다. 각 디스크 그룹에는 캐시 디스크 1개와 용량 디스크 3개가 포함됩니다.

기본 vSAN 스토리지 정책은 vSphere 클러스터에 생성되고 vSAN 데이터 저장소에 적용됩니다.  이 정책은 필요한 서비스 수준을 보장하기 위해 데이터 저장소 내에서 VM 스토리지 개체가 프로비저닝 및 할당되는 방식을 결정합니다.  스토리지 정책은 **FTT(허용 오류)** 및 **오류 허용 방법** 을 정의합니다.  새 스토리지 정책을 만들어 VM에 적용할 수 있습니다. SLA를 유지 관리하려면 vSAN 데이터 저장소에서 25% 예비 용량을 유지해야 합니다.  

### <a name="default-vsan-storage-policy"></a>기본 vSAN 스토리지 정책

아래 표에는 기본 vSAN 스토리지 정책 매개 변수가 나와 있습니다.

| vSphere 클러스터에 있는 노드 수 | FTT | 오류 허용 방법 |
|------------------------------------|-----|--------------------------|
| 노드 3개와 4개 | 1 | RAID 1(미러링) - 복사본 2개 생성 |
| 노드 5~16개 | 2 | RAID 1(미러링) - 복사본 3개 생성 |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center는 프라이빗 클라우드에서 네트워크 가상화, 마이크로 구분, 네트워크 보안 기능을 제공합니다.  NSX를 통해 프라이빗 클라우드에서 NSX Data Center가 지원하는 모든 서비스를 구성할 수 있습니다.  프라이빗 클라우드를 만들 때 다음 NSX 구성 요소가 설치되고 구성됩니다.

* NSXT 관리자
* 전송 영역
* 호스트 및 에지 업링크 프로필
* 에지 전송, Ext1, Ext2용 논리 스위치
* ESXi 전송 노드의 IP 풀
* 에지 전송 노드의 IP 풀
* 에지 노드
* 컨트롤러 및 에지 VM에 대한 DRS 선호도 방지 규칙
* 계층 0 라우터
* 계층 0 라우터에서 BGP 사용

## <a name="vsphere-cluster"></a>vSphere 클러스터

ESXi 호스트는 프라이빗 클라우드의 고가용성을 보장하기 위해 클러스터로 구성됩니다.  프라이빗 클라우드를 만들 때 vSphere의 관리 구성 요소가 첫 번째 클러스터에 배포됩니다.  관리 구성 요소에 대한 리소스 풀이 생성되고, 이 리소스 풀에 모든 관리 VM이 배포됩니다. 첫 번째 클러스터는 프라이빗 클라우드를 축소하기 위해 삭제할 수 없습니다.  vSphere 클러스터는 **vSphere HA** 를 사용하여 VM에 대한 고가용성을 제공합니다.  허용 오류는 클러스터에서 사용 가능한 노드 수를 기반으로 합니다.  ```Number of nodes = 2N+1``` 수식을 사용할 수 있습니다. 여기서 ```N```은 허용할 오류 수입니다.

### <a name="vsphere-cluster-limits"></a>vSphere 클러스터 제한

| 리소스 | 제한 |
|----------|-------|
| 프라이빗 클라우드(첫 번째 vSphere 클러스터)를 만들기 위한 최소 노드 수 | 3 |
| 프라이빗 클라우드의 vSphere 클러스터에 있는 최대 노드 수 | 16 |
| 프라이빗 클라우드에 있는 최대 노드 수 | 64 |
| 프라이빗 클라우드에 있는 최대 vSphere 클러스터 수 | 21 |
| 새 vSphere 클러스터에 있는 최소 노드 수 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 인프라 유지 관리

VMware 인프라 구성을 변경해야 하는 경우가 있습니다. 현재 이 간격은 1~2개월마다 발생할 수 있지만 시간이 지남에 따라 빈도가 감소할 것으로 예상됩니다. 이 유형의 유지 관리는 일반적으로 CloudSimple 서비스의 정상적인 사용을 중단하지 않고 수행할 수 있습니다. VMware 유지 관리 간격 동안 다음 서비스는 영향을 받지 않고 계속 작동합니다.

* VMware 관리 평면 및 애플리케이션
* vCenter 액세스
* 모든 네트워킹 및 스토리지
* 모든 Azure 트래픽

## <a name="updates-and-upgrades"></a>업데이트 및 업그레이드

CloudSimple은 프라이빗 클라우드에서 VMware 소프트웨어(ESXi, vCenter, PSC, NSX)의 수명 주기 관리를 담당합니다.

소프트웨어 업데이트에는 다음이 포함됩니다.

* **패치**. VMware에서 릴리스한 보안 패치 또는 버그 수정입니다.
* **업데이트**. VMware 스택 구성 요소의 부 버전 변경입니다.
* **업그레이드**. VMware 스택 구성 요소의 주 버전 변경입니다.

CloudSimple은 VMware에서 제공하는 즉시 중요 보안 패치를 테스트합니다. SLA에 따라 CloudSimple은 1주일 이내에 프라이빗 클라우드 환경에 보안 패치를 배포합니다.

CloudSimple은 VMware 소프트웨어 구성 요소에 분기별 유지 관리 업데이트를 제공합니다. VMware 소프트웨어의 새로운 주 버전이 제공되면 CloudSimple은 고객과 협력하여 업그레이드에 적합한 유지 관리 기간을 조정합니다.  

## <a name="next-steps"></a>다음 단계

* [CloudSimple 유지 관리 및 업데이트](cloudsimple-maintenance-updates.md)
