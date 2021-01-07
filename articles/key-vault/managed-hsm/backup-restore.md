---
title: Azure 관리형 HSM에 대한 전체 백업/복원 및 선택적 복원
description: 이 문서에서는 전체 백업/복원 및 선택적 복원에 대해 설명합니다.
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e051a36b3c91fadc0c3b602cb4ba8e3dbcff1294
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367134"
---
# <a name="full-backup-and-restore"></a>전체 백업 및 복원

> [!NOTE]
> 이 기능은 리소스 유형 관리형 HSM에 대해서만 사용할 수 있습니다.

관리형 HSM은 모든 키, 버전, 특성, 태그 및 역할 할당을 포함하여 HSM의 전체 내용에 대한 전체 백업을 만드는 것을 지원합니다. 백업은 HSM의 보안 도메인과 연결된 암호화 키를 사용하여 암호화됩니다.

백업은 데이터 평면 작업입니다. 백업 작업을 시작하는 호출자에게 dataAction **Microsoft.KeyVault/managedHsm/backup/start/action** 을 수행할 수 있는 권한이 있어야 합니다.

다음 기본 제공 역할만 전체 백업을 수행할 수 있는 권한이 있습니다.
- 관리형 HSM 관리자
- 관리형 HSM 백업

전체 백업을 실행하려면 다음 정보를 제공해야 합니다.
- HSM 이름 또는 URL
- 스토리지 계정 이름
- 스토리지 계정 Blob 스토리지 컨테이너
- 사용 권한이 있는 스토리지 컨테이너 SAS 토큰 `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>전체 백업

백업은 장기 실행 작업이지만 작업 ID를 즉시 반환합니다. 이 작업 ID를 사용하여 백업 프로세스의 상태를 확인할 수 있습니다. 백업 프로세스는 **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** 명명 패턴을 사용하여 지정된 컨테이너 내에 폴더를 만듭니다. 여기서 HSM_NAME은 백업되는 관리형 HSM의 이름이고 YYYY, MM, DD, HH, MM, mm, SS는 백업 명령을 받은 UTC에서 날짜/시간의 연도, 월, 날짜, 시간, 분 및 초입니다.

백업이 진행되는 동안 일부 HSM 파티션은 백업 작업을 수행하므로 HSM은 전체 처리량에서 작동하지 않을 수 있습니다.

```azurecli-interactive
# time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>전체 복원

전체 복원을 사용하면 모든 키, 버전, 특성, 태그 및 역할 할당을 포함하여 이전 백업으로 HSM의 내용을 완전히 복원할 수 있습니다. 현재 HSM에 저장된 모든 항목은 초기화되며 원본 백업을 만들 때와 동일한 상태로 돌아갑니다.

> [!IMPORTANT]
> 전체 복원은 매우 파괴적인 작업입니다. 따라서 `restore` 작업을 수행하려면 지난 30분 내에 전체 백업을 완료해야 합니다.

복원은 데이터 평면 작업입니다. 복원 작업을 시작하는 호출자에게 dataAction **Microsoft.KeyVault/managedHsm/restore/start/action** 을 수행할 수 있는 권한이 있어야 합니다. 백업이 생성된 원본 HSM과 복원이 수행될 대상 HSM에는 **반드시** 동일한 보안 도메인이 있어야 합니다. [관리형 HSM 보안 도메인 정보](security-domain.md)에 대해 자세히 알아보세요.

전체 복원을 실행하려면 다음 정보를 제공해야 합니다.
- HSM 이름 또는 URL
- 스토리지 계정 이름
- 스토리지 계정 Blob 컨테이너
- 사용 권한이 있는 스토리지 컨테이너 SAS 토큰 `rl`
- 원본 백업이 저장되는 스토리지 컨테이너 폴더 이름

복원은 장기 실행 작업이지만 작업 ID를 즉시 반환합니다. 이 작업 ID를 사용하여 복원 프로세스의 상태를 확인할 수 있습니다. 복원 프로세스가 진행 중일 때 HSM은 복원 모드로 전환되고 복원 상태 확인을 제외하고 모든 데이터 평면 명령이 사용하지 않도록 설정됩니다.

```azurecli-interactive
#### time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="backup-hsm"></a>HSM 백업

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>다음 단계
- [Azure CLI를 사용하여 관리형 HSM 관리](key-management.md)를 참조하세요.
- [관리형 HSM 보안 도메인](security-domain.md)에 대해 자세히 알아보세요.