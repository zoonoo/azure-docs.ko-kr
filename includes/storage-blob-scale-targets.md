---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392377"
---
| 리소스 | 대상 파악        |
|----------|---------------|
| 단일 blob 컨테이너의 최대 크기 | 최대 저장소 계정 용량과 동일 |
| 블록 blob 또는 추가 blob의 최대 블록 수 | 50,000개 블록 |
| 블록 blob의 최대 블록 크기 | 100MiB |
| 블록 blob의 최대 크기 | 5만 X 100 MiB (약 4.75 TiB) |
| 추가 blob의 최대 블록 크기 | 4MiB |
| 추가 blob의 최대 크기 | 5만 x 4 MiB (약 195 GiB) |
| 페이지 blob의 최대 크기 | 8TiB |
| Blob 컨테이너 당 저장 된 액세스 정책의 최대 수 | 5 |
|단일 blob에 대 한 대상 요청 률 | 초당 최대 500 요청 수 |
|단일 페이지 blob에 대 한 대상 처리량 | 초당 최대 60MiB |
|단일 블록 blob에 대 한 대상 처리량 |최대 저장소 계정 수신/송신 제한<sup>1</sup> |

<sup>1</sup> 단일 blob에 대 한 처리량은 동시성, 요청 크기, 성능 계층, 업로드할 원본 속도, 다운로드 대상 등 여러 가지 요인에 따라 달라 집니다. [처리량이 높은 블록 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)의 성능 향상을 활용 하려면 더 큰 blob 또는 블록을 업로드 합니다. 구체적으로, standard storage 계정에 대해 4 MiB 보다 큰 blob 또는 블록 크기를 사용 하 여 [Put blob](/rest/api/storageservices/put-blob) 또는 [put block](/rest/api/storageservices/put-block) 작업을 호출 합니다. 프리미엄 블록 blob 또는 Data Lake Storage Gen2 저장소 계정의 경우 256 KiB 보다 큰 블록 또는 blob 크기를 사용 합니다.
