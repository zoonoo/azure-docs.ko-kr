---
title: CloudSimple의 Azure VMware 솔루션-CloudSimple 사설 클라우드 만들기
description: 운영 유연성과 연속성을 사용 하 여 클라우드로 VMware 워크 로드를 확장 하는 CloudSimple 사설 클라우드를 만드는 방법에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024792"
---
# <a name="create-a-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드 만들기

사설 클라우드는 ESXi 호스트, vCenter, vSAN 및 NSX를 지 원하는 격리 된 VMware 스택입니다. 사설 클라우드는 CloudSimple 포털을 통해 관리 됩니다. 자체 관리 도메인에 자체 vCenter 서버가 있습니다. 스택은 전용 노드 및 isolated 운영 체제 미 설치 하드웨어 노드에서 실행 됩니다.

사설 클라우드를 만들면 네트워크 인프라의 다양 한 일반적인 요구 사항을 해결할 수 있습니다.

* **증가**. 기존 인프라에 대 한 하드웨어 새로 고침 지점에 도달 하면 사설 클라우드를 통해 새 하드웨어 투자 없이 확장할 수 있습니다.

* **빠른 확장**. 임시 또는 계획 되지 않은 용량이 필요한 경우 사설 클라우드를 사용 하면 지연 없이 추가 용량을 만들 수 있습니다.

* **보호 기능이 향상**되었습니다. 3 개 이상의 노드의 사설 클라우드를 사용 하 여 자동 중복 및 고가용성 보호를 사용할 수 있습니다.

* **장기적인 인프라 요구 사항**. 데이터 센터가 용량에 있거나 비용을 절감 하기 위해 구조를 변경 하려는 경우에는 사설 클라우드를 사용 하 여 데이터 센터의 사용을 중지 하 고, 엔터프라이즈 작업과의 호환성을 유지 하면서 클라우드 기반 솔루션으로 마이그레이션할 수 있습니다.

사설 클라우드를 만들 때 단일 vSphere 클러스터와 해당 클러스터에 생성 된 모든 관리 Vm을 가져옵니다.

## <a name="before-you-begin"></a>시작하기 전에

사설 클라우드를 만들기 전에 노드를 프로 비전 해야 합니다. 프로 비전 노드에 대 한 자세한 내용은 [CloudSimple을 사용한 Azure VMware 솔루션에 대 한 노드 프로 비전](create-nodes.md)을 참조 하세요.

사설 클라우드에 대 한 vSphere/Vsphere 서브넷의 CIDR 범위를 할당 합니다. 사설 클라우드는 vCenter 서버에서 관리 하는 isolated VMware stack 환경 (ESXi 호스트, vCenter, vSAN 및 NSX 포함)으로 만들어집니다. 관리 구성 요소는 vSphere/Vsphere 서브넷 CIDR에 대해 선택 된 네트워크에 배포 됩니다. 네트워크 CIDR 범위는 배포 하는 동안 다른 서브넷으로 나뉩니다. VSphere/Vsphere 서브넷 주소 공간은 고유 해야 합니다. CloudSimple 환경과 통신 하는 네트워크와 겹치지 않아야 합니다. CloudSimple과 통신 하는 네트워크에는 온-프레미스 네트워크 및 Azure virtual network가 포함 됩니다. VSphere/Vsphere 서브넷에 대 한 자세한 내용은 Vlan 및 서브넷 개요를 참조 하세요.

* 최소 vSphere/Vsphere 서브넷 CIDR 범위 접두사:/24
* 최대 vSphere/Vsphere 서브넷 CIDR 범위 접두사:/21


## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

[CloudSimple 포털](access-cloudsimple-portal.md)에 액세스합니다.

## <a name="create-a-new-private-cloud"></a>새 사설 클라우드 만들기

1. **모든 서비스**를 선택합니다.
2. **Cloudsimple 서비스**를 검색 합니다.
3. 사설 클라우드를 만들려는 CloudSimple 서비스를 선택 합니다.
4. **개요**에서 **사설 클라우드 만들기** 를 클릭 하 여 cloudsimple 포털에 대 한 새 브라우저 탭을 엽니다. 메시지가 표시 되 면 Azure 로그인 자격 증명을 사용 하 여 로그인 합니다.

    ![Azure에서 사설 클라우드 만들기](media/create-private-cloud-from-azure.png)

5. CloudSimple 포털에서 사설 클라우드의 이름을 제공 합니다.
6. 사설 클라우드의 **위치** 를 선택 합니다.
7. Azure에서 프로 비전 한 것과 일치 하는 **노드 유형**을 선택 합니다.
8. **노드 수**를 지정 합니다.  사설 클라우드를 만들려면 노드가 세 개 이상 필요 합니다.

    ![사설 클라우드 만들기-기본 정보](media/create-private-cloud-basic-info.png)

9. **다음: 고급 옵션**을 클릭 합니다.
10. VSphere/Vsphere 서브넷의 CIDR 범위를 입력 합니다. CIDR 범위가 온-프레미스 또는 기타 Azure 서브넷 (가상 네트워크) 또는 게이트웨이 서브넷과 겹치지 않는지 확인 합니다.

    **CIDR 범위 옵션:** /24,/23,/22 또는/21. /24 CIDR 범위는 최대 9 개의 노드를 지원 하 고/23 CIDR 범위는 최대 41 노드를 지원 하며,/22 및/21 CIDR 범위는 최대 64 노드 (사설 클라우드의 최대 노드 수)를 지원 합니다.

    > [!IMPORTANT]
    > VSphere/Vsphere CIDR 범위의 IP 주소는 사설 클라우드 인프라에서 사용 하도록 예약 되어 있습니다.  가상 머신에서는이 범위의 IP 주소를 사용 하지 마세요.

11. **다음: 검토 및 만들기**를 클릭 합니다.
12. 설정을 검토 합니다. 설정을 변경 해야 하는 경우에는 **이전**을 클릭 합니다.
13. **만들기**를 클릭합니다.

사설 클라우드 프로 비전 프로세스가 시작 됩니다. 사설 클라우드를 프로 비전 하는 데 최대 2 시간이 걸릴 수 있습니다.

기존 사설 클라우드를 확장 하는 방법에 대 한 지침은 [사설 클라우드 확장](expand-private-cloud.md)을 참조 하세요.
