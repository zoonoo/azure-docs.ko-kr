---
title: "Azure Backup을 사용하여 하이브리드 백업을 보호하기 위한 보안 기능 | Microsoft Docs"
description: "Azure Backup의 보안 기능을 사용하여 백업을 좀 더 안전하게 만드는 방법 알아보기"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 5c49de40401235d38142efd60d22b0591752fe75
ms.openlocfilehash: f17802bf455b82f0b5239356c35024ecde7f1f35
ms.lasthandoff: 02/17/2017


---
# <a name="security-features-for-protecting-hybrid-backups-using-azure-backup"></a>Azure Backup을 사용하여 하이브리드 백업을 보호하기 위한 보안 기능
점점 더 많은 고객이 맬웨어, 랜섬웨어, 침입 등의 보안 문제를 겪고 있습니다. 이러한 보안 문제가 발생하면서 데이터 손실이 초래되고 보안 침해에 따른 비용 손실이 계속 증가되어 왔습니다. 이러한 공격을 방지하기 위해 Azure Backup은 이제 하이브리드 백업을 보호하기 위한 보안 기능을 제공합니다. 이 문서에서는 Microsoft Azure Recovery Services 에이전트 및 Microsoft Azure Backup Server를 사용하여 이러한 기능을 사용하도록 하고 활용하는 방법을 설명합니다. 이러한 기능은 다음 세 가지 보안 구조로 구성되어 있습니다.

1. **방지** - 암호 변경과 같은 중요한 작업이 수행될 때마다 추가 인증 계층이 추가됩니다. 이 유효성 검사는 유효한 Azure 자격 증명을 가진 사용자만 이러한 작업을 수행할 수 있는지 확인합니다.
2. **경고** - 백업 데이터 삭제와 같은 중요한 작업이 수행될 때마다 구독 관리자에게 전자 메일 알림이 전송됩니다. 이 전자 메일은 사용자에게 이러한 작업을 시기 적절하게 알립니다.
3. **복구** - 삭제된 백업 데이터는 삭제일로부터 추가 14일 동안 보존됩니다. 이를 통해 지정된 기간 내에는 데이터를 복구할 수 있게 되므로 공격이 발생하더라도 데이터가 손실되지 않습니다. 또한 데이터 손상으로부터 보호하기 위해 더 많은 수의 최소 복구 지점이 유지됩니다.

> [!NOTE]
> 다음을 사용하는 경우에만 보안 기능을 사용하도록 설정해야 합니다. <br/>
> * **MAB 에이전트** - 최소 에이전트 버전 2.0.9052. 이러한 기능을 사용하도록 설정하면 암호 변경, 데이터 삭제로 백업 중지와 같은 중요한 작업을 수행하기 위해 이 에이전트 버전으로 업그레이드해야 합니다. <br/>
> * **Azure Backup Server** - Azure Backup Server 업데이트 1을 포함한 최소 MAB 에이전트 버전 2.0.9052 <br/>
> * **DPM** - DPM 2012 R2 UR12 또는 DPM 2016 UR2를 포함한 최소 MAB 에이전트 버전 2.0.9052 <br/>
> * **IaaS VM 백업** - IaaS VM 백업에 대해서는 이러한 기능을 사용하도록 설정하지 마세요. 이러한 기능은 아직 IaaS VM 백업에 사용할 수 없으므로 사용하도록 설정해도 IaaS VM 백업에 영향을 미치지 않습니다.
> * 이러한 기능은 Recovery Services 자격 증명 모음에만 사용할 수 있습니다.
> * 새로 만든 모든 Recovery Services 자격 증명 모음은 기본적으로 이러한 기능을 사용합니다. 기존 Recovery Services 자격 증명 모음의 경우 사용자가 아래 섹션에 설명한 단계를 사용하여 이러한 기능을 사용하도록 설정해야 합니다.
> * 일단 사용하도록 설정하면 모든 Azure Recovery Services 에이전트(MARS) 컴퓨터, Azure Backup Server 및 DPM 서버에 대한 보안 기능을 자격 증명 모음에 등록합니다. <br/>
> * 이 기능의 설정은 일회성 작업이며 사용하도록 설정한 후에는 사용하지 않도록 설정할 수 없습니다. <br/>
>
>

