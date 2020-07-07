---
title: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 | Microsoft Docs
description: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 수행
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8485f3474da18e052bc0eab6c053be084ef884a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82192419"
---
# <a name="operating-system-upgrade"></a>운영 체제 업그레이드
이 문서에서는 HANA 대형 인스턴스에서 운영 체제 업그레이드에 대한 세부 정보를 설명합니다.

>[!NOTE]
>OS 업그레이드는 고객의 책임입니다. Microsoft operations support는 업그레이드 하는 동안 시청할 주요 영역을 안내할 수 있습니다. 업그레이드에 대해 계획하기 전에도 해당 운영 체제 공급 업체와 상담해야 합니다.

HLI 유닛 프로 비전 중에 Microsoft 운영 팀에서 운영 체제를 설치 합니다.
시간이 지남에 따라 HLI 단위에서 운영 체제를 유지 관리해야 합니다(예: 패치, 튜닝, 업그레이드 등).

운영 체제에 대 한 주요 변경 작업을 수행 하기 전에 (예: s p 1에서 s p 1으로 업그레이드) Microsoft Operations 팀에 문의 하 여 지원 티켓을 열어 문의 해야 합니다.

티켓에 포함:

* HLI 구독 ID
* 서버 이름
* 적용하려는 패치 수준
* 변경하려는 날짜 

운영 팀이 서버 블레이드에서 펌웨어 업그레이드가 필요한지 확인하기 때문에 원하는 업그레이드 날짜 최소 일주일 전에 이 티켓을 여는 것이 좋습니다.


Linux 버전이 다른 여러 SAP HANA 버전에 대한 지원 매트릭스는 [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.


## <a name="known-issues"></a>알려진 문제

다음은 업그레이드 동안 몇 가지 알려진 일반적인 문제입니다.
- SKU Type II 클래스 SKU에서 SFS(software foundation server)는 OS 업그레이드 후 제거됩니다. OS 업그레이드 후 호환 되는 SFS를 다시 설치 해야 합니다.
- 이더넷 카드 드라이버(ENIC 및 FNIC)가 이전 버전으로 롤백했습니다. 업그레이드 후에 호환 되는 드라이버 버전을 다시 설치 해야 합니다.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA Large Instance (Type I) 권장 구성

운영 체제 구성은 패치, 시스템 업그레이드 및 고객이 변경한 내용으로 인해 시간이 지남에 따라 권장 설정에서 드리프트 할 수 있습니다. 또한 Microsoft는 기존 시스템이 최적의 성능 및 복원 력을 위해 최적으로 구성 되었는지 확인 하는 데 필요한 업데이트를 식별 합니다. 다음 지침에서는 네트워크 성능, 시스템 안정성 및 최적의 HANA 성능을 다루는 권장 사항을 간략하게 설명 합니다.

### <a name="compatible-enicfnic-driver-versions"></a>호환 되는 eNIC/fNIC 드라이버 버전
  네트워크 성능과 시스템 안정성을 적절 하 게 유지 하려면 다음 호환성 테이블에 표시 된 대로 OS 관련 적절 한 버전의 eNIC 및 fNIC 드라이버가 설치 되어 있는지 확인 하는 것이 좋습니다. 서버는 호환 되는 버전이 있는 고객에 게 배달 됩니다. 일부 경우에 OS/커널 패치를 적용 하는 동안 드라이버는 기본 드라이버 버전으로 롤백할 수 있습니다. 적절 한 드라이버 버전이 post OS/Kernel 패칭 작업을 실행 중인지 확인 합니다.
       
      
  |  OS 공급 업체    |  OS 패키지 버전     |  펌웨어 버전  |  eNIC 드라이버 |  fNIC 드라이버 | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3 i           |  2.3.0.47    |   2.0.0.54   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>드라이버를 업그레이드 하 고 이전 rpm 패키지를 정리 하는 명령

#### <a name="command-to-check-existing-installed-drivers"></a>기존에 설치 된 드라이버를 확인 하는 명령
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>기존 eNIC/fNIC rpm 삭제
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>권장 eNIC/fNIC 드라이버 패키지를 설치 합니다.
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>설치를 확인 하는 명령
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB 업데이트 실패
Azure HANA Large Instances (유형 I)의 SAP는 업그레이드 후 부팅할 수 없는 상태가 될 수 있습니다. 아래 절차에서는이 문제를 해결 합니다.
#### <a name="execution-steps"></a>실행 단계


*   `multipath -ll`명령을 실행 합니다.
*   크기가 약 50G 인 LUN ID를 가져오거나 명령을 사용 합니다.`fdisk -l | grep mapper`
*   `/etc/default/grub_installdevice`줄을 사용 하 여 파일을 업데이트 `/dev/mapper/<LUN ID>` 합니다. 예:/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN ID는 서버 마다 다릅니다.


### <a name="disable-edac"></a>EDAC 사용 안 함 
   EDAC (오류 검색 및 수정) 모듈은 메모리 오류를 검색 하 고 수정 하는 데 도움이 됩니다. 그러나 Azure의 SAP HANA(대규모 인스턴스) (유형 I)의 기본 하드웨어는 이미 같은 기능을 수행 하 고 있습니다. 하드웨어 및 운영 체제 (OS) 수준에서 동일한 기능을 사용 하도록 설정 하면 충돌이 발생할 수 있으며 예기치 않은 서버의 종료를 유발할 수 있습니다. 따라서 OS에서 모듈을 사용 하지 않도록 설정 하는 것이 좋습니다.

#### <a name="execution-steps"></a>실행 단계

* EDAC 모듈이 사용 되는지 확인 합니다. 아래 명령에서 출력이 반환 되는 경우에는 모듈이 사용 하도록 설정 되어 있는 것입니다. 
```
lsmod | grep -i edac 
```
* 파일에 다음 줄을 추가 하 여 모듈을 사용 하지 않도록 설정 합니다.`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
변경 내용을 적용 하려면 다시 부팅 해야 합니다. `lsmod`명령을 실행 하 고 모듈이 출력에 표시 되지 않는지 확인 합니다.


### <a name="kernel-parameters"></a>커널 매개 변수
   ,, 및에 대 한 올바른 설정이 적용 되었는지 확인 `transparent_hugepage` `numa_balancing` `processor.max_cstate` `ignore_ce` `intel_idle.max_cstate` 합니다.

* intel_idle max_cstate = 1
* 프로세서. max_cstate = 1
* transparent_hugepage = 안 함
* numa_balancing = 사용 안 함
* mce = ignore_ce


#### <a name="execution-steps"></a>실행 단계

* 파일의 줄에 이러한 매개 변수를 추가 합니다. `GRB_CMDLINE_LINUX``/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* 새 grub 파일을 만듭니다.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* 시스템을 다시 부팅 합니다.


## <a name="next-steps"></a>다음 단계
- OS 백업 Type I SKU 클래스에 대해서는 [Backup 및 복원](hana-overview-high-availability-disaster-recovery.md)을 참조합니다.
- 유형 II SKU 클래스에 대 한 [수정 버전 3 스탬프의 유형 Ii sku에 대 한 OS 백업을](os-backup-type-ii-skus.md) 참조 하세요.
