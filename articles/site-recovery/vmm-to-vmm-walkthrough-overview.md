---
title: "Azure Site Recovery를 사용하여 보조 VMM 사이트에 Hyper-V VM 복제 | Microsoft 문서"
description: "Azure Portal을 사용하여 보조 VMM 사이트에 Hyper-V VM을 복제하는 과정을 대략적으로 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>보조 VMM 사이트에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제

> [!div class="op_single_selector"]
> * [Azure 포털](site-recovery-vmm-to-vmm.md)
> * [클래식 포털](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md)를 사용해 System Center Virtual Machine Manager(VMM) 클라우드에서 관리되는 온-프레미스 Hyper-V VM(가상 컴퓨터)을 보조 VMM 위치로 복제하기 위해 수행해야 하는 단계를 간략하게 설명합니다.

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.


## <a name="step-1-review-the-scenario-architecture"></a>1단계: 시나리오 아키텍처 검토

배포를 시작하기 전에 시나리오 아키텍처를 검토하고 배포에 필요한 모든 구성 요소를 이해해야 합니다.

[1단계: 아키텍처 검토](vmm-to-vmm-walkthrough-architecture.md)로 이동합니다.

## <a name="step-2-review-prerequisites-and-limitations"></a>2단계: 필수 구성 요소 및 제한 사항 검토

배포 필수 구성 요소 및 제한 사항을 이해해야 합니다.

**Azure 필수 구성 요소**: 복제를 오케이스트레이션하고 관리하기 위한 Azure Recovery Services 자격 증명 모음과 Microsoft Azure 구독이 필요합니다.
**온-프레미스 VMM 서버 및 Hyper-V 호스트**: VMM 서버 및 Hyper-V 호스트가 Site Recovery 배포용으로 준비되었으며 배포 규격에 맞는지 확인합니다.

[2단계: 필수 구성 요소 및 제한 사항 확인](vmm-to-vmm-walkthrough-prerequisites.md)으로 이동합니다.

## <a name="step-3-plan-networking"></a>3단계: 네트워킹 계획

몇 가지 네트워크 계획을 수행하여 시나리오를 배포할 때 VMM VM 네트워크 간에 네트워크 매핑을 구성할 수 있는지 확인해야 합니다.

[3단계: 네트워킹 계획](vmm-to-vmm-walkthrough-network.md)으로 이동합니다.


## <a name="step-4-prepare-vmm-and-hyper-v"></a>4단계: VMM 및 Hyper-V 준비

Site Recovery 배포용 VMM 서버 및 Hyper-V 호스트를 준비합니다.

[4단계: 온-프레미스 서버 준비](vmm-to-vmm-walkthrough-vmm-hyper-v.md)로 이동합니다.

## <a name="step-5-set-up-a-vault"></a>5단계: 자격 증명 모음 설정

Recovery Services 자격 증명 모음을 설정합니다. 자격 증명 모음은 구성 설정을 포함하며 복제를 오케스트레이션합니다.

[5단계: 자격 증명 모음 설정](vmm-to-vmm-walkthrough-create-vault.md).

## <a name="step-6-set-up-source-and-target-settings"></a>6단계: 원본 및 대상 설정 지정

원본 및 대상 복제 VMM 위치를 설정합니다. VMM 서버를 자격 증명 모음에 추가하고 Site Recovery 구성 요소의 설치 파일을 다운로드합니다. VMM 서버에서 Azure Site Recovery Provider 설치를 실행합니다. 설치를 실행하면 VMM 서버에 Azure Site Recovery Provider가 설치되고 자격 증명 모음에 서버가 등록됩니다. 각 Hyper-V 호스트에 Microsoft Recovery Services 에이전트를 설치합니다.

[6단계: 원본 및 대상 설정 지정](vmm-to-vmm-walkthrough-source-target.md)으로 이동합니다.

## <a name="step-7-configure-network-mapping"></a>7단계: 네트워크 매핑 구성

원본 및 대상 위치에서 VMM VM 네트워크를 매핑합니다. 장애 조치(failover) 후에는 원본 Hyper-V VM이 있는 원본 VM 네트워크에 매핑되는 대상 네트워크에 VM이 작성됩니다.

[7단계: 네트워크 매핑 구성](vmm-to-vmm-walkthrough-network-mapping.md)으로 이동합니다.


## <a name="step-8-set-up-a-replication-policy"></a>8단계: 복제 정책 설정

VMM 위치 간에 VM을 복제할 방법을 지정합니다.

[8단계: 복제 정책 설정](vmm-to-vmm-walkthrough-replication.md)으로 이동합니다.


## <a name="step-9-enable-replication-for-vms"></a>9단계: VM을 복제하도록 설정

복제할 VM을 선택합니다. VM을 복제할 수 있도록 설정하면 보조 사이트로의 초기 복제가 트리거된 다음 델타 복제가 계속 진행됩니다.

[9단계: 복제 활성화](vmm-to-vmm-walkthrough-enable-replication.md)로 이동합니다.


## <a name="step-10-run-a-test-failover"></a>10단계: 테스트 장애 조치(failover) 실행

모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.

[10단계: 테스트 장애 조치(failover) 실행](vmm-to-vmm-walkthrough-test-failover.md)으로 이동합니다.
