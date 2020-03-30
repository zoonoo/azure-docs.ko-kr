---
title: 프라이빗 클라우드 VMware 구성 요소
titleSuffix: Azure VMware Solution by CloudSimple
description: 프라이빗 클라우드에 VMware 구성 요소를 설치하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279508"
---
# <a name="private-cloud-vmware-components"></a>프라이빗 클라우드 VMware 구성 요소

프라이빗 클라우드는 관리 도메인의 vCenter 서버에서 관리하는 격리된 VMware 스택(ESXi 호스트, vCenter, vSAN 및 NSX) 환경입니다.  CloudSimple 서비스를 사용하면 Azure 위치의 Azure 베어 메탈 인프라에 기본적으로 VMware를 배포할 수 있습니다.  프라이빗 클라우드는 나머지 Azure 클라우드와 통합됩니다.  프라이빗 클라우드는 다음과 같은 VMware 스택 구성 요소와 함께 배포됩니다.

* **VMware ESXi -** Azure 전용 노드의 하이퍼바이저
* **VM웨어 v센터 -** 프라이빗 클라우드 vSphere 환경의 중앙 집중식 관리를 위한 어플라이언스
* **VMware vSAN -** 하이퍼 컨버지드 인프라 솔루션
* **VMware NSX 데이터 센터 -** 네트워크 가상화 및 보안 소프트웨어  

## <a name="vmware-component-versions"></a>VMware 구성 요소 버전

프라이빗 클라우드 VMware 스택은 다음 소프트웨어 버전으로 배포됩니다.

| 구성 요소 | 버전 | 라이센스 버전 |
|-----------|---------|------------------|
| Esxi | 6.7U2 | 엔터프라이즈 플러스 |
| vCenter | 6.7U2 | v센터 스탠다드 |
| vSAN | 6.7 | Enterprise |
| NSX 데이터 센터 | 2.4.1 | 고급 |

## <a name="esxi"></a>Esxi

VMware ESXi는 프라이빗 클라우드를 만들 때 프로비저닝된 CloudSimple 노드에 설치됩니다.  ESXi는 워크로드 가상 시스템(VM)을 배포하기 위한 하이퍼바이저를 제공합니다.  노드는 프라이빗 클라우드에서 하이퍼 컨버지드 인프라(컴퓨팅 및 스토리지)를 제공합니다.  노드는 프라이빗 클라우드의 vSphere 클러스터의 일부입니다.  각 노드에는 언더레이 네트워크에 연결된 4개의 물리적 네트워크 인터페이스가 있습니다.  두 개의 물리적 네트워크 인터페이스는 vCenter에서 **vSphere 분산 스위치(VDS)를** 만드는 데 사용되며 두 인터페이스는 **NSX 관리 가상 분산 스위치(N-VDS)를**만드는 데 사용됩니다.  네트워크 인터페이스는 고가용성을 위해 활성 모드로 구성됩니다.

VMware ESXi에 대해 자세히 알아보기

## <a name="vcenter-server-appliance"></a>v센터 서버 어플라이언스

vCenter 서버 어플라이언스(VCSA)는 CloudSimple에 의한 VMware 솔루션에 대한 인증, 관리 및 오케스트레이션 기능을 제공합니다. PSC(임베디드 플랫폼 서비스 컨트롤러)가 있는 VCSA는 프라이빗 클라우드를 만들 때 배포됩니다.  VCSA는 프라이빗 클라우드를 배포할 때 생성되는 vSphere 클러스터에 배포됩니다.  각 프라이빗 클라우드에는 자체 VCSA가 있습니다.  프라이빗 클라우드의 확장은 프라이빗 클라우드의 VCSA에 노드를 추가합니다.

### <a name="vcenter-single-sign-on"></a>v센터 단일 사인온

VCSA의 임베디드 플랫폼 서비스 컨트롤러는 **vCenter 단일 사인온 도메인과**연결됩니다.  도메인 이름은 **cloudsimple.local**입니다.  기본 사용자가 **CloudOwner@cloudsimple.com** vCenter에 액세스할 수 있도록 만들어집니다.  vCenter 에 대한 온-프레미스/Azure 활성 디렉터리 [ID 원본을](set-vcenter-identity.md)추가할 수 있습니다.

## <a name="vsan-storage"></a>vSAN 스토리지

프라이빗 클라우드는 클러스터에 로컬로 완전히 구성된 올플래시 vSAN 스토리지로 만들어집니다.  vSAN 데이터스토어가 있는 vSphere 클러스터를 만들려면 동일한 SKU의 최소 3개의 노드가 필요합니다.  기본적으로 vSAN 데이터스토어에서 중복 해제 및 압축이 활성화됩니다.  vSphere 클러스터의 각 노드에 두 개의 디스크 그룹이 만들어집니다. 각 디스크 그룹에는 하나의 캐시 디스크와 세 개의 용량 디스크가 포함되어 있습니다.

기본 vSAN 저장소 정책은 vSphere 클러스터에서 만들어지고 vSAN 데이터 스토어에 적용됩니다.  이 정책은 필요한 서비스 수준을 보장하기 위해 데이터스토어 내에서 VM 저장소 개체를 프로비전하고 할당하는 방법을 결정합니다.  저장소 정책은 **FTT(허용 실패)** 및 실패 **허용 오차 방법을**정의합니다.  새 저장소 정책을 만들고 VM에 적용할 수 있습니다. SLA를 유지 관리하려면 vSAN 데이터스토어에서 25%의 예비 용량을 유지해야 합니다.  

