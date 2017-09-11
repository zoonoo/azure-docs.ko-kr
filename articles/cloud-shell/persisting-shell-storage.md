---
title: "Azure Cloud Shell에서 파일 유지(미리 보기) | Microsoft Docs"
description: "Azure Cloud Shell에서 파일을 유지하는 방법의 연습입니다."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 61a8bfcf3704f361432400771d8fcc8b81927b53
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---

# <a name="persist-files-in-azure-cloud-shell"></a>Azure Cloud Shell에서 파일 유지
Cloud Shell은 Azure 파일 저장소를 활용하여 세션 간에 파일을 유지합니다.

## <a name="set-up-a-clouddrive-file-share"></a>`clouddrive` 파일 공유 설정
처음 시작 시 Cloud Shell은 세션 간에 파일을 유지하기 위해 새 또는 기존 파일 공유를 연결하도록 요구합니다.

### <a name="create-new-storage"></a>새 저장소 만들기

기본 설정을 사용하고 구독만 선택하면 Cloud Shell은 가장 가까운 지원되는 지역에서 사용자를 대신에 3개 리소스를 만듭니다.
* 리소스 그룹: `cloud-shell-storage-<region>`
* 저장소 계정: `cs<uniqueGuid>`
* 파일 공유: `cs-<user>-<domain>-com-<uniqueGuid>`

![구독 설정](media/basic-storage.png)

파일 공유는 `$Home` 디렉터리에서 `clouddrive`로 마운트됩니다. 또한 이 파일 공유를 사용하여 만들어진 5GB 이미지를 저장하고 `$Home` 디렉터리를 자동으로 업데이트하고 유지합니다. 이것은 일회성 작업이며 파일 공유는 후속 세션에서 자동으로 마운트됩니다.

### <a name="use-existing-resources"></a>기존 리소스 사용

고급 옵션을 사용하여 기존 리소스를 연결할 수 있습니다. 저장소 설정 프롬프트가 나타나면 **고급 옵션 표시**를 선택하여 추가 옵션을 봅니다.  기존 파일 공유는 `$Home` 디렉터리를 유지하기 위한 5GB 사용자 이미지를 받습니다. Cloud Shell 지역, 로컬 중복 및 지역 중복 저장소 계정에 대해 드롭다운 메뉴가 필터링됩니다.

