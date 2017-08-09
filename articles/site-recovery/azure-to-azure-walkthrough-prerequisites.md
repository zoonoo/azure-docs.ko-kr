---
title: "Azure VM을 다른 지역으로 복제하기 전에 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 Azure 지역 간에 Azure VM을 복제하기 전에 수행해야 하는 단계를 요약하고 있습니다."
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
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 147019a9650df2d421d4d930aa2932904d5174ab
ms.contentlocale: ko-kr
ms.lasthandoff: 08/03/2017

---

# <a name="step-2-before-you-start"></a>2단계: 시작하기 전에

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure 지역 간에 Azure VM(가상 컴퓨터)을 복제하는 [아키텍처](azure-to-azure-walkthrough-architecture.md)를 검토한 후에 이 문서를 사용하여 필수 구성 요소를 확인합니다. 

- 이 문서를 완료하면 배포 작업을 수행하는 데 필요한 사항을 명확히 이해하고 필수 구성 요소 단계를 완료해야 합니다.
- 이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 질문하세요.

>[!NOTE]
>
> Azure VM 복제는 현재 미리 보기로 제공됩니다.



## <a name="support-recommendations"></a>지원 권장 사항

아래 표를 검토하세요.

**구성 요소** | **요구 사항**
--- | ---
**Recovery Services 자격 증명 모음** | 재해 복구에 사용할 대상 Azure 지역에 Recovery Services 자격 증명 모음을 만드는 것이 좋습니다. 예를 들어 미국 동부의 원본 VM을 미국 중부에 복제하려는 경우 미국 중부에서 자격 증명 모음을 만듭니다.
**Azure 구독** | 재해 복구 지역으로 사용할 대상 위치에 VM을 만들려면 Azure 구독을 활성화해야 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.
**대상 지역 용량** | 대상 Azure 지역에서 구독에는 VM, 저장소 계정 및 네트워크 구성 요소에 맞게 충분한 용량이 있어야 합니다.
**저장소** | 성능 문제를 방지하기 위해 원본 Azure VM에 대한 [저장소 지침](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)을 사용합니다.<br/><br/> 저장소 계정은 자격 증명 모음과 동일한 지역에 있어야 합니다.<br/><br/> 중부 및 남부 인도에서는 프리미엄 계정에 복제할 수 없습니다.<br/><br/> 기본 설정으로 복제를 배포하면 Site Recovery에서 원본 구성에 따라 필요한 저장소 계정을 만듭니다. 설정을 사용자 지정하는 경우 [VM 디스크에 대한 확장성 목표](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)를 따릅니다.
**네트워킹** | Azure VM에서 특정 URL/IP 범위에 대한 아웃바운드 연결을 허용해야 합니다.<br/><br/> 네트워크 계정은 자격 증명 모음과 동일한 지역에 있어야 합니다. 
**Azure VM** | 모든 최신 루트 인증서가 Windows/Linux Azure VM에 있는지 확인합니다. 그렇지 않은 경우 보안 제약으로 인해 Site Recovery에서 VM을 등록할 수 없습니다.
**Azure 사용자 계정** | Azure Virtual Machines 복제를 사용하려면 Azure 사용자 계정에 특정 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 합니다.

[지원 매트릭스](site-recovery-support-matrix-azure-to-azure.md)에서 지원 요구 사항의 전체 목록을 얻으세요.


## <a name="set-permissions-on-the-account"></a>계정에 권한 설정

1. 복제에 필요한 [권한](site-recovery-role-based-linked-access-control.md)에 대해 알아봅니다.
2. [이러한 지침](../active-directory/role-based-access-control-configure.md#add-access)에 따라 권한을 추가합니다.


## <a name="verify-target-resources"></a>대상 리소스 확인

1. 재해 복구에서 재해 복구 지역으로 사용할 대상 지역에 VM을 만들 수 있도록 Azure 구독을 활성화합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.
2. 구독에 원본 VM과 동일한 크기의 VM을 사용할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. 기본적으로 복제를 설정할 때 Site Recovery에서 대상 VM에 대해 동일한 크기 또는 가장 가깝게 가능한 크기를 선택합니다. 대상 리소스 [문제 해결](site-recovery-azure-to-azure-troubleshoot-errors.md#azure-resource-quota-issues-error-code-150097)에 대해 자세히 알아보세요.

## <a name="verify-azure-vm-certificates"></a>Azure VM 인증서 확인

1. 복제하려는 Windows 또는 Linux VM에 최신 루트 인증서가 모두 있는지 확인합니다. 최신 루트 인증서가 없는 경우 보안 제약 조건으로 인해 Site Recovery에 VM을 등록할 수 없습니다.
2. Windows VM의 경우 다음을 수행합니다.

    - VM에 최신 Windows 업데이트를 모두 설치하여 모든 신뢰할 수 있는 루트 인증서가 컴퓨터에 있도록 합니다.
    - 연결이 끊긴 환경에서 조직의 표준 Windows 업데이트 프로세스/인증서 업데이트 프로세스에 따라 최신 루트 인증서 및 업데이트된 CRL을 VM에 가져옵니다.
3. Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 최신 인증서 해지 목록을 VM에 가져옵니다. 신뢰할 수 있는 루트 [문제 해결](site-recovery-azure-to-azure-troubleshoot-errors.md#trusted-root-certificates-error-code-151066)에 대해 자세히 알아보세요.


## <a name="next-steps"></a>다음 단계

[3단계: 네트워킹 계획](azure-to-azure-walkthrough-network.md)으로 이동하여 아웃바운드 연결을 설정합니다.

