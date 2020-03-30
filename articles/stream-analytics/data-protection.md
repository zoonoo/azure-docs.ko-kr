---
title: Azure 스트림 분석의 데이터 보호
description: 이 문서에서는 Azure Stream Analytics 작업에서 사용하는 개인 데이터를 암호화하는 방법을 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299399"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure 스트림 분석의 데이터 보호 

Azure Stream Analytics는 실시간 분석 파이프라인을 빌드할 수 있는 서비스로서의 완전히 관리되는 플랫폼입니다. 클러스터 프로비저닝, 사용량을 수용하기 위한 노드 크기 조정 및 내부 검사점 관리와 같은 모든 과중한 문제는 뒤에서 관리됩니다.

## <a name="encrypt-your-data"></a>데이터 암호화

Stream Analytics는 자동으로 인프라 전반에 걸쳐 동급 최강의 암호화 표준을 사용하여 데이터를 암호화하고 보호합니다. Stream Analytics를 신뢰하기만 하면 모든 데이터를 안전하게 저장하므로 인프라 관리에 대해 걱정할 필요가 없습니다.

CMK(고객 관리 키)를 사용하여 데이터를 암호화하려는 경우 자체 저장소 계정(범용 V1 또는 V2)을 사용하여 Stream Analytics 런타임에 필요한 개인 데이터 자산을 저장할 수 있습니다. 저장소 계정은 필요에 따라 암호화할 수 있습니다. 개인 데이터 자산은 Stream Analytics 인프라에 의해 영구적으로 저장되지 않습니다. 

이 설정은 Stream Analytics 작업 생성 시 구성되어야 하며 작업의 수명 주기 동안 수정할 수 없습니다. Stream Analytics에서 사용 중인 저장소를 수정하거나 삭제하는 것은 권장되지 않습니다. 저장소 계정을 삭제하면 모든 개인 데이터 자산을 영구적으로 삭제하여 작업이 실패하게 됩니다. 

Stream Analytics 포털을 사용하면 저장소 계정에 키를 업데이트하거나 회전할 수 없습니다. 나머지 API를 사용하여 키를 업데이트할 수 있습니다.


## <a name="configure-storage-account-for-private-data"></a>개인 데이터에 대한 저장소 계정 구성 

다음 단계를 사용하여 개인 데이터 자산에 대한 저장소 계정을 구성합니다. 이 구성은 저장소 계정이 아닌 Stream Analytics 작업에서 이루어집니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다. 

1. 결과 목록에서 **분석** > **스트림 분석 작업을** 선택합니다. 

1. 이름, 지역 및 배율과 같은 필요한 세부 정보가 있는 검색 분석 작업 페이지를 작성합니다. 

1. 내 저장소 계정에서 *이 작업에 필요한 모든 개인 데이터 자산 보안이라는 확인란을*선택합니다.

1. 구독에서 저장소 계정을 선택합니다. 이 설정은 작업의 수명 주기 동안 수정할 수 없습니다. 

   ![개인 데이터 저장소 계정 설정](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>저장된 개인 데이터 자산

Stream Analytics에서 유지해야 하는 모든 개인 데이터는 저장소 계정에 저장됩니다. 개인 데이터 자산의 예는 다음과 같습니다. 

* 작성한 쿼리 및 관련 구성  

* 사용자 정의 함수 

* 스트림 분석 런타임에 필요한 검사점

* 참조 데이터의 스냅샷 

Stream Analytics 작업에서 사용되는 리소스의 연결 세부 정보도 저장됩니다. 저장소 계정을 암호화하여 모든 데이터를 보호합니다. 

규제 대상 산업 또는 환경에서 규정 준수 의무를 충족하는 데 도움이 되는 Microsoft 규정 준수 제품에 대해 자세히 확인할 수 [있습니다.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="next-steps"></a>다음 단계

* [Azure 저장소 계정 만들기](../storage/common/storage-account-create.md)
* [Azure Stream Analytics의 입력 이해](stream-analytics-add-inputs.md)
* [Azure Stream Analytics 작업의 검사점 및 재생 개념](stream-analytics-concepts-checkpoint-replay.md)
* [Stream Analytics에서 조회에 대한 참조 데이터 사용](stream-analytics-use-reference-data.md)
