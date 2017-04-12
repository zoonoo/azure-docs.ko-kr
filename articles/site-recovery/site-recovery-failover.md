---
title: "사이트 복구에서 장애 조치(failover) | Microsoft Docs"
description: "Azure Site Recovery는 가상 컴퓨터 및 실제 서버의 복제, 장애 조치 및 복구를 조정합니다. Azure로 또는 보조 데이터 센터로 장애 조치에 대해 알아봅니다."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/15/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: b818d5083f1436035185b1b0d7990b5a36716da4
ms.openlocfilehash: 1fca09ad0c9e1bc72109910cd0dcaf186d6a7c3d
ms.lasthandoff: 02/23/2017


---
# <a name="failover-in-site-recovery"></a>사이트 복구에서 장애 조치
이 문서에서는 Site Recovery에서 보호하는 가상 컴퓨터 및 물리적 서버를 장애 조치하는 방법에 대해 설명합니다. 

## <a name="prerequisites"></a>필수 조건
1. 장애 조치를 수행하기 전에 [테스트 장애 조치](site-recovery-test-failover-to-azure.md)를 수행하여 모든 것이 예상대로 작동하는지 확인합니다. 
1. 장애 조치를 수행하기 전에 대상 위치에서 [네트워크를 준비](site-recovery-network-design.md)합니다.  


## <a name="run-a-failover"></a>장애 조치(Failover) 실행
이 절차에서는 [복구 계획](site-recovery-create-recovery-plans.md)에 대한 장애 조치를 실행하는 방법을 설명합니다. 또는 **복제된 항목** 페이지에서 단일 가상 컴퓨터 또는 물리적 서버에 대한 장애 조치를 실행할 수 있습니다.


![장애 조치(Failover)](./media/site-recovery-failover/Failover.png)

1. **복구 계획** > *recoveryplan_name*을 선택합니다. **장애 조치(Failover)**를 클릭합니다. 
2. **장애 조치(Failover)** 화면에서 장애 조치할 **복구 지점**을 선택합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
    1.    **최신**(기본값): 먼저 Site Recovery 서비스로 보낸 모든 데이터를 처리한 다음 각 가상 컴퓨터에 대한 복구 지점을 만든 후에 해당 복구 지점에 장애 조치합니다. 이 옵션은 장애 조치를 트리거했을 때 Site Recovery 서비스로 복제된 모든 데이터가 장애 조치 후에 만들어진 가상 컴퓨터에 있게 되므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다. 
    1.  **가장 최근에 처리된 시점**: 복구 계획의 모든 가상 컴퓨터를 Site Recovery 서비스에서 이미 처리한 최근 복구 지점으로 장애 조치합니다. 가상 컴퓨터의 테스트 장애 조치(Failover)를 수행할 때 가장 최근에 처리된 복구 지점의 타임스탬프도 표시됩니다. 복구 계획의 장애 조치를 수행하는 경우 개별 가상 컴퓨터로 이동하여 **최근 복구 지점** 타일을 살펴보면 이 정보를 얻을 수 있습니다. 처리되지 않은 데이터를 처리하느라 소요되는 시간이 없기 때문에 이 옵션은 낮은 RTO(복구 시간 목표) 장애 조치(Failover) 옵션을 제공합니다. 
    1.    **최신 응용 프로그램 일치**: 복구 계획의 모든 가상 컴퓨터를 Site Recovery 서비스에서 이미 처리한 최신 응용 프로그램 일치 복구 지점으로 장애 조치합니다. 가상 컴퓨터의 테스트 장애 조치(Failover)를 수행할 때 최신 앱 일치 복구 지점의 타임스탬프도 표시됩니다. 복구 계획의 장애 조치를 수행하는 경우 개별 가상 컴퓨터로 이동하여 **최근 복구 지점** 타일을 살펴보면 이 정보를 얻을 수 있습니다. 
    1.    **사용자 지정**: 가상 컴퓨터의 테스트 장애 조치를 수행하는 경우 이 옵션을 사용하여 특정 복구 지점으로 장애 조치할 수 있습니다.

    > [!NOTE]
    > 복구 지점을 선택하는 옵션은 Azure로 장애 조치하는 경우에만 사용할 수 있습니다. 
    >
    > 


1. 복구 계획의 가상 컴퓨터 중 일부가 이전 실행에서 장애 조치되었고 이제 가상 컴퓨터가 원본 위치 및 대상 위치에서 모두 사용되고 있는 경우 장애 조치가 발생되어야 하는 방향은 **방향 변경** 옵션으로 결정할 수 있습니다.
1. Azure로 장애 조치하고 클라우드에 대해 데이터 암호화를 사용하는 경우(VMM 서버에서 Hyper-V 가상 컴퓨터를 보호한 경우에만 적용됨) VMM 서버에 설치하는 중에 데이터 암호화를 사용하도록 설정할 때 발행된 인증서를 **암호화 키**에서 선택합니다.
1. 장애 조치를 트리거하기 전에 Site Recovery에서 원본 가상 컴퓨터를 종료하려고 시도하는 경우 **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다**를 선택합니다. 종료가 실패하더라도 장애 조치는 계속됩니다.  
          
    > [!NOTE]
    > Hyper-V 가상 컴퓨터의 경우에도 이 옵션은 장애 조치를 트리거하기 전에 아직 서비스로 보내지 않은 온-프레미스 데이터를 동기화하려고 시도합니다. 
    >
    > 
     
1. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다. 계획되지 않은 장애 조치 중에 오류가 발생하더라도 복구 계획은 완료될 때까지 실행됩니다.
1. 장애 조치 후에 가상 컴퓨터에 로그인하여 해당 가상 컴퓨터의 유효성을 검사합니다. 가상 컴퓨터에 대한 다른 복구 지점으로 이동하려면 **복구 지점 변경** 옵션을 사용할 수 있습니다.
1. 장애 조치된 가상 컴퓨터에 만족하면 장애 조치를 **커밋**할 수 있습니다. 이 경우 서비스에서 사용할 수 있는 복구 지점을 모두 삭제하고 **복구 지점 변경** 옵션을 더 이상 사용할 수 없게 됩니다.

## <a name="planned-failover"></a>계획된 장애 조치
Site Recovery를 사용하여 보호되는 Hyper-V 가상 컴퓨터는 장애 조치와는 별도로 **계획된 장애 조치**도 지원합니다. 이는 데이터 무손실 장애 조치 옵션입니다. 계획된 장애 조치가 트리거되면 먼저 원본 가상 컴퓨터가 종료되고, 아직 동기화할 데이터가 동기화되지 않은 다음 장애 조치가 트리거됩니다. 

> [!NOTE]
> 하나의 온-프레미스 사이트에서 다른 온-프레미스 사이트로 Hyper-V 가상 컴퓨터를 장애 조치하는 경우 기본 온-프레미스 사이트로 다시 돌아가려면 먼저 가상 컴퓨터를 기본 사이트로 **역방향 복제**한 다음 장애 조치를 트리거해야 합니다. 기본 가상 컴퓨터를 사용할 수 없는 경우 **역방향 복제**를 시작하기 전에 백업에서 가상 컴퓨터를 복원해야 합니다.   
>
> 

## <a name="failover-job"></a>장애 조치 작업

![장애 조치(Failover)](./media/site-recovery-failover/FailoverJob.png)

장애 조치가 트리거되면 다음 단계를 수행합니다.

1. 필수 조건 확인: 장애 조치에 필요한 모든 조건이 충족되는지 확인합니다.
1. 장애 조치: 데이터를 처리하고 Azure 가상 컴퓨터를 만들 수 있도록 준비합니다. **최신** 복구 지점을 선택한 경우 이 단계는 서비스로 보낸 데이터에서 복구 지점을 만듭니다.
1. 시작: 이전 단계에서 처리한 데이터를 사용하여 Azure 가상 컴퓨터를 만듭니다.

> [!WARNING]
> **진행 중인 장애 조치 취소 안 함**: 장애 조치를 시작하기 전에 가상 컴퓨터에 대한 복제가 중지됩니다. 진행 중인 작업을 **취소**하면 장애 조치는 중지되지만 가상 컴퓨터는 복제를 시작하지 않습니다. 복제를 다시 시작할 수 없습니다. 
>
> 



## <a name="using-scripts-in-failover"></a>장애 조치에서 스크립트 사용
장애 조치를 수행하는 동안 특정 작업을 자동화할 수 있습니다. [복구 계획](site-recovery-create-recovery-plans.md)에서 스크립트 또는 [Azure Automation runbook](site-recovery-runbook-automation.md)을 사용하여 자동화할 수 있습니다.

## <a name="other-considerations"></a>기타 고려 사항
* **드라이브 문자** - 장애 조치 후 가상 컴퓨터에서 드라이브 문자를 유지하려면 가상 컴퓨터에 대한 **SAN 정책**을 **OnlineAll**로 설정하면 됩니다. [자세히 알아보기](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).



## <a name="next-steps"></a>다음 단계
가상 컴퓨터를 장애 조치하고 온-프레미스 데이터 센터를 사용할 수 있게 되면 VMware 가상 컴퓨터를 온-프레미스 데이터 센터로 [**다시 보호**](site-recovery-how-to-reprotect.md)해야 합니다.

[**계획된 장애 조치**](site-recovery-failback-from-azure-to-hyper-v.md) 옵션을 사용하여 Hyper-V 가상 컴퓨터를 Azure에서 온-프레미스로 **장애 복구**합니다.

Hyper-V 가상 컴퓨터를 VMM 서버에서 관리하는 다른 온-프레미스 데이터 센터로 장애 조치하고 기본 데이터 센터를 사용할 수 있는 경우 **역방향 복제** 옵션을 사용하여 기본 데이터 센터로 복제를 다시 시작합니다. 


