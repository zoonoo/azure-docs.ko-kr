---
title: "Azure Backup에서 System Center Data Protection Manager 문제 해결 | Microsoft Docs"
description: "System Center Data Protection Manager 문제 해결"
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>System Center Data Protection Manager 문제 해결

SC DPM의 최신 릴리스 정보는 [여기](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)에서 제공합니다.
보호 지원 매트릭스는 [여기](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)에서 제공합니다.

## <a name="replica-is-inconsistent"></a>복제본이 불일치

이 오류는 복제본 만들기 작업 실패, 저널 변경 문제, 볼륨 수준 필터 비트맵 오류, 원본 컴퓨터가 예기치 않게 종료됨, 동기화 로그 오버플로 또는 실제로 복제본 불일치 등, 다양한 이유로 발생할 수 있습니다.  이 문제를 해결하려면 다음 단계를 따릅니다.
- 일관되지 않은 상태를 제거하려면 일관성 검사를 수동으로 실행하거나 일일 일관성 검사를 예약합니다.
- MAB Server 또는 System Center DPM의 최신 버전을 사용 중인지 확인합니다.
- 자동 일관성 검사를 사용하도록 설정되었는지 확인합니다.
- 명령 프롬프트에서 서비스를 다시 시작해 봅니다("net stop dpmra" 다음에 "net start dpmra").
- 네트워크 연결 및 대역폭 요구 사항에 부합하는지 확인합니다.
- 원본 컴퓨터가 예기치 않게 종료되었는지 확인합니다.
- 디스크가 정상 상태이고 복제본을 위한 공간이 충분한지 확인합니다.
- 중복된 백업 작업이 동시에 실행되지 않는지 확인합니다.

## <a name="online-recovery-point-creation-failed"></a>온라인 복구 지점 생성에 실패했습니다.

이 문제를 해결하려면 다음 단계를 따릅니다.
- 최신 버전의 Azure Backup Agent가 설치되어 있는지 확인합니다.
- 수동으로 보호 작업 영역에서 복구 지점을 만들어 봅니다.
- 데이터 원본에 대해 일관성 검사를 실행합니다.
- 네트워크 연결 및 대역폭 요구 사항에 부합하는지 확인합니다.
- 복제본 데이터의 상태가 일관되지 않습니다. 이 데이터 원본의 디스크 복구 지점을 만듭니다.
- 복제본이 있고, 누락되지 않았는지 확인합니다.
- 복제본이 USN 저널을 만드는 데 충분한 공간을 가집니다.

## <a name="unable-to-configure-protection"></a>보호를 구성할 수 없음

이 오류는 DPM 서버가 보호되는 서버와 연결할 수 없을 때 나타납니다. 이 문제를 해결하려면 다음 단계를 따릅니다.
- 최신 버전의 Azure Backup Agent가 설치되어 있는지 확인합니다.
- DPM 서버와 보호되는 서버 사이에 연결(네트워크/방화벽/프록시)이 있는지 확인합니다.
- SQL Server를 보호하는 경우 NT AUTHORITY\SYSTEM에서 로그인 속성에 sysadmin이 사용하도록 설정되었는지 확인합니다. 

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>이 서버는 저장소 자격 증명을 통해 지정된 저장소에 등록되지 않았습니다.

이 오류는 선택한 보관 자격 증명 파일이 복구를 시도하려는 System Center DPM/Azure Backup Server와 연결된 Recovery Services 자격 증명 모음에 속해 있지 않을 때 나타납니다. 이 문제를 해결하려면 다음 단계를 따릅니다.
- System Center DPM/Azure Backup Server가 등록된 Recovery Services 자격 증명 모음에서 보관 자격 증명 파일을 다운로드합니다.
- 다운로드한 최신 자격 증명 모음 파일을 사용하여 자격 증명 모음에 서버를 등록해 봅니다.

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>복구 가능한 데이터가 없거나 선택한 서버가 DPM 서버가 아닙니다.
이 오류는 Recovery Services 자격 증명 모음에 다른 System Center DPM/Azure Backup Server가 등록되지 않았거나, 서버에서 아직 메타데이터를 업로드하지 않았거나, 선택한 서버가 System Center DPM/Azure Backup Server가 아닐 때 나타납니다.
- Recovery Services 자격 증명 모음에 다른 System Center DPM/Azure Backup Server가 등록된 경우 최신 Azure Backup 에이전트가 설치되어 있는지 확인합니다.
- 다른 System Center DPM/Azure Backup Server가 Recovery Services 자격 증명 모음에 등록된 경우, 설치하고 하루 동안 기다린 다음, 복구 프로세스를 시작하세요. 야간 작업을 통해 보호된 모든 백업에 대한 메타데이터가 클라우드로 업로드됩니다. 이제 데이터를 복구에 사용할 수 있습니다.

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>암호화 암호가 다음 서버에 연결된 암호와 일치하지 않습니다.

> [!NOTE]
>암호를 잊거나 분실한 경우 데이터를 복구할 옵션이 없습니다. 유일한 방법은 암호를 다시 생성하여 향후 백업 데이터의 암호화에 사용하는 것입니다.
>
>

이 오류는 데이터를 복구하려는 System Center DPM/Azure Backup Server의 데이터를 암호화하는 데 사용된 암호화 암호가 입력한 암호화 암호와 일치하지 않아 발생합니다. 에이전트는 데이터의 암호를 해독할 수 없습니다. 따라서 복구가 실패합니다. 이 문제를 해결하려면 다음 단계를 따릅니다.
- 데이터를 복구하려는 System Center DPM/Azure Backup Server에 연결된 암호화 암호를 정확하게 입력합니다. 
