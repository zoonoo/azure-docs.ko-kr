---
title: "Azure Site Recovery를 사용하여 Azure로 온-프레미스 컴퓨터 마이그레이션 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 Azure로 온-프레미스 컴퓨터를 마이그레이션하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: bbee77e669f49bdebb57121df8672a9253945b3c
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="migrate-on-premises-machines-to-azure"></a>Azure로 온-프레미스 컴퓨터 마이그레이션

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 복제, 장애 조치(Failover) 및 장애 복구(failback)를 관리하고 오케스트레이션합니다.

이 자습서는 Site Recovery를 사용하여 Azure로 온-프레미스 VM 및 물리적 서버를 마이그레이션하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 배포를 위한 필수 구성 요소 설정
> * Site Recovery에 대한 Recovery Services 자격 증명 모음 만들기
> * 온-프레미스 관리 서버 배포
> * 복제 정책 설정 및 복제 사용
> * 재해 복구 훈련을 실행하여 모든 항목이 제대로 작동하는지 확인
> * Azure에 대한 일회성 장애 조치(Failover) 실행

## <a name="overview"></a>개요

복제를 사용하도록 설정하고 Azure로 장애 조치(Failover)하여 컴퓨터를 마이그레이션합니다.


## <a name="prerequisites"></a>필수 조건

이 자습서에 대해 수행해야 할 작업은 다음과 같습니다.

- Azure 구독, Azure Virtual Network 및 저장소 계정을 포함하는 Azure 리소스를 [준비](tutorial-prepare-azure.md)합니다.
- VMware 온-프레미스 VMware 서버 및 VM을 [준비](tutorial-prepare-on-premises-vmware.md)합니다.
- 반가상화 드라이버에서 내보낸 장치는 지원되지 않습니다.


## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.
1. **Recovery Services 자격 증명 모음** > 자격 증명 모음을 클릭합니다.
2. 리소스 메뉴 >에서 **Site Recovery** > **인프라 준비** > **보호 목표**를 클릭합니다.
3. **보호 목표**에서 다음을 선택합니다.
    - **VMware**: **Azure에** > **예, VMware vSphere 하이퍼바이저 사용**을 차례로 선택합니다.
    - **물리적 컴퓨터**: **Azure에** > **가상화되지 않음/기타**를 선택합니다.
    - **Hyper-V**: **Azure에** > **예, Hyper-V 사용**을 선택합니다.


## <a name="set-up-the-source-environment"></a>원본 환경 설정

- VMware VM에 대한 원본 환경을 [설정](tutorial-vmware-to-azure.md#set-up-the-source-environment)합니다.
- 물리적 서버에 대한 원본 환경을 [설정](tutorial-physical-to-azure.md#set-up-the-source-environment)합니다.
- Hyper-V VM에 대한 원본 환경을 [설정](tutorial-hyper-v-to-azure.md#set-up-the-source-environment)합니다.

## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델을 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

## <a name="create-a-replication-policy"></a>복제 정책 만들기

- VMware VM에 대한 [복제 정책을 설정](tutorial-vmware-to-azure.md#create-a-replication-policy)합니다.


## <a name="enable-replication"></a>복제 활성화

- VMware VM에 대해 [복제를 사용하도록 설정](tutorial-vmware-to-azure.md#enable-replication)합니다.


## <a name="run-a-test-migration"></a>테스트 마이그레이션 실행

모든 것이 예상대로 작동하는지 확인할 수 있도록 Azure에 대해 [테스트 장애 조치(Failover)](tutorial-dr-drill-azure.md)를 실행합니다.


## <a name="migrate-to-azure"></a>Azure로 마이그레이션

마이그레이션할 컴퓨터에 대해 장애 조치(Failover)를 실행합니다.

1. **설정** > **복제된 항목**에서 컴퓨터 > **장애 조치(Failover)**를 클릭합니다.
2. **장애 조치(Failover)**에서 장애 조치할 **복구 지점**을 선택합니다. 최신 복구 지점을 선택합니다.
3. 암호화 키 설정은 이 시나리오와 관련이 없습니다.
4. 장애 조치를 트리거하기 전에 Site Recovery에서 원본 가상 컴퓨터를 종료하려고 시도하는 경우 **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다**를 선택합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
5. Azure VM이 예상대로 Azure에 표시되는지 확인합니다.
6. **복제된 항목**에서 VM를 마우스 오른쪽 단추로 클릭하고 **마이그레이션 완료**를 클릭합니다. 그러면 마이그레이션 프로세스가 완료되고, VM에 대한 복제가 중지되고, VM에 대한 Site Recovery 청구가 중지됩니다.

    ![마이그레이션 완료](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **진행 중인 장애 조치(Failover) 취소 안 함**: 장애 조치(Failover)를 시작하기 전에 VM 복제가 중지됩니다. 진행 중인 장애 조치(Failover)를 취소하면 장애 조치(Failover)가 중지되지만 VM은 다시 복제되지 않습니다.

일부 시나리오에서는 장애 조치(Failover)를 위해서는 추가 처리가 필요하며 이러한 작업을 완료하는 데는 약 8~10분이 소요됩니다. 물리적 서버, VMware Linux 컴퓨터, DHCP 서비스가 사용되도록 설정되지 않은 VMware VM과 부팅 드라이버인 storvsc, vmbus, storflt, intelide, atapi가 없는 VMware VM의 경우 테스트 장애 조치(Failover)가 더 오래 걸릴 수 있습니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure로 마이그레이션한 후 Azure VM을 다른 지역에 복제](site-recovery-azure-to-azure-after-migration.md)

