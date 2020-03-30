---
title: Azure 사이트 복구에서 VMware VM 복제 대상 준비
description: 이 아티클에서는 Azure에 대한 VMware VM 복제를 위해 대상 Azure 환경을 준비하는 방법을 설명합니다.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73693176"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Azure로 VMware VM 또는 물리적 서버를 재해 복구하기 위한 대상 환경 준비

이 문서에서는 VMware 가상 머신 또는 실제 서버를 Azure로 복제하는 작업을 시작하기 위해 대상 Azure 환경을 준비하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

문서에서는 다음을 가정합니다.
- 원본 컴퓨터를 보호하기 위해 [Azure 포털에서](https://portal.azure.com "Azure portal") 복구 서비스 자격 증명 모음을 만들었습니다.
- 원본 [VMware 가상 머신](vmware-azure-set-up-source.md) 또는 [실제 서버](physical-azure-set-up-source.md)를 Azure에 복제하기 위한 온-프레미스 환경을 설정했습니다.

## <a name="prepare-target"></a>대상 준비

**1단계: 보호 목표 선택** 및 **2단계: 원본 준비**를 완료하고 나면 **3단계: 대상**이 시작됩니다.

![대상 준비](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **구독:** 드롭다운 메뉴에서 가상 머신이나 실제 서버를 복제할 대상 구독을 선택합니다.
2. **배포 모델:** 배포 모델(클래식 또는 리소스 관리자)을 선택합니다.

선택한 배포 모델에 따라 유효성 검사가 실행되어 대상 구독에 하나 이상의 가상 네트워크가 있어 가상 컴퓨터 또는 물리적 서버를 복제하고 장애 조치(failover)할 수 있습니다.

유효성 검사가 성공적으로 완료되면 확인을 클릭하여 다음 단계로 이동합니다.

가상 네트워크가 없는 경우 페이지 상단의 **+ 네트워크** 버튼을 클릭하여 가상 네트워크를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
[복제 설정 구성](vmware-azure-set-up-replication.md)
