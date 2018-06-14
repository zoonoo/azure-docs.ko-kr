---
title: 대상 준비(물리적 대상에서 Azure로) | Microsoft Docs
description: 이 문서에서는 Azure 환경을 준비하여 Windows 또는 Linux를 실행 중인 물리적 서버를 Azure에 복제하기 시작하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: 6704ddc24db8415051a6064bbde79a6fc527871a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767548"
---
# <a name="prepare-target-physical-to-azure"></a>대상 준비(물리적 대상에서 Azure로)
> [!div class="op_single_selector"]
> * [VMware에서 Azure로](./site-recovery-prepare-target-vmware-to-azure.md)
> * [물리적 서버에서 Azure로](./site-recovery-prepare-target-physical-to-azure.md)

이 문서에서는 Azure 환경을 준비하여 Windows 또는 Linux를 실행 중인 물리적 서버(x64)를 Azure에 복제하기 시작하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

문서에서는 다음을 가정합니다.
- 물리적 컴퓨터를 보호하기 위해 Recovery Services 자격 증명 모음을 만들었습니다. [Azure Portal](http://portal.azure.com "Azure Portal")에서 Recovery Services 자격 증명 모음을 만들 수 있습니다.
- 물리적 컴퓨터를 Azure에 복제하도록 [온-프레미스 환경을 설정](./site-recovery-set-up-physical-to-azure.md)했습니다.

## <a name="prepare-target"></a>대상 준비

**1단계: 보호 목표 선택** 및 **2단계: 원본 준비**를 완료한 후 **3단계: 대상**으로 이동합니다.

![대상 준비](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **구독:** 드롭다운 메뉴에서 물리적 컴퓨터를 복제할 구독을 선택합니다.
2. **배포 모델:** 배포 모델(클래식 또는 리소스 관리자)을 선택합니다.

선택한 배포 모델에 따라 유효성 검사를 실행하여 물리적 컴퓨터를 복제하고 장애 조치를 수행할 대상 구독에 하나 이상의 호환되는 저장소 계정 및 가상 네트워크가 있는지 확인합니다.

유효성 검사가 성공적으로 완료되면 확인을 클릭하여 다음 단계로 이동합니다.

호환되는 Resource Manager 저장소 계정 또는 가상 네트워크가 없는 경우 페이지 맨 위에 있는 **+Storage 계정** 또는 **+네트워크** 단추를 클릭하여 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
[복제 설정 구성](./site-recovery-setup-replication-settings-vmware.md)
