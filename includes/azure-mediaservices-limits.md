---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334862"
---
>[!NOTE]
>고정되지 않은 리소스의 경우 지원 티켓을 열어 할당량 증가를 요청합니다. 더 높은 한도를 얻기 위해 Azure Media Services 계정을 추가로 만들지 마십시오.

| 리소스 | 제한 | 
| --- | --- | 
| Azure 미디어 서비스 단일 구독에서 계정 | 25(고정) |
| 미디어 서비스 계정당 미디어 예약 단위 |25(S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Media Services 계정 작업당 작업 | 50,000<sup>2</sup> |
| 작업당 연결된 태스크 | 30(고정) |
| 미디어 서비스 계정당 자산 | 1,000,000|
| 태스크당 자산 | 50 |
| 작업당 자산 | 100 |
| 한번에 자산과 연결된 고유 로케이터 | 5<sup>4</sup> |
| 미디어 서비스 계정당 라이브 채널 |5|
| 채널당 중지 상태인 프로그램 |50|
| 채널당 실행 상태인 프로그램 |3|
| 미디어 서비스 계정당 중지되거나 실행 중인 스트리밍 끝점|2|
| 스트리밍 엔드포인트당 스트리밍 단위 |10 |
| Storage 계정 | 1,000<sup>5</sup>(고정) |
| 정책 | 1,000,000<sup>6</sup> |
| 파일 크기| 일부 시나리오에서는 Media Services에서 처리하기 위해 지원되는 최대 파일 크기에 제한이 있습니다. <sup>7명</sup> |

<sup>1개</sup> 예를 들어 유형을 S2에서 S1로 변경하면 최대 예약 단위 제한이 재설정됩니다.

<sup>2개</sup> 이 숫자에는 큐, 완료, 활성 및 취소된 작업이 포함됩니다. 삭제된 작업은 포함되지 않습니다. **IJob.Delete** 또는 **삭제** HTTP 요청을 사용하여 이전 작업을 삭제할 수 있습니다.

2017년 4월 1일부터 90일이 지난 계정의 모든 작업 레코드는 관련 작업 레코드와 함께 자동으로 삭제됩니다. 레코드의 총 수가 최대 할당량 미만인 경우에도 자동 삭제가 발생합니다. 작업 및 작업 정보를 보관하려면 미디어 [서비스 .NET SDK를 사용하여 자산 관리에](../articles/media-services/previous/media-services-dotnet-manage-entities.md)설명된 코드를 사용합니다.

<sup>3개</sup> 작업 엔터티 를 나열하도록 요청하면 요청당 최대 1,000개의 작업이 반환됩니다. 제출된 모든 작업을 추적하려면 [OData 시스템 쿼리 옵션에](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))설명된 대로 위쪽 또는 건너뛰기 쿼리를 사용합니다.

<sup>4개</sup> 로케이터는 사용자별 액세스 제어를 관리하도록 설계되지 않았습니다. 개별 사용자에게 다른 액세스 권한을 부여하려면 DRM(디지털 권한 관리) 솔루션을 사용합니다. 자세한 내용은 [Azure 미디어 서비스를 통해 콘텐츠 보호를](../articles/media-services/previous/media-services-content-protection-overview.md)참조하십시오.

<sup>5개</sup> 저장소 계정은 동일한 Azure 구독에 있어야 합니다.

<sup>6개</sup> 다른 미디어 서비스 정책에 대한 정책은 1,000,000개로 제한됩니다. 로케이터 정책 또는 ContentKey권한 부여 정책에 대한 예제가 있습니다. 

>[!NOTE]
> 항상 같은 요일 및 액세스 권한을 사용하는 경우 동일한 정책 ID를 사용합니다. 자세한 정보 및 예제는 [미디어 서비스 .NET SDK를 사용하여 자산 관리를](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)참조하십시오.

<sup>7명</sup> 단일 Blob에 대해 지원되는 최대 크기는 현재 Azure Blob 저장소에서 최대 5TB입니다. 서비스에서 사용되는 VM 크기를 기반으로 미디어 서비스에 추가 제한이 적용됩니다. 크기 제한은 업로드하는 파일과 미디어 서비스 처리(인코딩 또는 분석)의 결과로 생성되는 파일에도 적용됩니다. 원본 파일이 260GB보다 크면 작업이 실패할 수 있습니다. 

다음 표에서는 미디어 예약 단위 S1, S2 및 S3에 대한 제한을 보여 주며 있습니다. 원본 파일이 테이블에 정의된 제한보다 큰 경우 인코딩 작업이 실패합니다. 4K 해상도 소스를 긴 기간으로 인코딩하는 경우 필요한 성능을 얻기 위해 S3 미디어 예약 장치를 사용해야 합니다. S3 미디어 예약 단위의 260GB 제한보다 큰 4K 콘텐츠가 있는 경우 지원 티켓을 엽니다.

|미디어 예약 단위 유형    |최대 입력 크기(GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
