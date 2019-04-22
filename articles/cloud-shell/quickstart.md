---
title: Azure Cloud Shell의 Bash 빠른 시작 | Microsoft Docs
description: Azure Cloud Shell의 Bash에 대한 빠른 시작
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: b8f96de7214a46c9e38182c141343a46c0e28139
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882092"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Azure Cloud Shell의 Bash에 대한 빠른 시작

이 문서는 [Azure Portal](https://ms.portal.azure.com/)에서 Azure Cloud Shell의 Bash를 사용하는 방법을 자세히 설명합니다.

> [!NOTE]
> [Azure Cloud Shell의 PowerShell](quickstart-powershell.md) 빠른 시작도 사용할 수 있습니다.

## <a name="start-cloud-shell"></a>Cloud Shell 시작
1. Azure Portal의 위쪽 탐색 모음에서 **Cloud Shell**을 시작합니다. <br>
![](media/quickstart/shell-icon.png)

2. 구독을 선택하여 저장소 계정 및 Microsoft Azure Files 공유를 만듭니다.
3. "저장소 만들기"를 선택합니다.

> [!TIP]
> 모든 세션에서 Azure CLI에 자동으로 인증됩니다.

### <a name="select-the-bash-environment"></a>Bash 환경 선택
셸 창 왼쪽에서 환경 드롭다운에 `Bash`가 표시되었는지 확인합니다. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>구독을 설정합니다.
1. 액세스할 수 있는 구독을 나열합니다.
   ```azurecli-interactive
   az account list
   ```

2. 기본 구독을 설정합니다. <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> `/home/<user>/.azure/azureProfile.json`을 사용하여 이후 세션에서 구독을 기억합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
미국 서부에 "MyRG"라는 새 리소스 그룹을 만듭니다.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Linux VM 만들기
새 리소스 그룹에서 Ubuntu VM을 만듭니다. Azure CLI는 SSH 키를 만들고 해당 키를 사용하여 VM을 설정합니다. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> `--generate-ssh-keys`를 사용하면 VM 및 `$Home` 디렉터리에서 공용 및 개인 키를 설정하도록 Azure CLI에 지시하게 됩니다. 기본적으로 키는 `/home/<user>/.ssh/id_rsa` 및 `/home/<user>/.ssh/id_rsa.pub`의 Cloud Shell에 배치됩니다. `.ssh` 폴더는 `$Home`을 유지하는 데 사용되는 연결된 파일 공유의 5GB 이미지에서 유지됩니다.

이 VM의 사용자 이름은 Cloud Shell에서 사용되는 사용자 이름입니다($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>Linux VM으로 SSH
1. Azure Portal 검색 표시줄에서 VM 이름을 검색합니다.
2. "연결"을 클릭하여 VM 이름 및 공용 IP 주소를 가져옵니다. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. `ssh` cmd를 사용하여 VM에 SSH합니다.
   ```
   ssh username@ipaddress
   ```

SSH 연결을 설정할 때 Ubuntu 시작 프롬프트가 표시되어야 합니다. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>정리 
1. SSH 세션을 종료합니다.
   ```azurecli-interactive
   exit
   ```

2. 리소스 그룹 및 해당 그룹 내의 모든 리소스를 삭제합니다.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>다음 단계
[Azure Cloud Shell의 Bash에 대한 파일 유지에 관해 알아보기](persisting-shell-storage.md) <br>
[Azure CLI에 대한 자세한 정보](https://docs.microsoft.com/cli/azure/) <br>
[Azure Files Storage에 대해 알아보기](../storage/files/storage-files-introduction.md) <br>
