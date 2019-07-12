---
title: Azure 마이그레이션 서버 마이그레이션을 사용 하 여 VMware 마이그레이션 옵션을 선택 | Microsoft Docs
description: Azure 마이그레이션 서버 마이그레이션을 사용 하 여 Azure에 VMware Vm 마이그레이션에 대 한 옵션의 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811571"
---
# <a name="select-a-vmware-migration-option"></a>VMware 마이그레이션 옵션을 선택 합니다.

Azure 마이그레이션 Server 마이그레이션 도구를 사용 하 여 Azure에 VMware Vm을 마이그레이션할 수 있습니다. 이 도구는 두 가지 VMware VM 마이그레이션에 대 한 옵션을 제공합니다.

- 에이전트 없는 복제를 사용 하 여 마이그레이션입니다. 에 아무 것도 설치 하지 않고도 Vm을 마이그레이션하십시오.
- 복제 에이전트를 사용 하 여 마이그레이션입니다. 복제를 위해 VM에 에이전트를 설치 합니다.


에이전트 없는 복제 하는 것이 배포 관점에서에서 더 쉽지만, 많은 제한 사항이 갖고 있습니다.

## <a name="agentless-migration-limitations"></a>에이전트 없는 마이그레이션 제한 사항

제한 사항은 다음과 같습니다.

- **동시 복제**: VCenter Server에서에서 최대 50 개의 Vm 동시에 복제할 수 있습니다.<br/> 마이그레이션에 대 한 50 개 이상의 Vm에 있는 경우 Vm의 여러 일괄 처리를 만듭니다.<br/> 한 번 더 복제 성능이 영향을 받습니다.
- **VM 디스크**: 마이그레이션하려는 VM에는 60 개 디스크가 있어야 합니다.
- **VM 운영 체제**: 일반적으로 모든 Windows Server 또는 Linux 운영 체제를 Azure Migrate를 마이그레이션할 수 있지만 Azure에서 실행할 수 있도록 Vm에서 변경 내용이 필요할 수 있습니다. Azure Migrate는 이러한 운영 체제에 대 한 자동으로 변경 합니다.
    - Red Hat Enterprise Linux 6.5+, 7.0+
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
    - Debian 7, 8
    - 다른 운영 체제 마이그레이션 시작 하기 전에 수동으로 조정 해야 합니다. 합니다 [자습서를 마이그레이션](tutorial-migrate-vmware.md) 이 작업을 수행 하는 방법에 설명 합니다.
- **Linux boot**: 전용 파티션에 /boot 인 경우 OS 디스크에 있어야 하 고 여러 디스크에 걸쳐 분산 하지 않습니다.<br/> /Boot 루트 (/) 파티션의 일부 이면 다음 '/' 파티션 OS 디스크에 있고 다른 디스크에 걸쳐 있지 않고 합니다.
- **UEFI 부팅**: UEFI 부팅을 사용 하 여 Vm 마이그레이션에 대 한 지원 되지 않습니다.
- **암호화 (BitLocker, cryptfs) 디스크/볼륨**: 암호화 된 디스크/볼륨을 사용 하 여 Vm 마이그레이션에 대 한 지원 되지 않습니다.
- **RDM/통과 디스크가**: Vm이 RDM 또는 통과 디스크에 있는 경우 이러한 디스크를 Azure에 복제할 수 없습니다.
- **NFS**: Vm의 볼륨으로 탑재 하는 NFS 볼륨을 복제할 수 없습니다.
- **대상 저장소**: 관리 되는 디스크 (HDD 표준, 프리미엄 SSD)를 사용 하 여 Azure Vm에 VMware Vm만 마이그레이션할 수 있습니다.



## <a name="deployment-steps-comparison"></a>배포 단계 비교

한계를 검토 한 후 각 솔루션을 배포 하는 단계를 이해 도움이 선택할 옵션을 결정 합니다.

**태스크** | **세부 정보** |**에이전트 없는** | **에이전트 기반**
--- | --- | --- | ---
**마이그레이션을 위해 VMware 서버 및 Vm 준비** | VMware 서버 및 Vm에서 다양 한 설정 구성 합니다. | 필수 | 필수
**서버 마이그레이션 도구를 추가 합니다.** | Azure Migrate 프로젝트에서 Azure 마이그레이션 Server 마이그레이션 도구를 추가 합니다. | 필수 | 필수
**Azure Migrate 어플라이언스를 배포 합니다.** | VM 검색 및 평가 대 한 VMware VM에 경량 어플라이언스를 설정 합니다. | 필수 | 필수 아님.
**Vm에 모바일 서비스 설치** | 복제 하려는 각 VM에 모바일 서비스 설치 | 필요하지 않음 | 필수
**Azure 마이그레이션 서버 마이그레이션 복제 어플라이언스를 배포 합니다.** | Vm을 검색 하는 VMware VM에 어플라이언스를 설정 하 고 Vm 및 Azure 마이그레이션 서버 마이그레이션에서 실행 되는 모바일 서비스 간의 연결 | 필요하지 않음 | 필수
**Vm을 복제**합니다. VM 복제를 사용 하도록 설정 합니다. | 복제 설정을 구성 하 고 복제할 Vm 선택 | 필수 | 필수
**테스트 마이그레이션 실행** | 모든 항목이 예상 대로 작동 되도록 테스트 마이그레이션을 실행 합니다. | 필수 | 필수
**전체 마이그레이션 실행** | Vm을 마이그레이션하십시오. | 필수 | 필수




## <a name="next-steps"></a>다음 단계

[VMware Vm을 마이그레이션할](tutorial-migrate-vmware.md) 에이전트 없이 마이그레이션을 사용 하 여 합니다.



