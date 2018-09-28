---
title: 사용량 API 관련 Faq | Microsoft Docs
description: Azure Stack 미터의 Azure 사용량 API, 사용 시간과 보고 된 시간, 오류 코드는 비교 목록입니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: e4bb17bd068076a9ffa9bcab5826f0efa84a19d7
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406008"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Azure Stack 사용량 API의에서 질문과 대답

이 문서는 Azure Stack 사용량 API에 대 한 일부 자주 묻는 질문을 답변합니다.

## <a name="what-meter-ids-can-i-see"></a>어떤 미터 Id를 볼 수 있나요?
다음과 같은 리소스 공급자에 대 한 사용 현황 보고 됩니다.

### <a name="network"></a>네트워크
  
**측정기 ID**: F271A8A388C44D93956A063E1D2FA80B  
**미터 이름**: 고정 IP 주소 사용  
**단위**: IP 주소  
**메모**: 사용 된 수의 IP 주소입니다. 일일 세분성을 사용 하 여 사용량 API를 호출 하는 경우 미터 시간 수를 곱한 IP 주소를 반환 합니다.  
  
**측정기 ID**: 9E2739BA86744796B465F64674B822BA  
**미터 이름**: 동적 IP 주소 사용  
**단위**: IP 주소  
**메모**: 사용 된 수의 IP 주소입니다. 일일 세분성을 사용 하 여 사용량 API를 호출 하는 경우 미터 시간 수를 곱한 IP 주소를 반환 합니다.  
  
### <a name="storage"></a>Storage
  
**측정기 ID**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**미터 이름**: TableCapacity  
**단위**: GB\*시간  
**메모**: 총 용량 테이블에서 사용 합니다.  
  
**측정기 ID**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**미터 이름**: PageBlobCapacity  
**단위**: GB\*시간  
**메모**: 총 용량 페이지 blob으로 사용 합니다.  
  
**측정기 ID**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**미터 이름**: QueueCapacity  
**단위**: GB\*시간  
**메모**: 총 용량 큐에서 사용 합니다.  
  
**측정기 ID**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**미터 이름**: BlockBlobCapacity  
**단위**: GB\*시간  
**메모**: 총 블록 blob에 의해 사용 된 용량입니다.  
  
**측정기 ID**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**미터 이름**: TableTransactions  
**단위**: 000's 수가 10에서 요청  
**메모**: 서비스 요청 (단위: 10,000) 테이블입니다.  
  
**측정기 ID**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**미터 이름**: TableDataTransIn  
**단위**: 수신 데이터 (GB)  
**메모**: 테이블 서비스 데이터 수신 (GB)입니다.  
  
**측정기 ID**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**미터 이름**: TableDataTransOut  
**단위**: gb에서 송신  
**메모**: 테이블 서비스 데이터 송신 (GB)  
  
**측정기 ID**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**미터 이름**: BlobTransactions  
**단위**: 000's 10에서 요청 수  
**메모**: 서비스 요청 (단위: 10,000)에서 Blob입니다.  
  
**측정기 ID**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**미터 이름**: BlobDataTransIn  
**단위**: 수신 데이터 (GB)  
**메모**: Blob 서비스 데이터 수신 (GB)입니다.  
  
**측정기 ID**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**미터 이름**: BlobDataTransOut  
**단위**: gb에서 송신  
**메모**: Blob 서비스 데이터 송신 (GB)입니다.  
  
**측정기 ID**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**미터 이름**: QueueTransactions  
**단위**: 000's 10에서 요청 수  
**메모**: (단위: 10,000)에서 서비스 요청을 큐 대기 합니다.  
  
**측정기 ID**: E518E809-E369-4A45-9274-2017B29FFF25  
**미터 이름**: QueueDataTransIn  
**단위**: 수신 데이터 (GB)  
**메모**: 큐 서비스 데이터 수신 (GB)입니다.  
  
**측정기 ID**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**미터 이름**: QueueDataTransOut  
**단위**: gb에서 송신  
**메모**: 큐 서비스 데이터 송신 (GB)  

### <a name="compute"></a>컴퓨팅 
  
**측정기 ID**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**미터 이름**: 기본 VM 크기 시간  
**단위**: 가상 코어 시간  
**메모**: 가상 코어 수를 곱하고 VM 실행 시간입니다.  
  
