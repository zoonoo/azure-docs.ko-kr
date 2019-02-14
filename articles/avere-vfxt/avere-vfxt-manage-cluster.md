---
title: Avere vFXT 클러스터 관리 - Azure
description: Avere 클러스터를 관리하는 방법 - 노드 추가 또는 제거, vFXT 클러스터 다시 부팅, 중지 또는 삭제
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: bc91b052d3d69924af9afeb012c0ebb5be01dfbf
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745557"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Avere vFXT 클러스터 관리

클러스터가 만들어지면 클러스터 노드를 추가하거나 클러스터를 중지 또는 다시 부팅해야 합니다. 그리고 프로젝트가 완료되면 클러스터를 영구적으로 중지 및 제거하는 방법을 알아야 합니다. 

클러스터 관리 작업에 따라 Avere 제어판, vfxt.py 명령줄 클러스터 만들기 스크립트 또는 Azure Portal을 사용해야 할 수도 있습니다. 

다음 표에는 각 작업에 사용할 수 있는 도구에 대한 개요가 나와 있습니다. 

| 조치 | Avere 제어판 | vfxt.py  | Azure portal |
| --- | --- | --- | --- |
| 클러스터 노드 추가 | no | 예 | no |
| 클러스터 노드 제거 | 예 | no | no |
| 클러스터 노드 중지 | 예(서비스를 다시 시작하거나 다시 부팅할 수도 있음) | no | 포털에서 노드 VM의 작동을 중지하면 노드 실패로 해석됩니다. |
| 중지된 노드 시작 | no | no | 예 |
| 단일 클러스터 노드 삭제 | no | no | 예 |
| 클러스터 다시 부팅 |  |  |  |
| 안전하게 클러스터 종료 또는 중지 | 예 | 예 | no |
| 클러스터 삭제  | no | 예 | 예, 하지만 데이터 무결성은 보장되지 않습니다. |

각 도구에 대한 자세한 지침은 아래에 나와 있습니다.

## <a name="about-stopped-instances-in-azure"></a>Azure에서 중지된 인스턴스 정보

Azure VM을 종료하거나 중지하면 계산 비용이 발생하지 않지만 저장소 비용은 계속 지불해야 합니다. vFXT 노드 또는 전체 vFXT 클러스터를 종료하고 다시 시작하지 않으려면 Azure Portal을 사용하여 관련 VM을 삭제해야 합니다. 

Azure Portal에서 *중지된* 노드(다시 시작할 수 있음)는 Azure Portal에서 **중지됨** 상태를 표시하고, *삭제된* 노드는 **중지됨(할당 취소됨)** 상태를 표시하며, 더 이상 계산 또는 저장소 요금이 발생되지 않습니다.

VM을 삭제하기 전에 Avere 제어판 또는 vfxt.py 옵션을 통해 클러스터를 중지하거나 종료하여 변경된 모든 데이터가 캐시에서 백 엔드 저장소로 기록되었는지 확인합니다.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Avere 제어판을 사용하여 클러스터 관리 

Avere 제어판을 사용할 수 있는 작업은 다음과 같습니다. 

* 개별 노드 중지 또는 다시 부팅
* 클러스터에서 노드 제거
* 전체 클러스터 중지 또는 다시 부팅

Avere 제어판은 데이터 무결성의 우선 순위를 지정하므로 가능한 파괴적인 작업을 수행하기 전에 변경된 데이터를 백 엔드 저장소에 쓰려고 시도합니다. 이는 Avere 포털보다 안전한 옵션입니다. 

웹 브라우저에서 Avere 제어판에 액세스합니다. 도움이 필요한 경우 [vFXT 클러스터에 액세스](avere-vfxt-cluster-gui.md)의 지침을 따릅니다.

### <a name="manage-nodes-with-avere-control-panel"></a>Avere 제어판을 사용하여 노드 관리

**FXT 노드** 설정 페이지에는 개별 노드를 관리하기 위한 컨트롤이 있습니다.

노드를 종료, 다시 부팅 또는 제거하려면 **FXT 노드** 페이지의 목록에서 노드를 찾은 다음, **작업** 열에서 해당 단추를 클릭합니다.

> [!NOTE] 
> 활성 노드 수가 변경되면 IP 주소가 클러스터 노드 간에 이동할 수 있습니다.

자세한 내용은 Avere 클러스터 설정 가이드의 [클러스터 > FXT 노드](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>)를 참조하세요.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Avere 제어판을 사용하여 클러스터 중지 또는 다시 부팅

