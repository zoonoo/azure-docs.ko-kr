---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 112c30fdd242c20f11c43f42ba54e3717e074bbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706041"
---
다음은 Data Box 장치에 대해 지원 되는 저장소 계정 및 저장소 유형 목록입니다. 모든 유형의 저장소 계정에 대 한 모든 기능의 전체 목록은 [저장소 계정 유형](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)을 참조 하세요.

가져오기 주문의 경우 다음 표에서는 지원 되는 저장소 계정을 보여 줍니다.

| **스토리지 계정/지원되는 스토리지 유형** | **블록 Blob** |**페이지 blob** _ |_ *Azure 파일** |**참고 사항**|
| --- | --- | -- | -- | -- |
| Classic Standard | Y | Y | Y |
| 범용 v1 Standard  | Y | Y | Y | 핫 및 쿨이 모두 지원됩니다.|
| 범용 v1 Premium  |  | Y| | |
| 범용 v2 Standard  | Y | Y | Y | 핫 및 쿨이 모두 지원됩니다.|
| 범용 v2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |  
| Blob Storage Standard |Y | | |핫 및 쿨이 모두 지원됩니다. |

\*  *- 페이지 Blob에 업로드된 데이터는 VHD와 같이 512바이트로 정렬되어야 합니다.*

내보내기 주문의 경우 다음 표에서는 지원 되는 저장소 계정을 보여 줍니다.

| **스토리지 계정/지원되는 스토리지 유형** | **블록 Blob** |**페이지 blob** _ |_ *Azure 파일** |**지원되는 액세스 계층**|
| --- | --- | -- | -- | -- |
| Classic Standard | Y | Y | Y | |
| 범용 v1 Standard  | Y | Y | Y | 핫, 쿨|
| 범용 v1 Premium  |  | Y| | |
| 범용 v2 Standard  | Y | Y | Y | 핫, 쿨|
| 범용 v2 Premium  |  |Y | | |
| Azure Premium FileStorage |  |  | Y |  |
| Blob Storage Standard |Y | | |핫, 쿨 |
| 블록 Blob storage Premium |Y | | |핫, 쿨 |
| 페이지 Blob storage Premium | |Y | | |

> [!IMPORTANT]
> - 범용 계정의 경우에는 Data Box에서 가져오기 순서에 대 한 큐, 테이블 및 디스크 저장소 형식을 지원 하지 않습니다. 내보내기 주문의 경우 Data Box는 범용 계정에 대 한 Queue, Table, Disk 및 Azure Data Lake Gen 2 저장소 형식을 지원 하지 않습니다.
> - Data Box는 Blob Storage 및 블록 Blob Storage 계정에 대 한 추가 blob을 지원 하지 않습니다.
> - NFS (네트워크 파일 시스템) 3.0 Azure Blob 저장소의 프로토콜 지원은 Data Box 지원 되지 않습니다.
> - 페이지 blob에 업로드 된 데이터는 Vhd와 같이 512 바이트 정렬 되어야 합니다.
> - 최대 80 TB를 내보낼 수 있습니다.
> - 파일 기록과 blob 스냅숏은 내보내지 않습니다.