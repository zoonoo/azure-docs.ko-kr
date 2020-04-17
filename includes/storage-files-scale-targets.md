---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536444"
---
| 리소스 | 표준 파일 공유 | 프리미엄 파일 공유 |
|----------|---------------|------------------------------------------|
| 파일 공유의 최소 크기 | 최소 없음; 당신이 가서 지불 | 100 GiB; 프로 비전 |
| 파일 공유의 최대 크기 | 100 TiB*, 5 TiB | 100 TiB |
| 파일 공유에 있는 파일의 최대 크기 | 1TiB | 1TiB |
| 파일 공유의 최대 파일 수 | 제한 없음 | 제한 없음 |
| 주당 최대 IOPS | 10,000 IOPS*, 1,000 IOPS | 100,000 IOPS |
| 파일 공유당 저장된 액세스 정책 의 최대 수 | 5 | 5 |
| 단일 파일 공유에 대한 목표 처리량 | 최대 300MiB/sec*, 최대 60MiB/sec  | 프리미엄 파일 공유 침투 및 송신 값 보기|
| 단일 파일 공유에 대한 최대 송신 | 표준 파일 공유 대상 처리량 보기 | 최대 6,204MiB/s |
| 단일 파일 공유에 대한 최대 사용 | 표준 파일 공유 대상 처리량 보기 | 최대 4,136MiB/s |
| 파일별 최대 열린 핸들 수 | 2,000개의 열린 핸들 | 2,000개의 열린 핸들 |
| 최대 공유 스냅샷 수 | 200개의 공유 스냅샷 | 200개의 공유 스냅샷 |
| 최대 개체(디렉터리 및 파일) 이름 길이 | 2,048자 | 2,048자 |
| 최대 경로 이름 구성 요소(경로 \A\B\C\D의 각 문자가 구성 요소) | 255자 | 255자 |

\*표준 파일 공유의 기본값은 5 TiB이며, 표준 파일 공유를 최대 100TiB까지 늘리는 방법에 대한 자세한 내용은 [사용 및 큰 파일 공유를 만듭니다.](../articles/storage/files/storage-files-how-to-create-large-file-share.md)
