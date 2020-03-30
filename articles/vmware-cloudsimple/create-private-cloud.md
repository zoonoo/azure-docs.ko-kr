---
title: 클라우드에 의한 Azure VMware 솔루션 간단함 - 클라우드 단순 프라이빗 클라우드 만들기
description: 운영 유연성과 연속성을 통해 VMware 워크로드를 클라우드로 확장하기 위해 CloudSimple 프라이빗 클라우드를 만드는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024792"
---
# <a name="create-a-cloudsimple-private-cloud"></a>클라우드 심플 프라이빗 클라우드 만들기

프라이빗 클라우드는 ESXi 호스트, vCenter, vSAN 및 NSX를 지원하는 격리된 VMware 스택입니다. 프라이빗 클라우드는 CloudSimple 포털을 통해 관리됩니다. 자체 관리 도메인에 자체 vCenter 서버가 있습니다. 스택은 전용 노드 및 격리된 베어 메탈 하드웨어 노드에서 실행됩니다.

프라이빗 클라우드를 만들면 네트워크 인프라에 대한 다양한 일반적인 요구 사항을 해결할 수 있습니다.

* **성장**. 기존 인프라에 대한 하드웨어 새로 고침 지점에 도달한 경우 Private Cloud를 사용하면 새로운 하드웨어 투자 없이 확장할 수 있습니다.

* **빠른 확장**. 일시적 또는 계획되지 않은 용량이 필요한 경우 프라이빗 클라우드를 사용하면 지연 없이 추가 용량을 만들 수 있습니다.

* **증가 된 보호.** 3개 이상의 노드로 구성된 프라이빗 클라우드를 사용하면 자동 중복성 및 고가용성 보호 기능을 사용할 수 있습니다.

* **장기적인 인프라 요구**사항. 데이터 센터가 용량에 있거나 비용을 절감하기 위해 재구성하려는 경우 Private Cloud를 사용하면 엔터프라이즈 운영과 호환되는 유지하면서 데이터 센터를 폐기하고 클라우드 기반 솔루션으로 마이그레이션할 수 있습니다.

프라이빗 클라우드를 만들면 단일 vSphere 클러스터와 해당 클러스터에서 생성된 모든 관리 VM을 얻게 됩니다.

## <a name="before-you-begin"></a>시작하기 전에

프라이빗 클라우드를 만들려면 먼저 노드를 프로비전해야 합니다. 노드 프로비저닝에 대한 자세한 내용은 [CloudSimple에 의한 Azure VMware 솔루션에 대한 프로비전 노드를](create-nodes.md)참조하십시오.

프라이빗 클라우드에 vSphere/vSAN 서브넷에 CIDR 범위를 할당합니다. 프라이빗 클라우드는 vCenter 서버에서 관리하는 ESXi 호스트, vCenter, vSAN 및 NSX를 사용하는 격리된 VMware 스택 환경으로 만들어집니다. 관리 구성 요소는 vSphere/vSAN 서브넷 CIDR에 대해 선택된 네트워크에 배포됩니다. 네트워크 CIDR 범위는 배포 하는 동안 다른 서브넷으로 분할 됩니다. vSphere/vSAN 서브넷 주소 공간은 고유해야 합니다. CloudSimple 환경과 통신하는 네트워크와 겹치지 않아야 합니다. CloudSimple과 통신하는 네트워크에는 온-프레미스 네트워크 및 Azure 가상 네트워크가 포함됩니다. vSphere/vSAN 서브넷에 대한 자세한 내용은 VLAN 및 서브넷 개요를 참조하십시오.

* 최소 vSphere/vSAN 서브넷 CIDR 범위 접두사: /24
* 최대 vSphere/vSAN 서브넷 CIDR 범위 접두사: /21


## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

[CloudSimple 포털에 액세스합니다.](access-cloudsimple-portal.md)

## <a name="create-a-new-private-cloud"></a>새로운 프라이빗 클라우드 만들기

1. **모든 서비스를**선택합니다.
2. 클라우드 **간단한 서비스를**검색합니다.
3. 프라이빗 클라우드를 만들려는 CloudSimple 서비스를 선택합니다.
4. **개요에서**사설 **클라우드 만들기를** 클릭하여 CloudSimple 포털에 대한 새 브라우저 탭을 엽니다. 메시지가 표시되면 Azure 로그인 자격 증명으로 로그인합니다.

    ![Azure에서 사설 클라우드 만들기](media/create-private-cloud-from-azure.png)

5. CloudSimple 포털에서 프라이빗 클라우드의 이름을 입력합니다.
6. 프라이빗 클라우드의 **위치를** 선택합니다.
7. **Azure에서**프로비전한 것과 일치하는 노드 유형을 선택합니다.
8. **노드 수를 지정합니다.**  프라이빗 클라우드를 만들려면 최소 3개의 노드가 필요합니다.

    ![프라이빗 클라우드 만들기 - 기본 정보](media/create-private-cloud-basic-info.png)

9. **다음을 클릭합니다.**
10. vSphere/vSAN 서브넷의 CIDR 범위를 입력합니다. CIDR 범위가 온-프레미스 또는 다른 Azure 서브넷(가상 네트워크) 또는 게이트웨이 서브넷과 겹치지 않도록 합니다.

    **CIDR 범위 옵션:** /24, /23, /22 또는 /21. /24 CIDR 범위는 최대 9개의 노드를 지원하며, /23 CIDR 범위는 최대 41개의 노드를 지원하며, /22 및 /21 CIDR 범위는 최대 64개의 노드(프라이빗 클라우드의 최대 노드 수)를 지원합니다.

    > [!IMPORTANT]
    > vSphere/vSAN CIDR 범위의 IP 주소는 프라이빗 클라우드 인프라에서 사용하도록 예약되어 있습니다.  가상 컴퓨터에서 이 범위의 IP 주소를 사용하지 마십시오.

11. **다음을 클릭합니다.**
12. 설정을 검토합니다. 설정을 변경해야 하는 경우 이전 을 **클릭합니다.**
13. **만들기**를 클릭합니다.

프라이빗 클라우드 프로비저닝 프로세스가 시작됩니다. 프라이빗 클라우드를 프로비전하는 데 최대 2시간이 걸릴 수 있습니다.

기존 프라이빗 클라우드 확장에 대한 지침은 [프라이빗 클라우드 확장을](expand-private-cloud.md)참조하십시오.
