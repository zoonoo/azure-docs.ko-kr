---
title: "Azure Site Recovery를 사용하여 Azure에 복제되는 물리적 서버에 대해 복제 설정 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 물리적 서버에 대해 Azure로 복제를 설정하는 데 필요한 단계를 요약합니다."
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 42f35c53eec06a346281fd90c97aecfd2269307d
ms.contentlocale: ko-kr
ms.lasthandoff: 06/29/2017


---
# <a name="step-10-enable-replication-for-physical-servers-to-azure"></a>10단계: Azure에 물리적 서버 복제 설정


이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure에 온-프레미스 Windows/Linux 물리적 서버를 복제하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## <a name="before-you-start"></a>시작하기 전에

- 서버에는 [모바일 서비스 구성 요소가 설치](physical-walkthrough-install-mobility.md)되어 있어야 합니다.
- 복제를 사용하도록 설정하는 경우 푸시 설치용으로 VM이 준비되면 프로세스 서버가 자동으로 모바일 서비스를 설치합니다.
- 컴퓨터에서 복제를 사용하도록 설정하면 Azure Storage를 사용하고 Azure VM을 만들 수 있도록 Azure 사용자 계정에 특정 [권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 필요합니다.
- 서버에 대한 IP 주소를 추가하거나 수정하는 경우 변경 사항이 적용되어 포털에 표시되는 데 15분 이상이 걸릴 수 있습니다.


## <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

기본적으로 컴퓨터의 모든 디스크가 복제됩니다. 디스크를 복제에서 제외할 수 있습니다. 예를 들어 임시 데이터 또는 컴퓨터나 응용 프로그램이 다시 시작할 때마다 새로 고쳐지는 데이터(예: pagefile.sys 또는 SQL Server tempdb)가 포함된 디스크를 복제하고 싶지 않을 수 있습니다. [자세히 알아보기](site-recovery-exclude-disk.md)

## <a name="replicate-servers"></a>서버 복제

1. **2단계: 응용 프로그램 복제** > **원본**을 클릭합니다.
2. **원본**에서 구성 서버를 선택합니다.
3. **컴퓨터 형식**에서 **물리적 컴퓨터**를 선택합니다.
4. 프로세스 서버를 선택합니다. 추가 프로세스 서버를 만들지 않은 경우 이 프로세스 서버가 구성 서버가 됩니다. 그런 후 **OK**를 클릭합니다.
5. **대상**에서 장애 조치 VM을 만들려는 구독 및 리소스 그룹을 선택합니다. 장애 조치 VM에 대해 Azure(클래식 또는 리소스 관리)에서 사용할 배포 모델을 선택합니다.
6. 데이터 복제에 사용할 Azure Storage 계정을 선택합니다. 이미 설정한 계정을 사용하지 않으려면 새로 만들 수 있습니다.
7. 장애 조치(Failover) 후 Azure VM이 연결될 **Azure 네트워크** 및 **서브넷**을 선택합니다. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성** 을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다. 기존 네트워크를 사용하지 않으려면 하나를 만들 수 있습니다.

    ![복제 활성화](./media/physical-walkthrough-enable-replication/targetsettings.png)

8. **물리적 컴퓨터**에서 **+물리적 컴퓨터**를 클릭하고 **이름** 및 **IP 주소**를 입력합니다. 복제하려는 컴퓨터의 운영 체제를 선택합니다. 컴퓨터를 검색하여 목록에 표시할 때까지 몇 분이 걸립니다.
9. **속성** > **속성 구성**에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다.
10. 기본적으로 모든 디스크가 복제됩니다. **모든 디스크** 를 클릭하고 복제하지 않으려는 디스크를 지웁니다. 그런 후 **OK**를 클릭합니다. 나중에 추가 VM 속성을 설정할 수 있습니다.

    ![복제 활성화](./media/physical-walkthrough-enable-replication/enable-replication6.png)
11. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다. 정책을 수정하면 복제 중인 컴퓨터와 새 컴퓨터에 변경 내용이 적용됩니다.
12. 컴퓨터를 복제 그룹으로 수집하려는 경우 **다중 VM 일관성** 을 사용하도록 설정하고 그룹에 대한 이름을 지정합니다. 그런 후 **OK**를 클릭합니다. 다음 사항에 유의하세요.

    * 복제 그룹의 컴퓨터는 함께 복제되고 장애 조치(failover) 시 공유 크래시 일관성 및 앱 일치 복구 지점을 갖습니다.
    * 워크로드를 미러링하도록 물리적 서버를 함께 수집하는 것이 좋습니다. 다중 VM 일관성을 사용하도록 설정하면 워크로드 성능에 영향을 줄 수 있습니다. 컴퓨터가 동일한 워크로드를 실행하고 일관성이 필요한 경우에만 사용해야 합니다.

13. **복제 사용**을 클릭합니다. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.

## <a name="next-steps"></a>다음 단계

[11단계: 테스트 장애 조치(Failover) 실행](physical-walkthrough-test-failover.md)으로 이동합니다.

