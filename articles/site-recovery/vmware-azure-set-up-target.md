---
title: Azure에 대한 VMware 복제를 위한 대상 환경 준비 | Microsoft Docs
description: 이 아티클에서는 Azure에 대한 VMware VM 복제를 위해 대상 Azure 환경을 준비하는 방법을 설명합니다.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: cbf9c1a49481de49fb8d9c6599acec7bc3600df3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837514"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Azure로 VMware VM 또는 물리적 서버를 재해 복구하기 위한 대상 환경 준비

이 문서에서는 VMware 가상 머신 또는 실제 서버를 Azure로 복제하는 작업을 시작하기 위해 대상 Azure 환경을 준비하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

문서에서는 다음을 가정합니다.
- 원본 컴퓨터를 보호하기 위해 [Azure Portal](http://portal.azure.com "Azure Portal")에서 Recovery Services Vault를 만들었습니다.
- 원본 [VMware 가상 머신](vmware-azure-set-up-source.md) 또는 [실제 서버](physical-azure-set-up-source.md)를 Azure에 복제하기 위한 온-프레미스 환경을 설정했습니다.

## <a name="prepare-target"></a>대상 준비

**1단계: 보호 목표 선택** 및 **2단계: 원본 준비**를 완료한 후, **3단계: 대상**을 수행합니다.

![대상 준비](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **구독:** 드롭다운 메뉴에서 가상 머신이나 실제 서버를 복제할 대상 구독을 선택합니다.
2. **배포 모델:** 배포 모델(클래식 또는 Resource Manager)을 선택합니다.

선택한 배포 모델에 따라 유효성 검사가 실행되어 가상 머신이나 실제 서버를 복제하고 장애 조치(failover)를 수행할 대상 구독에 하나 이상의 호환되는 저장소 계정 및 가상 네트워크가 있는지를 확인합니다.

유효성 검사가 성공적으로 완료되면 확인을 클릭하여 다음 단계로 이동합니다.

호환되는 Resource Manager 스토리지 계정 또는 가상 네트워크가 없는 경우 페이지 맨 위에 있는 **+Storage 계정** 또는 **+네트워크** 단추를 클릭하여 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
[복제 설정 구성](vmware-azure-set-up-replication.md)
