---
title: Azure SQL Edge에 대 한 릴리스 정보
description: Azure SQL Edge 이미지에서 새로운 기능 또는 변경 된 기능을 자세히 설명 하는 릴리스 정보입니다.
keywords: 릴리스 정보 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: e078fb91b3279b6f4321cd51dfb094f82bbe5f14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98696382"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 릴리스 정보 

이 문서에서는 Azure SQL Edge의 모든 새로운 빌드에 대해 새롭게 제공 되는 기능과 변경 된 기능을 설명 합니다.

## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

SQL 엔진 빌드 15.0.2000.1554

### <a name="fixes"></a>수정 프로그램

- T-sql 스트리밍  
   - 스트리밍 개체에 대 한 소유권 및 권한 수정
   - 로그 회전 및 로그 접두사로 향상 된 로깅
   - Azure Stream Analytics: 로깅 향상, 어댑터의 오류 코드/오류 메시지 개선 

- ONNX
    - 병렬 쿼리 시나리오 및 모델 정리 오류에 대 한 버그 수정
    - ONNX 런타임을 1.5.1로 업그레이드

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

SQL 엔진 빌드 15.0.2000.1553

### <a name="whats-new"></a>새로운 기능

- 계산 열에 정의 된 Date_Bucket 식을 허용 합니다.

### <a name="fixes"></a>수정 프로그램

- 무기한 시간 제한으로 사용 하도록 설정 된 보존 정책이 있는 테이블을 삭제 하는 보존 정책 수정
- 스트리밍 기능 및 보존 정책 기능에 대 한 DacFx 배포 지원 
- SAS URL의 중첩 된 폴더에서 배포를 사용 하도록 설정 하는 DacFx 배포 수정 
- 오류 메시지에서 긴 열 이름을 지원 하도록 수정 예측

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

SQL 엔진 빌드 15.0.2000.1552

### <a name="whats-new"></a>새로운 기능
- Ubuntu 18.04을 기반으로 하는 컨테이너 이미지 
- 및 `IGNORE NULL` 함수로 및 `RESPECT NULL` 구문에 `LAST_VALUE()` 대 한 지원 `FIRST_VALUE()` 
- ONNX를 사용 하 여 예측 하기 위한 안정성 향상
- 데이터 보존 정책을 기반으로 하는 정리 지원:
   - 문제 해결을 위한 보존 정리 태스크에 대 한 링 버퍼 지원
- 새 기능 지원: 
   - 빠른 복구
   - 쿼리 자동 조정
   - 병렬 실행 시나리오
- 낮은 전원 모드의 전원 절약 기능 향상
- 스트리밍 새로운 기능 지원: 
   - [스냅숏 창](/stream-analytics-query/snapshot-window-azure-stream-analytics): 새 창 유형을 사용 하면 동시에 도착 하는 이벤트 별로 그룹화 할 수 있습니다.
   - [Topone](/stream-analytics-query/topone-azure-stream-analytics) 및 [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) 는 분석 함수로 사용 하도록 설정할 수 있습니다. 선택한 열을 기준으로 정렬 된 레코드를 반환할 수 있습니다. 창의 일부일 필요는 없습니다. 
   - [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)개선 

### <a name="fixes"></a>수정 프로그램
- T-sql 스트리밍 작업 문제 해결에 대 한 추가 오류 메시지 및 세부 정보 
- 유휴 모드에서 배터리 수명을 보존할 수 있는 향상 된 기능 
- T-sql 스트리밍 엔진 수정: 
   - 중지 된 스트리밍 작업 정리 
   - 지역화에 대 한 수정 
   - 향상 된 유니코드 처리 
   - 사용자가 get_streaming_job에서 작업 오류 오류를 쿼리할 수 있도록 하는 SQL Edge T-sql 스트리밍에 대 한 향상 된 디버깅
- 데이터 보존 정책을 기반으로 하는 정리: 
    - 보존 정책 만들기 및 정리 시나리오에 대 한 수정
- 낮은 전원 모드의 전원 절약을 위해 백그라운드 타이머 작업을 수정 합니다.

### <a name="known-issues"></a>알려진 문제 
- Date_Bucket T-sql 함수는 계산 열에서 사용할 수 없습니다.


## <a name="ctp-23"></a>CTP 2.3
SQL 엔진 빌드 15.0.2000.1549
### <a name="whats-new"></a>새로운 기능
- Date_Bucket () 함수의 사용자 지정 원본 지원 
- SQL 배포의 일부로 BACPAC 파일 지원
- 데이터 보존 정책을 기반으로 하는 정리 지원:      
   - 보존 정책을 사용 하도록 설정 하는 DDL 지원 
   - 저장 프로시저 및 백그라운드 정리 태스크 정리
   - 정리 작업을 모니터링 하는 확장 이벤트

