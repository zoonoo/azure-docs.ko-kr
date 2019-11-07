---
title: 저장소 계정 컨테이너 복구
description: 저장소 계정 컨테이너 복구
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
ms.openlocfilehash: f095bdfe7bbb5777a2ad2aabb3bda92d0974457d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693580"
---
# <a name="storage-account-container-recovery"></a>저장소 계정 컨테이너 복구

Azure Storage는 자동화된 복제본을 통해 데이터 복구 기능을 제공합니다. 그러나 Azure Storage는 우발적이든 악의적이든 애플리케이션 코드나 사용자가 데이터를 손상하는 것을 방지하지 못합니다. 애플리케이션이나 사용자 오류가 발생하는 경우 데이터의 정확성을 유지 관리하려면 감사 로그가 포함된 데이터를 보조 스토리지 위치에 복사하는 등 고급 기술이 필요합니다.

## <a name="checking-azure-storage-account-type"></a>Azure Storage 계정 유형을 확인 하는 중

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. 스토리지 계정을 찾습니다.

3. **개요** 섹션에서 **복제**를 확인 합니다.

   ![이미지](media/storage-account-container-recovery/1.png)

4. 복제 유형이 **GRS/RA-GRS**인 경우에는 보증 없이 계정 컨테이너 복구를 수행할 수 있습니다. 다른 모든 복제 유형의 경우에는 불가능 합니다.

5. 다음 정보를 수집 하 고 Microsoft 지원를 사용 하 여 지원 요청을 파일 합니다.

   * 저장소 계정 이름:
   * 컨테이너 이름:
   * 삭제 시간:

   다음 표에서는 복제 전략에 따라 저장소 계정 컨테이너 복구 범위에 대 한 개요를 제공 합니다.

   |콘텐츠 형식|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |스토리지 컨테이너|아니요|아니요|예|예| 

   * 저장소 계정 컨테이너를 복원 하는 것은 보장 하지 않고 시도할 수 있습니다. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>복구가 성공 하기 위해 수행 하지 않는 작업

* 컨테이너를 같은 이름으로 다시 만들지 마세요.  
* 컨테이너를 이미 다시 만든 경우 복구 요청을 제출 하기 전에 컨테이너를 삭제 해야 합니다.

## <a name="steps-to-prevent-this-in-the-future"></a>나중에이를 방지 하는 단계

1. 나중에이 문제를 방지 하기 위해 사용 하는 가장 좋은 기능은 [일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)입니다.

2. 또한 [스냅숏](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) 기능을 권장 합니다.
 
## <a name="next-steps"></a>다음 단계

다음은이 기능에 대 한 두 가지 샘플 코드입니다.

  * [.NET에서 blob 스냅숏 만들기 및 관리](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [PowerShell에서 blob 스냅숏 사용](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

