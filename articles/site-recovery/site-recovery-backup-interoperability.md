---
title: Azure Site Recovery-백업 상호 운용성 | Microsoft Docs
description: 어떻게 Azure Site Recovery 및 Azure Backup과 함께 사용할 수 있는 개요를 제공 합니다.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731875"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>Site Recovery 및 Backup 상호 운용성에 대 한

이 문서에서는 Azure IaaS VM Backup 및 Azure VM 재해 복구를 성공적으로 사용 하기 위한 지침을 제공 합니다.

## <a name="azure-backup"></a>Azure Backup

Azure Backup은 온-프레미스 서버, 가상 머신, 가상화 된 워크 로드, SQL server, SharePoint 서버 등에 대 한 데이터를 보호 합니다. Azure Site Recovery는 오케스트레이션 하 고 Azure Vm, 온-프레미스 Vm 및 물리적 서버에 대 한 재해 복구를 관리 합니다.

## <a name="azure-site-recovery"></a>Azure Site Recovery

VM 또는 Vm의 그룹에 Azure Backup 및 Azure Site Recovery를 구성 하는 것이 가능 합니다. 상호 운용 가능한 제품은 모두입니다. Backup 및 Azure Site Recovery 간의 상호 운용성이 중요 한 몇 가지 시나리오는 다음과 같습니다.

### <a name="file-backuprestore"></a>파일 백업/복원

백업 및 복제를 둘 다 사용 하도록 설정 하 고 백업을 수행한 경우에 원본 측 VM 또는 Vm의 그룹에 모든 파일을 복원 하 여 없습니다 문제가 있습니다. 복제는 복제 상태 변경 없이 정상적으로 계속 됩니다.

### <a name="disk-backuprestore"></a>디스크 백업/복원

디스크 백업에서 복원 하는 경우 가상 컴퓨터의 보호에 다시 사용 하도록 설정 합니다.

### <a name="vm-backuprestore"></a>VM 백업/복원

VM 또는 Vm 그룹의 백업 및 복원은 지원 되지 않습니다. 작동을 위해 보호 다시 사용 하도록 설정 해야 합니다.

**시나리오** | **Azure Site Recovery에서 지원 되나요?** | **문제를 해결 하려면 있는 경우**  
--- | --- | ---
파일/폴더 백업 | 예 | 해당 없음
디스크 백업 | 현재는 아닙니다 | 사용 하지 않도록 설정 하 고 보호를 사용 하도록 설정
VM 백업 | 아닙니다. | 사용 하지 않도록 설정 하 고 보호를 사용 하도록 설정
