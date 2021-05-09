---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563245"
---
| 사용 모델 | 캐싱 모드 | 백 엔드 확인 | 최대 다시 쓰기 지연 |
|--|--|--|--|
| 대량의 드문 쓰기 읽기 | 읽기 | 안 함 | 없음 |
| 15%보다 큰 쓰기 | 읽기/쓰기 | 8시간 | 20분 |
| 클라이언트가 캐시 무시 | 읽기 | 30초 | 없음 |
| 15% 초과 쓰기, 빈번한 백 엔드 검사(30초) | 읽기/쓰기 | 30초 | 20분 |
| 15% 초과 쓰기, 빈번한 백 엔드 검사(60초) | 읽기/쓰기 | 60초 | 20분 |
| 15% 초과 쓰기, 빈번한 다시 쓰기 | 읽기/쓰기 | 30초 | 30초 |
| 대량 읽기, 3시간마다 지원 서버 확인 | 읽기 | 3시간 | 없음 |