![리소스 그룹 설정](media/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure 리소스 정책으로 리소스 만들기 제한
Cloud Shell에서 생성된 저장소 계정에 `ms-resource-usage:azure-cloud-shell` 태그가 지정됩니다. 사용자가 Cloud Shell에서 저장소 계정을 만드는 것을 허용하지 않으려면 이 특정 태그로 트리거되는 [태그에 대한 Azure 리소스 정책](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)을 만듭니다.

## <a name="how-cloud-shell-works"></a>Cloud Shell 작동 방식
Cloud Shell은 다음 방법 모두를 통해 파일을 유지합니다.
* `$Home` 디렉터리 내에 모든 콘텐츠를 유지하기 위해 해당 디렉터리의 디스크 이미지를 만듭니다. 디스크 이미지는 `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`에서 `acc_<User>.img`로 지정된 파일 공유에 저장되고 변경 내용을 자동으로 동기화합니다.

* 직접 파일 공유의 상호 작용을 위해 `$Home` 디렉터리에서 지정된 파일 공유를 `clouddrive`로 마운트합니다. `/Home/<User>/clouddrive`은 `fileshare.storage.windows.net/fileshare`에 매핑됩니다.
 
> [!NOTE]
> SSH 키와 같이 `$Home` 디렉터리의 모든 파일은 마운트된 파일 공유에 저장된 사용자 디스크 이미지에서 유지됩니다. `$Home` 디렉터리 및 마운트된 파일 공유에서 정보를 유지하는 경우 모범 사례를 적용합니다.

## <a name="use-the-clouddrive-command"></a>`clouddrive` 명령 사용
Cloud Shell에서 `clouddrive` 명령을 실행하여 Cloud Shell에 마운트된 파일 공유를 수동으로 업데이트할 수 있습니다.
![clouddrive 명령 실행](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>새 `clouddrive` 마운트

### <a name="prerequisites-for-manual-mounting"></a>수동 마운트를 위한 필수 구성 요소
`clouddrive mount` 명령을 사용하여 Cloud Shell과 연결된 파일 공유를 업데이트할 수 있습니다.

기존 파일 공유를 마운트할 경우 저장소 계정은 다음과 같아야 합니다.
* 파일 공유를 지원하는 로컬 중복 저장소 또는 지역 중복 저장소
* 할당된 지역에 위치합니다. 시작할 때 사용자가 할당된 지역이 리소스 그룹 이름 `cloud-shell-storage-<region>`에 나열됩니다.

### <a name="supported-storage-regions"></a>지원되는 저장소 지역
Azure 파일은 사용자가 마운트되는 대상 Cloud Shell 컴퓨터와 동일한 지역에 있어야 합니다. Cloud Shell 클러스터는 현재 다음 지역에 위치합니다.
|영역|지역|
|---|---|
|아메리카|미국 동부, 미국 중남부, 미국 서부|
|유럽|북유럽, 서유럽|
|아시아 태평양|인도 중부, 동남 아시아|

### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` 명령

> [!NOTE]
> 새 파일 공유를 마운트하는 경우 이전 `$Home` 이미지가 이전 파일 공유에 보관되는 것처럼 `$Home` 디렉터리에 대해 새 사용자 이미지가 만들어집니다.

다음 매개 변수를 사용하여 `clouddrive mount` 명령을 실행합니다.

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

자세한 세부 정보를 보려면 아래와 같이 `clouddrive mount -h`를 실행합니다.

![‘clouddrive mount’ 명령 실행](media/mount-h.png)

## <a name="unmount-clouddrive"></a>`clouddrive` 마운트 해제
언제든지 Cloud Shell에 마운트된 파일 공유의 마운트를 해제할 수 있습니다. 파일 공유가 분리되면 다음 세션을 진행하기 전에 새 파일 공유를 탑재하라는 메시지가 나타납니다.

Cloud Shell에서 파일 공유를 제거하려면
1. `clouddrive unmount`을 실행합니다.
2. 프롬프트를 승인 및 확인합니다.

파일 공유는 수동으로 삭제하지 않은 한 계속 유지됩니다. Cloud Shell은 후속 세션에서 이 파일 공유를 더 이상 검색하지 않습니다.

자세한 세부 정보를 보려면 아래와 같이 `clouddrive unmount -h`를 실행합니다.

![‘clouddrive unmount’ 명령 실행](media/unmount-h.png)

> [!WARNING]
> 이 명령을 실행하면 리소스가 삭제되지 않습니다. Cloud Shell에 매핑된 리소스 그룹, 저장소 계정 또는 파일 공유를 수동으로 삭제하면 `$Home` 디렉터리 디스크 이미지 및 파일 공유의 다른 모든 파일이 영구적으로 지워집니다. 이 작업은 취소할 수 없습니다.

## <a name="list-clouddrive-file-shares"></a>`clouddrive` 파일 공유 나열
`clouddrive`로 마운트된 파일 공유를 확인하려면 다음 `df` 명령을 실행합니다. 

clouddrive에 대한 파일 경로는 URL에서 저장소 계정 이름 및 파일 공유를 표시합니다. 예를 들어 `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                               1K-blocks     Used Available Use% Mounted on
overlay                                                   30428648 15585636  14826628  52% /
tmpfs                                                       986704        0    986704   0% /dev
tmpfs                                                       986704        0    986704   0% /sys/fs/cgroup
/dev/sda1                                                 30428648 15585636  14826628  52% /etc/hosts
shm                                                          65536        0     65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName        6291456  5242944   1048512  84% /usr/justin/clouddrive
/dev/loop0                                                 5160576   601652   4296780  13% /home/justin
```

## <a name="transfer-local-files-to-cloud-shell"></a>Cloud Shell에 로컬 파일 전송
`clouddrive` 디렉터리가 Azure Portal 저장소 블레이드에 동기화됩니다. 이 블레이드를 사용하여 파일 공유 간에 로컬 파일을 전송합니다. Cloud Shell 내의 파일을 업데이트하면 블레이드를 새로 고칠 때 파일 저장소 GUI에 반영됩니다.

### <a name="download-files"></a>파일 다운로드

![로컬 파일 목록](media/download.png)
1. Azure Portal에서 마운트된 파일 공유로 이동합니다.
2. 대상 파일을 선택합니다.
3. **다운로드** 단추를 선택합니다.

### <a name="upload-files"></a>파일 업로드

![업로드할 로컬 파일](media/upload.png)
1. 마운트된 파일 공유로 이동합니다.
2. **업데이트** 단추를 선택합니다.
3. 업로드할 파일을 하나 이상 선택합니다.
4. 업로드를 확인합니다.

이제 Cloud Shell의 `clouddrive` 디렉터리에서 액세스할 수 있는 파일이 표시됩니다.

## <a name="next-steps"></a>다음 단계
[Cloud Shell 빠른 시작](quickstart.md) <br>
[Azure File Storage에 대해 알아보기](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[저장소 태그에 대해 알아보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

