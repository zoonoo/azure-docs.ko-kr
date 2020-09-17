---
title: Azure NetApp Files 볼륨 영역 간 복제를 사용 하기 위한 요구 사항 및 고려 사항 | Microsoft Docs
description: Azure NetApp Files의 볼륨 지역 간 복제 기능을 사용 하기 위한 요구 사항 및 고려 사항을 설명 합니다.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: f2a50872fdb71419a0c3f068712ec67523a098e0
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708785"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>지역 간 복제 사용을 위한 요구 사항 및 고려 사항 

Azure NetApp Files의 [볼륨의 지역 간 복제 기능 사용](cross-region-replication-create-peering.md) 에 대 한 다음 요구 사항 및 고려 사항에 유의 하세요.  

## <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항 

* 지역 간 복제 기능은 현재 공개 미리 보기로 제공 됩니다. [Azure NetApp Files 지역 간 복제 waitlist 제출 페이지](https://aka.ms/anfcrrpreviewsignup)를 통해 기능에 액세스 하기 위한 waitlist 요청을 제출 해야 합니다. 지역 간 복제 기능을 사용 하기 전에 Azure NetApp Files 팀의 공식 확인 전자 메일을 기다리십시오.
* Azure NetApp Files 복제는 특정 고정 영역 쌍 에서만 사용할 수 있습니다. [지원 되는 지역 쌍](cross-region-replication-introduction.md#supported-region-pairs)을 참조 하세요. 
* SMB 볼륨은 NFS 볼륨과 함께 지원 됩니다. SMB 볼륨을 복제 하려면 원본 및 대상 NetApp 계정에 Active Directory 연결이 필요 합니다. 대상 AD 연결에는 DNS 서버에 대 한 액세스 권한이 있어야 합니다. 그렇지 않으면 대상 지역의 위임 된 서브넷에서 연결할 수 있는 도메인 컨트롤러가 추가 됩니다. 자세한 내용은 [Active Directory 연결에 대 한 요구 사항](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)을 참조 하세요. 
* 대상 계정은 원본 볼륨 영역에서 다른 지역에 있어야 합니다. 다른 지역에서 기존 NetApp 계정을 선택할 수도 있습니다.  
* Azure NetApp Files 복제는 현재 여러 구독을 지원 하지 않습니다. 모든 복제는 단일 구독에서 수행 해야 합니다.
* 지역별 단일 구독 내에서 복제할 볼륨을 최대 5 개까지 설정할 수 있습니다. 지원 티켓을 열어 5 개의 복제 대상 볼륨 (한 지역의 구독 당)의 기본 할당량 증가를 요청할 수 있습니다. 
* 인터페이스가 원본 볼륨에 새로 추가 된 스냅숏을 반영 하는 데 최대 5 분이 걸릴 수 있습니다.  
* 연계 및 팬 in/out 토폴로지는 지원 되지 않습니다.
* 스냅숏에서 생성 된 원본 볼륨에 대 한 볼륨 복제 구성은 지금은 지원 되지 않습니다.
* 지역 간 복제를 설정 하 고 나면 복제 프로세스에서 원본 볼륨과 대상 볼륨 간의 참조를 제공 하는 *snapmirror 스냅숏을* 만듭니다. Snapmirror 스냅숏은 모든 증분 전송에 대해 새 스냅숏이 생성 될 때 자동으로 순환 됩니다. 복제 관계와 볼륨이 삭제 될 때까지 snapmirror 스냅숏을 삭제할 수 없습니다. 
* 복제 관계가 활성 상태 이거나 손상 된 경우 및 복제 관계가 삭제 된 후에 복제 관계의 원본 볼륨에서 수동 스냅숏을 삭제할 수 있습니다. 복제 관계가 끊어질 때까지 대상 볼륨의 수동 스냅숏을 삭제할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [복제 피어 링 만들기](cross-region-replication-create-peering.md)
* [복제 관계의 상태를 표시 합니다.](cross-region-replication-display-health-status.md)
* [재해 복구 관리](cross-region-replication-manage-disaster-recovery.md)
* [볼륨 복제 메트릭](azure-netapp-files-metrics.md#replication)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)