**측정기 ID**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**미터 이름**: Windows VM 크기 시간  
**단위**: 가상 코어 시간  
**메모**: 가상 코어 수를 곱하고 VM에서 실행 되는 시간입니다.  
  
**측정기 ID**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**미터 이름**: VM 크기 시간  
**단위**: VM 시간  
**메모**: 기본 및 Windows VM 캡처합니다. 코어에 대 한 조정 되지 않습니다.  
  
### <a name="managed-disks"></a>Managed Disks

**측정기 ID**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**미터 이름**: S4   
**단위**: 디스크 수   
**메모**: 표준 관리 디스크 – 32GB 

**측정기 ID**: dc9fc6a9-0782-432a-b8dc-978130457494   
**미터 이름**: S6   
**단위**: 디스크 수   
**메모**: 표준 관리 디스크-64GB 

**측정기 ID**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**미터 이름**: S10   
**단위**: 디스크 수   
**메모**: 표준 관리 디스크-128GB 

**측정기 ID**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**미터 이름**: S15   
**단위**: 디스크 수   
**메모**: 표준 관리 디스크-256GB 

**측정기 ID**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**미터 이름**: 하면 S20   
**단위**: 디스크 수      
**메모**: 표준 관리 디스크-512 GB 

**측정기 ID**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**미터 이름**: S30   
**단위**: 디스크 수   
**메모**: 표준 관리 디스크 – 1024GB 

**측정기 ID**: d9aac1eb-a5d1-42f2-b617-9e3ea94fed88   
**미터 이름**: S40   
**단위**: 디스크 수   
**메모**: 표준 관리 디스크 – 2,048GB 

**측정기 ID**: a54899dd-458e-4a40-9abd-f57cafd936a7   
**미터 이름**: S50   
**단위**: 디스크 수   
**메모**: 표준 관리 디스크 – 4096GB 

**측정기 ID**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**미터 이름**: P4   
**단위**: 디스크 수   
**메모**: 프리미엄 관리 디스크 – 32GB 

**측정기 ID**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**미터 이름**: P6   
**단위**: 디스크 수   
**메모**: 프리미엄 관리 디스크-64GB 

**측정기 ID**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**미터 이름**: P10   
**단위**: 디스크 수   
**메모**: 프리미엄 관리 디스크-128GB  

**측정기 ID**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**미터 이름**: P15  
**단위**: 디스크 수   
**메모**: 프리미엄 관리 디스크-256GB 

**측정기 ID**: c7e7839c-293b-4761-ae4c-848eda91130b   
**미터 이름**: P20   
**단위**: 디스크 수   
**메모**: 프리미엄 관리 디스크-512 GB 

**측정기 ID**: 9f502103-adf4-4488-b494-456c95d23a9f   
**미터 이름**: P30   
**단위**: 디스크 수   
**메모**: 프리미엄 관리 디스크 – 1024GB 

**측정기 ID**: 043757fc-049f-4e8b-8379-45bb203c36b1   
**미터 이름**: P40   
**단위**: 디스크 수    
**메모**: 프리미엄 관리 디스크 – 2,048GB 

**측정기 ID**: c0342c6f-810b-4942-85d3-6eaa561b6570   
**미터 이름**: P50   
**단위**: 디스크 수   
**메모**: 프리미엄 관리 디스크 – 4096GB 

**측정기 ID**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**미터 이름**: ActualStandardDiskSize   
**단위**: 바이트      
**메모**: 표준 관리 디스크의 디스크에 실제 크기  

**측정기 ID**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**미터 이름**: ActualPremiumDiskSize   
**단위**: 바이트      
**메모**: 실제 디스크의 크기를 프리미엄 관리 디스크 

**측정기 ID**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**미터 이름**: ActualStandardSnapshotSize   
**단위**: 바이트   
**메모**: 실제 디스크의 크기를 관리 되는 표준 스냅숏.  

**측정기 ID**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**미터 이름**: ActualPremiumSnapshotSize   
**단위**: 바이트   
**메모**: 관리 되는 프리미엄 디스크에 실제 크기입니다.   

### <a name="sql-rp"></a>Sql RP
  
**측정기 ID**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**미터 이름**: DatabaseSizeHourSqlMeter  
**단위**: MB\*시간  
**메모**: 총 DB 용량을 만들 때. 일일 세분성을 사용 하 여 사용량 API를 호출 하는 경우 미터 시간 수를 곱한 (mb)를 반환 합니다.  
  
