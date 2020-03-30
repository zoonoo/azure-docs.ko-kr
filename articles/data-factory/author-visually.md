---
title: 비주얼 작성
description: Azure Data Factory에서 시각적 작성을 사용하는 방법에 알아봅니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: 09d4055ba98da2dd87efc9421402f2827a87ba16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440926"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory에서 시각적 작성

Azure Data Factory UX(사용자 인터페이스 환경)을 사용하면 코드를 작성할 필요 없이 데이터 팩터리에 대한 리소스를 시각적으로 작성하고 배포할 수 있습니다. 파이프라인 캔버스에 작업을 끌어다 놓고, 테스트 실행을 수행하고, 반복적으로 디버그하고, 파이프라인 실행을 배포 및 모니터링할 수 있습니다.

현재 Azure 데이터 팩토리 UX는 마이크로소프트 에지와 구글 크롬에서만 지원됩니다.

## <a name="authoring-canvas"></a>캔버스 작성

**작성 캔버스를**열려면 연필 아이콘을 클릭합니다. 

![캔버스 작성](media/author-visually/authoring-canvas.png)

여기서는 팩터리로 구성된 파이프라인, 활동, 데이터 집합, 연결된 서비스, 데이터 흐름, 트리거 및 통합 런타임을 작성합니다. 작성 캔버스를 사용하여 파이프라인 빌드를 시작하려면 [활동 복사를 사용하여 데이터 복사를](tutorial-copy-data-portal.md)참조하세요. 

기본 시각적 작성 환경은 데이터 팩터리 서비스와 직접 작업하는 것입니다. Azure Repos Git 또는 GitHub 통합은 데이터 팩터리 파이프라인에 대한 작업을 위해 소스 제어 및 공동 작업을 허용하도록 지원됩니다. 이러한 작성 환경 간의 차이점에 대한 자세한 내용은 [Azure 데이터 팩터리의 소스 제어를](source-control.md)참조하십시오.

## <a name="expressions-and-functions"></a>식 및 함수

정적 값 대신 표현식 및 함수를 사용하여 Azure Data Factory에서 많은 속성을 지정할 수 있습니다.

속성 값에 대한 식을 지정하려면 **동적 콘텐츠 추가를** 선택하거나 필드에 초점을 맞추면서 **Alt + P를** 클릭합니다.

![동적 콘텐츠 추가](media/author-visually/dynamic-content-1.png)

이렇게 하면 지원되는 시스템 변수, 활동 출력, 함수 및 사용자 지정 변수 또는 매개 변수에서 식을 빌드할 수 있는 **데이터 팩터리 익스프레션 빌더가** 열립니다. 

![식 작성기](media/author-visually/dynamic-content-2.png)

식 언어에 대한 자세한 내용은 [Azure 데이터 팩터리의 표현식 및 함수를](control-flow-expression-language-functions.md)참조하십시오.

## <a name="provide-feedback"></a>피드백 제공

기능에 대한 의견을 남기거나 Microsoft에 도구 관련 문제를 알리려면 **피드백**을 선택하세요.

![사용자 의견](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)를 참조하세요.
