---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114799"
---
| Resource | 대상        |
|----------|---------------|
| 단일 blob 컨테이너의 최대 크기 | 최대 저장소 계정 용량 동일 |
| 최대 블록의 블록 blob 또는 추가 blob | 50,000개 블록 |
| 블록 blob에 블록의 최대 크기 | 100MiB |
| 블록 blob의 최대 크기 | 50,000 X 100 MiB (약 4.75tib) |
| 추가 blob의 블록의 최대 크기 | 4MiB |
| 추가 blob의 최대 크기 | 50,000 x 4mib((약 195 GiB) |
| 페이지 blob의 최대 크기 | 8TiB |
| Blob 컨테이너 별로 저장 된 액세스 정책 최대 수 | 5 |
|단일 Blob의 목표 처리량 |저장소 계정 송/수신 제한까지<sup>1</sup> |

<sup>1</sup> 단일 개체 처리량 등을 망라 하는 여러 가지 요인에 따라 달라 집니다: 동시성, 요청 크기, 성능 계층, 업로드에 대 한 소스 및 다운로드에 대 한 대상의 속도. 활용 하기 위해 [처리량이 높은 블록 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) > 4 MiB (> 256 3:n2}kib/ 프리미엄 성능 블록 blob storage 또는 Data Lake 저장소 Gen2)는 Put Blob 또는 블록 배치 요청 크기를 사용 하는 성능 향상.
