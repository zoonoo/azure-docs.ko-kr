---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536444"
---
| 리소스 | 표준 파일 공유 | 프리미엄 파일 공유 |
|----------|---------------|------------------------------------------|
| 파일 공유의 최소 크기 | 최소값 없음 종 량 제 | 100 GiB; 구축 |
| 파일 공유의 최대 크기 | 100 TiB *, 5 TiB | 100 TiB |
| 파일 공유의 최대 파일 크기 | 1TiB | 1TiB |
| 파일 공유의 최대 파일 수 | 제한 없음 | 제한 없음 |
| 공유 당 최대 IOPS | 1만 IOPS *, 1000 IOPS | 100,000 IOPS |
| 파일 공유 당 저장 된 액세스 정책의 최대 수 | 5 | 5 |
| 단일 파일 공유에 대 한 대상 처리량 | 최대 300 MiB/초 *, 최대 60 MiB/초,  | 프리미엄 파일 공유 수신 및 송신 값 참조|
| 단일 파일 공유에 대 한 최대 송신 | 표준 파일 공유 대상 처리량 참조 | 최대 6204 MiB/s |
| 단일 파일 공유에 대 한 최대 수신 | 표준 파일 공유 대상 처리량 참조 | 최대 4136 MiB/s |
| 파일별 최대 열린 핸들 수 | 2,000개의 열린 핸들 | 2,000개의 열린 핸들 |
| 최대 공유 스냅샷 수 | 200개의 공유 스냅샷 | 200개의 공유 스냅샷 |
| 최대 개체(디렉터리 및 파일) 이름 길이 | 2048 문자 | 2048 문자 |
| 최대 경로 이름 구성 요소(경로 \A\B\C\D의 각 문자가 구성 요소) | 255자 | 255자 |

\*표준 파일 공유의 기본값은 5 TiB입니다. 표준 파일 공유 크기를 100 TiB까지 늘리는 방법에 대 한 자세한 내용은 [대용량 파일 공유 사용 및 만들기](../articles/storage/files/storage-files-how-to-create-large-file-share.md) 를 참조 하세요.
