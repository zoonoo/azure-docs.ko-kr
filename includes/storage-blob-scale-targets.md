---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67182415"
---
| 리소스 | 대상        |
|----------|---------------|
| 단일 blob 컨테이너의 최대 크기 | 최대 저장소 계정 용량과 동일 |
| 블록 blob 또는 추가 blob의 최대 블록 수 | 50,000개 블록 |
| 블록 blob의 최대 블록 크기 | 100MiB |
| 블록 blob의 최대 크기 | 5만 X 100 MiB (약 4.75 TiB) |
| 추가 blob의 최대 블록 크기 | 4MiB |
| 추가 blob의 최대 크기 | 5만 x 4 MiB (약 195 GiB) |
| 페이지 blob의 최대 크기 | 8TiB |
| Blob 컨테이너 당 저장 된 액세스 정책의 최대 수 | 5 |
|단일 Blob의 목표 처리량 |최대 저장소 계정 수신/송신 제한<sup>1</sup> |

<sup>1</sup> 단일 개체 처리량은 동시성, 요청 크기, 성능 계층, 업로드할 원본 속도, 다운로드 대상 등 여러 가지 요인에 따라 달라 집니다. [높은 처리량 블록 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) 성능 향상 기능을 활용 하려면 put blob을 사용 하거나 > 4 MiB의 블록 요청 크기를 설정 합니다 (프리미엄 성능 블록 blob 저장소의 경우 또는 Data Lake Storage Gen2의 경우 > 256 KiB).
