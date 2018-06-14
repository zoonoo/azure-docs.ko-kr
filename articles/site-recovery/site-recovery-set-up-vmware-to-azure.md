---
title: 원본 환경 설정(Azure-VMware) | Microsoft Docs
description: 이 문서에서는 온-프레미스 환경을 설정하여 VMware 가상 머신을 Azure에 복제하기 시작하는 방법을 설명합니다.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767578"
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>원본 환경 설정(Azure-VMware)
> [!div class="op_single_selector"]
> * [VMware에서 Azure로](./site-recovery-set-up-vmware-to-azure.md)
> * [물리적 서버에서 Azure로](./site-recovery-set-up-physical-to-azure.md)

이 문서에서는 원본, 온-프레미스 환경을 설정하여 VMware에서 실행 중인 가상 머신을 Azure에 복제하는 방법을 설명합니다. 여기에는 복제 시나리오 선택, 온-프레미스 컴퓨터를 Site Recovery 구성 서버로 설정, 온-프레미스 VM 자동 검색을 위한 단계가 포함됩니다. 

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음 작업을 이미 수행한 것으로 가정합니다.
- [Azure Portal](http://portal.azure.com)에서 [리소스 설정](tutorial-prepare-azure.md)
- 자동 검색용 전용 계정을 포함하는 [온-프레미스 VMware 설정](tutorial-prepare-on-premises-vmware.md)



## <a name="choose-your-protection-goals"></a>보호 목표 선택

1. Azure Portal에서 **Recovery Services** 자격 증명 모음 블레이드로 이동한 후 사용자 자격 증명 모음을 선택합니다.
2. 자격 증명 모음의 리소스 메뉴에서 **시작** > **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**로 이동합니다.

    ![목표 선택](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. **보호 목표**에서 **Azure에**를 선택하고 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![목표 선택](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>구성 서버 설정

구성 서버를 온-프레미스 VMware VM으로 설정하고, OVF(Open Virtualization Format) 템플릿을 사용합니다. VMware VM에 설치되는 구성 요소에 대해 [자세히 알아봅니다](concepts-vmware-to-azure-architecture.md). 

1. 구성 서버 배포에 대한 [필수 구성 요소](how-to-deploy-configuration-server.md#prerequisites)에 대해 알아봅니다. 배포에 대한 [용량 수치를 확인](how-to-deploy-configuration-server.md#capacity-planning)합니다.
2. 구성 서버를 실행하는 온-프레미스 VMware VM을 설치하기 위한 OVF 템플릿(how-to-deploy-configuration-server.md)을 [다운로드](how-to-deploy-configuration-server.md#download-the-template)하고 [가져옵니다](how-to-deploy-configuration-server.md#import-the-template-in-vmware).
3. VMware VM을 켜고 Recovery Services 자격 증명 모음에 [등록](how-to-deploy-configuration-server.md#register-the-configuration-server)합니다.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>자동 검색에 VMware 계정 추가

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>VMware 서버에 연결

Azure Site Recovery가 온-프레미스 환경에서 실행 중인 가상 머신을 검색할 수 있게 하려면 Site Recovery와 VMware vCenter 서버 또는 vSphere ESXi 호스트를 연결해야 합니다.

**+vCenter**를 선택하여 VMware vCenter 서버 또는 VMware vSphere ESXi 호스트를 연결하기 시작합니다.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>일반적인 문제
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>다음 단계
Azure에서 [대상 환경 설정](./site-recovery-prepare-target-vmware-to-azure.md).
