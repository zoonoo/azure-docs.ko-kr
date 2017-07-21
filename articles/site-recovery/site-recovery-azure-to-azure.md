---
title: "재해 복구 요구 사항을 위한 Azure 지역 간 Azure VM 복제: Azure 간 | Microsoft Docs"
description: "재해 복구 요구 사항을 위해 Azure Site Recovery 서비스를 사용하여 Azure 지역 간(Azure 간)에 Azure VM을 복제하는 데 필요한 단계를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.contentlocale: ko-kr
ms.lasthandoff: 06/20/2017


---

# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 지역 간 Azure VM 복제

>[!NOTE]
>
> Azure VM(가상 컴퓨터)에 대한 Azure Site Recovery 복제는 현재 미리 보기로 제공됩니다.

이 문서에서는 Azure Portal에서 Azure [Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure 지역 간에 Azure VM을 복제하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="disaster-recovery-in-azure"></a>Azure에서 재해 복구

기본 제공 Azure 인프라의 기능 및 특징은 Azure VM에서 실행되는 워크로드에 대한 강력하고 복원력 있는 가용성 전략에 기여합니다. 그러나 Azure 지역 간 재해 복구를 계획해야 하는 여러 가지 이유가 있습니다.

- BCDR(비즈니스 연속성 및 재해 복구) 전략이 필요한 특정 앱 및 워크로드에 대한 규정 준수 지침을 준수해야 합니다.
- 기본 제공되는 Azure 기능뿐 아니라 비즈니스 결정 사항에 따라 Azure VM을 보호하고 복구할 수 있습니다.
- 프로덕션에 영향 없이 비즈니스 및 규정 준수 요구 사항에 따라 장애 조치 및 복구를 테스트해야 합니다.
- 재해 발생 시 복구 지역으로 장애 조치하고 원본 지역으로 원활하게 장애 복구해야 합니다.

Azure 간 VM 복제에 Site Recovery를 사용하면 이 모든 작업을 쉽게 수행할 수 있습니다.


## <a name="why-use-site-recovery"></a>사이트 복구를 사용하는 이유는?      

Site Recovery는 지역 간에 Azure VM을 복제하는 간단한 방법을 제공합니다.

- **자동 배포**. 활성-활성 복제 모델과 달리 보조 지역에 비싸고 복잡한 인프라가 필요 없습니다. 복제를 사용하도록 설정하면 Site Recovery가 원본 지역 설정에 따라 대상 지역에 필요한 리소스를 자동으로 만듭니다.
- **지역 제어**. Site Recovery를 사용하면 대륙 내 모든 지역 간에 복제할 수 있습니다. 반면, 읽기 액세스 지역 중복 저장소는 표준 [쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 간에만 비동기식으로 복제합니다. 또한 읽기 액세스 지역 중복 저장소는 대상 지역의 데이터에 대한 읽기 전용 액세스를 제공합니다.
- **복제 자동화**. Site Recovery는 자동화된 연속 복제를 제공합니다. 한 번의 클릭으로 장애 조치 및 장애 복구를 트리거할 수 있습니다.
- **RTO 및 RPO**. Site Recovery는 지역을 연결하는 Azure 네트워크 인프라를 활용하여 RTO 및 RPO를 매우 낮게 유지합니다.
- **테스트**. 프로덕션 워크로드 또는 진행 중인 복제에 영향을 주지 않고 필요할 때 주문형 테스트 장애 조치를 사용하여 재해 복구 드릴을 실행할 수 있습니다.
- **복구 계획**. 복구 계획을 사용하여 여러 VM에서 실행되는 전체 응용 프로그램의 장애 조치 및 장애 복구를 오케스트레이션할 수 있습니다. 복구 계획 기능은 Azure Automation Runbook과의 최고 수준의 통합을 지원합니다.


## <a name="deployment-summary"></a>배포 요약

Azure 지역 간의 VM 복제를 설정하기 위해 수행해야 하는 작업을 요약하면 다음과 같습니다.

1. Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음은 구성 설정을 포함하며 복제를 오케스트레이션합니다.
2. Azure VM에 대해 복제를 활성화합니다.
3. 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.

>[!IMPORTANT]
>
> [Azure VM 복제에 대한 지원 매트릭스](./site-recovery-support-matrix-azure-to-azure.md)를 확인할 수 있습니다.

>[!IMPORTANT]
>
> Site Recovery 복제를 위해 Azure VM에 필요한 네트워크 아웃바운드 연결을 구성하는 방법에 대한 자세한 내용은 [네트워킹 지침 문서](./site-recovery-azure-to-azure-networking-guidance.md)를 참조하세요.

### <a name="before-you-start"></a>시작하기 전에

* Azure Virtual Machines 복제를 사용하려면 Azure 사용자 계정에 특정 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 합니다.
* 재해 복구 지역으로 사용할 대상 위치에서 VM을 만들려면 Azure 구독을 활성화해야 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> VM을 복제할 위치에 Recovery Services 자격 증명 모음을 만드는 것이 좋습니다. 예를 들어 대상 위치가 미국 중부인 경우 **미국 중부**에 자격 증명 모음을 만듭니다.

## <a name="enable-replication"></a>복제 활성화

**Recovery Services 자격 증명 모음**에서 자격 증명 모음 이름을 클릭합니다. 자격 증명 모음에서 **+복제** 단추를 클릭합니다.

### <a name="step-1-configure-the-source"></a>1단계. 원본 구성
1. **원본**에서 **Azure - 미리 보기**를 선택합니다.
2. **원본 위치**에서 VM이 현재 실행 중인 원본 Azure 지역을 선택합니다.
3. VM의 배포 모델(**Resource Manager** 또는 **클래식**)을 선택합니다.
4. **원본 리소스 그룹**(Resource Manager VM의 경우) 또는 **클라우드 서비스**(클래식 VM의 경우)를 선택합니다.

    ![원본 구성](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>2단계. 가상 컴퓨터 선택

* 복제할 VM을 선택하고 **확인**을 클릭합니다.

    ![VM 선택](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>3단계. 설정 구성

1. 기본 대상 설정을 재정의하고 원하는 설정을 지정하려면 **사용자 지정**을 클릭합니다. 자세한 내용은 [대상 리소스 사용자 지정](site-recovery-replicate-azure-to-azure.md##customize-target-resources)을 참조하세요.

    ![설정 구성](./media/site-recovery-azure-to-azure/settings.png)


2. 기본적으로 Site Recovery는 4시간마다 앱 일치 스냅숏을 만들고 24시간 동안 복구 지점을 유지하는 복제 정책을 만듭니다. 다른 설정으로 정책을 만들려면 **복제 정책** 옆의 **사용자 지정**을 클릭합니다.

    ![정책 사용자 지정](./media/site-recovery-azure-to-azure/customize-policy.png)

3. 대상 리소스 프로비전을 시작하려면 **대상 리소스 만들기**를 클릭합니다. 프로비전에 1분 정도 걸립니다. 프로비전하는 동안 블레이드를 닫지 마세요. 블레이드를 닫으면 처음부터 다시 시작해야 합니다.

4. 선택한 VM의 복제를 트리거하려면 **복제 사용**을 클릭합니다.

5. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다.

6. **설정** > **복제된 항목**에서 VM의 상태 및 초기 복제 진행률을 볼 수 있습니다. VM을 클릭하여 해당 설정으로 드릴다운합니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행

모든 항목을 설정한 후 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.

1. 단일 컴퓨터를 장애 조치(failover)하려면 **설정** > **복제된 항목**에서 VM **+테스트 장애 조치(failover)** 아이콘을 클릭합니다.

2. 복구 계획을 장애 조치(Failover)하려면 **설정** > **복구 계획**에서 계획을 마우스 오른쪽 버튼으로 클릭하고 **테스트 장애 조치(Failover)**를 클릭합니다. 복구 계획을 만들려면 [다음 지침을 따릅니다](site-recovery-create-recovery-plans.md). 

3. **테스트 장애 조치**에서 장애 조치가 발생한 후에 Azure VM을 연결할 대상 Azure 가상 네트워크를 선택합니다.

4. 장애 조치를 시작하려면 **확인**을 클릭합니다. 진행률을 추적하려면 VM을 클릭하여 해당 속성을 엽니다. 또는 자격 증명 모음 이름 > **설정** > **작업** > **Site Recovery 작업**에서 **테스트 장애 조치** 작업을 클릭해도 됩니다.

5. 장애 조치가 완료되면 Azure Portal > **가상 컴퓨터**에 복제본 Azure 컴퓨터가 표시됩니다. VM의 크기가 적당하고, 올바른 네트워크에 연결되었고, 실행 중인지 확인합니다.

6. 테스트 장애 조치 중에 만든 VM을 삭제하려면 복제된 항목 또는 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다. 

테스트 장애 조치(failover)에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).


## <a name="next-steps"></a>다음 단계

배포를 테스트한 후

- 여러 장애 조치(failover) 유형 및 장애 조치(failover) 실행 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).
- [복구 계획을 사용](site-recovery-create-recovery-plans.md)하여 RTO를 줄이는 방법에 대해 자세히 알아봅니다.

