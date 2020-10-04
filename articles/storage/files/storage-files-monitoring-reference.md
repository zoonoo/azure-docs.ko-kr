---
title: Azure Files 모니터링 데이터 참조 | Microsoft Docs
description: Azure Files의 모니터링 데이터에 대 한 로그 및 메트릭 참조입니다.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 11f89a0a42a42fd84d1dc0c8606128f5af9e751e
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711566"
---
# <a name="azure-files-monitoring-data-reference"></a>Azure Files 모니터링 데이터 참조

Azure Files 모니터링 데이터 수집 및 분석에 대 한 자세한 내용은 [Azure Files 모니터링](storage-files-monitoring.md) 을 참조 하세요.

## <a name="metrics"></a>메트릭

다음 표에서는 Azure Files에 대해 수집 된 플랫폼 메트릭을 나열 합니다. 

### <a name="capacity-metrics"></a>용량 메트릭

용량 메트릭 값은 매 1시간마다 Azure Monitor에 전송됩니다. 값은 매일 새로 고쳐집니다. 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 용량 메트릭에 대해 지원되는 시간 조직은 1시간(PT1H)입니다.

Azure Files Azure Monitor에서 다음과 같은 용량 메트릭을 제공 합니다.

#### <a name="account-level"></a>계정 수준

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure 파일

다음 표에서는 [Azure Files 메트릭을](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices)보여 줍니다.

| 메트릭 | Description |
| ------------------- | ----------------- |
| FileCapacity | 스토리지 계정에 사용한 File Storage 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| FileCount   | 스토리지 계정의 파일 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| FileShareCapacityQuota | Azure Files 서비스에서 사용할 수 있는 저장소 크기의 상한 (바이트)입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024|
| FileShareCount | 스토리지 계정의 파일 공유 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| FileShareProvisionedIOPS | 파일 공유의 프로 비전 된 IOPS 수입니다. 이 메트릭은 premium file storage에만 적용 됩니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 |
| FileShareSnapshotCount | 저장소 계정의 Azure Files 서비스에서 공유에 있는 스냅숏의 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 | 
|FileShareSnapshotSize|저장소 계정의 Azure Files 서비스에서 스냅숏에 사용 되는 저장소의 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균|

### <a name="transaction-metrics"></a>트랜잭션 메트릭

트랜잭션 메트릭은 스토리지 계정에 대한 모든 요청이 있을 때 Azure Storage에서 Azure Monitor로 내보내집니다. 스토리지 계정에 작업이 없는 경우 해당 기간에 트랜잭션 메트릭에도 데이터가 없습니다. 모든 트랜잭션 메트릭은 계정 및 Azure Files 서비스 수준에서 모두 사용할 수 있습니다. 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 트랜잭션 메트릭에 대해 지원되는 시간 조직은 PT1H 및 PT1M입니다.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>메트릭 차원

Azure Files Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원 합니다.

> [!NOTE] 
> 표준 파일 공유에는 파일 공유 차원을 사용할 수 없습니다 (프리미엄 파일 공유에만 해당). 표준 파일 공유를 사용 하는 경우 제공 되는 메트릭은 저장소 계정의 모든 파일 공유에 대 한 것입니다. 표준 파일 공유에 대 한 공유 별 메트릭을 얻으려면 저장소 계정 마다 하나의 파일 공유를 만듭니다.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>리소스 로그(미리 보기)

> [!NOTE]
> Azure Monitor의 Azure Storage 로그는 현재 공개 미리 보기이며 모든 퍼블릭 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 미리 보기에 등록하려면 [이 페이지](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)를 참조하세요.  이 미리 보기에서는 BLOB(Azure Data Lake Storage Gen2 포함), 파일, 큐, 테이블, 범용 v1의 프리미엄 스토리지 계정, 범용 v2 스토리지 계정에 로그를 사용할 수 있습니다. 클래식 스토리지 계정은 지원되지 않습니다.

다음 표에는 Azure Monitor Logs 또는 Azure Storage에서 수집되는 경우 Azure Storage 리소스 로그의 속성이 나와 있습니다. 속성은 작업, 서비스, 작업을 수행하는 데 사용된 인증 유형을 설명합니다.

### <a name="fields-that-describe-the-operation"></a>작업을 설명하는 필드


[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>작업이 인증된 방식을 설명하는 필드

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>서비스를 설명하는 필드

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>참조

- Azure Storage 모니터링에 대 한 설명은 [monitoring Azure Files](storage-files-monitoring-reference.md) 를 참조 하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../azure-monitor/insights/monitor-azure-resource.md)을 참조하세요.