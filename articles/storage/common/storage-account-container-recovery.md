---
title: 저장소 계정 컨테이너 복구
description: 저장소 계정 컨테이너 복구
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562278"
---
# <a name="storage-account-container-recovery"></a>스토리지 계정 컨테이너 복구

Azure Storage는 자동화된 복제본을 통해 데이터 복구 기능을 제공합니다. 그러나 Azure Storage는 우발적이든 악의적이든 애플리케이션 코드나 사용자가 데이터를 손상하는 것을 방지하지 못합니다. 애플리케이션이나 사용자 오류가 발생하는 경우 데이터의 정확성을 유지 관리하려면 감사 로그가 포함된 데이터를 보조 스토리지 위치에 복사하는 등 고급 기술이 필요합니다.

## <a name="checking-azure-storage-account-type"></a>Azure 저장소 계정 유형 확인

1. [Azure 포털로](https://portal.azure.com/)이동합니다.

2. 스토리지 계정을 찾습니다.

3. **개요** 섹션에서 **복제를**확인합니다.

   ![이미지](media/storage-account-container-recovery/1.png)

4. 복제 유형이 **GRS/RA-GRS인**경우 계정 컨테이너 복구가 보장 없이 가능합니다. 다른 모든 복제 형식의 경우 불가능합니다.

5. 다음 정보를 수집하고 Microsoft 지원에 지원 요청을 제출합니다.

   * 저장소 계정 이름:
   * 컨테이너 이름:
   * 삭제 시간:

   다음 표에서는 복제 전략에 따라 저장소 계정 컨테이너 복구 범위에 대한 개요를 제공합니다.

   |콘텐츠 유형|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |스토리지 컨테이너|예|예|yes|yes| 

   * 저장소 계정 컨테이너를 복원하려고 시도할 수 있지만 아무 보증도 하지 않습니다. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>성공하기 위해 복구하기 위해 하지 말아야 할 일

* 컨테이너를 다시 만들지 마십시오(이름이 같은).  
* 컨테이너를 이미 다시 만든 경우 복구에 대한 지원 요청을 제출하기 전에 컨테이너를 삭제해야 합니다.

## <a name="steps-to-prevent-this-in-the-future"></a>미래에 이를 방지하기 위한 단계

1. 나중에 이 것을 방지하려면 가장 권장되는 기능은 [소프트 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)입니다.

2. [스냅샷](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) 기능도 권장합니다.
 
## <a name="next-steps"></a>다음 단계

다음은 기능에 대한 두 가지 샘플 코드입니다.

  * [.NET에서 Blob 스냅샷 생성 및 관리](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [PowerShell을 사용하여 Blob 스냅숏 사용](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