### <a name="fixes"></a>수정 프로그램
- T-sql 스트리밍 작업 문제 해결에 대 한 추가 오류 메시지 및 세부 정보 
- 유휴 모드에서 배터리 수명을 보존할 수 있는 향상 된 기능 
- T-sql 스트리밍 엔진: 
   - Substreamed 도약 창에서 정지 워터 마크에 대 한 수정 
   - 프레임 워크 예외 처리를 수정 하 여 사용자에 게 조치 가능한 오류로 수집 되는지 확인 합니다.


## <a name="ctp-22"></a>CTP 2.2
SQL 엔진 빌드 15.0.2000.1546
### <a name="whats-new"></a>새로운 기능
- 루트가 아닌 컨테이너 지원 
- 사용 현황 및 진단 데이터 수집에 대 한 지원 
- T-sql 스트리밍 업데이트:
   - 스트림 개체 이름의 유니코드 문자 지원

### <a name="fixes"></a>수정 프로그램
- T-sql 스트리밍 업데이트:
   - 프로세스 정리 기능 향상
   - 로깅 및 진단 기능 향상
- 데이터 수집에 대 한 성능 향상

## <a name="ctp-21"></a>CTP 2.1 
SQL 엔진 빌드 15.0.2000.1545
### <a name="fixes"></a>수정 프로그램
- ARM의 CPUID 문제를 처리 하는 예측-ONNX 모델을 사용할 수 있습니다. 
- T-sql 스트리밍이 시작 될 때 오류 경로 처리 향상
- 데이터가 없는 경우 작업 메트릭의 워터 마크 지연 값을 수정 했습니다.
- 어댑터에서 일괄 처리 사이에 변수 스키마가 있는 경우 출력 어댑터의 문제를 수정 합니다.  

## <a name="ctp-20"></a>CTP 2.0 
SQL 엔진 빌드 15.0.2000.1401
### <a name="whats-new"></a>새로운 기능
-   *AZURE SQL Edge* 로 업데이트 된 제품 이름
-  Date_Bucket 함수:
    - Date, Time 및 DateTime 형식에 대 한 지원
- ONNX를 사용 하 여 예측:
    - 런타임 매개 변수에 대 한 ONNX 요구 사항  
- T-sql 스트리밍 지원 (제한 된 미리 보기) 
 
### <a name="known-issues"></a>알려진 문제

- 문제: 타이밍 문제로 인해 DACPAC를 시작 시 적용 하는 동안 오류가 발생할 수 있습니다.
- 해결 방법: SQL Server을 다시 시작 합니다. 그렇지 않으면 컨테이너는 DACPAC 적용을 다시 시도 합니다.

### <a name="request-support"></a>지원 요청
지원 [페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)에서 지원을 요청할 수 있습니다. 다음 필드를 선택 합니다. 
- **문제 유형**: *기술* 
- **서비스**: *IoT Edge*
- **문제 유형**: *내 문제가 IoT Edge 모듈과 관련이* 있습니다.
- **문제 하위 유형**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="샘플 지원 티켓을 보여 주는 스크린샷":::

## <a name="ctp-15"></a>CTP 1.5
SQL 엔진 빌드 15.0.2000.1331
### <a name="whats-new"></a>새로운 기능
- Date_Bucket 함수:
    - DateTimeOffset 형식에 대 한 지원
- ONNX 모델을 사용 하 여 예측:
  - NVARCHAR 지원
 
## <a name="ctp-14"></a>CTP 1.4
SQL 엔진 빌드 15.0.2000.1247
### <a name="whats-new"></a>새로운 기능
-   ONNX 모델을 사용 하 여 예측:
    - VARCHAR 지원
    - ONNX 런타임 버전 1.0으로 마이그레이션 

- 사용할 수 있는 기능은 다음과 같습니다.
  - CDC 지원
  - 압축 된 기록 테이블
  - 로그 미리 읽기에 대 한 상위 비율
  - 일괄 처리 모드 ES 필터 푸시 다운
  - 미리 읽기 최적화
 
## <a name="ctp-13"></a>CTP 1.3
SQL 엔진 빌드 15.0.2000.1147
### <a name="whats-new"></a>새로운 기능
- Azure IoT 포털 배포: 
    - AMD64 및 ARM 이미지 배포 지원
    - 스트리밍 작업 만들기 지원
    - DACPAC 배포
- ONNX 모델을 사용 하 여 예측:
    - 숫자 형식 지원
- 사용할 수 있는 기능은 다음과 같습니다.
    - 열 저장소 검색으로 푸시 다운 집계
    - 메리-라운드 스캔
- 공간 및 메모리 사용량 감소 작업
