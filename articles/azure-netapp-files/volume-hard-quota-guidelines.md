---
title: Azure NetApp Files 서비스에 대 한 볼륨 하드 할당량을 변경 하는 것은 무엇 인가요? Microsoft Docs
description: 볼륨 하드 할당량 사용에 대 한 변경 내용, 변경을 계획 하는 방법 및 용량을 모니터링 하 고 관리 하는 방법을 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 12807e83f7841bc67999ce385d0cb82bf15f4c71
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175994"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Azure NetApp Files 서비스에 대 한 볼륨 하드 할당량을 변경 하는 것은 무엇 인가요?

서비스의 시작부터 Azure NetApp Files는 용량 풀 프로 비전 및 자동 증가 메커니즘을 사용 했습니다. Azure NetApp Files 볼륨은 선택한 계층 및 크기의 고객이 프로 비전 된 용량 풀을 중심으로 씬 프로 비전 됩니다. 볼륨 크기 (할당량)는 성능 및 용량을 제공 하는 데 사용 되며, 언제 든 지 즉시 할당량을 조정할 수 있습니다. 이 동작은 현재 볼륨 할당량이 볼륨에 대 한 대역폭을 제어 하는 데 사용 되는 성능 레버 임을 의미 합니다. 현재 용량이 가득 차면 용량 풀을 자동으로 확장 합니다.   

> [!IMPORTANT] 
> 볼륨 및 용량 풀 프로 비전의 Azure NetApp Files 동작은 *수동* 및 *제어* 가능한 메커니즘으로 변경 됩니다. **4 월 1 일부 터 2021 (업데이트 됨)부터 볼륨 크기 (할당량)는 프로 비전 된 용량 뿐만 아니라 대역폭 성능도 관리 하며, 기본 용량 풀은 더 이상 자동으로 증가 하지 않습니다.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>볼륨 하드 할당량 변경 이유

대부분의 고객은 *초기* 동작을 통해 다음과 같은 세 가지 주요 문제를 나타냈습니다.
* VM 클라이언트는 OS 공간 또는 용량 모니터링 도구를 사용할 때 지정 된 볼륨의 씬 프로 비전 된 (100 TiB) 용량을 확인 하 여 부정확 한 클라이언트 또는 응용 프로그램 측 용량 표시 유형을 제공 합니다.
* 응용 프로그램 소유자는 용량 풀 자동 증가 동작 때문에 프로 비전 된 용량 풀 공간 및 관련 비용을 제어할 수 없습니다. "실행 프로세스"가 프로 비전 된 용량과 비용을 신속 하 게 정리 하 고 확장할 수 있는 환경에서는 이러한 상황이 복잡 합니다.
* 고객은 볼륨 크기 (할당량)와 성능 사이의 직접적인 상관 관계를 확인 하 고 유지 관리 하려고 합니다. (암시적) 볼륨의 현재 동작 (용량 단위) 및 풀 자동 증가를 사용 하는 경우 볼륨 할당량이 적극적으로 설정 되거나 다시 설정 될 때까지 고객은 직접적인 상관 관계를 갖지 않습니다. 

많은 고객이 프로 비전 된 용량에 대 한 직접 제어를 요청 했습니다. 저장소 용량 및 사용률을 제어 하 고 균형을 조정 하려고 합니다. 또한 응용 프로그램 볼륨의 사용 가능, 사용, 프로 비전 된 용량 및 성능에 대 한 응용 프로그램 쪽 및 클라이언트 쪽 표시와 함께 비용을 제어 하려고 합니다. 

## <a name="what-is-the-volume-hard-quota-change"></a>볼륨 하드 할당량 변경 내용   

볼륨 하드 할당량을 변경 하면 Azure NetApp Files 볼륨이 더 이상 (최대) 100 TiB에서 씬 프로 비전 되지 않습니다. 볼륨은 실제 구성 된 크기 (할당량)로 프로 비전 됩니다. 또한 용량 풀을 많이 사용 하는 것은 전체 용량 소비에 도달 하면 더 이상 자동으로 증가 하지 않습니다. 이 변경은 자동 용량 증가 없이 있는 그대로 프로 비전 되는 Azure managed disks와 같은 동작을 반영 합니다.

