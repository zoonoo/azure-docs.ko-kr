---
title: "Azure Site Recovery를 사용하여 Azure VM을 다른 Azure 지역으로 복제할 수 있도록 설정 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 Azure VM을 다른 Azure 지역으로 복제할 수 있도록 설정하는 단계를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a309644f-d36b-4188-bba7-ad45a2d9bede
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 8/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: f426ba4341e61d3c7da820d7d5097b217e94ca0e
ms.contentlocale: ko-kr
ms.lasthandoff: 08/02/2017

---


# <a name="step-5-enable-replication-for-azure-vms"></a>5단계: Azure VM을 복제하도록 설정


[Recovery Services 자격 증명 모음](azure-to-azure-walkthrough-vault.md)을 설정한 후에는 이 문서를 참조하여 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 VM(가상 컴퓨터)을 다른 Azure 지역으로 복제할 수 있도록 설정합니다. 복제를 사용하도록 설정하려면 원본 및 대상 설정을 지정하고, 복제 정책을 확인하고, 복제할 VM을 선택합니다.

- 이 문서의 단계를 완료하면 Azure VM이 보조 Azure 지역으로 복제됩니다.
- 이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 질문하세요.

>[!NOTE]
>
> Azure VM 복제는 현재 미리 보기로 제공됩니다.


## <a name="select-the-source"></a>원본 선택

1. Recovery Services 자격 증명 모음에서 자격 증명 모음 이름 > **+복제**를 클릭합니다.
2. **원본**에서 **Azure - 미리 보기**를 선택합니다.
2. **원본 위치**에서 VM이 현재 실행 중인 원본 Azure 지역을 선택합니다.
3. VM의 **Azure 가상 컴퓨터 배포 모델**(**Resource Manager** 또는 **클래식**)을 선택합니다.
4. **원본 리소스 그룹**(Resource Manager VM의 경우) 또는 **클라우드 서비스**(클래식 VM의 경우)를 선택합니다.
5. **확인** 을 클릭하여 설정을 저장합니다.

    ![원본 구성](./media/azure-to-azure-walkthrough-enable-replication/source.png)

## <a name="select-the-vms"></a>VM 선택

Site Recovery는 구독 및 리소스 그룹/클라우드 서비스와 연결된 VM 목록을 검색합니다.

1. **Virtual Machines**에서 복제할 VM을 선택합니다.
2. **확인**을 클릭합니다.

    ![VM 선택](./media/azure-to-azure-walkthrough-enable-replication/vms.png)


## <a name="configure-settings"></a>설정 구성

Site Recovery는 대상 지역의 기본 설정(원본 지역 설정 기반)과 복제 정책을 제공합니다.

   - **대상 위치**: 재해 복구에 사용할 대상 지역입니다. 대상 위치가 Site Recovery 자격 증명 모음의 위치와 일치하는 것이 좋습니다.
   - **대상 리소스 그룹**: 장애 조치(failover) 후 대상 지역의 Azure VM이 소속될 리소스 그룹입니다. 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 리소스 그룹을 만듭니다.
   - **대상 가상 네트워크**: 장애 조치(failover) 후 대상 지역의 Azure VM이 배치될 네트워크입니다. 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 가상 네트워크(및 서브넷)를 만듭니다. 이 네트워크는 원본 네트워크에 매핑됩니다. 원본 위치와 대상 위치에 동일한 IP 주소를 유지해야 하는 경우 VM 장애 조치(failover) 후 특정 IP 주소를 할당할 수 있습니다.
   - **저장소 계정 캐시**: Site Recovery는 원본 영역의 저장소 계정을 사용합니다. 원본 VM의 변경 내용이 이 계정으로 전송된 후 대상 위치에 복제됩니다.
   - **저장소 계정을 대상**: 기본적으로 Site Recovery는 대상 지역에 새 저장소 계정을 만들어서 원본 VM 저장소 계정을 미러링합니다.
   -  **대상 가용성 집합:** 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 가용성 집합을 만듭니다.
   - **복제 정책 이름**: 정책 이름입니다.
   - **복구 지점 유지**: 기본적으로 Site Recovery는 복구 지점을 24시간 동안 유지합니다. 이 값을 1-72시간 사이에서 구성할 수 있습니다.
   - **앱 일치 스냅숏 빈도**: 기본적으로 Site Recovery는 4시간마다 앱 일치 스냅숏을 만듭니다. 이 값을 1-12시간 사이에서 구성할 수 있습니다. 데이터가 지속적으로 복제됩니다.
    - 크래시 일치 복구 지점을 만들면 일관적인 데이터 쓰기-순서가 유지됩니다. 앱이 데이터 불일치 없이 크래시에서 복구되도록 설계된 경우 대부분은 이 유형의 복구 지점이면 충분합니다.
    - 몇 분마다 크래시 일치 복구 지점이 생성됩니다. 이러한 복구 지점을 사용하여 VM을 장애 조치(failover)하고 복구하면 복구 지점 목표(RPO)가 시간 순으로 제공됩니다.
    - 앱 일치 복구 지점(쓰기 순서 일관성 외에도)은 실행 중인 앱이 모든 작업을 완료하고 버퍼를 디스크에 플러시(응용 프로그램 정지)하도록 보장합니다. SQL Server, Oracle, Exchange 등의 데이터베이스 앱에는 이러한 복구 지점을 사용하는 것이 좋습니다.

    ![설정 구성](./media/azure-to-azure-walkthrough-enable-replication/settings.png)


### <a name="modify-settings"></a>설정 수정

대상 및 복제 정책 설정을 수정하려면 다음을 수행합니다.

1. 대상 설정을 확인 또는 수정하려면 **설정**을 클릭합니다.
2. 기본 대상 설정을 재정의하려면 **사용자 지정**을 클릭합니다. 대상 리소스 그룹, 가상 네트워크, 가용성 집합 및 대상 저장소 계정을 지정할 수 있습니다. VM이 원본 영역에 있는 집합의 일부인 경우 가용성 집합만 추가할 수 있습니다.

    ![설정 구성](./media/azure-to-azure-walkthrough-enable-replication/customize-target.png)

3. 복구 지점 및 앱 일치 스냅숏의 복제 설정을 재정의하려면 **복제 정책** 옆에 있는 **사용자 지정**을 클릭합니다.

    ![설정 구성](./media/azure-to-azure-walkthrough-enable-replication/customize-policy.png)

4. 대상 리소스 프로비전을 시작하려면 **대상 리소스 만들기**를 클릭합니다. 프로비전에 1분 정도 걸립니다. 프로비전하는 동안 블레이드를 닫지 마세요. 블레이드를 닫으면 처음부터 다시 시작해야 합니다.




## <a name="enable-replication"></a>복제 활성화

1. **설정에서** **복제 사용**을 클릭합니다. 그러면 선택한 VM의 초기 복제가 가능합니다. 초기 복제 상태가 새로 고쳐질 때까지 시간이 걸릴 수 있습니다. 최신 상태를 가져오려면 **새로 고침**을 클릭합니다.

2. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다.

3. **설정** > **복제된 항목**에서 VM의 상태 및 초기 복제 진행률을 볼 수 있습니다. VM을 클릭하여 해당 설정으로 드릴다운합니다.



## <a name="next-steps"></a>다음 단계

[6단계: 테스트 장애 조치(failover) 실행](azure-to-azure-walkthrough-test-failover.md)으로 이동

