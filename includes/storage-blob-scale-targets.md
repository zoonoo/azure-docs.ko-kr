---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/23/2020
ms.author: tamram
ms.openlocfilehash: 43a72d915fa5a00c54bef7a06fe3815a7d63f2fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805456"
---
| 리소스 | Target | 대상 (미리 보기) |
|-|-|-|
| 단일 blob 컨테이너의 최대 크기 | 최대 저장소 계정 용량과 동일 |  |
| 블록 blob 또는 추가 blob의 최대 블록 수 | 50,000개 블록 |  |
| 블록 blob의 최대 블록 크기 | 100MiB | 4000 MiB (미리 보기) |
| 블록 blob의 최대 크기 | 5만 X 100 MiB (약 4.75 TiB) | 5만 X 4000 MiB (약 190.7 TiB) (미리 보기) |
| 추가 blob의 최대 블록 크기 | 4MiB |  |
| 추가 blob의 최대 크기 | 5만 x 4 MiB (약 195 GiB) |  |
| 페이지 blob의 최대 크기 | 8TiB |  |
| Blob 컨테이너 당 저장 된 액세스 정책의 최대 수 | 5 |  |
| 단일 blob에 대 한 대상 요청 률 | 초당 최대 500 요청 수 |  |
| 단일 페이지 blob에 대 한 대상 처리량 | 초당 최대 60MiB |  |
| 단일 블록 blob에 대 한 대상 처리량 | 최대 저장소 계정 수신/송신 제한<sup>1</sup> |  |

<sup>1</sup> 단일 blob에 대 한 처리량은 동시성, 요청 크기, 성능 계층, 업로드할 원본 속도, 다운로드 대상 등 여러 가지 요인에 따라 달라 집니다. [처리량이 높은 블록 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)의 성능 향상을 활용 하려면 더 큰 blob 또는 블록을 업로드 합니다. 구체적으로, standard storage 계정에 대해 4 MiB 보다 큰 blob 또는 블록 크기를 사용 하 여 [Put blob](/rest/api/storageservices/put-blob) 또는 [put block](/rest/api/storageservices/put-block) 작업을 호출 합니다. 프리미엄 블록 blob 또는 Data Lake Storage Gen2 저장소 계정의 경우 256 KiB 보다 큰 블록 또는 blob 크기를 사용 합니다.

다음 표에서는 서비스 버전에서 허용 하는 최대 블록 및 blob 크기를 설명 합니다.

| 서비스 버전 | 최대 블록 크기 (Put 블록을 통해) | 최대 blob 크기 (Put 블록 목록을 통해) | 단일 쓰기 작업을 통한 최대 blob 크기 (Put Blob 통해) |
|-|-|-|-|
| 버전 2019-12-12 이상 | 4000 MiB (미리 보기) | 약 190.7 TiB (4000 MiB X 5만 블록) (미리 보기) | 5000 MiB (미리 보기) |
| 버전 2016-05-31 ~ 2019-07-07 | 100MiB | 약 4.75 TiB (100 MiB X 5만 블록) | 256 MiB |
| 2016-05-31 이전 버전 | 4MiB | 약 195 GiB (MiB X 5만 블록 4 개) | 64 MiB |
