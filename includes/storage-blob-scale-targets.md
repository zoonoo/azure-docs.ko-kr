---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.openlocfilehash: fe493ca4a2edf16bfda405e8b2c5581c0f745616
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665616"
---
| 리소스 | 대상 | 대상(미리 보기) |
|-|-|-|
| 단일 Blob 컨테이너의 최대 크기 | 최대 스토리지 계정 용량과 동일 |  |
| 블록 Blob 또는 추가 Blob의 최대 블록 수 | 50,000개 블록 |  |
| 블록 Blob의 최대 블록 크기 | 100MiB | 4,000MiB(미리 보기) |
| 블록 Blob의 최대 크기 | 50,000 X 100MiB(약 4.75TiB) | 50,000 X 4,000MiB(약 190.7TiB)(미리 보기) |
| 추가 Blob의 최대 블록 크기 | 4MiB |  |
| 추가 Blob의 최대 크기 | 50,000 x 4MiB(약 195GiB) |  |
| 페이지 Blob의 최대 크기 | 8TiB<sup>2</sup> |  |
| Blob 컨테이너별로 저장된 액세스 정책의 최대 수 | 5 |  |
| 단일 Blob의 대상 요청 속도 | 초당 최대 500개 요청 |  |
| 단일 페이지 Blob의 대상 처리량 | 초당 최대 60MiB<sup>2</sup> |  |
| 단일 블록 Blob의 대상 처리량 | 최대 스토리지 계정 수신/송신 한도<sup>1</sup> |  |

<sup>1</sup> 단일 Blob의 처리량은 동시성, 요청 크기, 성능 계층, 업로드할 원본 속도, 다운로드 대상 등 여러 가지 요인에 따라 달라집니다. [높은 처리량 블록 Blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)의 향상된 성능을 활용하려면 더 큰 Blob 또는 블록을 업로드하세요. 특히 표준 스토리지 계정의 경우 크기가 4MiB를 초과하는 Blob 또는 블록을 사용하여 [Blob 배치](/rest/api/storageservices/put-blob) 또는 [블록 배치](/rest/api/storageservices/put-block) 작업을 호출합니다. 프리미엄 블록 Blob 또는 Data Lake Storage Gen2 스토리지 계정의 경우 256KiB보다 큰 블록 또는 Blob 크기를 사용하세요.

<sup>2</sup> 페이지 Blob은 **계층 구조 네임스페이스** 설정이 없는 계정에서 아직 지원되지 않습니다.

다음 표에서는 서비스 버전별로 허용되는 최대 블록 및 Blob 크기를 설명합니다.

| 서비스 버전 | 최대 블록 크기(블록 배치 사용) | 최대 Blob 크기(블록 목록 배치 사용) | 단일 쓰기 작업을 통한 최대 Blob 크기(Blob 배치 사용) |
|-|-|-|-|
| 버전 2019-12-12 이상 | 4,000MiB(미리 보기) | 약 190.7TiB(4,000MiB X 50,000 블록)(미리 보기) | 5,000MiB(미리 보기) |
| 버전 2016-05-31~버전 2019-07-07 | 100MiB | 약 4.75TiB(100MiB X 50,000 블록) | 256MiB |
| 2016-05-31 이전 버전 | 4MiB | 약 195GiB(4MiB X 50,000 블록) | 64MiB |
