---
title: Azure SQL Edge에 대 한 릴리스 정보
description: Azure SQL Edge 이미지에서 새로운 기능 또는 변경 된 기능을 자세히 설명 하는 릴리스 정보
keywords: 릴리스 정보 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 06/05/2020
ms.openlocfilehash: d5d60db2cc0c35b908454be3f00716db6d2da945
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361645"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 릴리스 정보 

이 문서에서는 Azure SQL Edge의 모든 새 빌드에서 새로 제공 되는 기능과 변경 된 사항에 대해 설명 합니다.

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>SQL 엔진 빌드 번호-15.0.2000.1545
### <a name="fixes"></a>수정 프로그램
1. ONNX 모델을 사용 하 여 예측을 수정 하 여 ARM의 CPUID 문제 처리 
2. TSQL 스트리밍을 시작할 때 오류 경로 처리를 향상 시키기 위해 수정 
3. 데이터가 없는 경우 작업 메트릭에 있는 워터 마크 지연의 잘못 된 값을 수정 합니다. 
4. 어댑터에서 일괄 처리 사이에 변수 스키마가 있는 경우 출력 어댑터 문제를 해결 합니다.  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>SQL 엔진 빌드 번호-15.0.2000.1401
### <a name="whats-new"></a>새로운 기능
1.  ' Azure SQL Edge '로 업데이트 된 제품 이름
1.  Date_bucket 함수

    i.  Date, Time, DateTime 형식에 대 한 지원
3.  ONNX를 사용 하 여 예측
    
    i.  ONNX에 필요한 런타임 매개 변수 
    
4.  TSQL 스트리밍 지원 (제한 된 미리 보기) 
 
### <a name="known-issues"></a>알려진 문제

1. <b>문제:</b> 타이밍 문제로 인해 시작 시 dacpac를 적용 하는 동안 발생할 수 있는 잠재적 오류입니다.

    <b>해결 방법:</b> SQL Server 또는 컨테이너를 다시 시작 하면 dacpac 적용을 다시 시도 하 여 문제를 해결 해야 합니다.
### <a name="request-support"></a>지원 요청
1. 지원 [페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)에서 지원을 요청할 수 있습니다.

4. 다음 필드가 선택 되어 있는지 확인 합니다. 
    * 문제 유형-기술 
    * 서비스-IoT Edge
    * 문제 유형-문제가 IoT Edge 모듈과 관련이 있습니다.
    * 문제 하위 유형-Azure SQL Edge

   ![샘플 지원 티켓](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1.5
### <a name="sql-engine-build-number---15020001331"></a>SQL 엔진 빌드 번호-15.0.2000.1331
### <a name="whats-new"></a>새로운 기능
1. Date_bucket 함수
    
    i. DateTimeOffset 형식에 대 한 지원
2. ONNX 모델을 사용 하 여 예측

    i. nvarchar 지원
 
## <a name="ctp-14"></a>CTP 1.4
### <a name="sql-engine-build-number---15020001247"></a>SQL 엔진 빌드 번호-15.0.2000.1247
### <a name="whats-new"></a>새로운 기능
1.  ONNX 모델을 사용 하 여 예측
 
    i.  Varchar 지원
    
    ii. ONNX 런타임 버전 1.0으로 마이그레이션 
2.  기능 지원-다음 기능이 사용 됩니다.

    i.   CDC 지원

    ii.  압축 된 기록 테이블

    iii. 로그를 미리 읽기 위한 더 큰 배율 인수

    iv.  일괄 처리 모드 ES 필터 푸시 다운

    v.   미리 읽기 최적화
 
## <a name="ctp-13"></a>CTP 1.3
### <a name="sql-engine-build-number---15020001147"></a>SQL 엔진 빌드 번호-15.0.2000.1147
### <a name="whats-new"></a>새로운 기능
1. Azure IOT 포털 배포 

    i.   AMD64 및 ARM 이미지 배포 지원

    ii.  스트리밍 작업 만들기 지원

    iii. Dacpac 배포
2. ONNX 모델을 사용 하 여 예측

    i. 숫자 형식 지원
3. 기능 지원-다음 기능이 사용 됩니다.

    i.  열 저장소 검색으로 푸시 다운 집계

    ii. 메리-라운드 스캔
4. 공간 및 메모리 사용량 감소 작업
