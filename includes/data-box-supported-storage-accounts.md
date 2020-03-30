---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "66242205"
---
아래에는 Data Box 디바이스용으로 지원되는 스토리지 계정 및 스토리지 유형의 목록이 나와 있습니다. 모든 유형의 스토리지 계정과 해당 계정의 모든 기능이 포함된 전체 목록은 [스토리지 계정의 유형](/azure/storage/common/storage-account-overview#types-of-storage-accounts)을 참조하세요.

| **스토리지 계정/지원되는 스토리지 유형** | **블록 Blob** |**페이지 Blob*** |**Azure 파일** |**참고**|
| --- | --- | -- | -- | -- |
| Classic Standard | Y | Y | Y |
| 범용 v1 Standard  | Y | Y | Y | 핫 및 쿨이 모두 지원됩니다.|
| 범용 v1 Premium  |  | Y| | |
| 범용 v2 Standard  | Y | Y | Y | 핫 및 쿨이 모두 지원됩니다.|
| 범용 v2 Premium  |  |Y | | |
| Blob Storage Standard |Y | | |핫 및 쿨이 모두 지원됩니다. |

\**- 페이지 Blob에 업로드된 데이터는 VHD와 같이 정렬된 512바이트여야 합니다.*

>[!NOTE]
> Azure Data Lake Storage Gen 2 계정은 지원되지 않습니다.