예를 들어 4 TiB 울트라 서비스 수준 용량 풀에서 1-TiB 크기 (할당량)로 구성 된 Azure NetApp Files 볼륨을 고려 합니다. 응용 프로그램이 볼륨에 지속적으로 데이터를 쓰고 있습니다.

*초기* 동작:  
* 예상 대역폭: 128 MiB/s
* 사용 가능한 총 (및 클라이언트 표시) 용량: 100 TiB   
    이 크기를 초과 하는 볼륨에 더 많은 데이터를 쓸 수 없습니다.
* 용량 풀: 가득 찬 경우 TiB 1 씩 자동으로 증가 합니다.
* 볼륨 할당량 변경: 볼륨의 성능 (대역폭)만 변경 합니다. 클라이언트의 표시 또는 사용 가능한 용량을 변경 하지 않습니다.

*변경* 된 동작:  
* 예상 대역폭: 128 MiB/s
* 사용 가능한 총 (및 클라이언트 표시) 용량: 1 TiB이 크기를 초과 하는 볼륨에 더 많은 데이터를 쓸 수 없습니다.
* 용량 풀: 크기를 4 TiB으로 유지 하 고 자동으로 증가 하지 않습니다. 
* 볼륨 할당량 변경: 볼륨의 성능 (대역폭) 및 클라이언트의 표시 또는 사용 가능한 용량을 변경 합니다.

Azure NetApp Files 볼륨 및 용량 풀의 사용률을 사전에 모니터링 해야 합니다. 거의 모든 사용을 위해 볼륨 및 풀 사용률을 의도적으로 변경 해야 합니다. Azure NetApp Files는 [즉시 볼륨 및 용량 풀 크기 조정 작업](azure-netapp-files-resize-capacity-pools-or-volumes.md)을 계속 허용 합니다.

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>볼륨 하드 할당량 변경을 운영 하는 방법

이 섹션에서는 부드러운 전환에 대 한 볼륨 하드 할당량 변경을 운영 하는 방법에 대 한 지침을 제공 합니다. 또한 현재 프로 비전 된 볼륨 및 용량 풀, 진행 중인 모니터링, 경고 및 용량 관리 옵션에 대 한 정보를 제공 합니다.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>현재 프로 비전 된 볼륨 및 용량 풀

볼륨 하드 할당량 변경으로 인해 운영 모델을 변경 해야 합니다. 프로 비전 된 볼륨과 용량 풀에는 지속적인 용량 관리가 필요 합니다.  변경 된 동작이 즉시 발생 하므로 Azure NetApp Files 팀은이 섹션에 설명 된 대로 기존의 이전에 프로 비전 된 볼륨 및 용량 풀에 대해 일련의 일회성 수정 조치를 권장 합니다.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>일회성 수정 또는 예방 조치 권장 사항  

볼륨 하드 할당량 변경으로 인해 이전에 프로 비전 된 볼륨 및 풀에 대해 프로 비전 되 고 사용 가능한 용량이 변경 됩니다. 따라서 일부 용량 할당 과제가 발생할 수 있습니다. 고객의 단기 공간 부족 상황을 방지 하기 위해 Azure NetApp Files 팀은 다음과 같은 일회성 교정/예방 조치를 권장 합니다. 

