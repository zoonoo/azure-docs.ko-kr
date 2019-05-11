---
title: CloudSimple-사설 클라우드 VMware 구성 요소에서 azure의 VMware 솔루션
description: 사설 클라우드에서 VMware 구성 요소는 설치 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e6548a5a04e32b374a8a9c29d2ca5f89fd65c78
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160187"
---
# <a name="private-cloud-vmware-components"></a>사설 클라우드 VMware 구성 요소

사설 클라우드는 격리 된 VMware 스택 (ESXi 호스트, vCenter, vSAN을 및 NSX) 환경 관리에 vCenter 서버에서 관리 합니다.  CloudSimple 서비스를 사용 하면 Azure 위치에서 운영 체제 미 설치 인프라를 Azure에 VMware를 고유 하 게 배포할 수 있습니다.  사설 클라우드는 Azure 클라우드 rest와 통합 됩니다.  사설 클라우드는 다음과 같은 VMware 스택 구성 요소를 사용 하 여 배포 됩니다.

* **VMware ESXi-** 하이퍼바이저 Azure에서 전용 노드
* **VMware vCenter-** 사설 클라우드 vSphere 환경의 중앙 집중식된 관리에 대 한 어플라이언스
* **VMware의 vSAN-** 인프라 하이퍼 수렴 형 솔루션
* **VMware NSX 데이터 센터-** 네트워크 가상화 및 보안 소프트웨어  

## <a name="vmware-component-versions"></a>VMware 구성 요소 버전

사설 클라우드 VMware 스택은 다음 소프트웨어 버전을 사용 하 여 배포 됩니다.

| 구성 요소 | Version | 사용이 허가 된 버전 |
|-----------|---------|------------------|
| ESXi | 6.7U1 | 엔터프라이즈 및 |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX 데이터 센터 | 2.3 | 고급 |

## <a name="esxi"></a>ESXi

VMware ESXi는 사설 클라우드를 만들 때 구매한 CloudSimple 노드에 설치 됩니다.  ESXi는 virtual machines (Vm) 워크 로드 배포에 대 한 하이퍼바이저를 제공 합니다.  노드는 사설 클라우드에서 하이퍼 수렴 형 인프라 (계산 및 저장소)를 제공합니다.  노드는 사설 클라우드에서 vSphere 클러스터의 일부가 됩니다.  각 노드에 4 개의 실제 네트워크 인터페이스 언더레이 네트워크에 연결 합니다.  두 개의 실제 네트워크 인터페이스를 만드는 데 사용 되는 **vSphere 분산 스위치 (VDS)** vCenter 두에 만드는 데 사용 되는 **NSX 관리 가상 분산된 스위치 (N-VDS)** 합니다.  네트워크 인터페이스는 고가용성을 위해 활성-활성 모드에서 구성 됩니다.

VMware ESXi에 자세히 알아보려면

## <a name="vcenter-server-appliance"></a>vCenter server 어플라이언스

vCenter server 어플라이언스 (VCSA) CloudSimple 하 여 VMware 솔루션에 대 한 인증, 관리 및 오케스트레이션 기능을 제공합니다. 사설 클라우드를 만들 때 VCSA 포함 된 플랫폼 서비스 컨트롤러 (PSC)을 사용 하 여 배포 됩니다.  VCSA 사설 클라우드를 배포할 때 생성 되는 vSphere 클러스터에 배포 됩니다.  각 사설 클라우드에 고유한 VCSA에 있습니다.  사설 클라우드에 VCSA에 노드를 추가 하는 사설 클라우드를 확장 합니다.

### <a name="vcenter-single-sign-on"></a>vCenter에서 single sign-on

