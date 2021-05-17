---
author: baanders
description: Azure Digital Twins용 파일 포함 - Azure Digital Twins 탐색기로 시각화
ms.service: digital-twins
ms.topic: include
ms.date: 7/10/2020
ms.author: baanders
ms.openlocfilehash: 37bccbcd92f627fd45a7f786725afe5b2e475f3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94533882"
---
## <a name="visualization"></a>시각화

Azure Digital Twins 인스턴스와 상호 작용하는 기본 방법은 [API 및 SDK](../articles/digital-twins/how-to-use-apis-sdks.md)를 사용하는 것이지만, 인스턴스에서 만드는 쌍 및 그래프의 시각화를 확인하는 것이 유용할 수 있습니다.

[**ADT(Azure Digital Twins) 탐색기**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)는 Azure Digital Twins를 시각화하고 사용하기 위한 샘플 애플리케이션입니다. 샘플과 함께 제공된 지침에 따라 애플리케이션 코드를 다운로드하여 실행합니다. 

이 샘플을 사용하면 다른 생성 및 관리 작업 중에서 디지털 트윈과 디지털 트윈이 만드는 그래프의 시각적 표현을 볼 수 있습니다.

샘플 시각화의 모양은 다음과 같습니다.

:::image type="content" source="../articles/digital-twins/media/includes/azure-digital-twins-explorer.png" alt-text="디지털 쌍을 나타내는 노드의 그래프를 보여주는 Azure Digital Twins 탐색기 샘플 애플리케이션의 스크린샷" lightbox="../articles/digital-twins/media/includes/azure-digital-twins-explorer.png":::

샘플을 사용하여 다음 작업을 수행할 수도 있습니다.
* 모델 업로드 및 탐색
* 트윈 그래프 업로드 및 편집
* 여러 레이아웃 기법을 사용하여 트윈 그래프 시각화
* 트윈 속성 편집
* 트윈 그래프에 대해 쿼리 실행