### <a name="mysql-rp"></a>MySql RP   
  
**측정기 ID**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**미터 이름**: DatabaseSizeHourMySqlMeter  
**단위**: MB\*시간  
**메모**: 총 DB 용량을 만들 때. 일일 세분성을 사용 하 여 사용량 API를 호출 하는 경우 미터 시간 수를 곱한 (mb)를 반환 합니다.    
### <a name="key-vault"></a>Key Vault   
  
**측정기 ID**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**미터 이름**: Key Vault 트랜잭션에  
**단위**: 000's 수가 10에서 요청  
**메모**: 키 자격 증명 모음 데이터 평면은에 의해 수신 된 수의 REST API 요청 합니다.  
  
**측정기 ID**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**미터 이름**: 고급 키 트랜잭션  
**단위**: 10k 트랜잭션에  
**메모**: RSA 4k 3 K/ECC 키 트랜잭션. (미리 보기)입니다.  
  
### <a name="app-service"></a>앱 서비스   
  
**측정기 ID**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**미터 이름**: App Service  
**단위**: 가상 코어 시간  
**메모**: 앱 서비스를 실행 하는 데 사용 되는 가상 코어 수입니다. 참고: Microsoft Azure Stack에서 App Service를 청구이 미터를 사용 합니다. 클라우드 서비스 공급자 해당 테 넌 트에 대 한 사용량을 계산 하 다른 App Service 요금 청구 기준 (아래)를 사용할 수 있습니다.  
  
**측정기 ID**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**미터 이름**: 함수 요청  
**단위**: 10 개 요청  
**메모**: (10 실행) 당 요청 된 실행의 총 수입니다. 실행 함수 이벤트에 대 한 응답으로 실행 하거나 바인딩에서 트리거될 때마다 계산 됩니다.  
  
**측정기 ID**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**미터 이름**: 함수-Compute  
**단위**: GB-초  
**메모**: 리소스 소비 (기가바이트 초) (GB/s). **리소스 사용량을 관찰** 평균 메모리 크기 (GB) 함수를 실행 하는 데 걸리는 시간 (밀리초) 시간을 곱하여 계산 됩니다. 실행 시간을 반올림 하 여 계산을 사용 하 여 최대 1, 536 MB의 최대 메모리 크기를 가장 가까운 128MB까지 반올림 하 여 함수에 의해 사용 되는 메모리 측정은까지 ms. 최소 실행 시간 및 단일 함수 실행에 대 한 메모리 100ms 및 128mb 각각입니다.  
  
**측정기 ID**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**미터 이름**: 공유 앱 서비스 시간  
**단위**: 1 시간  
**메모**: App Service 계획 하는 분할 된 데이터베이스의 시간 사용량에 따른 합니다. 계획 당 앱 별로 측정 됩니다.  
  
**측정기 ID**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**미터 이름**: 무료 앱 서비스 시간  
**단위**: 1 시간  
**메모**: 당 무료 App Service 계획 시간 사용 합니다. 계획 당 앱 별로 측정 됩니다.  
  
**측정기 ID**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**미터 이름**: 작은 표준 앱 서비스 시간  
**단위**: 1 시간  
**메모**:에 따라 크기 및 인스턴스 수를 계산 합니다.  
  
**측정기 ID**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**미터 이름**: 표준 중간 앱 서비스 시간  
**단위**: 1 시간  
**메모**:에 따라 크기 및 인스턴스 수를 계산 합니다.  
  
**측정기 ID**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**미터 이름**: 큰 표준 앱 서비스 시간  
**단위**: 1 시간  
**메모**:에 따라 크기 및 인스턴스 수를 계산 합니다.  
  
### <a name="custom-worker-tiers"></a>사용자 지정 작업자 계층   
  
**측정기 ID**: *사용자 지정 작업자 계층*  
**미터 이름**: 사용자 지정 작업자 계층  
**단위**: 시간  
**메모**: 결정적 미터 ID SKU 및 사용자 지정 작업자 계층 이름에 따라 만들어집니다. 이 미터 ID는 각 사용자 지정 작업자 계층에 대해 고유 합니다.  
  
