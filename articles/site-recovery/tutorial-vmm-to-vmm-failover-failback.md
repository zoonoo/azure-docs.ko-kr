---
title: "Site Recovery를 사용하여 복제된 Hyper-V VM을 보조 데이터 센터로 장애 조치(Failover) 및 장애 복구(failback) | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Hyper-V VM을 보조 온-프레미스 사이트로 장애 조치(Failover)하고, 주 사이트로 장애 복구(failback)하는 방법 알아보기"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44a662fa-2e7a-4996-86df-fdd6d6f5dedf
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8f139070de99c4249207d048d445e86dd41e9060
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>복제된 Hyper-V VM을 보조 온-프레미스 사이트로 장애 조치(Failover) 및 장애 복구(failback)

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 복제, 장애 조치(Failover) 및 장애 복구(failback)를 관리하고 오케스트레이션합니다.

이 자습서에는 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 Hyper-V VM을 보조 VMM 사이트로 장애 조치(Failover)하는 방법을 설명합니다. 장애 조치(Failover)한 후에 온-프레미스 사이트가 사용 가능해지면 장애 복구(failback)합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 주 VMM 클라우드에서 보조 VMM 클라우드로 Hyper-V VM 장애 조치(Failover)
> * 보조 사이트에서 주 사이트로 다시 보호 및 장애 복구(failback)
> * 필요에 따라 주 사이트에서 보조 사이트로 다시 복제 시작

## <a name="overview"></a>개요

장애 조치(Failover) 및 장애 복구(failback)는 다음 3단계로 진행됩니다.

1. **보조 사이트로 장애 조치(Failover)**: 주 사이트에서 보조 사이트로 컴퓨터를 장애 조치(Failover)합니다.
2. **보조 사이트에서 장애 복구(failback)**: VM을 보조 사이트에서 주 사이트로 복제하고, 예정된 장애 조치(Failover)를 실행하여 장애 복구(failback)합니다.
3. 예정된 장애 조치(Failover) 후에는 선택적으로 주 사이트에서 보조 사이트로 복제가 다시 시작됩니다.


## <a name="fail-over-to-a-secondary-site"></a>보조 사이트로 장애 조치(Failover)

### <a name="failover-prerequisites"></a>장애 조치 필수 구성 요소

[재해 복구 훈련](tutorial-dr-drill-secondary.md)을 완료하여 모든 항목이 예상대로 작동하는지 확인합니다.


### <a name="run-a-failover-from-primary-to-secondary"></a>주 사이트에서 보조 사이트로의 장애 조치(Failover) 실행

Hyper-V VM에 대해 주기적이거나 예정된 장애 조치(Failover)를 실행할 수 있습니다.

- 예기치 않은 중단에 대비해서 주기적인 장애 조치(Failover)를 사용합니다. 이 장애 조치(Failover)를 실행하면 Site Recovery에서 보조 사이트에 VM을 만들고 작동시킵니다. 특정 복구 지점에 대해 장애 조치(Failover)를 실행합니다. 사용하는 복구 지점에 따라 데이터 손실이 발생할 수 있습니다.
- 유지 관리 또는 예정된 중단에 대해서는 계획된 장애 조치(Failover)를 사용할 수 있습니다. 이 옵션은 데이터 손실을 발생하지 않습니다. 예정된 장애 조치(Failover)가 트리거되면 원본 VM이 종료됩니다. 동기화되지 않은 데이터가 동기화되고 장애 조치(Failover)가 트리거됩니다. 
- 
이 절차에서는 주기적 장애 조치(Failover)를 실행하는 방법을 설명합니다.


1. **설정** > **복제된 항목**에서 VM > **장애 조치(Failover)**를 클릭합니다.
2. **장애 조치(Failover)**에서 장애 조치할 **복구 지점**을 선택합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
    - **최신**(기본값): 이 옵션은 먼저 Site Recovery로 전송된 모든 데이터를 처리합니다. 이 옵션은 장애 조치(Failover) 후에 생성된 복제본 VM은 장애 조치(Failover)가 트리거되었을 때 Site Recovery로 복제된 모든 데이터를 보유하므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
    - **가장 최근에 처리됨**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 복구 지점으로 장애 조치(Failover)합니다. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO(복구 시간 목표)를 제공합니다.
    - **최신 앱 일치**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 앱 일치 복구 지점으로 장애 조치(Failover)합니다. 
