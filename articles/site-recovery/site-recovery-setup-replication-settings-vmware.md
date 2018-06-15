---
title: Azure Site Recovery에 대한 복제 설정 | Microsoft Docs
description: Site Recovery를 배포하여 VMM 클라우드의 Hyper-V VM을 Azure에 복제, 장애 조치 및 복구하는 작업을 조정하는 방법에 대해 설명합니다.
services: site-recovery
documentationcenter: ''
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/22/2018
ms.author: sutalasi
ms.openlocfilehash: 0a567f31bf1991d4c2a95468d2abc31c51a878f3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767427"
---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Azure에 대한 VMware의 복제 정책 관리


## <a name="create-a-replication-policy"></a>복제 정책 만들기

1. **관리** > **Site Recovery 인프라**를 선택합니다.
2. **VMware 및 실제 컴퓨터의 경우** 아래에서 **복제 정책**을 선택합니다.
3. **+ 복제 정책**을 선택합니다.

    ![복제 정책 만들기](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. 정책 이름을 입력합니다.

5. **RPO 임계값**에서 RPO 제한을 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
6. **복구 지점 보존**에서 각 복구 지점의 보존 기간을 시간 단위로 지정합니다. 보호된 컴퓨터는 보존 기간 내의 모든 지점으로 복구할 수 있습니다.

    > [!NOTE]
    > 프리미엄 저장소에 복제된 컴퓨터의 경우 최대 24시간 동안 보존하도록 지원됩니다. 표준 저장소에 복제된 컴퓨터의 경우 최대 72시간 동안 보존하도록 지원됩니다.

    > [!NOTE]
    > 장애 복구에 대한 복제 정책은 자동으로 생성됩니다.

7. **앱 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(분)를 지정합니다.

8. **확인**을 클릭합니다. 정책은 30~60초 내에 만들어야 합니다.

![복제 정책 생성](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-a-configuration-server-with-a-replication-policy"></a>복제 정책과 구성 서버 연결
1. 구성 서버를 연결하려는 복제 정책을 선택합니다.
2. **연결**을 클릭합니다.
![구성 서버 연결](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. 서버 목록에서 구성 서버를 선택합니다.
4. **확인**을 클릭합니다. 구성 서버는 1~2분 내에 연결해야 합니다.

![구성 서버 연결](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-a-replication-policy"></a>복제 정책 편집
1. 복제 설정을 편집하려는 복제 정책을 선택합니다.
![복제 정책 편집](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. **설정 편집**을 클릭합니다.
![복제 정책 설정 편집](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. 필요에 따라 설정을 변경합니다.
4. **저장**을 클릭합니다. 정책은 해당 복제 정책을 사용하는 VM 개수에 따라 2~5분 내에 저장해야 합니다.

![복제 정책 저장](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-a-configuration-server-from-a-replication-policy"></a>복제 정책에서 구성 서버 분리
1. 구성 서버를 연결하려는 복제 정책을 선택합니다.
2. **분리**를 클릭합니다.
3. 서버 목록에서 구성 서버를 선택합니다.
4. **확인**을 클릭합니다. 구성 서버는 1~2분 내에 분리해야 합니다.

    > [!NOTE]
    > 정책을 사용하는 복제된 항목이 하나 이상 있는 경우 구성 서버를 분리할 수 없습니다. 구성 서버를 분리하기 전에 정책을 사용하는 복제된 항목이 있는지 확인합니다.

## <a name="delete-a-replication-policy"></a>복제 정책 삭제

1. 삭제하려는 복제 정책을 선택합니다.
2. **삭제**를 클릭합니다. 정책은 30~60초 내에 삭제해야 합니다.

    > [!NOTE]
    > 구성 서버가 하나 이상 연결되어 있는 경우 복제 정책을 삭제할 수 없습니다. 정책을 사용하는 복제된 항목이 없는지 확인하고 정책을 삭제하기 전에 연결된 구성 서버를 모두 삭제합니다.
