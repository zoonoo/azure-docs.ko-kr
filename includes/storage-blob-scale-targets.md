---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392377"
---
| 리소스 | 대상        |
|----------|---------------|
| 단일 Blob 컨테이너의 최대 크기 | 최대 저장소 계정 용량과 동일 |
| 블록 Blob 또는 부속 Blob의 최대 블록 수 | 50,000개 블록 |
| 블록 Blob의 최대 크기 | 100MiB |
| 블록 Blob의 최대 크기 | 50,000 X 100 MiB (약 4.75 TiB) |
| 추가 Blob의 최대 블록 크기 | 4MiB |
| 추가 Blob의 최대 크기 | 50,000 x 4 MiB (약 195 GiB) |
| 페이지 Blob의 최대 크기 | 8TiB |
| Blob 컨테이너당 최대 저장된 액세스 정책 수 | 5 |
|단일 Blob에 대한 대상 요청 속도 | 초당 최대 500개의 요청 |
|단일 페이지 Blob에 대한 대상 처리량 | 초당 최대 60MiB |
|단일 블록 Blob에 대한 대상 처리량 |최대 저장소 계정 수레/송신 제한<sup>1</sup> |

<sup>1</sup> 단일 Blob의 처리량은 동시성, 요청 크기, 성능 계층, 업로드 소스 속도 및 다운로드 대상을 포함하되 이에 국한되지 않는 여러 요인에 따라 달라집니다. [처리량이 높은 블록 Blob의](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)성능 향상을 활용하려면 더 큰 Blob 또는 블록을 업로드합니다. 특히 표준 저장소 계정의 경우 Blob 또는 블록 크기가 4MiB보다 큰 Blob 또는 블록 크기로 [Blob](/rest/api/storageservices/put-blob) 또는 [Put 블록](/rest/api/storageservices/put-block) 작업을 호출합니다. 프리미엄 블록 Blob 또는 Data Lake Storage Gen2 저장소 계정의 경우 256KiB보다 큰 블록 또는 Blob 크기를 사용합니다.