**시스템 유지 관리** 설정 페이지에는 클러스터 서비스를 다시 시작하거나, 클러스터를 다시 부팅하거나, 클러스터의 작동을 안전하게 중지하는 명령이 있습니다. 자세한 내용은 Avere 클러스터 설정 가이드의 [관리 > 시스템 유지 관리](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>)를 참조하세요.

클러스터가 종료되면 처음에는 상태 메시지를 **대시보드** 탭에 게시합니다. 잠시 후에 Avere 제어판 세션에서 응답을 중지합니다. 이는 클러스터가 종료되었음을 의미합니다.

## <a name="manage-the-cluster-with-vfxtpy"></a>vfxt.py를 사용하여 클러스터 관리

vfxt.py는 클러스터 만들기 및 관리용 명령줄 도구입니다. 

vfxt.py는 클러스터 컨트롤러 VM에 사전 설치됩니다. 다른 시스템에 vfxt.py를 설치하려면 <https://github.com/Azure/AvereSDK>에서 설명서를 참조하세요.

vfxt.py 스크립트를 사용할 수 있는 클러스터 관리 작업은 다음과 같습니다.

* 클러스터에 새 노드 추가
* 클러스터 시작 또는 중지  
* 클러스터 삭제

Avere 제어판과 마찬가지로, vfxt.py 작업은 클러스터 또는 노드를 종료하거나 삭제하기 전에 변경된 데이터가 백 엔드 저장소에 영구적으로 저장되도록 합니다. 이는 Avere 포털보다 안전한 옵션입니다.