## <a name="enabling-security-features"></a>보안 기능 사용
Recovery Services 자격 증명 모음을 만드는 사용자는 모든 보안 기능을 사용할 수 있습니다. 기존 Recovery Services 자격 증명 모음의 경우 이러한 기능을 사용하도록 설정하기 위해 다음 단계를 사용해야 합니다.

1. Azure 자격 증명을 사용하여 Azure Portal에 로그인
2. 허브 메뉴에서 Recovery Services를 입력하여 Recovery Services 목록으로 이동합니다.

    ![복구 서비스 자격 증명 모음 만들기 1단계](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    복구 서비스 자격 증명 모음의 목록이 표시됩니다. 이 목록에서 자격 증명 모음을 선택합니다.

    선택한 자격 증명 모음 대시보드가 열립니다.
3. 자격 증명 모음 아래쪽에 나타나는 항목 목록의 **설정**에서 **속성**을 클릭합니다.

    ![자격 증명 모음 속성 열기](./media/backup-azure-security-feature/vault-list-properties.png)
4. **보안 설정**에서 **업데이트**를 클릭합니다.

    ![보안 설정 열기](./media/backup-azure-security-feature/security-settings-update.png)

    업데이트 링크를 클릭하면 이러한 기능을 사용하도록 설정하고 기능 요약을 볼 수 있는 보안 설정 블레이드가 열립니다.
5. **Azure Multi-factor Authentication을 구성했습니까?** 드롭다운에서 값을 선택하여 [Azure Multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)을 사용하도록 설정했는지 확인합니다. 이 기능을 사용하도록 설정했으면 Azure Portal에 로그인하는 동안 다른 장치(예: 휴대폰)에서 인증을 받을지 묻는 메시지가 표시됩니다.

   보안 기능의 일부로 Azure Backup에서 중요한 작업을 수행할 때 Azure Portal에서 사용할 수 있는 보안 PIN을 입력해야 합니다. Multi-Factor Authentication을 사용하도록 설정하면 보안 계층이 추가되고, 유효한 Azure 자격 증명이 있으며 또 다른 장치에서 인증을 받은 허가된 사용자만 Azure Portal에 액세스하고 이러한 중요한 작업을 수행할 수 있습니다.
6. 설정/해제 단추를 사용하여 **사용**을 클릭하고 그림과 같이 위쪽의 **저장** 단추를 클릭하여 보안 설정을 저장합니다. “Azure Multi-factor Authentication을 구성했습니까?” 드롭다운에서 값을 선택한 후에만 **사용**을 선택할 수 있습니다.

    ![보안 설정 사용](./media/backup-azure-security-feature/enable-security-settings.png)

## <a name="recovering-deleted-backup-data"></a>삭제된 백업 데이터 복구
보안 조치로, Azure Backup은 추가로 14일 동안 삭제된 백업 데이터를 유지하고, 백업 데이터 삭제 작업으로 백업 중지를 수행한 경우에도 백업 데이터를 즉시 삭제하지 않습니다. 14일 동안 이 데이터를 복원하려면 다음 단계를 사용합니다.

**Microsoft Recovery Services 에이전트(MARS)** 사용자:

1. 백업이 발생하는 컴퓨터를 계속 사용할 수 있는 경우 MARS의 [동일한 컴퓨터로 데이터 복구](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine)를 사용하여 모든 이전 복구 지점에서 복구합니다.
2. 위에서 언급한 컴퓨터를 사용할 수 없는 경우 [다른 컴퓨터로 복구](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)를 사용하여 다른 MARS 컴퓨터를 통해 이 데이터를 가져옵니다.

**Azure Backup Server** 사용자:

1. 백업이 발생하는 서버를 계속 사용할 수 있는 경우 삭제된 데이터 원본을 다시 보호하고 데이터 복구 기능을 사용하여 모든 이전 복구 지점에서 복구합니다.
2. 위에서 언급한 컴퓨터를 사용할 수 없는 경우 [다른 Azure Backup Server에서 데이터 복구](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server)를 사용하여 다른 Azure Backup Server를 통해 이 데이터를 가져옵니다.

**Data Protection Manager(DPM)** 사용자의 경우:

1. 백업이 발생하는 서버를 계속 사용할 수 있는 경우 삭제된 데이터 원본을 다시 보호하고 데이터 복구 기능을 사용하여 모든 이전 복구 지점에서 복구합니다.
2. 위에서 언급한 컴퓨터를 사용할 수 없는 경우 [외부 DPM 추가](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server)를 사용하여 이 데이터를 가져오기 위해 다른 DPM 서버를 사용합니다.

## <a name="preventing-attacks"></a>공격 방지
이 기능의 일부로, 유효한 사용자만 다양한 작업을 수행할 수 있는지 확인하기 위한 검사가 추가되었습니다.

### <a name="authentication-to-perform-critical-operations"></a>중요한 작업을 수행하기 위한 인증
중요한 작업에 대한 추가 인증 계층의 일부로, 데이터 삭제로 보호 중지 및 암호 변경 작업을 수행할 때 보안 SIN을 입력하도록 요구됩니다.

보안 PIN을 받으려면 다음 단계를 사용합니다.

1. Azure Portal에 로그인합니다.
2. Recovery Services 자격 증명 모음 > 설정 > 속성으로 이동합니다.
3. 보안 PIN 아래에서 **생성**을 클릭합니다. 생성 링크를 클릭하면 Azure Recovery Services 에이전트 UI에 입력할 보안 PIN을 포함하는 블레이드가 열립니다.
    이 PIN은 5분에 동안만 유효하며 5분 후에는 자동으로 생성됩니다.

### <a name="maintaining-minimum-retention-range"></a>최소 보존 범위 유지 관리
항상 올바른 수의 복구 지점을 사용할 수 있게 하기 위해 다음과 같은 검사가 추가되었습니다.

1. 일일 보존의 경우 최소 **7**일의 보존이 수행되어야 합니다.
2. 주간 보존의 경우 최소 **4**주의 보존이 수행되어야 합니다.
3. 월간 보존의 경우 최소 **3**개월의 보존이 수행되어야 합니다.
4. 연간 보존의 경우 최소 **1**년의 보존이 수행되어야 합니다.

## <a name="notifications-for-critical-operations"></a>중요한 작업에 대한 알림
몇 가지 중요한 작업을 수행할 때마다 구독 관리자는 작업에 대한 세부 정보가 포함된 전자 메일 알림을 받게 됩니다. 전자 메일 알림의 받을 추가 전자 메일 ID를 구성하려는 경우 Azure Portal을 사용할 수 있습니다.

이 문서에 언급된 보안 기능은 대상이 지정된 공격에 대해 방어 메커니즘을 제공하여 공격자가 백업을 건드리지 못하게 합니다. 무엇보다도 이러한 기능은 공격이 발생할 때 데이터를 복구할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계
* [Azure Recovery Services 자격 증명 모음을 시작](backup-azure-vms-first-look-arm.md)하여 이러한 기능을 사용하도록 설정
* [최신 Azure Recovery Services 에이전트를 다운로드](http://aka.ms/azurebackup_agent)하여 공격으로부터 Windows 컴퓨터 및 백업 데이터 보호
* [최신 Azure Backup Server를 다운로드](https://aka.ms/latest_azurebackupserver)하여 공격으로부터 작업 및 백업 데이터 보호
* [System Center 2012 R2 Data Protection Manager용 UR12를 다운로드](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager)하거나 [System Center 2016 Data Protection Manager용 UR2를 다운로드](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager)하여 워크로드 보호 및 공격으로부터 백업 데이터 보호

