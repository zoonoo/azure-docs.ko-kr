---
title: Azure NetApp Files 스냅숏의 작동 방식 | Microsoft Docs
description: 스냅숏을 만드는 방법, 스냅숏을 복원 하는 방법, 지역 간 복제 설정에서 스냅숏을 사용 하는 방법을 비롯 하 여 Azure NetApp Files 스냅숏의 작동 방식을 설명 합니다.
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
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: beadd250ec4472b894f0f474b1057ad44cf474ed
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133517"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Azure NetApp Files 스냅숏의 작동 방법

이 문서에서는 Azure NetApp Files 스냅숏의 작동 방식을 설명 합니다. Azure NetApp Files 스냅숏 기술은 성능에 영향을 주지 않고 안정성, 확장성 및 빠른 복구 기능을 제공 합니다. Azure NetApp Files snapshot 기술은 단일 파일 복원, 볼륨 복원 및 클론, 지역 간 복제를 포함 하 여 데이터 보호 솔루션에 대 한 토대를 제공 합니다. 

볼륨 스냅숏을 사용 하는 방법에 대 한 단계는 [Azure NetApp Files 사용 하 여 스냅숏 관리](azure-netapp-files-manage-snapshots.md)를 참조 하세요. 지역 간 복제의 스냅숏 관리에 대 한 고려 사항은 [지역 간 복제 사용을 위한 요구 사항 및 고려](cross-region-replication-requirements-considerations.md)사항을 참조 하세요.

## <a name="what-volume-snapshots-are"></a>볼륨 스냅숏의 정의  

Azure NetApp Files 스냅숏은 특정 시점 파일 시스템 (볼륨) 이미지입니다. 이상적인 온라인 백업으로 사용할 수 있습니다. 스냅숏을 사용 하 여 [새 볼륨을 만들거나](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), 파일을 [복원](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)하거나, [볼륨을 되돌릴](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)수 있습니다. Azure NetApp Files 볼륨에 저장 된 특정 응용 프로그램 데이터에서 응용 프로그램 일관성을 보장 하기 위해 추가 단계가 필요할 수 있습니다.  

Azure NetApp Files의 일부인 볼륨 가상화 기술에 대 한 고유 기능을 통해 오버 헤드가 낮은 스냅숏을 만들 수 있습니다. 데이터베이스와 마찬가지로이 계층은 디스크의 실제 데이터 블록에 대 한 포인터를 사용 합니다. 그러나 데이터베이스와 달리 기존 블록은 다시 작성 되지 않습니다. 업데이트 된 데이터를 새 블록에 쓰고 포인터를 변경 합니다. Azure NetApp Files 스냅숏은 단순히 블록 포인터를 조작 하 고, 응용 프로그램에서 특수 한 프로그래밍 없이 이전 버전의 파일 및 디렉터리 계층 구조에 액세스할 수 있도록 하는 볼륨의 읽기 전용 보기를 만듭니다. 실제 데이터 블록은 복사 되지 않습니다. 따라서 스냅숏은 생성 하는 데 필요한 시간에 효율적입니다. 볼륨 크기에 관계 없이 거의 즉각적입니다. 스냅숏은 저장소 공간 에서도 효율적입니다. 자체는 공간을 사용 하지 않으며 스냅숏과 활성 볼륨 간의 델타 블록만 유지 됩니다. 

다음 다이어그램에서는 이러한 개념을 보여 줍니다.  

![스냅숏의 주요 개념을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-concepts.png)

다이어그램에서 스냅숏은 그림 1a에서 가져옵니다. 그림 1b에서 변경 된 데이터는 *새 블록* 에 기록 되 고 포인터는 업데이트 됩니다. 그러나 스냅숏 포인터는 여전히 *이전에 작성 된 블록* 을 가리키지만 데이터의 라이브 및 기록 보기를 제공 합니다. 다른 스냅숏은 그림 1c에서 가져옵니다. 이제 세 개의 전체 복사본에 필요한 볼륨 공간을 차지 하지 않고도 세 개의 데이터 세대 (라이브 데이터, 스냅숏 2 및 스냅숏 1)에 액세스할 수 있습니다. 