### <a name="default-vsan-storage-policy"></a>기본 vSAN 저장소 정책

아래 표에는 기본 vSAN 저장소 정책 매개 변수가 표시됩니다.

| vSphere 클러스터의 노드 수 | Ftt | 고장 허용 오차 방법 |
|------------------------------------|-----|--------------------------|
| 3개 및 4개 노드 | 1 | RAID 1(미러링) - 2부 생성 |
| 5 ~16개의 노드 | 2 | RAID 1(미러링) - 3개 사본 생성 |

## <a name="nsx-data-center"></a>NSX 데이터 센터

NSX 데이터 센터는 프라이빗 클라우드에서 네트워크 가상화, 마이크로 세분화 및 네트워크 보안 기능을 제공합니다.  NSX를 통해 프라이빗 클라우드에서 NSX 데이터 센터에서 지원하는 모든 서비스를 구성할 수 있습니다.  프라이빗 클라우드를 만들면 다음 NSX 구성 요소가 설치되고 구성됩니다.

* NSXT 매니저
* 운송 구역
* 호스트 및 에지 업링크 프로필
* 에지 전송, 내선 1 및 내선 2를 위한 논리 스위치
* ESXi 전송 노드용 IP 풀
* 에지 전송 노드에 대한 IP 풀
* 모서리 노드
* 컨트롤러 및 에지 VM에 대한 DRS 선호도 방지 규칙
* 계층 0 라우터
* Tier0 라우터에서 BGP 사용

## <a name="vsphere-cluster"></a>v스피어 클러스터

ESXi 호스트는 프라이빗 클라우드의 고가용성을 보장하기 위해 클러스터로 구성됩니다.  프라이빗 클라우드를 만들면 vSphere의 관리 구성 요소가 첫 번째 클러스터에 배포됩니다.  관리 구성 요소에 대해 리소스 풀이 만들어지고 모든 관리 VM이 이 리소스 풀에 배포됩니다. 프라이빗 클라우드를 축소하기 위해 첫 번째 클러스터를 삭제할 수 없습니다.  vSphere 클러스터는 **vSphere HA를**사용하여 VM에 대한 고가용성을 제공합니다.  허용되지 않는 오류는 클러스터에서 사용 가능한 노드 수를 기반으로 합니다.  허용할 실패 ```Number of nodes = 2N+1``` ```N``` 횟수인 수식을 사용할 수 있습니다.

### <a name="vsphere-cluster-limits"></a>v스피어 클러스터 제한

| 리소스 | 제한 |
|----------|-------|
| 프라이빗 클라우드를 만들 수 있는 최소 노드 수(첫 번째 vSphere 클러스터) | 3 |
| 프라이빗 클라우드의 vSphere 클러스터의 최대 노드 수 | 16 |
| 프라이빗 클라우드의 최대 노드 수 | 64 |
| 프라이빗 클라우드의 최대 vSphere 클러스터 수 | 21 |
| 새 vSphere 클러스터의 최소 노드 수 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 인프라 유지 관리

VMware 인프라의 구성을 변경해야 하는 경우가 있습니다. 현재 이러한 간격은 1-2개월마다 발생할 수 있지만 시간이 지남에 따라 빈도가 감소할 것으로 예상됩니다. 이러한 유형의 유지 관리는 일반적으로 CloudSimple 서비스의 정상적인 소비를 중단하지 않고 수행할 수 있습니다. VMware 유지 관리 간격 동안 다음과 같은 서비스는 아무런 영향 없이 계속 작동합니다.

* VMware 관리 평면 및 응용 프로그램
* v센터 액세스
* 모든 네트워킹 및 스토리지
* 모든 Azure 트래픽

## <a name="updates-and-upgrades"></a>업데이트 및 업그레이드

CloudSimple은 프라이빗 클라우드에서 VMware 소프트웨어(ESXi, vCenter, PSC 및 NSX)의 수명 주기 관리를 담당합니다.

소프트웨어 업데이트에는 다음이 포함됩니다.

* **패치**. VMware에서 릴리스한 보안 패치 또는 버그 수정.
* **업데이트**. VMware 스택 구성 요소의 부 버전 변경.
* **업그레이드 .** VMware 스택 구성 요소의 주요 버전 변경.

CloudSimple은 VMware에서 사용할 수 있게 되는 즉시 중요한 보안 패치를 테스트합니다. SLA에 따라 CloudSimple은 1주일 이내에 사설 클라우드 환경에 보안 패치를 제공합니다.

CloudSimple은 VMware 소프트웨어 구성 요소에 분기별 유지 관리 업데이트를 제공합니다. 새로운 주요 버전의 VMware 소프트웨어를 사용할 수 있는 경우 CloudSimple은 고객과 협력하여 업그레이드에 적합한 유지 관리 기간을 조정합니다.  

## <a name="next-steps"></a>다음 단계

* [클라우드 단순 유지 관리 및 업데이트](cloudsimple-maintenance-updates.md)
