---
title: "Azure Site Recovery에 대한 복제 설정 | Microsoft Docs"
description: "Site Recovery를 배포하고 VMM 클라우드의 Hyper-V VM을 Azure에 복제, 장애 조치(Failover) 및 복구하는 작업을 어떻게 조정해야 하는지 설명합니다."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Azure에 대한 VMware의 복제 정책 관리


## <a name="create-a-new-replication-policy"></a>새 복제 정책 만들기

1. 왼쪽에 있는 메뉴에서 '관리 -> 사이트 복구 인프라'를 클릭합니다. 
2. 'VMware 및 실제 컴퓨터의 경우' 섹션 아래에서 '복제 정책'을 선택합니다.
3. 위쪽에서 '+복제 정책'을 클릭합니다.

    ![복제 정책 만들기](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. 정책 이름을 입력합니다.

5. RPO 임계값에서 RPO 제한을 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
6. 복구 지점 보존에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다. 

    > [!NOTE] 
    > 프리미엄 저장소에 복제된 컴퓨터의 경우 최대 24시간 보존이 지원되고 표준 저장소에 복제된 컴퓨터의 경우 72시간 보존이 지원됩니다.
    
    > [!NOTE] 
    > 장애 복구에 대한 복제 정책은 자동으로 생성됩니다.

7. [응용 프로그램 일치 스냅숏 빈도]에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(분)를 지정합니다.

8. '확인'을 클릭합니다. 30초에서 1분 내에 정책을 만들어야 합니다.

![복제 정책 만들기](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>복제 정책과 구성 서버 연결
1. 구성 서버를 연결하려는 복제 정책을 클릭합니다.
2. 맨 위에서 '연결'을 클릭합니다.
![복제 정책 만들기](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. 서버 목록에서 '구성 서버'를 선택합니다.
4. 확인을 클릭합니다. 약 1~2분 안에 구성 서버를 연결해야 합니다.

![복제 정책 만들기](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>복제 정책 편집
1. 복제 설정을 편집하려는 복제 정책을 클릭합니다.
![복제 정책 만들기](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. 위에서 '설정 편집'을 클릭합니다.
![복제 정책 만들기](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. 필요에 따라 설정을 변경합니다.
4. 맨 위에서 '저장'을 클릭합니다. 해당 복제 정책을 사용하는 VM 개수에 따라 약 2~5분 내에 정책을 저장해야 합니다.

![복제 정책 만들기](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>복제 정책에서 구성 서버 연결
1. 구성 서버를 연결하려는 복제 정책을 클릭합니다.
2. 맨 위에서 '분리'를 클릭합니다.
3. 서버 목록에서 '구성 서버'를 선택합니다.
4. 확인을 클릭합니다. 약 1~2분 안에 구성 서버를 분리해야 합니다.
    
    > [!NOTE] 
    > 하나 이상의 복제된 항목이 정책을 사용하는 경우 구성 서버를 분리할 수 없습니다. 구성 서버를 분리하기 전에 복제된 항목이 정책을 사용하는지 확인합니다.

## <a name="delete-replication-policy"></a>복제 정책 삭제 

1. 삭제하려는 복제 정책을 클릭합니다.
2. 삭제를 클릭합니다. 30초에서 1분 내에 정책을 삭제해야 합니다.

    > [!NOTE] 
    > 1개 이상의 구성 서버가 연결된 경우 복제 정책을 삭제할 수 없습니다. 복제 된 항목이 정책을 사용하지 않는지 확인하고 정책을 삭제하기 전에 연결된 구성 서버를 모두 삭제합니다.


<!--HONumber=Jan17_HO4-->