스냅숏은 볼륨 메타 데이터 (*inode 테이블*)의 복사본만 사용 합니다. 볼륨 크기, 사용 된 용량 또는 볼륨의 작업 수준에 관계 없이 만드는 데 몇 초 밖에 걸리지 않습니다. 따라서 100 TiB 볼륨의 스냅숏을 만드는 것은 100 GiB 볼륨의 스냅숏을 만드는 것과 동일한 시간 (0)을 사용 합니다. 스냅숏이 생성 된 후에는 데이터 파일에 대 한 변경 내용이 일반 파일의 활성 버전에 반영 됩니다.

한편 스냅숏에서 가리키는 데이터 블록은 안정적이 고 변경할 수 없는 상태로 유지 됩니다. Azure NetApp Files 볼륨의 "리디렉션 시 리디렉션" 특성 때문에 스냅숏에는 성능 오버 헤드가 발생 하지 않으며 그 자체로도 공간이 소비 되지 않습니다. 시간에 따라 볼륨당 최대 255 개의 스냅숏을 저장할 수 있습니다. 모든 데이터는 읽기 전용 및 온라인 버전의 데이터로 액세스할 수 있으며 각 스냅숏 간에 변경 된 블록 수 만큼의 용량을 소비 합니다. 수정 된 블록은 활성 볼륨에 저장 됩니다. 스냅숏에서 가리키는 블록은 보관을 위해 볼륨에서 읽기 전용으로 유지 되며, 활성 볼륨과 스냅숏의 모든 포인터를 선택 취소 한 경우에만 사용할 수 있습니다. 따라서 볼륨 사용률은 스냅숏에 유지 되는 새 데이터 블록 또는 (수정 된) 데이터 블록을 사용 하 여 시간이 지남에 따라 증가 합니다.

 다음 다이어그램은 볼륨의 스냅숏과 시간이 지남에 따른 사용 공간을 보여 줍니다. 

![볼륨의 스냅숏과 시간이 지남에 따른 사용 공간을 보여 주는 다이어그램](../media/azure-netapp-files/snapshots-used-space-over-time.png)

볼륨 스냅숏은 최신 스냅숏 이후 변경 된 블록만 기록 하기 때문에 다음과 같은 주요 이점을 제공 합니다.