* **프로 비전 된 볼륨 크기**:   
    모든 프로 비전 된 볼륨의 크기를 조정 하 여 변경 비율 및 경고를 기반으로 적절 한 버퍼를 사용 하거나 시간을 조정 합니다 (예: 일반적인 워크 로드 고려 사항에 따라 20%), 최대 100 TiB ( [볼륨 크기 제한](azure-netapp-files-resource-limits.md#resource-limits)). 버퍼 용량을 포함 하 여이 새 볼륨 크기는 다음과 같은 요인을 기반으로 해야 합니다.
    * 사용 된 용량이 프로 비전 된 볼륨 할당량 보다 작은 경우 **프로 비전** 된 볼륨 용량
    * 사용 된 용량이 프로 비전 된 볼륨 할당량 보다 많은 경우 **사용** 된 볼륨 용량입니다.  
    용량 풀이 증가 하지 않아도 되는 경우 볼륨 수준의 용량 증가에 대 한 추가 요금이 부과 되지 않습니다. 이러한 변경으로 인해 볼륨에 대 한 대역폭 제한이 *증가* 하는 것을 확인할 수 있습니다 ( [자동 QoS 용량 풀 유형이](azure-netapp-files-understand-storage-hierarchy.md#qos_types) 사용 되는 경우).

* **프로 비전 된 용량 풀 크기**:   
    볼륨 크기를 조정한 후 볼륨 크기의 합계가 호스팅 용량 풀의 크기 보다 커지면 용량 풀은 볼륨의 합계 보다 크거나 같은 크기 ( [용량 풀 크기 제한](azure-netapp-files-resource-limits.md#resource-limits)) 500의 크기 보다 크거나 같은 크기의 용량으로 증가 해야 합니다. 추가 용량 풀 용량은 일반적으로 ACR 요금이 적용 됩니다.

아래 섹션에 설명 된 대로 모니터링 또는 경고를 설정 하는 데 도움이 필요한 경우 Azure NetApp Files 전문가와 협력 하 여 환경의 유효성을 검사 해야 합니다.

### <a name="ongoing-capacity-management"></a>지속적인 용량 관리  

일회성 정정 조치를 수행한 후에는 용량을 모니터링 하 고 관리 하기 위해 진행 중인 프로세스를 함께 배치 해야 합니다. 다음 섹션에서는 용량 모니터링 및 관리에 대 한 제안 사항 및 대안을 제공 합니다.

### <a name="monitor-capacity-utilization"></a>용량 사용률 모니터링

다양 한 수준에서 용량 사용률을 모니터링할 수 있습니다. 

#### <a name="vm-level-monitoring"></a>VM 수준 모니터링 

응용 프로그램 가상 컴퓨터 내에서 가장 높은 수준의 모니터링 (응용 프로그램에 가장 가까이)이 있습니다. VM 클라이언트 OS 내에서 용량 보고의 동작이 변경 되는 것을 확인할 수 있습니다.

다음 두 가지 시나리오에서는 TiB 크기 (할당량)로 구성 된 Azure NetApp Files 볼륨을 4 TiB의 Ultra 서비스 수준 용량 풀에 고려 합니다. 

##### <a name="windows"></a>Windows

Windows 클라이언트는 네트워크 매핑된 드라이브 속성을 사용 하 여 볼륨의 사용 된 용량과 사용 가능한 용량을 확인할 수 있습니다. **탐색기**  ->  **드라이브**  ->  **속성** 옵션을 사용할 수 있습니다.  

다음 예제에서는 변경 된 동작 *전의* Windows에서 볼륨 용량 보고를 보여 줍니다.

![동작 변경 전 볼륨의 저장소 용량 예를 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

명령 프롬프트에서 다음과 같이 명령을 사용할 수도 있습니다 `dir` .

![동작을 변경 하기 전에 명령을 사용 하 여 볼륨의 저장소 용량을 표시 하는 것을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

다음 예에서는 변경 된 동작 *후* Windows의 볼륨 용량 보고를 보여 줍니다.

![동작 변경 후 볼륨의 저장소 용량 예를 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

다음 예에서는 명령 출력을 보여 줍니다 `dir` .  

![동작 변경 후 볼륨의 저장소 용량을 표시 하는 명령을 사용 하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux 클라이언트는 [ `df` 명령을](https://linux.die.net/man/1/df)사용 하 여 볼륨의 사용 된 용량과 사용 가능한 용량을 확인할 수 있습니다. `-h`옵션은 M, G 및 T 단위 크기를 사용 하 여 사용자가 읽을 수 있는 형식의 크기, 사용 된 공간 및 사용 가능한 공간을 표시 합니다.

다음 예제에서는 변경 된 동작 *전에* Linux의 볼륨 용량 보고를 보여 줍니다.  

![동작을 변경 하기 전에 Linux를 사용 하 여 볼륨의 저장소 용량을 표시 하는 스크린샷](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

다음 예제에서는 변경 된 동작 *후* Linux의 볼륨 용량 보고 기능을 보여 줍니다.  

![동작 변경 후 Linux를 사용 하 여 볼륨의 저장소 용량을 표시 하는 스크린샷](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>ANFCapacityManager를 사용 하 여 경고 구성

커뮤니티 지원 Logic Apps ANFCapacityManager 도구를 사용 하 여 Azure NetApp Files 용량을 모니터링 하 고 맞춤형 경고를 받을 수 있습니다. ANFCapacityManager 도구는 [ANFCapacityManager GitHub 페이지](https://github.com/ANFTechTeam/ANFCapacityManager)에서 사용할 수 있습니다.

ANFCapacityManager는 용량 기반 경고 규칙을 관리 하는 Azure 논리 앱입니다. Azure NetApp Files 볼륨의 공간이 부족 하지 않도록 볼륨 크기가 자동으로 늘어납니다. 배포 하기 쉬우며 다음과 같은 경고 관리 기능을 제공 합니다.

* Azure NetApp Files 용량 풀 또는 볼륨이 생성 되 면 ANFCapacityManager는 지정 된 사용 된 비율 임계값에 따라 메트릭 경고 규칙을 만듭니다.
* Azure NetApp Files 용량 풀 또는 볼륨의 크기를 조정 하는 경우 ANFCapacityManager는 지정 된 비율의 용량을 사용 하 여 메트릭 경고 규칙을 수정 합니다. 경고 규칙이 없으면 생성 됩니다.
* Azure NetApp Files 용량 풀 또는 볼륨을 삭제 하면 해당 하는 메트릭 경고 규칙이 삭제 됩니다.

다음 키 경고 설정을 구성할 수 있습니다.  

* **용량 풀% Full Threshold** -이 설정은 용량 풀에 대 한 경고를 트리거하는 데 사용 된 임계값을 결정 합니다. 90 값을 사용 하면 용량 풀이 사용 된 90%에 도달 하면 경고가 트리거됩니다.
* **볼륨% 전체 임계값** -이 설정은 볼륨에 대 한 경고를 트리거하는 데 사용 된 임계값을 결정 합니다. 80 값을 사용 하면 볼륨이 80%에 도달 하면 경고가 트리거됩니다.
* **용량 알림에 대 한 기존 작업 그룹** -이 설정은 용량 기반 경고에 대해 트리거되는 작업 그룹입니다. 이 설정은 사용자가 미리 만들어야 합니다. 작업 그룹은 전자 메일, SMS 또는 다른 형식을 보낼 수 있습니다.

다음 그림에서는 경고 구성을 보여 줍니다.  

![ANFCapacityManager를 사용 하 여 경고 구성을 보여 주는 그림입니다.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

ANFCapacityManager를 설치한 후에는 다음 동작을 수행할 수 있습니다. Azure NetApp Files 용량 풀 또는 볼륨이 생성, 수정 또는 삭제 되 면 논리 앱에서 이름이 또는 인 용량 기반 메트릭 경고 규칙을 자동으로 생성, 수정 또는 삭제 합니다. `ANF_Pool_poolname` `ANF_Volume_poolname_volname` 

### <a name="manage-capacity"></a>용량 관리

모니터링 및 경고 외에도, 용량 소비를 Azure NetApp Files 관리 하는 응용 프로그램 용량 관리 관행을 통합 해야 합니다 (증가). Azure NetApp Files 볼륨이 나 용량 풀이 가득 차면 [응용 프로그램 중단 없이 추가 용량을 즉시 제공할 수 있습니다](azure-netapp-files-resize-capacity-pools-or-volumes.md). 이 섹션에서는 필요에 따라 볼륨 및 용량 풀에서 프로 비전 된 공간을 늘리는 다양 한 수동 및 자동화 된 방법을 설명 합니다.
 
#### <a name="manual"></a>수동 

포털 또는 CLI를 사용 하 여 볼륨 또는 용량 풀 크기를 수동으로 늘릴 수 있습니다. 

##### <a name="portal"></a>포털 

필요에 따라 [볼륨의 크기를 변경할](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) 수 있습니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.

1. NetApp 계정 관리 블레이드에서 **볼륨** 을 클릭합니다.  
2. 크기를 조정 하려는 볼륨의 이름을 마우스 오른쪽 단추로 클릭 하거나 `…` 볼륨의 행 끝에 있는 아이콘을 클릭 하 여 상황에 맞는 메뉴를 표시 합니다. 
3. 상황에 맞는 메뉴 옵션을 사용하여 볼륨을 크기 조정하거나 삭제합니다.   

   ![볼륨에 대 한 상황에 맞는 메뉴 옵션을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![볼륨 할당량 업데이트 창을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

경우에 따라 호스트 용량 풀의 용량이 부족 하 여 볼륨의 크기를 조정할 수 없습니다. 그러나 [용량 풀 크기는](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) 1에서 TiB 증분 또는 감소 단위로 변경할 수 있습니다. 용량 풀 크기는 4 TiB 보다 작을 수 없습니다. *용량 풀 크기를 조정하면 구매한 Azure NetApp Files 용량을 변경합니다.*

1. NetApp 계정 관리 블레이드에서 크기를 조정하려는 용량 풀을 클릭합니다.
2. 용량 풀 이름을 마우스 오른쪽 단추로 클릭 하거나 `…` 용량 풀의 행 끝에 있는 아이콘을 클릭 하 여 상황에 맞는 메뉴를 표시 합니다.
3. 상황에 맞는 메뉴 옵션을 사용하여 용량 풀을 크기 조정하거나 삭제합니다.    

   ![용량 풀의 상황에 맞는 메뉴 옵션을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![풀 크기 조정 창을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI 또는 PowerShell

Azure CLI 및 Azure PowerShell를 비롯 한 [AZURE NETAPP FILES CLI 도구](azure-netapp-files-sdk-cli.md#cli-tools)를 사용 하 여 볼륨 또는 용량 풀 크기를 수동으로 변경할 수 있습니다.  다음 두 명령을 사용 하 여 Azure NetApp Files 볼륨 및 풀 리소스를 관리할 수 있습니다.  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Azure CLI를 사용 하 여 Azure NetApp Files 리소스를 관리 하려면 Azure Portal을 열고 메뉴 모음의 맨 위에 있는 Azure **Cloud Shell** 링크를 선택 합니다. 

[![Cloud Shell 링크 ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png) 에 액세스 하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

이 작업을 수행 하면 Azure Cloud Shell 열립니다.

[![Cloud Shell 창을 ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png) 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

다음 예에서는 명령을 사용 하 여 볼륨의 크기를 [표시](/cli/azure/netappfiles/volume#az-netappfiles-volume-show) 하 고 [업데이트](/cli/azure/netappfiles/volume#az-netappfiles-volume-update) 합니다.
 
[![PowerShell을 사용 하 여 볼륨 크기 ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png) 를 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![PowerShell을 사용 하 여 볼륨 크기 ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png) 를 업데이트 하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

다음 예에서는 명령을 사용 하 여 용량 풀의 크기를 [표시](/cli/azure/netappfiles/pool#az-netappfiles-pool-show) 하 고 [업데이트](/cli/azure/netappfiles/pool#az-netappfiles-pool-update) 합니다.

[![PowerShell을 사용 하 여 용량 풀 크기 ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png) 를 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![PowerShell을 사용 하 여 용량 풀 크기 ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png) 를 업데이트 하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>자동  

자동화 된 프로세스를 빌드하여 변경 된 동작을 관리할 수 있습니다.

##### <a name="rest-api"></a>REST API   

Azure NetApp Files 서비스용 REST API는 NetApp 계정, 용량 풀, 볼륨 및 스냅샷과 같은 리소스에 대한 HTTP 작업을 정의합니다. Azure NetApp Files에 대 한 REST API 사양은 [Azure NetApp Files 리소스 관리자 GitHub 페이지](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)]를 통해 게시 됩니다. GitHub에서 [REST api와 함께 사용할 예제 코드](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) 를 찾을 수 있습니다.

[REST API를 사용 하 여 Azure NetApp Files 개발을](azure-netapp-files-develop-with-rest-api.md)참조 하세요. 

##### <a name="rest-api-using-powershell"></a>PowerShell을 사용하는 REST API  

Azure NetApp Files 서비스용 REST API는 NetApp 계정, 용량 풀, 볼륨 및 스냅샷과 같은 리소스에 대한 HTTP 작업을 정의합니다. [Azure NetApp Files에 대 한 REST API 사양은](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) GitHub를 통해 게시 됩니다.

[PowerShell을 사용 하 여 REST API Azure NetApp Files 개발](develop-rest-api-powershell.md)을 참조 하세요.

##### <a name="capacity-management-using-anfcapacitymanager"></a>ANFCapacityManager를 사용 하 여 용량 관리

ANFCapacityManager는 용량 기반 경고 규칙을 관리 하는 Azure 논리 앱입니다. Azure NetApp Files 볼륨의 공간이 부족 하지 않도록 볼륨 크기가 자동으로 늘어납니다. 경고를 전송 하는 것 외에도 볼륨 및 용량 풀 크기의 자동 증가를 사용 하 여 Azure NetApp Files 볼륨의 공간이 부족 하지 않도록 할 수 있습니다. 

* 필요에 따라 Azure NetApp Files 볼륨이 지정 된 사용 된 임계값에 도달 하면 볼륨 할당량 (크기)이 10-100 사이에 지정 된 백분율로 증가 합니다.  
* 볼륨 크기를 늘리면 포함 하는 용량 풀의 용량을 초과 하는 경우 새 볼륨 크기를 수용할 수 있도록 용량 풀 크기도 늘어납니다.

다음 키 용량 관리 설정을 구성할 수 있습니다.  

* 지정 된 **% Full 임계값** 에 도달 하는 경우 볼륨을 자동으로 증가 하도록 기존 볼륨 크기의 백분율 **증가** 비율 값 0은 자동 증가 기능을 사용 하지 않도록 설정 합니다. 10에서 100 사이의 값을 권장 합니다.

    ![볼륨 자동 증가 백분율 설정을 보여 주는 스크린샷](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>FAQ 

이 섹션에서는 볼륨 하드 할당량 변경에 대 한 몇 가지 질문에 대답 합니다. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>스냅숏 공간은 볼륨의 사용 가능 또는 프로 비전 된 용량에 대해 계산 되나요?

예, 사용 되는 스냅숏 용량은 볼륨에서 프로 비전 된 공간을 계산 합니다. 볼륨이 전체 실행 되는 경우 다음 두 가지 수정 옵션을 고려 하십시오.

* 이 문서에 설명 된 대로 볼륨의 크기를 조정 합니다.
* 이전 스냅숏을 제거 하 여 호스팅 볼륨의 공간을 확보 합니다.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>이렇게 변경 하면 볼륨 자동 증가 동작이 Azure NetApp Files에서 사라집니다.

일반적인 오해 Azure NetApp Files *볼륨이* 가득 찰 때 자동으로 증가 하는 것입니다. 볼륨은 실제 설정 할당량에 관계 없이 100 TiB 크기를 사용 하 여 씬 프로 비전 되었으며, *용량 풀* 을 포함 하는는 TiB 증분으로 자동으로 증가 합니다. 이렇게 변경 하면 *볼륨* 크기 (표시 및 사용 가능)를 설정 된 할당량으로 처리 하 고 *용량 풀* 은 더 이상 자동으로 증가 하지 않습니다. 이 변경으로 인해 일반적으로 원하는 정확한 클라이언트 쪽 공간 및 용량 보고가 발생 합니다. "런어웨이" 용량 소비를 방지 합니다.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>이 변경은 지역 간 복제 (미리 보기)를 사용 하 여 복제 된 볼륨에 영향을 주지 않나요? 

복제 대상 볼륨에는 하드 볼륨 할당량이 적용 되지 않습니다.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>이 변경 내용은 현재 Azure Monitor에서 사용할 수 있는 메트릭에 영향을 주지 않습니다.

포털 메트릭 및 Azure Monitor 통계는 새 할당 및 사용률 모델을 정확 하 게 반영 합니다.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>이 변경은 Azure NetApp Files에 대 한 리소스 제한에 영향을 주지 않습니다.

이 문서에서 설명 하는 할당량 변경 내용 외 Azure NetApp Files에 대 한 리소스 제한은 변경 되지 않습니다.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>ANFCapacityManager 워크플로 예가 있나요?  

예. 볼륨 자동 [증가 워크플로 예제 GitHub 페이지](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md)를 참조 하세요.

### <a name="is-anfcapacitymanager-microsoft-supported"></a>ANFCapacityManager Microsoft가 지원 되나요?  

[ANFCapacityManager 논리 앱은 있는 그대로 제공 되며 netapp 또는 Microsoft에서 지원 되지](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer)않습니다. 특정 환경 또는 요구 사항에 맞게 수정 하는 것이 좋습니다. 업무상 중요 하거나 프로덕션 환경에 배포 하기 전에 기능을 테스트 해야 합니다.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>ANFCapacityManger에 대 한 버그를 보고 하거나 기능 요청을 제출 하려면 어떻게 해야 하나요?
[ANFCapacityManager GitHub 페이지](https://github.com/ANFTechTeam/ANFCapacityManager/issues)에서 **새 문제** 를 클릭 하 여 버그 및 기능 요청을 제출할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [용량 풀 또는 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)