전체 vfxt.py 사용 가이드는 GitHub: [vfxt.py를 사용한 클라우드 클러스터 관리](https://github.com/azure/averesdk/blob/master/docs/README.md)에서 사용할 수 있습니다.

### <a name="add-cluster-nodes-with-vfxtpy"></a>vfxt.py를 사용하여 클러스터 노드 추가

클러스터 노드를 추가하기 위한 명령 스크립트 샘플이 클러스터 컨트롤러에 포함되어 있습니다. 컨트롤러에서 ``./add-nodes``를 찾고, 편집기에서 열어 클러스터 정보로 사용자 지정합니다. 

이 명령을 사용하려면 클러스터가 실행 중이어야 합니다. 

다음 값을 제공합니다. 

* 클러스터에 대한 리소스 그룹 이름 및 클러스터와 동일하지 않은 경우 네트워크 및 저장소 리소스에 대한 리소스 그룹 이름
* 클러스터 위치
* 클러스터 네트워크 및 서브넷 
* 클러스터 노드 액세스 역할 
* 클러스터 관리 IP 주소 및 관리 암호 
* 추가할 노드 수(1, 2 또는 3)
* 노드 인스턴스 유형 및 캐시 크기 값 

프로토타입을 사용하지 않는 경우 위에서 설명한 모든 정보를 포함하여 다음과 같은 명령을 생성해야 합니다. 

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \ 
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role ROLE_NAME \
   --log ~/vfxt.log
```

자세한 내용은 vfxt.py 사용 가이드의 [클러스터에 노드 추가](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster)를 참조하세요.

### <a name="stop-a-cluster-with-vfxtpy"></a>vfxt.py를 사용하여 클러스터 중지

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>vfxt.py를 사용하여 중지된 클러스터 시작

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

클러스터가 중지되었으므로 인스턴스 식별자를 전달하여 클러스터 노드를 지정해야 합니다. 자세한 내용은 vfxt.py 사용 가이드의 [수정할 클러스터 지정](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify)을 참조하세요.

### <a name="destroy-a-cluster-with-vfxtpy"></a>vfxt.py를 사용하여 클러스터 삭제

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

클러스터 캐시에서 변경된 데이터를 쓰지 않으려는 경우 ``--quick-destroy`` 옵션을 사용할 수 있습니다.

자세한 내용은 [vfxt.py 사용 가이드](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>)를 참조하세요.  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Azure Portal에서 클러스터 VM 관리 

Azure Portal을 사용하여 클러스터 VM을 개별적으로 삭제할 수 있지만, 먼저 클러스터가 완전히 종료되지 않으면 데이터 무결성이 보장되지 않습니다. 

Azure Portal을 사용할 수 있는 클러스터 관리 작업은 다음과 같습니다. 

* 중지된 vFXT 노드 시작
* 개별 vFXT 노드 중지(클러스터에서 노드 실패로 해석함)
* *클러스터 캐시에서 변경된 데이터가 코어 파일러에 기록되었는지 확인할 필요가 없는 경우* vFXT 클러스터 삭제
* vFXT 노드 및 다른 클러스터 리소스를 안전하게 종료한 후 영구적으로 제거

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Azure Portal에서 vFXT 인스턴스 다시 시작

중지된 노드를 다시 시작해야 하는 경우 Azure Portal을 사용해야 합니다. 왼쪽 메뉴에서 **가상 머신**을 선택한 다음, 목록에서 VM 이름을 클릭하여 해당 개요 페이지를 엽니다.

개요 페이지의 위쪽에서 **시작** 단추를 클릭하여 VM을 다시 활성화합니다.

![중지된 VM을 시작하는 옵션을 보여 주는 Azure Portal 화면](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>클러스터 노드 삭제

vFXT 클러스터에서 하나의 노드를 삭제하지만 클러스터의 나머지 노드는 그대로 유지하려면 먼저 Avere 제어판을 사용하여 [클러스터에서 노드를 제거](#manage-nodes-with-avere-control-panel)해야 합니다.

> [!CAUTION]
> 먼저 vFXT 클러스터에서 노드를 제거하지 않고 삭제하면 데이터가 손실될 수 있습니다.

vFXT 노드로 사용되는 하나 이상의 인스턴스를 영구적으로 삭제하려면 Azure Portal을 사용합니다.
왼쪽 메뉴에서 **가상 머신**을 선택한 다음, 목록에서 VM 이름을 클릭하여 해당 개요 페이지를 엽니다.

개요 페이지의 위쪽에서 **삭제** 단추를 클릭하여 VM을 영구적으로 삭제합니다.

클러스터 노드가 안전하게 종료된 후 이 방법을 사용하여 해당 클러스터 노드를 영구적으로 제거할 수 있습니다. 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Azure Portal에서 클러스터 삭제

> [!NOTE] 
> 캐시의 나머지 클라이언트 변경 내용을 백 엔드 저장소에 기록하려면 Azure Portal에서 노드 인스턴스를 제거하기 전에 `--destroy` vfxt.py 옵션을 사용하거나 Avere 제어판을 사용하여 클러스터를 완전히 종료합니다.

노드 인스턴스는 Azure Portal에서 삭제하여 영구적으로 삭제할 수 있습니다. 위에서 설명한 대로 한 번에 하나씩 삭제할 수 있거나, **Virtual Machines** 페이지를 사용하여 모든 클러스터 VM을 찾아서 확인란을 선택하고 **삭제** 단추를 클릭하여 한 번에 모두 제거할 수 있습니다.

![포털에서 "클러스터"라는 용어로 필터링된 VM 목록(4개 중 3개가 선택 및 강조 표시됨)](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Azure Portal에서 추가 클러스터 리소스 삭제

vFXT 클러스터에 대해 특별히 추가 리소스를 만든 경우 클러스터 분해의 일환으로 해당 리소스를 제거하는 것이 좋습니다. 필요한 데이터 또는 다른 프로젝트와 공유되는 항목이 포함된 요소는 삭제하면 안됩니다.

클러스터 노드를 삭제하는 것 외에도 다음 구성 요소를 제거하는 것이 좋습니다. 

* 클러스터 컨트롤러 VM
* 클러스터 노드와 연결된 데이터 디스크
* 클러스터 구성 요소와 연결된 네트워크 인터페이스 및 공용 IP
* 가상 네트워크
* 저장소 계정(중요한 데이터가 없는 **경우에만**)
* 가용성 집합 

![테스트 클러스터에 대해 만들어진 리소스를 보여 주는 Azure Portal의 "모든 리소스" 목록](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Azure Portal에서 클러스터의 리소스 그룹 삭제

클러스터를 수용하는 전용 리소스 그룹을 만든 경우 리소스 그룹을 삭제하여 해당 클러스터와 관련된 모든 리소스를 삭제할 수 있습니다. 

> [!Caution] 
> 그룹에 값이 없는 것이 확실한 경우에만 리소스 그룹을 삭제합니다. 예를 들어 리소스 그룹 내의 모든 저장소 컨테이너에서 필요한 데이터를 모두 이동했는지 확인합니다.  

리소스 그룹을 삭제하려면 포털의 왼쪽 메뉴에서 **리소스 그룹**을 클릭하고, 리소스 그룹 목록을 필터링하여 vFXT 클러스터에 대해 만든 리소스 그룹을 찾습니다. 리소스 그룹을 선택하고, 패널의 오른쪽에 있는 세 개의 점을 클릭합니다. **리소스 그룹 삭제**를 선택합니다. 포털에서 되돌릴 수 없는 삭제를 확인하도록 요청합니다.  

!["리소스 그룹 삭제" 작업을 보여 주는 리소스 그룹](media/avere-vfxt-delete-resource-group.png)
