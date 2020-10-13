---
title: Azure 테이블 저장소 모니터링 데이터 참조 | Microsoft Docs
description: Azure 테이블 저장소에서 데이터를 모니터링 하기 위한 로그 및 메트릭 참조입니다.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: e86be87faf6bb0c58674a15882e52dcef43fe42e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711521"
---
# <a name="azure-table-storage-monitoring-data-reference"></a>Azure 테이블 저장소 모니터링 데이터 참조

Azure Storage의 모니터링 데이터를 수집하고 분석하는 방법은 [Azure Storage 모니터링](monitor-table-storage.md)을 참조하세요.

## <a name="metrics"></a>메트릭

다음 표에는 Azure Storage에 대해 수집된 플랫폼 메트릭이 나와 있습니다. 

### <a name="capacity-metrics"></a>용량 메트릭

용량 메트릭 값은 매 1시간마다 Azure Monitor에 전송됩니다. 값은 매일 새로 고쳐집니다. 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 용량 메트릭에 대해 지원되는 시간 조직은 1시간(PT1H)입니다.

Azure Storage는 Azure Monitor에서 다음과 같은 용량 메트릭을 제공합니다.

#### <a name="account-level"></a>계정 수준

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="table-storage"></a>Table Storage

다음 표에서는 [테이블 저장소 메트릭을](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountstableservices)보여 줍니다.

| 메트릭 | Description |
| ------------------- | ----------------- |
| TableCapacity | 스토리지 계정에 사용한 Table Storage 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| TableCount   | 스토리지 계정의 테이블 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| TableEntityCount | 스토리지 계정의 테이블 엔터티 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

### <a name="transaction-metrics"></a>트랜잭션 메트릭

트랜잭션 메트릭은 스토리지 계정에 대한 모든 요청이 있을 때 Azure Storage에서 Azure Monitor로 내보내집니다. 스토리지 계정에 작업이 없는 경우 해당 기간에 트랜잭션 메트릭에도 데이터가 없습니다. 모든 트랜잭션 메트릭은 계정 및 테이블 저장소 서비스 수준에서 모두 사용할 수 있습니다. 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 트랜잭션 메트릭에 대해 지원되는 시간 조직은 PT1H 및 PT1M입니다.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>메트릭 차원

Azure Storage는 Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원합니다.

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

- Azure Storage 모니터링에 대 한 설명은 [Azure 테이블 저장소 모니터링](monitor-table-storage.md) 을 참조 하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../azure-monitor/insights/monitor-azure-resource.md)을 참조하세요.