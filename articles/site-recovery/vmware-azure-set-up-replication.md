---
title: Azure 사이트 복구를 사용 하 고 VM웨어 재해 복구에 대 한 복제 정책 설정 | 마이크로 소프트 문서
description: Azure Site Recovery를 사용하여 Azure에 대한 VMware 재해 복구의 복제 설정을 구성하는 방법에 대해 설명합니다.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257122"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>VMware 재해 복구를 위한 복제 정책 구성 및 관리

이 아티클에서는 VMware VM을 Azure에 복제할 때 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 복제 정책을 구성하는 방법을 설명합니다.

## <a name="create-a-policy"></a>정책 만들기

1. 사이트 복구 인프라 **관리를** > **선택합니다.**
2. **VMware 및 실제 컴퓨터의 경우**에서 **복제 정책**을 선택합니다.
3. **+복제 정책**을 클릭하고 정책 이름을 지정합니다.
4. **RPO 임계값에서**RPO 제한을 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
5. **복구 지점 보존**에서 각 복구 지점의 보존 기간을 시간 단위로 지정합니다. 보호된 컴퓨터는 보존 기간 내의 모든 지점으로 복구할 수 있습니다. Premium Storage에 복제된 컴퓨터의 경우 최대 24시간 동안 보존하도록 지원됩니다. 표준 스토리지에서 최대 72시간 동안 지원됩니다.
6. **앱 일관된 스냅숏 빈도에서**드롭다운에서 응용 프로그램 일관된 스냅숏을 포함하는 복구 지점을 만드는 빈도(시간)를 선택합니다. 응용 프로그램 일관성 포인트 생성을 해제하려면 드롭다운에서 "끄기" 값을 선택합니다.
7. **확인**을 클릭합니다. 정책은 30~60초 내에 만들어야 합니다.

복제 정책을 만들 때 접미사 "failback"을 사용하여 장애 복구 복제 일치 정책이 자동으로 만들어집니다. 정책을 만든 후에 해당 정책 > **설정 편집**을 선택하여 편집할 수 있습니다.

## <a name="associate-a-configuration-server"></a>구성 서버 연결

온-프레미스 구성 서버와 복제 정책을 연결합니다.

1. **연결**을 클릭하여 구성 서버를 선택합니다.

    ![구성 서버 연결](./media/vmware-azure-set-up-replication/associate1.png)
2. **확인**을 클릭합니다. 구성 서버는 1~2분 내에 연결해야 합니다.

    ![구성 서버 연결](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>정책 편집

복제 정책을 만든 후 수정할 수 있습니다.

- 정책의 변경 내용은 정책을 사용하는 모든 컴퓨터에 적용됩니다.
- 복제된 컴퓨터를 다른 복제 정책과 연결하려면 관련 컴퓨터에 대한 보호를 비활성화하고 다시 사용하도록 설정해야 합니다.

다음과 같이 정책을 편집합니다.
1. **사이트 복구 인프라** > **복제 정책** **관리를** > 선택합니다.
2. 수정할 복제 정책을 선택합니다.
3. **설정 편집을**클릭하고 필요에 따라 RPO 임계값/복구 지점 보존 시간/앱 일관된 스냅샷 빈도 필드를 업데이트합니다.
4. 응용 프로그램 일관성 포인트 생성을 해제하려면 앱 **일관된 스냅숏 빈도**필드의 드롭다운에서 "끄기" 값을 선택합니다.
5. **저장**을 클릭합니다. 정책은 30~60초 안에 업데이트되어야 합니다.



## <a name="disassociate-or-delete-a-replication-policy"></a>복제 정책 분리 또는 삭제

1. 복제 정책을 선택합니다.
    a. 정책 구성 서버에서 연결을 분리하려면 복제된 컴퓨터가 정책을 사용하지 않도록 합니다. 그런 다음, **분리**를 클릭합니다.
    b. 정책을 삭제하려면 해당 정책이 구성 서버와 연결되지 않도록 합니다. 그런 다음 **에서 삭제를 클릭합니다.** 삭제하는 데 30~60초가 걸립니다.
2. **확인**을 클릭합니다.
