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
ms.date: 07/17/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4603bcbbb752bba341b3e372266b98a1f0863195
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>Azure Cloud Shell에서 파일 유지
Cloud Shell은 Azure 파일 저장소를 활용하여 세션 간에 파일을 유지합니다.

## <a name="setup-clouddrive"></a>clouddrive 설정
처음 시작 시 Cloud Shell은 세션 간에 파일을 유지하기 위해 새 또는 기존 파일 공유를 연결하도록 요구합니다.

### <a name="creating-new-storage"></a>새 저장소 만들기
![](media/basic-storage.png)

기본 설정을 사용하고 구독을 선택할 때만 지원되는 가장 가까운 하위 지역에서 리소스가 자동으로 생성됩니다.
1. 리소스 그룹: `cloud-shell-storage-<region>`
2. 저장소 계정: `cs-uniqueGuid`
3. 파일 공유: `cs-<user>-<domain>-com-uniqueGuid`

이 파일 공유는 $Home 디렉터리에서 `clouddrive`로 탑재됩니다. 또한 $Home 디렉터리를 자동으로 업데이트하고 유지하는 5GB 이미지를 저장하는 데 이 파일 공유를 사용합니다. 일회성 작업이며 후속 세션에서 자동으로 탑재됩니다.

### <a name="use-existing-resources"></a>기존 리소스 사용
![](media/advanced-storage.png) 기존 리소스를 연결할 수 있도록 하는 고급 옵션도 제공됩니다. 저장소 설정 프롬프트가 나타나면 "고급 설정 표시"를 선택하여 추가 옵션을 표시합니다. 기존 파일 공유는 $Home 디렉터리를 지속할 5GB 사용자 이미지를 수신합니다. 드롭다운이 필터링되면서 할당된 Cloud Shell 하위 지역 및 로컬/전역 중복 저장소 계정이 표시됩니다.

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure 리소스 정책으로 리소스 만들기 제한
Cloud Shell에서 생성된 저장소 계정에 `ms-resource-usage:azure-cloud-shell` 태그가 지정됩니다. 사용자가 Cloud Shell을 통해 저장소 계정을 만드는 것을 허용하지 않으려면 이 특정 태그로 트리거되는 [태그에 대한 Azure 리소스 정책](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)을 만듭니다.

## <a name="how-it-works"></a>작동 방법
### <a name="cloud-shell-persists-files-with-both-methods-below"></a>Cloud Shell은 아래 방법을 모두 사용하여 파일을 유지합니다.
1. $Home 내에 모든 콘텐츠를 유지하기 위해 $Home 디렉터리의 디스크 이미지를 만듭니다. 이 디스크 이미지는 `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`에서 `acc_<User>.img`로 지정된 파일 공유에 저장되고 변경 내용을 자동으로 동기화합니다.

2. 직접 파일 공유의 상호 작용을 위해 $Home 디렉터리에서 지정된 파일 공유를 `clouddrive`로 탑재합니다. 
`/Home/<User>/clouddrive`은 `fileshare.storage.windows.net/fileshare`에 매핑됩니다.
 
> [!Note]
> SSH 키와 같이 $Home 디렉터리의 모든 파일은 탑재된 파일 공유에 저장된 사용자 디스크 이미지에서 유지됩니다. $Home 디렉터리 및 탑재된 파일 공유에서 정보를 유지하는 경우 모범 사례를 적용합니다.

## <a name="using-the-clouddrive-command"></a>clouddrive 명령 사용
Cloud Shell을 사용하면 사용자가 Cloud Shell에 탑재된 파일 공유를 수동으로 업데이트하는 `clouddrive`라는 명령을 실행할 수 있습니다.
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>새 clouddrive 탑재

### <a name="pre-requisites-for-manual-mounting"></a>수동 탑재의 필수 구성 요소
`clouddrive mount` 명령을 사용하여 Cloud Shell에 연결된 파일 공유를 업데이트할 수 있습니다.

