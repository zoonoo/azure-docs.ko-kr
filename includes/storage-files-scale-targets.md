---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 8a8619da831dfa5b240bd93d3a046c49cc30affa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76901420"
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

\*대부분의 지역에서 사용할 수 있는 사용 가능한 지역에 대한 자세한 내용은 [지역 별 가용성을](../articles/storage/files/storage-files-planning.md#regional-availability) 참조하십시오.
