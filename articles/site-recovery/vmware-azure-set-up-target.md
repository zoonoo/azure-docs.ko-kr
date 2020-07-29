---
title: Azure Site Recovery에서 VMware VM 복제 대상 준비
description: 이 아티클에서는 Azure에 대한 VMware VM 복제를 위해 대상 Azure 환경을 준비하는 방법을 설명합니다.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "73693176"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Azure로 VMware VM 또는 물리적 서버를 재해 복구하기 위한 대상 환경 준비

이 문서에서는 VMware 가상 머신 또는 실제 서버를 Azure로 복제하는 작업을 시작하기 위해 대상 Azure 환경을 준비하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

문서에서는 다음을 가정합니다.
- 원본 컴퓨터를 보호 하기 위해 [Azure Portal](https://portal.azure.com "Azure portal") 에서 Recovery Services 자격 증명 모음을 만들었습니다.
- 원본 [VMware 가상 머신](vmware-azure-set-up-source.md) 또는 [실제 서버](physical-azure-set-up-source.md)를 Azure에 복제하기 위한 온-프레미스 환경을 설정했습니다.

## <a name="prepare-target"></a>대상 준비

**1단계: 보호 목표 선택** 및 **2단계: 원본 준비**를 완료하고 나면 **3단계: 대상**이 시작됩니다.

![대상 준비](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **구독:** 드롭다운 메뉴에서 가상 머신이나 실제 서버를 복제할 대상 구독을 선택합니다.
2. **배포 모델:** 배포 모델(클래식 또는 리소스 관리자)을 선택합니다.

선택한 배포 모델에 따라 유효성 검사를 실행 하 여 가상 머신 또는 물리적 서버를 복제 하 고 장애 조치 (failover) 할 대상 구독의 가상 네트워크가 하나 이상 있는지 확인 합니다.

유효성 검사가 성공적으로 완료되면 확인을 클릭하여 다음 단계로 이동합니다.

가상 네트워크가 없는 경우 페이지 맨 위에 있는 **+ 네트워크** 단추를 클릭 하 여 가상 네트워크를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
[복제 설정을 구성](vmware-azure-set-up-replication.md)합니다.