3. 암호화 키는 이 시나리오와 관련이 없습니다.
4. 장애 조치를 트리거하기 전에 Site Recovery에서 원본 VM을 종료하려고 시도하는 경우 **장애 조치(Failover)를 시작하기 전에 컴퓨터를 종료합니다**를 선택합니다. 또한 Site Recovery는 장애 조치(Failover)를 트리거하기 전에 보조 사이트로 아직 전송되지 않은 온-프레미스 데이터를 동기화하려고 합니다. 종료가 실패하더라도 장애 조치(Failover)는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
5. 이제 보조 VMM 클라우드의 VM을 볼 수 있습니다.
6. VM을 확인 한 후 장애 조치(Failover)를 **커밋**합니다. 그러면 사용 가능한 복구 지점이 모두 삭제됩니다.

> [!WARNING]
> **진행 중인 장애 조치(Failover) 취소 안 함**: 장애 조치(Failover)를 시작하기 전에 VM 복제가 중지됩니다. 진행 중인 장애 조치(Failover)를 취소하면 장애 조치(Failover)가 중지되지만 VM은 다시 복제되지 않습니다.  


## <a name="reprotect-and-fail-back-from-secondary-to-primary"></a>다시 보호하고 보조 사이트에서 주 사이트로 장애 복구(Failback)

### <a name="prerequisites-for-failback"></a>장애 복구(Failback)에 대한 필수 구성 요소

장애 복구(Failback)를 완료하려면 주 및 보조 VMM 서버가 Site Recovery에 연결되어 있는지 확인합니다.


### <a name="reprotect-and-fail-back"></a>다시 보호 및 장애 복구(Failback)

보조 사이트에서 주 사이트로의 복제를 시작하고 주 사이트로 장애 복구(Failback)합니다. 주 사이트에서 VM을 다시 실행한 후에는 보조 사이트로 다시 복제할 수 있습니다.  

1. **설정** > **복제된 항목**에서 VM을 클릭하고 **역방향 복제**를 사용하도록 설정합니다. VM은 주 사이트로 다시 복제되기 시작합니다.
2. VM > **계획된 장애 조치(Failover)**를 클릭합니다.
3. **계획된 장애 조치(Failover) 확인**에서 장애 조치(Failover) 방향(보조 VMM 클라우드에서)을 확인하고 원본 및 대상 위치를 선택합니다. 
4. **데이터 동기화**에서 동기화 방법을 지정합니다.
    - **장애 조치(Failover) 전 데이터 동기화(델타 변경 내용만 동기화)** - 이 옵션에서는 VM을 종료하지 않고 동기화하기 때문에 VM 가동 중지 시간이 최소로 유지됩니다. 수행하는 작업:
        - 복제본 VM의 스냅숏을 생성한 후 주 Hyper-V 호스트로 복사합니다. 복제본 VM은 계속 실행됩니다.
        - 복제본 VM을 종료하여 새로 변경되지 않도록 합니다. 최종 델타 변경 집합은 주 사이트로 전송되고, 주 사이트의 VM이 시작됩니다.
    - **장애 조치 중에만 데이터 동기화(전체 다운로드)** - 오랜 시간 동안 보조 사이트에서 실행한 경우 이 옵션을 사용합니다. 이 옵션에서는 여러 디스크가 변경되고 체크섬 계산에 시간이 소비되지 않으므로 더 빠릅니다. 이 옵션은 디스크 다운로드를 수행합니다. 주 VM이 삭제된 경우에도 유용합니다.
5. 암호화 키는 이 시나리오와 관련이 없습니다.
6. 장애 조치를 시작합니다. **작업** 탭에서 장애 조치 진행 상황을 따를 수 있습니다.
7. 장애 조치(Failover) 전에 데이터를 동기화하도록 선택한 경우, 초기 데이터 동기화가 완료되고 보조 사이트에서 복제본 VM을 종료할 준비가 되면, **작업** > 계획된 장애 조치(Failover) 작업 이름 > **장애 조치(Failover) 완료**를 차례로 클릭합니다. 이렇게 하면 보조 VM이 종료되고, 최신 변경 내용이 주 사이트로 전송되고, 주 VM이 시작됩니다.
8. 주 VMM 클라우드에서 VM를 사용할 수 있는지 확인합니다.
9. 주 VM은 이제 커밋 보류 중 상태가 됩니다. **커밋**을 클릭하여 장애 조치(Failover)를 커밋합니다.
10. 주 VM에서 보조 사이트로의 복제를 다시 시작하려면 **역방향 복제**를 사용하도록 설정합니다.


> [!NOTE]
> 역방향 복제는 복제본 VM이 해제된 후에 발생한 변경 내용만 복제하며 델타 변경 내용만 전송됩니다.


