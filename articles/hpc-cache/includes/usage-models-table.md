---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563245"
---
| 사용 모델 | 캐싱 모드 | 백 엔드 확인 | 최대 다시 쓰기 지연 |
|--|--|--|--|
| 자주 발생 하지 않는 매우 많은 쓰기 읽기 | 읽기 | 안 함 | 없음 |
| 쓰기 15% 초과 | 읽기/쓰기 | 8시간 | 20분 |
| 클라이언트에서 캐시 무시 | 읽기 | 30초 | 없음 |
| 쓰기 15% 초과, 자주 수행 하는 백 엔드 검사 (30 초) | 읽기/쓰기 | 30초 | 20분 |
| 쓰기 15% 초과, 빈번한 백 엔드 검사 (60 초) | 읽기/쓰기 | 60초 | 20분 |
| 쓰기 15% 이상, 자주 쓰기 복구 | 읽기/쓰기 | 30초 | 30초 |
| 3 시간 마다 자주 읽고 백업 서버 확인 | 읽기 | 3시간 | 없음 |
