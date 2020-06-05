---
title: 시각적 개체 작성
description: Azure Data Factory에서 시각적 작성을 사용하는 방법에 알아봅니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 05/15/2020
ms.openlocfilehash: 79b89c6a6a7598dd3fbdfc5030fd59fe1aab6625
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832786"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory에서 시각적 작성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory UX(사용자 인터페이스 환경)을 사용하면 코드를 작성할 필요 없이 데이터 팩터리에 대한 리소스를 시각적으로 작성하고 배포할 수 있습니다. 파이프라인 캔버스에 작업을 끌어다 놓고, 테스트 실행을 수행하고, 반복적으로 디버그하고, 파이프라인 실행을 배포 및 모니터링할 수 있습니다.

현재 Azure Data Factory UX는 Microsoft Edge 및 Google Chrome에서만 지원됩니다.

## <a name="authoring-canvas"></a>제작 캔버스

**제작 캔버스**를 열려면 연필 아이콘을 클릭합니다. 

![제작 캔버스](media/author-visually/authoring-canvas.png)

여기에서 팩터리를 구성하는 파이프라인, 활동, 데이터 세트, 연결된 서비스, 데이터 흐름, 트리거 및 통합 런타임을 작성합니다. 제작 캔버스를 사용하여 파이프라인 빌드를 시작하려면 [복사 작업을 사용하여 데이터 복사](tutorial-copy-data-portal.md)를 참조하세요. 

기본 시각적 제작 환경은 Data Factory 서비스를 직접 사용하는 것입니다. Azure Repos Git 또는 GitHub 통합은 데이터 팩터리 파이프라인에서 작업하기 위한 소스 제어 및 협업도 지원합니다. 이러한 제작 환경 간의 차이점에 대한 자세한 내용은 [Azure Data Factory의 소스 제어](source-control.md)를 참조하세요.

### <a name="properties-pane"></a>속성 창

파이프라인, 데이터 세트 및 데이터 흐름과 같은 최상위 리소스의 경우 캔버스 오른쪽에 있는 속성 창에서 상위 수준 속성을 편집할 수 있습니다. 속성 창에는 이름, 설명, 주석 및 기타 고급 속성 등의 속성이 포함됩니다. 파이프라인 활동 및 데이터 흐름 변환과 같은 하위 리소스는 캔버스 하단의 패널을 사용하여 편집됩니다. 

![제작 캔버스](media/author-visually/properties-pane.png)

속성 창은 기본적으로 리소스 생성 시에만 열립니다. 편집하려면 캔버스의 오른쪽 위 모서리에 있는 속성 창 아이콘을 클릭합니다.

## <a name="expressions-and-functions"></a>식 및 함수

정적 값 대신 함수를 사용하여 Azure Data Factory에서 많은 속성을 지정할 수 있습니다.

속성 값에 대한 식을 지정하려면 **동적 콘텐츠 추가**를 선택하거나 필드에 초점을 맞춘 상태에서 **Alt + P**를 클릭합니다.

![동적 콘텐츠 추가](media/author-visually/dynamic-content-1.png)

그러면 지원되는 시스템 변수, 작업 출력, 함수 및 사용자가 지정한 변수 또는 매개 변수에서 식을 빌드할 수 있는 **Data Factory 식 작성기**가 열립니다. 

![식 작성기](media/author-visually/dynamic-content-2.png)

식 언어에 대한 자세한 내용은 [Azure Data Factory의 식 및 함수](control-flow-expression-language-functions.md)를 참조하세요.

## <a name="provide-feedback"></a>피드백 제공

기능에 대한 의견을 남기거나 Microsoft에 도구 관련 문제를 알리려면 **피드백**을 선택하세요.

![사용자 의견](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)를 참조하세요.
