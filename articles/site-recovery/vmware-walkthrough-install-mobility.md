---
title: "Azure 복제를 위해 VMware에 모바일 서비스 설치 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery 서비스를 통해 Azure 복제를 위해 VMware에 모바일 서비스 에이전트를 설치하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-install-the-mobility-service"></a>10단계: 모바일 서비스 설치


이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure에 온-프레미스 VMware 가상 컴퓨터를 복제할 때 소스 및 대상 설정을 구성하는 방법을 설명합니다.

모바일 서비스는 컴퓨터에 기록된 데이터를 캡처하고 이를 프로세스 서버에 전달합니다. Azure에 복제하려는 각 컴퓨터에 설치해야 합니다.

모바일 서비스는 복제가 설정된 경우 Site Recovery 프로세스 서버에서 푸시 설치를 사용하여 수동으로 설치하거나 System Center Configuration Manager 도구를 사용할 수 있습니다. 푸시 설치를 사용하는 경우 복제를 활성화할 때 VM에 서비스를 설치합니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="install-manually"></a>수동 설치

1. 수동 설치를 위한 [필수 조건](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites)을 확인합니다.
2. 포털을 사용하여 수동 설치를 위한 [이러한 지침](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)을 따릅니다.
3. 명령줄에서 설치하는 것을 선호하는 경우 [이러한 지침](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)을 따릅니다.

## <a name="install-from-the-process-server"></a>프로세스 서버에서 설치

VM에 대해 복제가 설정된 경우 프로세스 서버에서 모바일 서비스 설치를 푸시하려면 프로세스 서버에서 VM에 액세스하는 데 사용할 수 있는 계정이 필요합니다. 이 계정은 푸시 설치에만 사용됩니다.

1. 푸시 설치에 사용할 수 있는 [계정을 만들](vmware-walkthrough-prepare-vmware.md)어야 합니다. 그런 다음 Site Recovery 배포 중에 소스 설정을 구성할 때 사용할 계정을 지정합니다.
2. Windows 또는 Linux를 실행하는 VM에서 모바일 서비스를 푸시하는 경우 [이러한 지침](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)을 따릅니다.

## <a name="other-methods"></a>다른 방법

[Configuration Manager 또는](site-recovery-install-mobility-service-using-sccm.md) [Azure Automation DSC](site-recovery-automate-mobility-service-install.md)를 사용하여 모바일 서비스를 설치하는 방법에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

[11단계: 복제 활성화](vmware-walkthrough-enable-replication.md)로 이동합니다.
