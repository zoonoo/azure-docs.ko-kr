---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일관 된 스냅숏 도구 시작 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구를 설치 하는 방법에 대 한 지침을 제공 합니다.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736365"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일관 된 스냅숏 도구 시작 (미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구를 설치 하는 방법에 대 한 지침을 제공 합니다.

## <a name="getting-the-snapshot-tools"></a>스냅숏 도구 가져오기

Microsoft에서 [AzAcSnap Installer](https://aka.ms/azacsnapdownload) 의 최신 버전을 가져오는 것이 좋습니다.

자동 설치 파일에는 다운로드 한 설치 관리자를 GPG 확인할 수 있도록 Microsoft의 공개 키로 서명 된 연결 된 [AzAcSnap 설치 관리자 서명 파일이](https://aka.ms/azacsnapdownloadsignature) 있습니다.

이러한 다운로드가 완료 되 면이 가이드의 단계에 따라를 설치 합니다.

### <a name="verifying-the-download"></a>다운로드 확인

위에서 다운로드할 수 있는 설치 관리자에는 파일 이름 확장명을 사용 하는 연결 된 PGP 서명 파일이 있습니다 `.asc` . 이 파일은 다운로드 한 설치 관리자가 확인 된 Microsoft 제공 파일 인지 확인 하는 데 사용할 수 있습니다. Linux 패키지 서명에 사용 되는 Microsoft PGP 공개 키는 여기 ()에서 사용할 수 <https://packages.microsoft.com/keys/microsoft.asc> 있으며 서명 파일에 서명 하는 데 사용 되었습니다.

다음과 같이 Microsoft PGP 공개 키를 사용자의 로컬으로 가져올 수 있습니다.

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

다음 명령은 Microsoft PGP 공개 키를 신뢰 합니다.

1. 저장소의 키를 나열 합니다.
2. Microsoft 키를 편집 합니다.
3. 지문을 확인 합니다. `fpr`
4. 키에 서명 하 여 신뢰 합니다.

```bash
gpg --list-keys
```

나열 된 키:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

대화형 `gpg` 세션 서명 Microsoft 공개 키에서 출력:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

설치 관리자에 대 한 PGP 서명 파일은 다음과 같이 확인할 수 있습니다.

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

GPG 사용에 대 한 자세한 내용은 [GNU 개인 정보 안내서](https://www.gnupg.org/gph/en/manual/book1.html)를 참조 하세요.

## <a name="supported-scenarios"></a>지원되는 시나리오

스냅숏 도구는 다음과 같은 시나리오에서 사용할 수 있습니다.

- 단일 SID
- 다중 SID
- HSR
- 확장
- MDC (단일 테 넌 트만 지원 됨)
- 단일 컨테이너
- SUSE 운영 체제
- RHEL 운영 체제
- SKU 유형 I
- SKU 형식 II

[HANA Large Instances에 대해 지원 되는 시나리오를](../virtual-machines/workloads/sap/hana-supported-scenario.md) 참조 하세요.

## <a name="snapshot-support-matrix-from-sap"></a>SAP의 스냅숏 지원 매트릭스

다음 매트릭스는 SAP에서 저장소 스냅숏 백업에 대해 지원 되는 SAP HANA 버전에 대 한 지침으로 제공 됩니다.

| 데이터베이스 버전       |1.0 SPS12|2.0 SPS0|2.0 SPS1|2.0 SPS2|2.0 SPS3|2.0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|단일 컨테이너 데이터베이스| √       | √      | -      | -      | -      | -      |
|MDC 단일 테 넌 트        | -       | -      | √      | √      | √      | √      |
|MDC 다중 테 넌 트     | -       | -      | -      | -      | -      | √      |
> √ = <small>SAP에서 저장소 스냅숏에 대해 지원 됨</small>

## <a name="important-things-to-remember"></a>유의해야 할 중요한 사항

- 스냅숏 도구를 설치한 후에는 사용 가능한 저장소 공간을 지속적으로 모니터링 하 고 필요한 경우 정기적으로 이전 스냅숏을 삭제 하 여 저장소를 채우지 않도록 합니다.
- 항상 최신 스냅숏 도구를 사용 합니다.
- 동일한 버전의 스냅숏 도구를 가로에서 사용 합니다.
- 프로덕션 시스템에서를 사용 하기 전에 스냅숏 도구를 테스트 하 고 필요한 매개 변수 및 명령의 출력을 확인 하세요.
- 이 문서의 아래 세부 정보에서 백업에 대 한 HANA 사용자를 설정 하는 경우 각 HANA 인스턴스에 대해 사용자를 설정 해야 합니다. MDC의 SYSTEMDB (SID 데이터베이스가 아닌)에서 HANA 인스턴스에 액세스 하는 SAP HANA 사용자 계정을 만듭니다. 단일 컨테이너 환경에서는 테 넌 트 데이터베이스에서 설정할 수 있습니다.
- 고객은 저장소 액세스를 위해 SSH 공개 키를 제공 해야 합니다. 이 작업은 노드 마다 한 번 수행 해야 하며 명령이 실행 되는 각 사용자에 대해 수행 해야 합니다.
- 볼륨당 스냅숏 수는 250 개로 제한 됩니다.
- 구성 파일을 수동으로 편집 하는 경우 메모장 등의 Windows 편집기가 아니라 항상 "vi" 등의 Linux 텍스트 편집기를 사용 합니다. Windows 편집기를 사용 하면 파일 형식이 손상 될 수 있습니다.
  - `hdbuserstore`SAP HANA 사용자가 SAP HANA와 통신할 수 있도록 설정 합니다.
- DR의 경우: dr을 설정 하기 전에 DR 노드에서 스냅숏 도구를 테스트 해야 합니다.
- 디스크 공간을 정기적으로 모니터링 하 고, 자동화 된 로그 삭제는 `--trim`   `azacsnap -c backup` SAP HANA 2 이상 버전의 옵션을 사용 하 여 관리 됩니다.
- **스냅숏이 수행 되지 않는 위험** -스냅숏 도구는 구성 파일에 지정 된 SAP HANA 시스템의 노드와만 상호 작용 합니다.  이 노드를 사용할 수 없게 되 면 자동으로 다른 노드와 통신을 시작 하는 메커니즘이 없습니다.  
  - 대기 시나리오 **를 사용 하는 SAP HANA Scale-Out** 의 경우에는 마스터 노드에 스냅숏 도구를 설치 하 고 구성 하는 것이 일반적입니다. 그러나 마스터 노드를 사용할 수 없게 되 면 대기 노드는 마스터 노드 역할을 수행 합니다. 이 경우 구현 팀은 누락 된 스냅숏을 방지 하기 위해 두 노드 (마스터 및 기타) 모두에 스냅숏 도구를 구성 해야 합니다. 표준 상태에서는 마스터 노드가 crontab에서 시작 된 HANA 스냅숏을 만들지만 마스터 노드 장애 조치 (failover) 후에는 새 마스터 노드 (이전의 대기)와 같은 다른 노드에서 해당 스냅숏을 실행 해야 합니다. 이러한 결과를 얻기 위해 대기 노드에는 장애 조치 (failover) 전에 준비 된 스냅숏 도구, 저장소 통신 사용, hdbuserstore 구성, `azacsnap.json` 구성 및 crontab 명령이 준비 되어 있어야 합니다.
  - **SAP HANA HSR HA** 시나리오의 경우 (주 및 보조) 노드에 스냅숏 도구를 설치, 구성 및 예약 하는 것이 좋습니다. 그런 다음 주 노드를 사용할 수 없게 되 면 보조 노드는 보조 데이터베이스에서 수행 되는 스냅숏과 함께 수행 됩니다. 표준 상태에서 주 노드는 crontab에 의해 시작 되는 HANA 스냅숏을 사용 하 고 보조 노드는 스냅숏을 만들려고 시도 하지만 주 데이터베이스가 올바르게 작동 하므로 실패 합니다.  그러나 주 노드 장애 조치 (failover) 후에 이러한 스냅숏은 보조 노드에서 실행 됩니다. 이러한 결과를 얻기 위해 보조 노드에는 설치 된 스냅숏 도구, 저장소 통신 사용, `hdbuserstore` 구성 됨, azacsnap.js구성 됨 및 장애 조치 (failover) 전에 crontab 사용이 설정 되어 있어야 합니다.

## <a name="guidance-provided-in-this-document"></a>이 문서에 제공 된 지침

스냅숏 도구를 사용 하는 방법을 보여 주기 위해 다음 지침이 제공 됩니다.

### <a name="taking-snapshot-backups"></a>스냅숏 백업 수행

- [저장소 스냅숏에 대 한 필수 구성 요소](azacsnap-installation.md#prerequisites-for-installation)
  - [저장소와의 통신 사용](azacsnap-installation.md#enable-communication-with-storage)
  - [SAP HANA와 통신 사용](azacsnap-installation.md#enable-communication-with-sap-hana)
- [수동으로 스냅숏을 만드는 방법](azacsnap-tips.md#take-snapshots-manually)
- [자동 스냅숏 백업을 설정 하는 방법](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [스냅숏을 모니터링 하는 방법](azacsnap-tips.md#monitor-the-snapshots)
- [스냅숏을 삭제 하는 방법](azacsnap-tips.md#delete-a-snapshot)
- [스냅숏을 복원 하는 방법](azacsnap-tips.md#restore-a-snapshot)
- [스냅숏을 복원 하는 방법 `boot`](azacsnap-tips.md#restore-a-boot-snapshot)
- [스냅숏에 대해 알아야 할 핵심 사항은 무엇 인가요?](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> 스냅숏은 단일 SID와 다중 SID에 대해 테스트 됩니다.

### <a name="performing-disaster-recovery"></a>재해 복구 수행

- [DR 설치를 위한 필수 구성 요소](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [재해 복구를 설정 하는 방법](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [기본 사이트에서 DR 사이트로의 데이터 복제를 모니터링 하는 방법](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [DR 사이트로 장애 조치 (failover)를 수행 하는 방법](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일치 스냅숏 도구 설치](azacsnap-installation.md)