* 스냅숏은 ***storage 효율성** _입니다.   
    스냅숏은 전체 볼륨의 데이터 블록을 복사 하지 않으므로 최소한의 저장소 공간을 사용 합니다. 시퀀스에서 수행 되는 두 스냅숏은 둘 사이의 시간 간격에서 추가 또는 변경 된 블록만 다릅니다. 이 블록 증분 동작은 관련 저장소 용량 소비를 제한 합니다. 많은 대체 스냅숏 구현은 활성 파일 시스템과 같은 저장소 볼륨을 사용 하 여 저장소 용량 요구 사항을 발생 시킵니다. 응용 프로그램 매일 _block 수준 *의 변경 률에 따라 Azure NetApp Files 스냅숏은 용량을 늘리거나 줄일 수 있지만 변경 된 데이터에 대해서만 사용 됩니다. 평균 일별 스냅숏 사용량 범위는 많은 응용 프로그램 볼륨에 사용 된 볼륨 용량의 1-5% 에서만 사용 되며, SAP HANA 데이터베이스 볼륨과 같은 볼륨의 경우 최대 20-30%입니다. 생성 및 유지 관리 되는 스냅숏 수를 기준으로 스냅숏 용량 소비에 대 한 [볼륨 및 스냅숏 사용량을 모니터링](azure-netapp-files-metrics.md#volumes) 해야 합니다.   

* 스냅숏은 ***빠른 생성, 복제, 복원 또는 복제** _입니다.   
    볼륨 크기와 활동 수준에 관계 없이 스냅숏을 생성, 복제, 복원 또는 복제 하는 데 몇 초 밖에 걸리지 않습니다. [요청 시](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)볼륨 스냅숏을 만들 수 있습니다. 또한 스냅숏 [정책을](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) 사용 하 여 Azure NetApp Files 스냅숏을 자동으로 만들어야 하는 경우와 볼륨에 대해 유지할 스냅숏 수를 지정할 수 있습니다.  응용 프로그램에 대 SAP HANA 한 오케스트레이션 [AzAcSnap 도구](azacsnap-introduction.md) 를 사용 하는 등의 방법으로 응용 프로그램 계층으로 스냅숏을 생성 하 여 응용 프로그램 일관성을 달성할 수 있습니다.

_ 스냅숏은 볼륨 ***성능** _에 영향을 주지 않습니다.   
    가장 많이 사용 되는 기술의 "쓰기 리디렉션" 특성 때문에 데이터 작업이 많은 경우에도 Azure NetApp Files 스냅숏을 저장 하거나 유지 해도 성능에 영향을 주지 않습니다. 스냅숏을 삭제 하는 것도 대부분의 경우 성능에 영향을 주지 않습니다. 

_ 스냅숏은 자주 생성 될 수 있고 많은 것을 유지할 수 있기 때문에 ***확장성** _을 제공 합니다.   
    Azure NetApp Files 볼륨은 최대 255 개의 스냅숏을 지원 합니다. 자주 생성 되는 많은 수의 낮은 영향을 저장 하는 기능을 통해 원하는 버전의 데이터가 성공적으로 복구 될 수 있는 가능성을 높일 수 있습니다.

_ 스냅숏은 ***사용자 가시성** _ 및 _*_파일 복구_*_ 기능을 제공 합니다.   
Azure NetApp Files 스냅숏 기술의 높은 성능, 확장성 및 안정성은 사용자 기반 복구를 위한 이상적인 온라인 백업을 제공 한다는 것을 의미 합니다. 스냅숏은 파일, 디렉터리 또는 볼륨 복원 용도로 사용자가 액세스할 수 있도록 만들 수 있습니다. 추가 솔루션을 사용 하면 오프 라인 저장소에 백업을 복사 하거나 보존 또는 재해 복구를 위해 [지역 간 복제](cross-region-replication-introduction.md) 를 수행할 수 있습니다.

## <a name="ways-to-create-snapshots"></a>스냅숏 생성 방법   

여러 가지 방법을 사용 하 여 스냅숏을 만들고 유지 관리할 수 있습니다.

_ 수동으로 (주문형):   
    * [Azure Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [REST API](/rest/api/netapp/snapshots), [Azure CLI](/cli/azure/netappfiles/snapshot)또는 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot) 도구
    * 스크립트 ( [예제](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts)참조)

* 다음을 사용 하 여 자동화 됨:
    * [Azure Portal](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [REST API](/rest/api/netapp/snapshotpolicies), [Azure CLI](/cli/azure/netappfiles/snapshot/policy)또는 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) 도구를 통해 스냅숏 정책
    * 응용 프로그램 일치 스냅숏 도구 (예: [AzAcSnap](azacsnap-introduction.md) )

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>DR에 대 한 볼륨 및 스냅숏이 지역 간 복제 되는 방식  

Azure NetApp Files는 DR (재해 복구)을 위해 [지역 간 복제](cross-region-replication-introduction.md) 를 지원 합니다. 지역 간 복제 Azure NetApp Files SnapMirror 기술을 사용 합니다. 변경 된 블록만 압축 된 효율적인 형식으로 네트워크를 통해 전송 됩니다. 볼륨 간에 영역 간 복제가 시작 되 면 전체 볼륨 내용 (즉, 실제 저장 된 데이터 블록)은 한 번만 전송 됩니다. 이 작업을 *기준 전송* 이라고 합니다. 초기 전송 후 변경 된 블록 (스냅숏에서 캡처한)만 전송 됩니다. 결과는 모든 스냅숏을 포함 하 여 원본 볼륨의 비동기 1:1 복제본입니다. 이 동작은 전체 및 증분 복제 메커니즘을 따릅니다. 이 기술은 지역에서 복제 하는 데 필요한 데이터의 양을 최소화 하므로 데이터 전송 비용이 절감 됩니다. 또한 복제 시간도 단축 됩니다. 제한 된 데이터 전송으로 더 많은 스냅숏을 만들고 더 자주 전송할 수 있으므로 더 작은 RPO (복구 지점 목표)를 달성할 수 있습니다. 또한 가상 컴퓨터 및 소프트웨어 라이선스 비용을 방지 하기 위해 호스트 기반 복제 메커니즘이 필요 하지 않습니다.

다음 다이어그램은 지역 간 복제 시나리오에서 스냅숏 트래픽을 보여 줍니다. 

![지역 간 복제 시나리오에서 스냅숏 트래픽을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>스냅숏에서 데이터를 복원 하는 방법  

Azure NetApp Files 스냅숏 기술은 백업의 빈도와 안정성을 크게 향상 시킵니다. 성능 오버 헤드가 최소화 되며 활성 볼륨에서 안전 하 게 만들 수 있습니다. Azure NetApp Files 스냅숏은 거의 즉각적이 고, 안전 하며, 선택적으로 사용자가 관리 하는 복원을 허용 합니다. 이 섹션에서는 Azure NetApp Files 스냅숏에서 데이터에 액세스 하거나 복원 하는 다양 한 방법에 대해 설명 합니다.

### <a name="restoring-files-or-directories-from-snapshots"></a>스냅숏에서 파일 또는 디렉터리 복원 

[스냅숏 경로 표시 유형이](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) 로 설정 되어 있지 않으면 `hidden` 사용자가 스냅숏에 직접 액세스 하 여 실수로 삭제, 손상 또는 데이터 수정 작업을 수행할 수 있습니다. 파일 및 디렉터리의 보안은 스냅숏에 유지 되며 스냅숏은 디자인에 의해 읽기 전용입니다. 따라서 복원은 안전 하 고 간단 합니다. 

다음 다이어그램은 스냅숏에 대 한 파일 또는 디렉터리 액세스를 보여 줍니다. 

![스냅숏에 대 한 파일 또는 디렉터리 액세스를 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-file-directory-access.png)

다이어그램에서 스냅숏 1은 활성 볼륨과 스냅숏 생성 순간 사이의 델타 블록만 사용 합니다. 그러나 볼륨 스냅숏 경로를 통해 스냅숏에 액세스 하는 경우 데이터는 스냅숏 생성 시의 전체 볼륨 용량 인 것 처럼 *표시* 됩니다. 사용자는 스냅숏 폴더에 액세스 하 여 선택한 스냅숏에서 파일 및 디렉터리를 복사 하 여 데이터를 자동으로 복원할 수 있습니다.

마찬가지로 대상 지역 간 복제 볼륨의 스냅숏은 DR 지역에서 데이터 복구를 위해 읽기 전용으로 액세스할 수 있습니다.  

다음 다이어그램은 지역 간 복제 시나리오에서 스냅숏 액세스를 보여 줍니다. 

![지역 간 복제의 스냅숏 액세스를 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

스냅숏에서 개별 파일 또는 디렉터리를 복원 하는 방법에 대 한 [클라이언트를 사용 하 여 스냅숏에서 파일 복원](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) 을 참조 하세요.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>스냅숏을 새 볼륨으로 복원 (복제)

별도의 독립적인 볼륨으로 Azure NetApp Files 스냅숏을 복원할 수 있습니다. 볼륨 크기 및 사용 되는 용량에 관계 없이이 작업은 거의 즉각적입니다. 실제 볼륨 및 스냅숏 데이터 블록을 복사 하는 동안 새로 만든 볼륨은 액세스에 거의 즉시 사용할 수 있습니다. 볼륨 크기와 용량에 따라이 프로세스는 부모 볼륨 및 스냅숏을 삭제할 수 없는 경우에 상당한 시간이 걸릴 수 있습니다. 그러나 복사본 프로세스가 백그라운드에서 진행 되는 동안 초기 생성 후에는 볼륨에 이미 액세스할 수 있습니다. 이 기능을 사용 하면 테스트 및 개발을 위한 데이터 복구 또는 볼륨 복제를 위한 빠른 볼륨 생성이 가능 합니다. 데이터 복사 프로세스의 특성상 복원이 완료 되 면 저장소 용량 풀 사용이 배가 되 고 새 볼륨에 원래 스냅숏의 전체 활성 용량이 표시 됩니다. 이 프로세스가 완료 되 면 볼륨은 독립적 이며 원래 볼륨과의 연결이 끊어지며 원본 볼륨과 스냅숏은 새 볼륨과 독립적으로 관리 하거나 제거할 수 있습니다.

다음 다이어그램은 스냅숏을 복원 (복제) 하 여 만든 새 볼륨을 보여 줍니다.   

![스냅숏을 복원 하 여 만든 새 볼륨을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

재해 복구 (DR) 볼륨에 복제 된 스냅숏에 대해 동일한 작업을 수행할 수 있습니다. 지역 간 복제가 활성 상태로 유지 되거나 진행 중인 경우에도 모든 스냅숏을 새 볼륨으로 복원할 수 있습니다. 이 기능을 통해 DR 지역에서 테스트 및 개발 환경에 대 한 중단 없는 생성을 수행할 수 있으며, 사용할 데이터를 저장 하는 반면 복제 된 볼륨은 DR 용도로만 사용 됩니다. 이 사용 사례를 통해 테스트 및 개발을 프로덕션에서 격리 하 여 프로덕션 환경에 대 한 잠재적 영향을 없앨 수 있습니다.  

다음 다이어그램에서는 지역 간 복제가 수행 되는 동안 DR 대상 볼륨 스냅숏을 사용 하 여 볼륨 복원 (복제)을 보여 줍니다.  

![DR 대상 볼륨 스냅숏을 사용 하 여 볼륨 복원을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

볼륨 복원 작업에 대 한 [새 볼륨으로 스냅숏 복원](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) 을 참조 하세요.

### <a name="restoring-reverting-a-snapshot-in-place"></a>원위치에서 스냅숏 복원 (되돌리기)

경우에 따라 새 볼륨에서 저장소 용량을 사용 하므로 스냅숏에서 새 볼륨을 만드는 것이 필요 하지 않을 수도 있습니다. 데이터 손상 으로부터 빠르게 복구 하기 위해 (예: 데이터베이스 손상 또는 랜 섬 웨어 공격) 볼륨 자체 내에서 스냅숏을 복원 하는 것이 더 적합할 수 있습니다. Azure NetApp Files 스냅숏 되돌리기 기능을 사용 하 여이 작업을 수행할 수 있습니다. 이 기능을 사용 하면 특정 스냅숏을 만들 때의 상태로 볼륨을 신속 하 게 되돌릴 수 있습니다. 대부분의 경우 볼륨을 되돌리기는 스냅숏에서 개별 파일을 활성 파일 시스템으로 복원 하는 것 보다 훨씬 더 빠릅니다. 특히 큰 멀티 TiB 볼륨에서 작동 합니다. 

볼륨 스냅숏을 되돌리는 작업은 거의 발생 하지 않으며, 가장 큰 볼륨의 경우에도 완료 하는 데 몇 초 밖에 걸리지 않습니다. 활성 볼륨 메타 데이터 (*inode 테이블*)는 스냅숏 생성 시의 스냅숏 메타 데이터로 대체 되므로 볼륨을 해당 특정 시점으로 롤백합니다. 되돌리기를 적용 하려면 데이터 블록을 복사 하지 않아도 됩니다. 따라서 스냅숏을 새 볼륨으로 복원 하는 것 보다 공간이 더 효율적입니다. 

다음 다이어그램은 이전 스냅숏으로 되돌리는 볼륨을 보여 줍니다.  

![이전 스냅숏으로의 볼륨 되돌리기를 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> 작성 된 활성 파일 시스템 데이터와 선택한 스냅숏이 만들어진 후에 수행 된 스냅숏이 손실 됩니다. 스냅숏 되돌리기 작업은 대상 볼륨의 모든 데이터를 선택한 스냅숏의 데이터로 바꿉니다. 스냅숏을 선택 하는 경우 스냅숏 콘텐츠와 만든 날짜에 주의를 기울여야 합니다. 스냅숏 되돌리기 작업을 실행 취소할 수 없습니다.  

이 기능을 사용 하는 방법에 대 한 [스냅숏 되돌리기를 사용 하 여 볼륨 되돌리기](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) 를 참조 하세요.

## <a name="how-snapshots-are-deleted"></a>스냅숏을 삭제 하는 방법 

스냅숏은 저장소 용량을 사용 합니다. 따라서 일반적으로 무기한 유지 되지 않습니다. 데이터 보호, 보존 및 복구를 위해 다양 한 시점에서 생성 된 많은 스냅숏이 RPO, RTO 및 보존 SLA 요구 사항에 따라 특정 기간 동안 온라인으로 유지 됩니다. 그러나 오래 된 스냅숏은 storage 서비스에 보관 하지 않아도 되 고 공간을 확보 하기 위해 삭제 해야 할 수 있습니다. 언제 든 지 관리자가 모든 스냅숏을 삭제할 수 있습니다 (반드시 만들 필요는 없음). 

> [!IMPORTANT]
> 스냅숏 삭제 작업은 실행 취소할 수 없습니다. 데이터 보호 및 보존을 위해 볼륨의 오프 라인 복사본을 유지 해야 합니다. 

스냅숏이 삭제 되 면 해당 스냅숏의 모든 포인터가 기존 데이터 블록으로 제거 됩니다. 데이터 블록에이를 가리키는 포인터가 더 이상 없으면 (활성 볼륨 또는 볼륨의 다른 스냅숏) 데이터 블록이 나중에 사용 하기 위해 사용 가능한 볼륨 공간으로 반환 됩니다. 따라서 스냅숏을 제거 하면 일반적으로 이전에 만든 스냅숏에서 데이터 블록이 캡처될 수 있으므로 활성 볼륨에서 데이터를 삭제 하는 것 보다 볼륨의 용량이 더 늘어납니다. 

다음 다이어그램은 볼륨에서 스냅숏 3 삭제의 저장소 사용량에 미치는 영향을 보여 줍니다.  

![스냅숏 삭제의 저장소 소비 효과를 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

[볼륨 및 스냅숏 사용량을 모니터링](azure-netapp-files-metrics.md#volumes) 하 고 응용 프로그램, 활성 볼륨 및 스냅숏 사용이 상호 작용 하는 방식을 이해 해야 합니다. 

스냅숏 삭제를 관리 하는 방법에 대 한 [스냅숏 삭제](azure-netapp-files-manage-snapshots.md#delete-snapshots) 를 참조 하세요. 이 프로세스를 자동화 하는 방법에 대 한 [스냅숏 정책 관리](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [NetApp Azure Files를 사용하여 스냅샷 관리](azure-netapp-files-manage-snapshots.md)
* [볼륨 및 스냅숏 메트릭 모니터링](azure-netapp-files-metrics.md#volumes)
* [스냅샷 정책 문제 해결](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅숏 101 비디오](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [NetApp Snapshot-NetApp 비디오 라이브러리](https://tv.netapp.com/detail/video/2579133646001/snapshot)



