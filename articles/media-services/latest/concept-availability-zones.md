---
title: 가용성 영역
description: Media Services는 오류 격리를 제공하는 가용성 영역을 지원합니다.
services: media-services
author: johndeu
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: johndeu
ms.openlocfilehash: d50af7acadb17fe060ede90ad93896e465d3bad6
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746954"
---
# <a name="availability-zones"></a>가용성 영역

Azure Media Services는 [가용성 영역](../../availability-zones/az-overview.md)을 사용하여 동일한 Azure 지역 내에서 오류 격리 위치를 제공합니다. Media Services는 기본적으로 [사용 가능한 위치](../../availability-zones/az-region.md#azure-regions-with-availability-zones)에서 영역 중복이며 이 기능을 사용하기 위해 계정에 대한 추가 구성이 필요하지 않습니다.  Media Services는 연결된 스토리지 계정에 미디어 데이터를 저장합니다.  이러한 스토리지 계정은 ZRS(영역 중복 스토리지) 또는 GZRS(지역 중복 스토리지)로 만들어 Media Services 계정과 동일한 수준의 중복성을 제공해야 합니다. 연결된 스토리지 계정에서 복제를 구성하는 방법에 대한 자세한 내용은 [스토리지 계정 복제 방법 변경](../../storage/common/redundancy-migration.md) 문서를 참조하세요.

## <a name="how-media-services-components-handle-an-availability-zone-fault"></a>Media Services 구성 요소가 가용성 영역 오류를 처리하는 방법

| 구성 요소             | 가용성 영역 오류에 대한 동작 |
|-----------            |----------------------|
| 컨트롤 플레인(Azure 리소스 관리) | 계속 정상적으로 작동합니다. |
| 키 배달            | 계속 정상적으로 작동합니다. |
| 작업                    | 작업은 다른 가용성 영역에서 다시 예약됩니다. 진행 중인 처리 작업이 가용성 영역에서 다시 시작되도록 예약됨에 따라 처리 시간이 지연됩니다. |
| 라이브 이벤트             | 라이브 이벤트에 대한 스트리밍 및 수집은 계속 정상적으로 작동합니다. 라이브 이벤트에서 "재설정"을 호출하는 것은 현재 가용성 영역 오류 동안 지원되지 않습니다. "재설정" 작업을 대체하기 위해 먼저 라이브 이벤트를 중지하고 다시 시작하는 것이 좋습니다. 영역 작동 중지 이벤트 중에 라이브 이벤트가 "실행 중" 상태로 전환된 경우 고객은 라이브 이벤트가 "시작 중" 상태로 멈춘 것을 볼 수 있습니다. 이 경우 새 라이브 이벤트를 시작하고 영역이 복구된 후 "시작 중" 라이브 이벤트를 정리하는 것이 좋습니다.  |
| 스트리밍 엔드포인트     | 계속 정상적으로 작동합니다. |


## <a name="high-availability-streaming-and-encoding-for-vod"></a>VOD를 위한 고가용성 스트리밍 및 인코딩

가용성 영역은 단일 지역에서 오류 격리를 증가시킵니다. 주문형 스트리밍 및 인코딩에 대한 고가용성을 제공하기 위해 다른 Azure 서비스를 사용하여 중복성, 상태 모니터링, 부하 분산, 데이터 백업 및 복구와 같은 문제를 다루는 아키텍처를 만들 수 있습니다. 이러한 아키텍처 중 하나는 [Media Services VOD를 통한 고가용성](architecture-high-availability-encoding-concept.md) 문서에서 제공됩니다.
문서 및 샘플 코드는 개별 지역 Media Services 계정을 사용하여 VOD 애플리케이션의 고가용성 아키텍처를 만드는 방법에 대한 솔루션을 제공합니다.

## <a name="media-services-support-for-availability-zones-by-region"></a>지역별 가용성 영역에 대한 Media Services 지원

가용성 영역은 현재 특정 Azure 지역에서만 지원됩니다. 가용성 영역 지역 지원에 대한 자세한 내용은 [가용성 영역이 있는 Azure 지역](../../availability-zones/az-region.md#azure-regions-with-availability-zones)을 참조하세요.

## <a name="further-reading"></a>추가 정보

가용성 영역에 대한 자세한 내용은 [Azure의 지역 및 가용성 영역](../../availability-zones/az-overview.md)을 참조하세요.

고가용성 인코딩 및 스트리밍에 대한 자세한 내용은 [Media Services VOD를 통한 고가용성](architecture-high-availability-encoding-concept.md)을 참조하세요.

가용성 영역을 지원하도록 스토리지 계정 복제를 올바르게 구성하는 방법을 알아보려면 [스토리지 계정 복제 방법 변경](../../storage/common/redundancy-migration.md)을 참조하세요.
