---
title: "Azure Site Recovery를 사용하여 Azure로 Hyper-V VM 복제 | Microsoft Docs"
description: "온-프레미스 Hyper-V VM을 Azure에 복제, 장애 조치(failover) 및 복구하는 작업을 조정하는 방법 설명"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: efddd986-bc13-4a1d-932d-5484cdc7ad8d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: da10b213bc2543942b5ac77cf5c5d8547c00220c
ms.contentlocale: ko-kr
ms.lasthandoff: 06/23/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure"></a>Azure로 Hyper-V 가상 컴퓨터(VMM 없음) 복제 

> [!div class="op_single_selector"]
> * [Azure 포털](site-recovery-hyper-v-site-to-azure.md)
> * [Azure 클래식](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure로 온-프레미스 Hyper-V 가상 컴퓨터를 복제하는 데 필요한 단계의 개요를 제공합니다. 이 배포에서는 Hyper-V VM이 System Center VMM(Virtual Machine Manager)을 통해 관리되지 않습니다.


이 문서를 읽은 후에는 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 기술적인 질문을 합니다.


## <a name="step-1-review-architecture-and-prerequisites"></a>1단계: 아키텍처 및 필수 구성 요소 검토

배포를 시작하기 전에 시나리오 아키텍처를 검토하고 배포에 필요한 모든 구성 요소를 이해해야 합니다.

[1단계: 아키텍처 검토](hyper-v-site-walkthrough-architecture.md)로 이동합니다.


## <a name="step-2-review-prerequisites"></a>2단계: 필수 구성 요소 검토

각 배포 구성 요소에 대한 필수 조건이 준비되어 있는지 확인합니다.

- **Azure 필수 구성 요소**: Microsoft Azure 계정, Azure 네트워크 및 저장소 계정이 필요합니다.
- **온-프레미스 Hyper-V 필수 구성 요소**: Hyper-V 호스트가 Site Recovery 배포에 준비되어 있는지 확인합니다.
- **복제된 VM**: 복제할 VM이 Azure 요구 사항을 준수해야 합니다.

[2단계: 필수 구성 요소 및 제한 사항 확인](hyper-v-site-walkthrough-prerequisites.md)으로 이동합니다.

## <a name="step-3-plan-capacity"></a>3단계: 용량 계획

전체 배포를 진행하려면 필요한 복제 리소스를 파악해야 합니다. 이 작업을 수행하는 데 도움이 되는 몇 가지 도구를 사용할 수 있습니다. 2단계로 이동합니다. 환경을 테스트하는 빠른 설정을 수행하는 경우 이 단계를 건너뛸 수 있습니다.

[3단계: 용량 계획](hyper-v-site-walkthrough-capacity.md)으로 이동합니다.

## <a name="step-4-plan-networking"></a>4단계: 네트워킹 계획

장애 조치(failover)가 발생한 후에 Azure VM이 네트워크에 연결되어 있는지 및 IP 주소가 올바른지를 확인하기 위해 일부 네트워크 계획을 수행해야 합니다.

[4단계: 네트워킹 계획](hyper-v-site-walkthrough-network.md)으로 이동합니다.

##  <a name="step-5-prepare-azure-resources"></a>5단계: Azure 리소스 준비

시작하기 전에 Azure 네트워크 및 저장소를 설정합니다. 이 작업은 배포하는 동안에도 수행할 수 있지만 시작하기 전에 수행하는 것이 좋습니다.

[5단계: Azure 준비](hyper-v-site-walkthrough-prepare-azure.md)로 이동합니다.


## <a name="step-6-prepare-hyper-v"></a>6단계: Hyper-V 준비

Hyper-V 서버가 Site Recovery 배포 요구 사항을 충족하는지 확인합니다.

[6단계: Hyper-V 준비](hyper-v-site-walkthrough-prepare-hyper-v.md)로 이동합니다.

## <a name="step-7-set-up-a-vault"></a>7단계: 자격 증명 모음 설정

복제를 오케스트레이션하고 관리하도록 Recovery Services 자격 증명 모음을 설정해야 합니다. 자격 증명 모음을 설정할 때 복제하려는 항목 및 위치를 지정합니다.

[7단계: 자격 증명 모음 만들기](hyper-v-site-walkthrough-create-vault.md)로 이동합니다.

## <a name="step-8-configure-source-and-target-settings"></a>8단계: 원본 및 대상 설정 구성

복제에 사용되는 원본 및 대상을 설정합니다. 원본 설정을 구성하는 것에는 Hyper-V 사이트에 대한 Hyper-V 호스트, 각 Hyper-V 호스트에 Site Recovery 공급자 및 Recovery Services 에이전트 설치 및 Recovery Services 자격 증명 모음에서 사이트 등록이 포함됩니다.

[8단계: 원본 및 대상 설정](hyper-v-site-walkthrough-source-target.md)으로 이동합니다.

## <a name="step-9-set-up-a-replication-policy"></a>9단계: 복제 정책 설정

자격 증명 모음에서 Hyper-V VM을 위한 복제 설정을 지정하는 정책을 설정합니다.

[9단계: 복제 정책 설정](hyper-v-site-walkthrough-replication.md)으로 이동합니다.


## <a name="step-10-enable-replication"></a>10단계: 복제 활성화

복제 정책을 준비한 후, 활성화하면 VM의 초기 복제가 발생합니다.

[10단계: 복제 활성화](hyper-v-site-walkthrough-enable-replication.md)로 이동합니다.

## <a name="step-11-run-a-test-failover"></a>11단계: 테스트 장애 조치(failover) 실행

초기 복제가 완료되고 델타 복제가 실행된 후에 테스트 장애 조치(failover)를 실행하여 예상대로 작동되는지 확인할 수 있습니다.

[11단계: 테스트 장애 조치(failover) 실행](hyper-v-site-walkthrough-test-failover.md)으로 이동합니다.

