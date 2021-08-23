---
ms.service: hpc-cache
ms.topic: include
ms.date: 06/17/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 37f2895ed476cc0dbe0bdb3def034a17448d8dca
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587227"
---
| 사용 모델 | 캐싱 모드 | 백 엔드 확인 | 최대 다시 쓰기 지연 |
|--|--|--|--|
| 대량의 드문 쓰기 읽기 <!--read_heavy_infreq-->| 읽기 | 안 함 | 없음 |
| 15%보다 큰 쓰기 <!--write_workload_15-->| 읽기/쓰기 | 8시간 | 1시간 |
| 클라이언트가 캐시 무시 <!--write_around-->| 읽기 | 30초 | 없음 |
| 15% 초과 쓰기, 빈번한 백 엔드 검사(30초) <!--write_workload_check_30-->| 읽기/쓰기 | 30초 | 1시간 |
| 15% 초과 쓰기, 빈번한 백 엔드 검사(60초) <!--write_workload_check_60-->| 읽기/쓰기 | 60초 | 1시간 |
| 15% 초과 쓰기, 빈번한 다시 쓰기 <!--write_workload_cloudws-->| 읽기/쓰기 | 30초 | 30초 |
| 대량 읽기, 3시간마다 지원 서버 확인 <!--read_heavy_check_180-->| 읽기 | 3시간 | 없음 |