VCSA에 포함 된 플랫폼 서비스 컨트롤러와 연결 된를 **vCenter에서 Single Sign-on 도메인**합니다.  도메인 이름은 **cloudsimple.local**합니다.  기본 사용자 **CloudOwner@cloudsimple.com** vCenter 액세스 하기 위한 만들어집니다.  온-프레미스/Azure active directory를 추가할 수 있습니다 [vCenter에 대 한 id 원본](https://docs.azure.cloudsimple.com/set-vcenter-identity/)합니다.

## <a name="vsan-storage"></a>vSAN 저장소

사설 클라우드는 완전히 구성 된 모든 플래시 vSAN storage를 로컬 클러스터에 생성 됩니다.  동일한 SKU의 최소 세 개의 노드 vSAN 데이터 저장소를 사용 하 여 vSphere 클러스터를 만드는 데 필요한 합니다.  중복 제거 및 압축은 기본적으로 vSAN 데이터 저장소에서 활성화 됩니다.  VSphere 클러스터의 각 노드에 두 개의 디스크 그룹이 만들어집니다. 각 디스크 그룹 하나 캐시 디스크 및 용량 디스크가 3 개를 포함합니다.

기본 vSAN 저장소 정책 vSphere 클러스터에 생성 및 vSAN 데이터 저장소에 적용 됩니다.  이 정책은 VM 저장소 개체 프로 비전 되 고 필요한 서비스 수준을 보장 하기 위해 데이터 저장소 내에 할당 되는 방식을 결정 합니다.  저장소 정책 정의 **(FTT)를 허용할 오류** 하며 **Failure 허용 오차 메서드**.  새 저장소 정책을 만들고 Vm에 적용할 수 있습니다. SLA를 유지 하려면 예비 용량을 25 %vSAN 데이터 저장소에 유지 되어야 합니다.  

### <a name="default-vsan-storage-policy"></a>기본 vSAN 저장소 정책

아래 표에서 기본 vSAN 저장소 정책 매개 변수를 보여 줍니다.

| VSphere 클러스터의에서 노드 수 | FTT | 오류 허용 오차 메서드 |
|------------------------------------|-----|--------------------------|
| 3 및 4 노드 | 1 | RAID (미러링)-1은 2 복사본을 생성 합니다. |
| 5 ~ 16 노드 | 2 | RAID (미러링)-1에 3 개의 복사본을 만듭니다. |

## <a name="nsx-data-center"></a>NSX 데이터 센터

NSX 데이터 센터는 사설 클라우드에서 네트워크 가상화, 마이크로 구분 및 네트워크 보안 기능을 제공합니다.  NSX NSX 통해 사설 클라우드 데이터 센터를 지 원하는 모든 서비스를 구성할 수 있습니다.  사설 클라우드를 만들 때 다음 NSX 구성 요소가 설치 되어 구성 됩니다.

* NSXT 관리자
* 전송 영역
* 호스트 및 Edge 업링크 프로필
* Edge 전송, Ext1, Ext2를 논리 스위치
* ESXi 전송 노드에 대 한 IP 풀
* Edge 전송 노드에 대 한 IP 풀
* 에 지 노드
* 컨트롤러 및에 지 Vm에 대 한 선호도 방지 DRS 규칙
* 계층 0 라우터
* Tier0 라우터에서 BGP를 사용 하도록 설정

## <a name="vsphere-cluster"></a>vSphere 클러스터

ESXi 호스트는 사설 클라우드의 고가용성을 보장 하는 클러스터로 구성 됩니다.  사설 클라우드를 만들 때 vSphere의 관리 구성 요소는 첫 번째 클러스터에 배포 됩니다.  리소스 풀 관리 구성 요소에 대해 만들어지고 모든 관리 Vm이 리소스 풀에 배포 됩니다. 사설 클라우드를 축소 하기 위해 첫 번째 클러스터를 삭제할 수 없습니다.  vSphere 클러스터 고가용성 Vm에 대 한 사용 하 여 제공 **vSphere HA**합니다.  오류를 허용할 수 있도록 클러스터의 사용 가능한 노드 수를 기반으로 합니다.  수식을 사용할 수 있습니다 ```Number of nodes = 2N+1``` 여기서 ```N``` 허용할 오류 횟수입니다.

### <a name="vsphere-cluster-limits"></a>vSphere 클러스터 제한

| 리소스 | 제한 |
|----------|-------|
| 사설 클라우드를 만드는 노드의 최소 수 (첫 번째 vSphere 클러스터) | 3 |
| VSphere에서 노드의 최대 수는 사설 클라우드에서 클러스터 | 16 |
| 사설 클라우드에서 노드의 최대 수 | 64 |
| 사설 클라우드에서 vSphere 최대 클러스터 | 21 |
| 새 vSphere 클러스터에서 노드의 최소 수 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 인프라 유지 관리

VMware 인프라의 구성을 변경 해야 하는 경우도 있습니다. 현재 이러한 간격에는 1 ~ 2 개월 마다 발생할 수 있지만 빈도 시간이 지남에 따라 거부 해야 합니다. 이 유형의 유지 관리는 일반적으로 일반 CloudSimple 서비스 사용을 중단 하지 않고 수행할 수 있습니다. VMware 유지 관리 간격 중 다음 서비스에는 영향을 주지 않고 함수 계속 합니다.

* VMware 관리 평면 및 응용 프로그램
* vCenter 액세스
* 모든 네트워킹 및 저장소
* 모든 Azure 트래픽

## <a name="updates-and-upgrades"></a>업데이트 및 업그레이드

CloudSimple는 VMware 소프트웨어 (ESXi, vCenter, PSC 및 NSX) 사설 클라우드에서 수명 주기 관리 담당합니다.

소프트웨어 업데이트는 다음과 같습니다.

* **패치**합니다. 보안 패치 또는 VMware에서 릴리스 버그 수정입니다.
* **업데이트**. VMware 스택 구성 요소의 변경 부 버전입니다.
* **업그레이드**합니다. VMware 스택 구성 요소의 주요 버전 변경입니다.

VMware에서 사용할 수는 즉시 CloudSimple 중요 보안 패치를 테스트 합니다. SLA를 당 CloudSimple 켜져 보안 패치를 사설 클라우드 환경에 1 주일 이내입니다.

CloudSimple는 VMware 소프트웨어 구성 요소에 대 한 분기별 유지 관리 업데이트를 제공합니다. VMware 소프트웨어의 새 주요 버전을 사용할 수 있는 경우 업그레이드에 대 한 적절 한 유지 관리 기간을 조정 하기 위해 고객과 CloudSimple 작동 합니다.  

## <a name="next-steps"></a>다음 단계

* [CloudSimple 유지 관리 및 업데이트](cloudsimple-maintenance-updates.md)