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
ms.openlocfilehash: 7fea0f74a90bc7b786a9b302d6282f9fb70e5412
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991486"
---
# <a name="operating-system-upgrade"></a>운영 체제 업그레이드
이 문서에서는 HANA 대형 인스턴스에서 운영 체제 업그레이드에 대한 세부 정보를 설명합니다.

>[!NOTE]
>OS 업그레이드는 고객의 책임이며 Microsoft 운영 지원은 업그레이드 중에 주의해야 할 주요 영역으로 안내할 수 있습니다. 업그레이드에 대해 계획하기 전에도 해당 운영 체제 공급 업체와 상담해야 합니다.

HLI 단위 프로비저닝 중에 Microsoft 운영 팀이 운영 체제를 설치합니다.
시간이 지남에 따라 HLI 단위에서 운영 체제를 유지 관리해야 합니다(예: 패치, 튜닝, 업그레이드 등).

운영 체제를 크게 변경하기 전에(예: SP1에서 SP2로 업그레이드) 상담할 지원 티켓을 열어 Microsoft 운영 팀에 문의해야 합니다.

항공권에 다음을 포함하십시오.

* HLI 구독 ID
* 서버 이름
* 적용하려는 패치 수준
* 변경하려는 날짜 

운영 팀이 서버 블레이드에서 펌웨어 업그레이드가 필요한지 확인하기 때문에 원하는 업그레이드 날짜 최소 일주일 전에 이 티켓을 여는 것이 좋습니다.


Linux 버전이 다른 여러 SAP HANA 버전에 대한 지원 매트릭스는 [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.


## <a name="known-issues"></a>알려진 문제

다음은 업그레이드 동안 몇 가지 알려진 일반적인 문제입니다.
- SKU Type II 클래스 SKU에서 SFS(software foundation server)는 OS 업그레이드 후 제거됩니다. OS 업그레이드 후 호환되는 SFS를 다시 설치해야 합니다.
- 이더넷 카드 드라이버(ENIC 및 FNIC)가 이전 버전으로 롤백했습니다. 업그레이드 후 호환되는 버전의 드라이버를 다시 설치해야 합니다.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA 대형 인스턴스(유형 I) 권장 구성

운영 체제 구성은 패치, 시스템 업그레이드 및 고객의 변경으로 인해 시간이 지남에 따라 권장되는 설정에서 멀어지일 수 있습니다. 또한 Microsoft는 최상의 성능과 복원력을 위해 기존 시스템에 필요한 업데이트를 식별합니다. 지침에 따라 네트워크 성능, 시스템 안정성 및 최적의 HANA 성능을 해결하는 권장 사항을 간략하게 설명합니다.

### <a name="compatible-enicfnic-driver-versions"></a>호환 되는 eNIC/fNIC 드라이버 버전
  적절한 네트워크 성능과 시스템 안정성을 위해 다음 호환성 표에 설명된 대로 OS별 적절한 버전의 eNIC 및 fNIC 드라이버가 설치되도록 하는 것이 좋습니다. 서버는 호환 되는 버전으로 고객에 게 제공 됩니다. 경우에 따라 OS/커널 패치 중에 드라이버가 기본 드라이버 버전으로 롤백될 수 있습니다. 적절한 드라이버 버전이 OS/커널 패치 작업 후를 실행하고 있는지 확인합니다.
       
      
  |  OS 벤더    |  OS 패키지 버전     |  펌웨어 버전  |  eNIC 드라이버 |  fNIC 드라이버 | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Suse        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   Suse        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   Suse        |  SLES 12 SP4            |   3.2.3b           |  2.3.0.47    |   2.0.0.54   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>드라이버 업그레이드 및 이전 rpm 패키지 청소 명령
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>확인 명령
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB 업데이트 실패
Azure HANA 대형 인스턴스(유형 I)의 SAP는 업그레이드 후 부팅할 수 없는 상태일 수 있습니다. 아래 절차에서 이 문제를 해결합니다.
#### <a name="execution-steps"></a>실행 단계


*   명령을 `multipath -ll` 실행합니다.
*   크기가 약 50G인 LUN ID를 얻거나 명령을 사용합니다.`fdisk -l | grep mapper`
*   파일을 `/etc/default/grub_installdevice` 줄로 `/dev/mapper/<LUN ID>`업데이트합니다. 예: /dev/매퍼/3600a0980330372f43f49522534a56
>[!NOTE]
>LUN ID는 서버마다 다릅니다.


### <a name="disable-edac"></a>EDAC 사용 안 함 
   오류 감지 및 수정(EDAC) 모듈은 메모리 오류를 감지하고 수정하는 데 도움이 됩니다. 그러나 Azure 대형 인스턴스(유형 I)에서 SAP HANA의 기본 하드웨어가 이미 동일한 기능을 수행하고 있습니다. 하드웨어 및 운영 체제(OS) 수준에서 동일한 기능을 사용하도록 설정하면 충돌이 발생할 수 있으며 서버가 계획되지 않은 경우 가끔 종료될 수 있습니다. 따라서 OS에서 모듈을 사용하지 않도록 설정하는 것이 좋습니다.

#### <a name="execution-steps"></a>실행 단계

* EDAC 모듈이 활성화되어 있는지 확인합니다. 출력이 아래 명령으로 반환되면 모듈이 활성화됨을 의미합니다. 
```
lsmod | grep -i edac 
```
* 파일에 다음 줄을 더하여 모듈을 사용하지 않도록 설정합니다.`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
변경 사항이 적용되려면 다시 부팅해야 합니다. 명령을 `lsmod` 실행하고 모듈이 출력에 없는지 확인합니다.


### <a name="kernel-parameters"></a>커널 매개 변수
   에 `processor.max_cstate` `ignore_ce` `intel_idle.max_cstate` 대한 올바른 설정이 적용되었는지 확인합니다. `transparent_hugepage` `numa_balancing`

* intel_idle.max_cstate=1
* 프로세서.max_cstate=1
* transparent_hugepage=절대 안
* numa_balancing=사용 안 함
* mce=ignore_ce


#### <a name="execution-steps"></a>실행 단계

* 파일의 `GRB_CMDLINE_LINUX` 줄에 이러한 매개 변수 추가`/etc/default/grub`
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
- 유형 [II SKU 클래스에 대한 개정 3 스탬프의 유형 II SKU에](os-backup-type-ii-skus.md) 대한 OS 백업을 참조하십시오.