**측정기 ID**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**미터 이름**: SNI SSL  
**단위**: SNI SSL 바인딩으로  
**메모**: App Service는 두 가지 유형의 SSL 연결: 서버 이름 표시 (SNI) SSL 연결과 IP 주소 SSL 연결 합니다. IP 기반 SSL은 모든 브라우저에서 작동하는 반면 SNI 기반 SSL은 최신 브라우저에서 작동합니다.  
  
**측정기 ID**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**미터 이름**: IP SSL  
**단위**: 각 IP 기반 SSL 바인딩을  
**메모**: App Service는 두 가지 유형의 SSL 연결: 서버 이름 표시 (SNI) SSL 연결과 IP 주소 SSL 연결 합니다. IP 기반 SSL은 모든 브라우저에서 작동하는 반면 SNI 기반 SSL은 최신 브라우저에서 작동합니다.  
  
**측정기 ID**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**미터 이름**: 웹 프로세스  
**단위**:  
**메모**: 시간당 활성 사이트 당으로 계산 합니다.  
  
**측정기 ID**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**미터 이름**: 외부 송신 대역폭  
**단위**: GB  
**메모**: 총 들어오는 요청 응답 바이트 + 총 발신 요청 바이트 + 총 들어오는 FTP 요청 응답 바이트 + 총 들어오는 웹 요청 응답 바이트를 배포 합니다.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Azure Stack 사용량 Api와 비교 하는 방법의 [Azure 사용량 API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (현재 공개 미리 보기로 제공)?
* 테 넌 트 사용량 API는 한 가지 예외를 사용 하 여 Azure API를 사용 하 여 일치: 합니다 *showDetails* 플래그 현재 Azure Stack에서 지원 되지 않습니다.
* 공급자 사용량 API는 Azure Stack에만 적용 됩니다.
* 현재는 [RateCard API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) 는 Azure에서 사용할 수 있는 Azure Stack에서 사용할 수 없는 합니다.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>사용 시간과 보고 된 시간 사이의 차이 무엇입니까?
사용 현황 데이터 보고서에 두 개의 기본 시간 값:

* **시간을 보고**합니다. 사용 이벤트 사용 시스템을 입력 하는 경우 시간
* **사용 시간**합니다. Azure Stack 리소스를 사용 하는 경우 시간

사용 시간과 보고 된 시간에 대 한 특정 사용 이벤트에 대 한 값으로 불일치를 볼 수 있습니다. 지연 환경에서 여러 시간으로 수 있습니다.

현재 쿼리하면 해야만 *보고 된 시간은*합니다.

## <a name="what-do-these-usage-api-error-codes-mean"></a>이러한 사용량 API 오류 코드의 의미
| **HTTP 상태 코드** | **오류 코드** | **설명** |
| --- | --- | --- |
| 400/잘못 된 요청 |*NoApiVersion* |합니다 *api-version* 쿼리 매개 변수가 누락 되었습니다. |
| 400/잘못 된 요청 |*InvalidProperty* |속성이 없거나 값이 잘못 되었습니다. 응답 본문에 오류 코드에서 메시지는 누락 된 속성을 식별합니다. |
| 400/잘못 된 요청 |*RequestEndTimeIsInFuture* |에 대 한 값 *ReportedEndTime* 시작일이 미래입니다. 않을이 인수에 대 한 나중에 값을 설치 하지 수 있습니다. |
| 400/잘못 된 요청 |*SubscriberIdIsNotDirectTenant* |공급자 API 호출에는 호출자의 유효한 테 넌 트가 아닌 구독 ID를 사용 했습니다. |
| 400/잘못 된 요청 |*SubscriptionIdMissingInRequest* |호출자의 구독 ID가 없습니다. |
| 400/잘못 된 요청 |*InvalidAggregationGranularity* |잘못 된 집계가 세분성이 요청 되었습니다. 유효한 값은 일별 및 시간입니다. |
| 503 |*ServiceUnavailable* |서비스 사용량이 또는 호출 제한 되 고 다시 시도 가능한 오류가 발생 했습니다. |

## <a name="next-steps"></a>다음 단계
[고객 청구 및 Azure Stack의 비용 정산](azure-stack-billing-and-chargeback.md)

[공급자 리소스 사용 API](azure-stack-provider-resource-api.md)

[테 넌 트 리소스 사용량 API](azure-stack-tenant-resource-usage-api.md)
