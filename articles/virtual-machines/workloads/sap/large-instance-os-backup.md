---
title: Azure(큰 인스턴스)에서 SAP HANA의 운영 체제 백업 및 복원| Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)의 운영 체제 백업 및 복원 수행
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c044d09626fe4c1d4409f98fc615b5a7d70c7cc8
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108230108"
---
# <a name="os-backup-and-restore"></a>OS 백업 및 복원

이 문서에서는 운영 체제 파일 수준의 백업 및 복원을 수행하는 단계에 대해 설명합니다. 프로시저는 형식 I 또는 형식 II, 수정 버전 3 이상, 위치 등의 특정 매개 변수에 따라 다릅니다. 리소스에 대해 이러한 매개 변수에 대한 정확한 값을 얻으려면 Microsoft 작업을 확인하세요.

## <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>수정 버전 3 스탬프의 유형 II SKU에 대한 OS 백업 및 복원

아래 정보에서는 수정 버전 3의 HANA 대규모 인스턴스의 **형식 II SKU** 의 운영 체제 파일 수준 백업 및 복원 수행 단계를 설명합니다.

>[!Important]
> **해당 문서는 수정 버전 4의 HANA 대규모 인스턴스 스탬프의 형식 II SKU 배포에 적용할 수 없습니다.** 형식 I SKU가 이미 수정 버전 3 스탬프에 있으므로 수정 버전 4의 HANA 대규모 인스턴스 스탬프에 배포된 형식 II HANA 대규모 인스턴스 유닛의 Boot LUNS는 스토리지 스냅샷으로 백업할 수 있습니다.


>[!NOTE]
>OS 백업 스크립트는 서버에 미리 설치되어 있는 ReaR 소프트웨어를 사용합니다.  

Microsoft `Service Management` 팀이 프로비저닝을 완료한 뒤에는 기본적으로 서버가 백업 일정 두 개로 구성되어 운영 체제의 파일 시스템 수준을 운영 체제 뒤로 백업할 수 있도록 합니다. 다음 명령을 사용하여 백업 작업 일정을 확인할 수 있습니다.
```
#crontab –l
```
다음 명령을 사용하여 언제든지 백업 일정을 변경할 수 있습니다.
```
#crontab -e
```
### <a name="how-to-take-a-manual-backup"></a>수동 백업을 수행하는 방법

**cron 작업** 을 사용하여 OS 파일 시스템 백업이 이미 예약되었습니다. 그러나 운영 체제 파일 수준 백업을 수동으로도 수행할 수 있습니다. 수동 백업을 수행하려면 다음 명령을 실행합니다.

```
#rear -v mkbackup
```
다음 화면에서는 샘플 수동 백업을 보여줍니다.

![방법](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


### <a name="how-to-restore-a-backup"></a>백업을 복원하는 방법

백업에서 전체 백업 또는 개별 파일을 복원할 수 있습니다. 복원하려면 다음 명령을 실행합니다.

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
복원한 후에 파일은 현재 작업 디렉터리에 복구됩니다.

다음 명령에서는 파일(*/etc/fstabfrom*) 및 백업 파일(*backup.tar.gz*)의 복원을 보여줍니다.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>백업에서 복원된 후에 원하는 위치에 파일을 복사해야 합니다.

다음 스크린샷은 전체 백업의 복원을 보여 줍니다.

![복원 관련 명령 프롬프트 창을 보여 주는 스크린샷.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

### <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>ReaR 도구를 설치하고 구성을 변경하는 방법 

ReaR(Relax-and-Recover) 패키지는 HANA 큰 인스턴스의 **형식 II SKU** 에 **미리 설치되어** 있고 추가 작업이 필요하지 않습니다. 운영 체제 백업을 위해 ReaR를 사용하여 직접 시작할 수 있습니다.
그러나 고유한 패키지를 설치해야 하는 경우에 ReaR 도구를 설치하고 구성하는 나열된 단계를 따르면 됩니다.

**ReaR** 백업 패키지를 설치하려면 다음 명령을 사용합니다.

**SLES** 운영 체제의 경우 다음 명령을 사용합니다.
```
#zypper install <rear rpm package>
```
**RHEL** 운영 체제의 경우 다음 명령을 사용합니다. 
```
#yum install rear -y
```
ReaR 도구를 구성하려면 *file /etc/rear/local.conf* 에서 **OUTPUT_URL** 및 **BACKUP_URL** 매개 변수를 업데이트해야 합니다.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

다음 스크린샷은 전체 백업의 복원을 보여 줍니다. ![스크린샷에 ReaR 도구를 사용하는 복원 관련 명령 프롬프트 창이 표시됩니다.](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)


## <a name="os-backup-and-restore-for-all-other-skus"></a>모든 다른 SKU에 대한 OS 백업 및 복원

아래 정보는 수정 버전 3의 HANA Large 인스턴스의 **유형 II SKU** 를 제외하고 모든 수정 버전의 모든 SKU에 대한 운영 체제 파일 수준의 백업 및 복원을 수행하는 단계를 설명합니다.

### <a name="how-to-take-a-manual-backup"></a>수동 백업을 수행하는 방법

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)에서 SAP HANA에 대한 최신 Microsoft 스냅샷 도구를 다운로드하고 `--type=boot` 플래그와 `crontab`을 통해 정기적으로 실행되도록 구성합니다. 이렇게 하면 정기적인 OS 백업이 보장됩니다. 다음 예에서는 Type-I SKU OS 백업에 대한 cron 일정을 `/etc/crontab`에서 보여 줍니다.

```
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```

다음 예에서는 Type-II SKU OS 백업에 대한 cron 일정을 `/etc/crontab`에서 보여 줍니다.

```
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

추가 참조 -
- [스토리지 스냅샷 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)
- [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)의 SAP HANA용 Microsoft 스냅샷 도구 가이드

### <a name="how-to-restore-a-backup"></a>백업을 복원하는 방법

OS 자체에서는 복원 작업을 수행할 수 없습니다. 이 작업을 수행하려면 Microsoft 작업을 통해 지원 티켓을 제기하세요. 복원 작업을 수행하려면 HLI 인스턴스가 전원이 꺼진 상태여야 하므로 그에 따라 예약하세요.

### <a name="managed-os-snapshots"></a>관리되는 OS 스냅샷

Azure는 HLI 리소스에 대해 자동으로 OS 백업을 수행할 수 있습니다. 이러한 백업은 매일 한 번씩 수행되며 Azure는 최근 3개의 백업을 유지합니다. 이는 다음 지역의 모든 고객에 대해 기본적으로 사용하도록 설정됩니다.
- 미국 서부
- 오스트레일리아 동부
- 오스트레일리아 남동부
- 미국 중남부
- 미국 동부 2

이 기능은 다음 지역에서 부분적으로 사용할 수 있습니다.
- 미국 동부
- 북유럽
- 서유럽

이 기능에서 수행하는 백업의 빈도 또는 보존 기간은 변경할 수 없습니다. HLI 리소스에 대해 다른 OS 백업 전략이 필요한 경우 Microsoft 작업을 사용하여 지원 티켓을 생성한 다음 이 문서의 이전 섹션에 제공된 지침을 사용하여 OS 백업을 수행하도록 SAP HANA용 Microsoft 스냅샷 도구를 구성하고 이 기능을 옵트아웃(opt out)하도록 선택할 수 있습니다.