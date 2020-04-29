---
title: 단일 면 렌더링
description: 단일 면 렌더링 설정 및 사용 사례에 대해 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682014"
---
# <a name="single-sided-rendering"></a>단일 면 렌더링

대부분의 렌더러는 [백 페이스 고르기](https://en.wikipedia.org/wiki/Back-face_culling) 를 사용 하 여 성능을 향상 시킵니다. 그러나 메시를 사용 하 여 메시 [를 잘라 면](cut-planes.md)사용자가 삼각형의 뒷면을 볼 수 있습니다. 이러한 삼각형이 추출 않으면 결과가 잘못 된 것으로 보입니다.

이 문제를 안정적으로 방지 하는 방법은 삼각형 양면을 렌더링 하는 *것입니다.* 고르기를 사용 하지 않는 것과 같이 성능에 영향을 미칠 수 있으므로 기본적으로 Azure 원격 렌더링은 잘라내기 평면과 교차 하는 메시에 대 한 양면 렌더링 으로만 전환 합니다.

*단일 면 렌더링* 설정을 사용 하 여이 동작을 사용자 지정할 수 있습니다.

> [!CAUTION]
> 단일 면 렌더링 설정은 실험적 기능입니다. 이후에 다시 제거 될 수도 있습니다. 응용 프로그램에서 중요 한 문제를 해결 하는 경우를 제외 하 고는 기본 설정을 변경 하지 마세요.

## <a name="prerequisites"></a>전제 조건

단일 면 렌더링 설정은로 `opaqueMaterialDefaultSidedness` `SingleSided`설정 된 옵션을 사용 하 여 [변환](../../how-tos/conversion/configure-model-conversion.md) 된 메시에만 적용 됩니다. 기본적으로이 옵션은로 `DoubleSided`설정 되어 있습니다.

## <a name="single-sided-rendering-setting"></a>단일 면 렌더링 설정

3 가지 모드가 있습니다.

**보통:** 이 모드에서 메시는 변환 될 때 항상 렌더링 됩니다. 즉,로 설정 된 `opaqueMaterialDefaultSidedness` 메시는 `SingleSided` 잘린 평면을 교차 하는 경우에도 항상 뒷면 얼굴 고르기 사용 하도록 렌더링 됩니다.

**DynamicDoubleSiding:** 이 모드에서 자르기 평면은 메시와 교차 하면 자동으로 양면 렌더링으로 전환 됩니다. 이 모드는 기본 모드입니다.

**AlwaysDoubleSided:** 모든 단일 면 기 하 도형을 항상 양면으로 렌더링 합니다. 이 모드는 주로 단일 면과 양면 렌더링의 성능 영향을 쉽게 비교할 수 있도록 노출 됩니다.

단일 면 렌더링 설정을 변경 하는 작업은 다음과 같이 수행할 수 있습니다.

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>다음 단계

* [평면 잘라내기](cut-planes.md)
* [모델 변환 구성](../../how-tos/conversion/configure-model-conversion.md)
