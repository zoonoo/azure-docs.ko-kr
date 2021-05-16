---
title: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 | Microsoft Docs
description: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 수행
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97c07f010fad6c12424b1684d4ff5e12c7cac3ce
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553246"
---
# <a name="operating-system-upgrade"></a>운영 체제 업그레이드
이 문서에서는 HANA 대형 인스턴스에서 운영 체제 업그레이드에 대한 세부 정보를 설명합니다.

>[!NOTE]
>OS 업그레이드는 고객 책임이며, Microsoft 운영 지원팀은 업그레이드하는 동안 주의해야 할 주요 영역을 안내할 수 있습니다. 업그레이드에 대해 계획하기 전에도 해당 운영 체제 공급 업체와 상담해야 합니다.

HLI 단위 프로비저닝 시 Microsoft 운영팀은 운영 체제를 설치합니다.
시간이 지남에 따라 HLI 단위에서 운영 체제를 유지 관리해야 합니다(예: 패치, 튜닝, 업그레이드 등).

운영 체제를 대폭 변경하기 전에(예: SP1을 SP2로 업그레이드) 참조할 지원 티켓을 열어 Microsoft 운영팀에 문의해야 합니다.

티켓에 포함된 내용은 다음과 같습니다.

* HLI 구독 ID
* 서버 이름
* 적용하려는 패치 수준
* 변경하려는 날짜 

업그레이드하기 최소 1주 전에 이 티켓을 여는 것이 좋습니다. 그러면 운영팀에서 원하는 펌웨어 버전에 대해 알 수 있습니다.

Linux 버전이 다른 여러 SAP HANA 버전에 대한 지원 매트릭스는 [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

다음은 업그레이드 동안 몇 가지 알려진 일반적인 문제입니다.
- SKU Type II 클래스 SKU에서 SFS(software foundation server)는 OS 업그레이드 후 제거됩니다. OS 업그레이드 후 호환되는 SFS를 다시 설치해야 합니다.
- 이더넷 카드 드라이버(ENIC 및 FNIC)가 이전 버전으로 롤백했습니다. 업그레이드 후 호환되는 버전의 드라이버를 다시 설치해야 합니다.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA Large Instance(Type I) 권장 구성

운영 체제 구성은 패치, 시스템 업그레이드 및 고객이 변경한 내용으로 인해 시간이 지남에 따라 권장 설정에서 드리프트할 수 있습니다. 또한 Microsoft는 기존 시스템이 최고의 성능과 복원력을 발휘하도록 최적으로 구성되어 있는지 확인하는 데 필요한 업데이트를 식별합니다. 다음 지침에서는 네트워크 성능, 시스템 안정성 및 최적의 HANA 성능을 다루는 권장 사항을 간략하게 설명합니다.

### <a name="compatible-enicfnic-driver-versions"></a>호환되는 eNIC/fNIC 드라이버 버전
  네트워크 성능과 시스템 안정성을 적절하게 유지하려면 다음 호환성 표에 나와 있는 대로 OS 관련 적절한 버전의 eNIC 및 fNIC 드라이버가 설치되어 있는지 확인하는 것이 좋습니다. 서버는 호환되는 버전이 있는 고객에게 제공됩니다. 경우에 따라 OS/커널 패치 적용 중에 드라이버가 기본 드라이버 버전으로 롤백될 수 있습니다. 적절한 드라이버 버전이 OS/Kernel 패치 적용 이후 작업을 실행 중인지 확인합니다.
       
      
  |  OS 공급업체    |  OS 패키지 버전     |  펌웨어 버전  |  eNIC 드라이버 |  fNIC 드라이버 | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>드라이버를 업그레이드하고 이전 rpm 패키지를 삭제하는 명령

#### <a name="command-to-check-existing-installed-drivers"></a>기존에 설치된 드라이버를 확인하는 명령
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>기존 eNIC/fNIC rpm 삭제
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>권장되는 eNIC/fNIC 드라이버 패키지 설치
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>설치를 확인하는 명령
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>OS 업그레이드 중 eNIC/fNIC 드라이버 설치 단계

* OS 버전 업그레이드
* 이전 rpm 패키지 제거
* 설치된 OS 버전에 따라 호환되는 eNIC/fNIC 드라이버 설치
* 시스템 재부팅
* 재부팅 후 eNIC/fNIC 버전 확인


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB 업데이트 실패
SAP on Azure HANA Large Instances(Type I)는 업그레이드 후 부팅할 수 없는 상태가 될 수 있습니다. 아래 절차를 통해 이 문제를 해결합니다.
#### <a name="execution-steps"></a>실행 단계


*   `multipath -ll` 명령을 실행합니다.
*   크기가 약 50G인 LUN ID를 가져오거나 `fdisk -l | grep mapper` 명령을 사용합니다.
*   `/etc/default/grub_installdevice` 줄을 사용하여 `/dev/mapper/<LUN ID>` 파일을 업데이트합니다. 예: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN ID는 서버마다 다릅니다.


### <a name="disable-edac"></a>EDAC 사용 안 함 
   EDAC(Error Detection And Correction) 모듈은 메모리 오류를 감지하고 수정하는 데 도움이 됩니다. 그러나 SAP HANA on Azure 대규모 인스턴스(Type I)의 기본 하드웨어는 이미 같은 기능을 수행하고 있습니다. 하드웨어 및 운영 체제(OS) 수준에서 동일한 기능을 사용하도록 설정하면 충돌이 발생할 수 있으며 예기치 않은 서버 종료를 유발할 수 있습니다. 따라서 OS에서 모듈을 사용하지 않도록 설정하는 것이 좋습니다.

#### <a name="execution-steps"></a>실행 단계

* EDAC 모듈이 사용되는지 확인합니다. 아래 명령에서 출력이 반환되는 경우에는 모듈이 사용하도록 설정되어 있는 것입니다. 
```
lsmod | grep -i edac 
```
* 파일에 `/etc/modprobe.d/blacklist.conf` 줄을 추가하여 모듈을 사용하지 않도록 설정합니다.
```
blacklist sb_edac
blacklist edac_core
```
변경 내용을 적용하려면 재부팅해야 합니다. `lsmod` 명령을 실행하고 모듈이 출력에 표시되지 않는지 확인합니다.

### <a name="kernel-parameters"></a>커널 매개 변수
   `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` 및 `intel_idle.max_cstate`에 대한 올바른 설정이 적용되었는지 확인합니다.

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>실행 단계

* `/etc/default/grub` 파일의 `GRB_CMDLINE_LINUX` 줄에 이러한 매개 변수를 추가합니다.
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* 새 grub 파일을 만듭니다.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* 시스템을 재부팅합니다.


## <a name="next-steps"></a>다음 단계
- OS 백업 Type I SKU 클래스에 대해서는 [Backup 및 복원](hana-overview-high-availability-disaster-recovery.md)을 참조합니다.
- Type II SKU 클래스는 [수정 버전 3 스탬프의 Type II SKU용 OS 백업](os-backup-type-ii-skus.md)을 참조하세요.
