---
title: Azure Cloud Shell의 Bash에 대한 파일 유지 | Microsoft Docs
description: Azure Cloud Shell의 Bash가 파일을 유지하는 방법에 대한 연습입니다.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: juluk
ms.openlocfilehash: 9a22b14df18e10342bb2a872b82b94ab4ea62d0a
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859870"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Cloud Shell 저장소 작동 방법 
Cloud Shell은 다음 방법 모두를 통해 파일을 유지합니다. 
* `$Home` 디렉터리 내에 모든 콘텐츠를 유지하기 위해 해당 디렉터리의 디스크 이미지를 만듭니다. 디스크 이미지는 `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`에서 `acc_<User>.img`로 지정된 파일 공유에 저장되고 변경 내용을 자동으로 동기화합니다. 
* 직접 파일 공유의 상호 작용을 위해 `$Home` 디렉터리에서 지정된 파일 공유를 `clouddrive`로 마운트합니다. `/Home/<User>/clouddrive`은 `fileshare.storage.windows.net/fileshare`에 매핑됩니다.
 
> [!NOTE]
> SSH 키와 같이 `$Home` 디렉터리의 모든 파일은 마운트된 파일 공유에 저장된 사용자 디스크 이미지에서 유지됩니다. `$Home` 디렉터리 및 마운트된 파일 공유에서 정보를 유지하는 경우 모범 사례를 적용합니다.

## <a name="bash-specific-commands"></a>Bash 관련 명령

### <a name="use-the-clouddrive-command"></a>`clouddrive` 명령 사용
Azure Cloud Shell의 Bash에서 `clouddrive` 명령을 실행하여 Azure Cloud Shell에 마운트된 파일 공유를 수동으로 업데이트할 수 있습니다.
![clouddrive 명령 실행](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>새 clouddrive 탑재

#### <a name="prerequisites-for-manual-mounting"></a>수동 마운트를 위한 필수 구성 요소
`clouddrive mount` 명령을 사용하여 Cloud Shell과 연결된 파일 공유를 업데이트할 수 있습니다.

기존 파일 공유를 마운트할 경우 저장소 계정은 다음과 같아야 합니다.
* 파일 공유를 지원하는 로컬 중복 저장소 또는 지역 중복 구성 저장소
* 할당된 지역에 위치합니다. 시작할 때 사용자가 할당된 지역이 리소스 그룹 이름 `cloud-shell-storage-<region>`에 나열됩니다.

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` 명령

> [!NOTE]
> 새 파일 공유를 탑재하는 경우 새 사용자 이미지가 `$Home` 디렉터리에 만들어집니다. 이전 `$Home` 이미지는 이전 파일 공유에 보관됩니다.

다음 매개 변수를 사용하여 `clouddrive mount` 명령을 실행합니다.

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

자세한 세부 정보를 보려면 아래와 같이 `clouddrive mount -h`를 실행합니다.

![‘clouddrive mount’ 명령 실행](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>clouddrive 탑재 해제
언제든지 Cloud Shell에 마운트된 파일 공유의 마운트를 해제할 수 있습니다. Azure Cloud Shell에는 사용될 탑재된 파일 공유가 필요하므로 다음 세션에서는 또 다른 파일 공유를 만들고 탑재하라는 메시지가 표시됩니다.

1. `clouddrive unmount`을 실행합니다.
2. 프롬프트 승인 및 확인

파일 공유는 수동으로 삭제하지 않은 한 계속 유지됩니다. Cloud Shell은 후속 세션에서 이 파일 공유를 더 이상 검색하지 않습니다. 자세한 세부 정보를 보려면 아래와 같이 `clouddrive unmount -h`를 실행합니다.

![‘clouddrive unmount’ 명령 실행](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> 이 명령을 실행해도 리소스가 삭제되는 것은 아니지만 Cloud Shell에 매핑된 리소스 그룹, 저장소 계정 또는 파일 공유를 수동으로 삭제하면 `$Home` 디렉터리 디스크 이미지 및 파일 공유의 모든 파일이 지워집니다. 이 작업은 취소할 수 없습니다.

### <a name="list-clouddrive"></a>목록 `clouddrive`
`clouddrive`로 마운트된 파일 공유를 확인하려면 `df` 명령을 실행합니다. 

clouddrive에 대한 파일 경로는 URL에서 저장소 계정 이름 및 파일 공유를 표시합니다. 예를 들어 `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```
## <a name="powershell-specific-commands"></a>PowerShell 관련 명령

### <a name="list-clouddrive-azure-file-shares"></a>`clouddrive` Azure 파일 공유 나열
`Get-CloudDrive` cmdlet은 Azure Cloud Shell에서 `clouddrive`에 현재 탑재된 Azure 파일 공유 정보를 검색합니다. <br>
![Get-CloudDrive 실행](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>`clouddrive` 마운트 해제
언제든지 Cloud Shell에 마운트된 Azure 파일 공유의 마운트를 해제할 수 있습니다. Azure 파일 공유가 제거된 경우 다음 세션에서 새 Azure 파일 공유를 만들고 탑재하라는 메시지가 표시됩니다.

`Dismount-CloudDrive` cmdlet은 현재 저장소 계정에서 Azure 파일 공유를 탑재 해제합니다. `clouddrive`를 탑재 해제하면 현재 세션이 종료됩니다. 사용자에게 다음 세션에서 새 Azure 파일 공유를 만들고 탑재하라는 메시지가 표시됩니다.
![CloudDrive 탑재 해제 실행](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>다음 단계
[Azure Cloud Shell의 Bash 빠른 시작](quickstart.md) <br>
[Cloud Shell의 PowerShell 빠른 시작](quickstart-powershell.md) <br>
[Microsoft Azure Files 저장소에 대해 알아보기](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[저장소 태그에 대해 알아보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