기존 파일 공유를 탑재하는 경우 저장소 계정은 다음과 같아야 합니다.
1. 파일 공유를 지원하는 LRS 또는 GRS입니다.
2. 할당된 지역에 위치합니다. 온보딩할 때 할당된 지역이 리소스 그룹 이름 `cloud-shell-storage-<region>`에서 나열됩니다.

### <a name="supported-storage-regions"></a>지원되는 저장소 지역
Azure Files은 탑재되는 Cloud Shell 컴퓨터와 동일한 하위 지역에 있어야 합니다. Cloud Shell 컴퓨터는 아래 하위 지역에 위치합니다.
|영역|지역|
|---|---|
|아메리카|미국 동부, 미국 중남부, 미국 서부|
|유럽|북유럽, 서유럽|
|아시아 태평양|인도 중부, 동남 아시아|

### <a name="mount-command"></a>탑재 명령

> [!NOTE]
> 새 파일 공유를 탑재하는 경우 이전 $Home 이미지가 이전 파일 공유에 보관되는 것처럼 $Home 디렉터리에 새 사용자 이미지가 만들어집니다.

다음 매개 변수를 사용하여 `clouddrive mount`을 실행합니다. <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

자세한 내용을 확인하려면 `clouddrive mount -h`을 실행합니다. <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>clouddrive 탑재 해제
언제든지 Cloud Shell에 탑재되는 파일 공유의 탑재를 해제할 수 있습니다. 그러나 탑재된 파일 공유를 제거하는 경우 다음 세션에서 새 파일 공유를 만들고 탑재하라는 메시지가 표시되므로 Cloud Shell에는 탑재된 파일 공유가 필요합니다.

Cloud Shell에서 파일 공유를 제거하려면
1. `clouddrive unmount`을 실행합니다.
2. 프롬프트 승인 및 확인

파일 공유를 수동으로 삭제하지 않은 경우 계속 유지됩니다. Cloud Shell은 후속 세션에서 이 파일 공유를 더 이상 검색하지 않습니다.

자세한 내용을 확인하려면 `clouddrive mount -h`을 실행합니다. <br>
![](media/unmount-h.png)

> [!WARNING]
> 이 명령은 리소스를 삭제하지 않습니다. 그러나 Cloud Shell에 매핑된 리소스 그룹, 저장소 계정 또는 파일 공유를 수동으로 삭제하면 $Home 디렉터리 디스크 이미지 및 파일 공유의 모든 파일이 지워집니다. 이 작업은 실행을 취소할 수 없습니다.

## <a name="list-clouddrive"></a>clouddrive 나열
`clouddrive`로 탑재된 파일 공유를 검색하려면:

`df`을 실행합니다. 

clouddrive에 대한 파일 경로는 URL에서 저장소 계정 이름 및 파일 공유를 표시합니다.

`//storageaccountname.file.core.windows.net/filesharename`

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

## <a name="transfer-local-files-to-cloud-shell"></a>Cloud Shell에 로컬 파일 전송
`clouddrive` 디렉터리가 Azure Portal 저장소 블레이드에 동기화됩니다. 이를 사용하여 파일 공유 간에 로컬 파일을 전송합니다. Cloud Shell 내의 파일을 업데이트하면 블레이드를 새로 고칠 때 File Storage GUI에서 반영됩니다.

### <a name="download-files"></a>파일 다운로드
![](media/download.png)
1. 탑재된 파일 공유로 이동합니다.
2. 포털에서 대상 파일을 선택합니다.
3. "다운로드"를 클릭합니다.

### <a name="upload-files"></a>파일 업로드
![](media/upload.png)
1. 탑재된 파일 공유로 이동합니다.
2. "업로드"를 선택합니다.
3. 업로드하려는 파일을 선택합니다.
4. 업로드를 확인합니다.

이제 Cloud Shell의 clouddrive 디렉터리에서 액세스할 수 있는 파일이 표시됩니다.

## <a name="next-steps"></a>다음 단계
[Cloud Shell 빠른 시작](quickstart.md) <br>
[Azure File Storage에 대해 알아보기](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Storage 태그에 대해 알아보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
