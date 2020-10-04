---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: b2e753a3f9741856dd8b81755912ad7bd78b5557
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711422"
---
Azure Storage는 Azure Monitor에서 다음과 같은 트랜잭션 메트릭을 제공합니다.

| 메트릭 | Description |
| ------------------- | ----------------- |
| 트랜잭션 | 스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 적용 가능한 차원: ResponseType, GeoType, ApiName, Authentication([정의](#metrics-dimensions))<br/> 값 예제: 1024 |
| 수신 | 수신 데이터 양입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| 송신 | 송신 데이터 양입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| SuccessServerLatency | Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다. <br/><br/> 단위: 밀리초 <br/> 집계 유형: 평균 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| SuccessE2ELatency | 스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다. <br/><br/> 단위: 밀리초 <br/> 집계 유형: 평균 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| 가용성 | 스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 총 청구 가능 요청 값을 적용 가능한 요청 수(예기치 않은 오류를 발생시킨 요청 포함)로 나누어서 계산합니다. 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다. <br/><br/> 단위: 백분율 <br/> 집계 유형: 평균 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 99.99 |