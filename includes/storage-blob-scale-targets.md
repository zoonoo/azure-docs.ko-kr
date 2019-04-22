---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/11/2019
ms.author: tamram
ms.openlocfilehash: b3e2f018a3f1ba2563ba8cf2df6dfd4959be592e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737193"
---
| 리소스 | 대상        |
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

<sup>1</sup> 단일 개체 처리량 등을 망라 하는 여러 가지 요인에 따라 달라 집니다: 동시성, 작업 크기, 성능 계층, 업로드에 대 한 소스 및 다운로드에 대 한 대상의 속도.