---
title: Azure 파일 동기화 서버 엔드포인트 추가/제거 | Microsoft Docs
description: Azure Files 배포를 계획할 때 고려할 사항을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 684b30a24e049722cb531cbc84e3a2cd90912ec8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255107"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Azure 파일 동기화 서버 엔드포인트 추가/제거
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. 이 작업은 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하여 수행합니다. Windows Server에서 사용할 수 있는 아무 프로토콜이나 사용하여 데이터를 로컬로(SMB, NFS 및 FTPS 포함) 액세스할 수 있으며 세계 전역에 걸쳐 필요한 만큼 캐시를 보유할 수 있습니다.

*서버 엔드포인트*는 서버 볼륨 또는 볼륨의 루트에 있는 폴더와 같이, *등록된 서버*의 특정 위치를 나타냅니다. 해당 네임스페이스가 겹치지만 않으면 여러 서버 엔드포인트가 같은 볼륨에 있을 수 있습니다(예: F:\sync1 및 F:\sync2). 각 서버 엔드포인트에 대해 개별적으로 클라우드 계층화 정책을 구성할 수 있습니다. 기존 파일 집합이 있는 서버 위치를 동기화 그룹에 서버 엔드포인트로 추가하는 경우 해당 파일은 동기화 그룹의 다른 엔드포인트에 이미 있는 다른 파일에 병합됩니다.

엔드투엔드 Azure 파일 동기화를 배포하는 방법에 대한 자세한 내용은 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

## <a name="prerequisites"></a>전제 조건
서버 엔드포인트를 만들려면 먼저 다음 조건이 충족되는지 확인해야 합니다. 
- 서버에 Azure 파일 동기화 에이전트가 설치되고 등록되어 있습니다. Azure 파일 동기화 에이전트를 설치하기 위한 지침은 [Azure 파일 동기화에서 서버 등록/등록 취소](storage-sync-files-server-registration.md) 문서를 참조하세요. 
- 스토리지 동기화 서비스가 배포되었는지 확인합니다. 스토리지 동기화 서비스를 배포하는 방법에 대한 자세한 내용은 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요. 
- 동기화 그룹을 배포했는지 확인합니다. [동기화 그룹을 만드는](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint) 방법을 알아보세요.
- 서버가 인터넷에 연결되어 있고 Azure가 액세스 가능한지 확인합니다. 서버와 서비스 간의 모든 통신에 포트 443을 사용합니다.

## <a name="add-a-server-endpoint"></a>서버 엔드포인트 추가
서버 엔드포인트를 추가하려면 원하는 동기화 그룹으로 이동한 후 "서버 엔드포인트 추가"를 선택합니다.

![동기화 그룹 창에서 새 서버 엔드포인트 추가](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

**서버 엔드포인트 추가**에서 다음 정보가 필요합니다.

- **등록된 서버**: 서버 엔드포인트를 만들 서버 또는 클러스터의 이름입니다.
- **경로**: 동기화 그룹의 일부분으로 동기화할 Windows Server의 경로입니다.
- **클라우드 계층화**: 클라우드 계층화를 사용하거나 사용하지 않도록 설정할 스위치입니다. 클라우드 계층화를 사용하도록 설정하면 파일을 Azure 파일 공유로 *계층화*합니다. 그러면 온-프레미스 파일 공유를 데이터 세트의 전체 복사본이 아닌 캐시로 변환하여 서버에서 공간 효율성을 관리할 수 있습니다.
- **사용 가능한 볼륨 공간**: 서버 엔드포인트가 있는 볼륨에 예약하려는 사용 가능한 공간의 크기입니다. 예를 들어 단일 서버 엔드포인트가 있는 볼륨에서 사용 가능한 볼륨 공간을 50%로 설정하는 경우 데이터 양의 절반 정도가 Azure Files로 계층화됩니다. 클라우드 계층화를 사용하도록 설정할지 여부에 관계없이, Azure 파일 공유는 항상 동기화 그룹에 데이터의 전체 복사본을 유지합니다.

**만들기**를 선택하여 서버 엔드포인트를 추가합니다. 동기화 그룹의 네임스페이스 내에 있는 파일은 이제 동기화 상태를 유지합니다. 

## <a name="remove-a-server-endpoint"></a>서버 엔드포인트 제거
지정된 서버 엔드포인트에서 파일 동기화의 사용을 중지 하려는 경우 서버 엔드포인트를 제거할 수 있습니다. 

> [!Warning]  
> Microsoft 엔지니어가 명시적으로 지시하기 전에 서버 엔드포인트를 제거하고 다시 만들어서 동기화, 클라우드 계층화 또는 Azure 파일 동기화의 다른 부분에서 발생한 문제를 해결하려고 하지 않습니다. 서버 엔트포인트를 제거하는 것은 파괴적 작업입니다. 서버 엔드포인트 내에 계층화된 파일은 서버 엔드포인트를 다시 만든 후에 Azure 파일 공유의 해당 위치에 "다시 연결"되지 않습니다. 그러면 동기화 오류가 발생합니다. 또한 서버 엔드포인트 네임스페이스 외부에 있는 계층화된 파일은 영구적으로 손실될 수 있습니다. 클라우드 계층화를 한 번도 사용하지 않아도 계층화된 파일이 서버 엔드포인트에 있을 수 있습니다.

서버 엔드포인트를 제거하기 전에 계층화된 모든 파일이 회수되었는지 확인하려면 서버 엔드포인트에서 클라우드 계층화를 사용하지 않도록 설정한 다음, 다음과 같은 PowerShell cmdlet을 실행하여 서버 엔드포인트 네임스페이스 내에서 모든 계층화된 파일을 회수합니다.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
를 `-Order CloudTieringPolicy` 지정 하면 가장 최근에 수정한 파일이 먼저 회수 됩니다.
고려할 수 있는 다른 옵션 이지만 유용한 매개 변수는 다음과 같습니다.
* `-ThreadCount`동시에 회수할 수 있는 파일 수를 결정 합니다.
* `-PerFileRetryCount`현재 차단 된 파일에 대 한 회수를 시도 하는 빈도를 결정 합니다.
* `-PerFileRetryDelaySeconds`재시도 사이의 재시도 간격 (초)을 결정 합니다 .이 시간은 항상 이전 매개 변수와 함께 사용 해야 합니다.

> [!Note]  
> 서버를 호스트하는 로컬 볼륨에 모든 계층화된 데이터를 기억할 충분한 사용 가능한 공간이 없으면 `Invoke-StorageSyncFileRecall` cmdlet이 실패합니다.  

서버 엔드포인트를 제거하려면

1. 서버가 등록되어 있는 스토리지 동기화 서비스로 이동합니다.
2. 원하는 동기화 그룹으로 이동합니다.
3. 스토리지 동기화 서비스의 동기화 그룹에서 원하는 서버 엔드포인트를 제거합니다. 이렇게 하려면 동기화 그룹 창에서 관련 서버 엔드포인트를 마우스 오른쪽 단추로 클릭합니다.

    ![동기화 그룹에서 서버 엔드포인트 제거](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화에 서버 등록/등록 취소](storage-sync-files-server-registration.md)
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [Azure 파일 동기화 모니터링](storage-sync-files-monitoring.md)
