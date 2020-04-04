---
title: Azure 미디어 서비스의 할당량 및 제한
description: 이 항목에서는 Microsoft Azure 미디어 서비스의 할당량 및 제한에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/01/2020
ms.author: juliako
ms.openlocfilehash: e9855a02a57d71793f3a5d6c5ce01cbfda341b70
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632219"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Azure 미디어 서비스 할당량 및 제한

이 문서에서는 할당량이라고도 하는 가장 일반적인 Microsoft Azure 미디어 서비스 제한 중 일부를 나열합니다.

> [!NOTE]
> 고정되지 않은 리소스의 경우 지원 티켓을 열어 할당량 증가를 요청합니다. 더 높은 한도를 얻기 위해 Azure Media Services 계정을 추가로 만들지 마십시오.

## <a name="account-limits"></a>계정 한도

| 리소스 | 기본 제한 | 
| --- | --- | 
| 단일 구독의 [미디어 서비스 계정](media-services-account-concept.md) | 25(고정) |

## <a name="asset-limits"></a>자산 한도

| 리소스 | 기본 제한 | 
| --- | --- | 
| 미디어 서비스 계정당 [자산](assets-concept.md) | 1,000,000|

## <a name="storage-limits"></a>스토리지 제한

| 리소스 | 기본 제한 | 
| --- | --- | 
| 파일 크기| 일부 시나리오에서는 Media Services에서 처리를 위해 지원되는 최대 파일 크기에 제한이 있습니다. <sup>(1)</sup> |
| [스토리지 계정](storage-account-concept.md) | 100<sup>(2)</sup> (고정) |

<sup>1</sup> 현재 Azure Blob Storage에서 단일 blob에 대해 지원되는 최대 크기는 최대 5TB입니다. 서비스에서 사용되는 VM 크기를 기반으로 미디어 서비스에 추가 제한이 적용됩니다. 크기 제한은 업로드하는 파일과 미디어 서비스 처리(인코딩 또는 분석)의 결과로 생성되는 파일에도 적용됩니다. 원본 파일이 260GB보다 크면 작업이 실패할 수 있습니다. 

다음 표에서는 미디어 예약 단위 S1, S2 및 S3에 대한 제한을 보여 주며 있습니다. 원본 파일이 테이블에 정의된 제한보다 큰 경우 인코딩 작업이 실패합니다. 4K 해상도 소스를 긴 기간으로 인코딩하는 경우 필요한 성능을 얻기 위해 S3 미디어 예약 장치를 사용해야 합니다. S3 미디어 예약 단위의 260GB 제한보다 큰 4K 콘텐츠가 있는 경우 지원 티켓을 엽니다.

|미디어 예약 단위 유형|최대 입력 크기(GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> 저장소 계정은 동일한 Azure 구독에 있어야 합니다.

## <a name="jobs-encoding--analyzing-limits"></a>작업(& 분석 인코딩) 제한

| 리소스 | 기본 제한 | 
| --- | --- | 
| 미디어 서비스 계정당 [채용 정보](transforms-jobs-concept.md) | 500,000 <sup>(3)</sup> (고정)|
| 작업당 작업 입력 | 50(고정)|
| 작업당 작업 출력 | 20(고정) |
| 미디어 서비스 계정당 [변환](transforms-jobs-concept.md) | 100(고정)|
| 변환에서 출력 변환 | 20(고정) |
| 작업 입력당 파일|10(고정)|

<sup>3</sup> 이 번호에는 큐, 완료, 활성 및 취소된 작업이 포함됩니다. 삭제된 작업은 포함되지 않습니다. 

레코드의 총 수가 최고 할당량 미만인 경우에도 사용자 계정에 있는 90일이 지난 작업 레코드는 자동으로 삭제됩니다. 

## <a name="live-streaming-limits"></a>라이브 스트리밍 제한

| 리소스 | 기본 제한 | 
| --- | --- | 
| [미디어](live-events-outputs-concept.md) 서비스 계정당 라이브 이벤트 <sup>(4)</sup> |5|
| 라이브 이벤트당 라이브 출력 |3 <sup>(5)</sup> |
| 최대 라이브 출력 지속 시간 | 25시간 |

<sup>4</sup> 라이브 이벤트 제한에 대한 자세한 내용은 [라이브 이벤트 유형 비교 및 제한을](live-event-types-comparison.md)참조하십시오.

<sup>5</sup> 라이브 출력은 생성 시 시작되고 삭제되면 중지됩니다.

## <a name="packaging--delivery-limits"></a>포장 & 배송 제한

| 리소스 | 기본 제한 | 
| --- | --- | 
| 미디어 서비스 계정당 [스트리밍 끝점(중지](streaming-endpoint-concept.md) 또는 실행)|2 |
| [동적 매니페스트 필터](filters-dynamic-manifest-overview.md)|100|
| [스트리밍 정책](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| 한 번에 에셋과 연결된 고유 [스트리밍 로케이터](streaming-locators-concept.md) | 100<sup>(7)</sup> (고정) |

<sup>6</sup> 사용자 지정 [스트리밍 정책을](https://docs.microsoft.com/rest/api/media/streamingpolicies)사용하는 경우 미디어 서비스 계정에 대해 제한된 정책 집합을 디자인하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocators에 다시 사용해야 합니다. 각 스트리밍 로케이터에 대해 새 스트리밍 정책을 만들지 않아야 합니다.

<sup>7</sup> 스트리밍 로케이터는 사용자별 액세스 제어를 관리하도록 설계되지 않았습니다. 개별 사용자에게 서로 다른 액세스 권한을 부여하려면 DRM(Digital Rights Management)솔루션을 사용 합니다.

## <a name="protection-limits"></a>보호 한도

| 리소스 | 기본 제한 | 
| --- | --- | 
| 콘텐츠 [키 정책별](content-key-policy-concept.md) 옵션 |30 | 
| 계정당 미디어 서비스 키 배달 서비스의 각 DRM 유형에 대한 월별 라이선스|1,000,000|

## <a name="support-ticket"></a>지원 티켓

고정되지 않은 리소스의 경우 [지원 티켓을](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)열어 할당량을 올리도록 요청할 수 있습니다. 원하는 할당량 변경, 사용 사례 시나리오 및 필요한 영역에 대한 세부 정보를 요청에 포함시킵니다. <br/>더 높은 한도를 얻기 위해 추가 Azure Media Services 계정을 만들지 **마십시오**.

## <a name="next-steps"></a>다음 단계

[개요](media-services-overview.md)
