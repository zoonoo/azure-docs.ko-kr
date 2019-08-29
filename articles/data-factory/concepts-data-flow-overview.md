---
title: Azure Data Factory 데이터 흐름 매핑 개요
description: Azure Data Factory의 데이터 흐름 매핑에 대한 간략한 설명
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123987"
---
# <a name="what-are-mapping-data-flows"></a>매핑 데이터 흐름 이란?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

데이터 흐름 매핑은 Azure Data Factory의 시각적으로 디자인 된 데이터 변환입니다. 데이터 흐름을 통해 데이터 엔지니어는 코드를 작성 하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있습니다. 결과 데이터 흐름은 스케일 아웃 Azure Databricks 클러스터를 사용하여 Azure Data Factory 파이프라인 내 작업으로 실행됩니다.

Azure Data Factory 데이터 흐름의 목적은 코딩할 필요 없이 완전 시각적 환경을 제공하는 것입니다. 데이터 흐름은 스케일 아웃 데이터 처리에 대해 사용자의 고유한 실행 클러스터에서 실행됩니다. Azure Data Factory는 모든 코드 변환, 경로 최적화 및 데이터 흐름 작업의 실행을 처리합니다.

변환 논리를 대화형으로 확인할 수 있도록 디버그 모드에서 데이터 흐름을 만들면서 시작합니다. 다음으로, 데이터 흐름 작업을 파이프라인에 추가하여 파이프라인 디버그에서 데이터 흐름을 실행 및 테스트하거나, 파이프라인에서 “지금 트리거”을 사용하여 파이프라인 활동의 데이터 흐름을 테스트합니다.

그런 다음, 데이터 흐름 활동을 실행하는 Azure Data Factory 파이프라인을 사용하여 데이터 흐름 작업을 예약 및 모니터링합니다.

데이터 흐름 디자인 화면의 디버그 모드 설정/해제 스위치로 데이터 변환의 대화형 빌드가 가능합니다. 디버그 모드는 데이터 흐름 생성을 위한 데이터 준비 및 데이터 미리 보기 환경을 제공 합니다.

## <a name="begin-building-your-data-flow-logical-graph"></a>데이터 흐름 논리 그래프 작성 시작

팩터리 리소스에서 + 기호를 사용 하 여 데이터 흐름을 작성 하기 시작 하 여 새 데이터 흐름을 만듭니다.

![새 데이터 흐름](media/data-flow/newdataflow2.png "새 데이터 흐름")

먼저 원본 변환을 구성한 다음 + 기호를 사용 하 여 각 후속 단계에 데이터 변환을 추가 합니다. 논리 그래프를 작성할 때 "그래프 표시" 및 "그래프 숨기기" 단추를 사용 하 여 그래프와 구성 모드 간을 전환할 수 있습니다.

![그래프 표시](media/data-flow/showg.png "그래프 표시")

## <a name="configure-transformation-logic"></a>변환 논리 구성

![그래프 숨기기](media/data-flow/hideg.png "그래프 숨기기")

그래프를 숨기면 변환 노드 수평을 탐색할 수 있습니다.

![탐색](media/data-flow/showhide.png "탐색")

## <a name="next-steps"></a>다음 단계

* [원본 변환 시작](data-flow-source.md)
