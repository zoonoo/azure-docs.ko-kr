---
title: "Azure Site Recovery를 사용하여 Azure에 물리적 온-프레미스 서버 복제 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 온-프레미스 Windows/Linux 물리적 서버에서 실행되는 워크로드를 Azure에 복제하는 단계에 대한 개요를 제공합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>Site Recovery를 사용하여 Azure에 물리적 서버 복제

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 Windows/Linux 물리적 서버를 Azure에 복제하는 데 필요한 단계의 개요를 제공합니다.


## <a name="step-1-review-architecture-and-prerequisites"></a>1단계: 아키텍처 및 필수 조건 검토

배포를 시작하기 전에 시나리오 아키텍처를 검토하고 배포를 설정하는 데 필요한 모든 구성 요소를 이해해야 합니다.

[1단계: 아키텍처 검토](physical-walkthrough-architecture.md)로 이동합니다.


## <a name="step-2-review-prerequisites"></a>2단계: 필수 구성 요소 검토

각 배포 구성 요소에 대한 필수 조건이 준비되어 있는지 확인합니다.

- **Azure 필수 조건**: Microsoft Azure 계정, Azure 네트워크 및 저장소 계정이 필요합니다.
- **온-프레미스 Site Recovery 구성 요소**: 컴퓨터가 온-프레미스 Site Recovery 구성 요소를 실행해야 합니다.
- **복제된 컴퓨터**: 복제하려는 서버는 온-프레미스 및 Azure 요구 사항을 준수 해야 합니다.

[2단계: 필수 조건 및 제한 사항 검토](physical-walkthrough-prerequisites.md)로 이동합니다.

## <a name="step-3-plan-capacity"></a>3단계: 용량 계획

전체 배포를 진행하려면 필요한 복제 리소스를 파악해야 합니다. 환경을 테스트하는 빠른 설정을 수행하려면 이 단계를 건너뛸 수 있습니다.

[3단계: 용량 계획](physical-walkthrough-capacity.md)으로 이동합니다.

## <a name="step-4-plan-networking"></a>4단계: 네트워킹 계획

장애 조치(failover)가 발생한 후에 Azure VM이 네트워크에 연결되어 있는지 및 IP 주소가 올바른지를 확인하기 위해 일부 네트워크 계획을 수행해야 합니다.

[4단계: 네트워킹 계획](physical-walkthrough-network.md)으로 이동합니다.

##  <a name="step-5-prepare-azure-resources"></a>5단계: Azure 리소스 준비

시작하기 전에 Azure 네트워크 및 저장소를 설정합니다. 

[5단계: Azure 준비](physical-walkthrough-prepare-azure.md)로 이동합니다.


## <a name="step-6-set-up-a-vault"></a>6단계: 자격 증명 모음 설정

복제를 오케스트레이션하고 관리하도록 Recovery Services 자격 증명 모음을 설정합니다. 자격 증명 모음을 설정할 때 복제하려는 항목 및 위치를 지정합니다.

[6단계: 자격 증명 모음 설정](physical-walkthrough-create-vault.md)으로 이동합니다.

## <a name="step-7-configure-source-and-target-settings"></a>7단계: 원본 및 대상 설정 구성

원본 및 대상(Azure) 사이트에 대한 설정을 구성합니다. 원본 설정에는 온-프레미스 Site Recovery 구성 요소를 설치하는 통합 설정 실행 작업이 포함됩니다.

[7단계: 원본 및 대상 설정](physical-walkthrough-source-target.md)으로 이동합니다.

## <a name="step-8-set-up-a-replication-policy"></a>8단계: 복제 정책 설정

물리적 서버가 복제되는 방식을 지정하는 정책을 설정합니다.

[8단계: 복제 정책 설정](physical-walkthrough-replication.md)으로 이동합니다.

## <a name="step-9-install-the-mobility-service"></a>9단계: 모바일 서비스 설치

복제하려는 각 서버에 모바일 서비스가 설치되어야 합니다. 푸시 또는 풀 설치를 사용하여 서비스를 설정하는 방법에는 몇 가지가 있습니다.

[9단계: 모바일 서비스 설치](physical-walkthrough-install-mobility.md)로 이동합니다.

## <a name="step-10-enable-replication"></a>10단계: 복제 활성화

서버에서 모바일 서비스를 실행한 후에 복제를 사용할 수 있습니다. 활성화한 후 VM의 초기 복제가 시작됩니다.

[10단계: 복제 활성화](physical-walkthrough-enable-replication.md)로 이동합니다.

## <a name="step-11-run-a-test-failover"></a>11단계: 테스트 장애 조치(Failover) 실행

초기 복제가 완료되고 델타 복제가 실행된 후에 테스트 장애 조치를 실행하여 모든 항목이 예상대로 작동되는지 확인할 수 있습니다.

[11단계: 테스트 장애 조치(Failover) 실행](physical-walkthrough-test-failover.md)으로 이동합니다.

