---
title: 단면 렌더링
description: 단면 렌더링 설정 및 사용 사례 설명
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682014"
---
# <a name="single-sided-rendering"></a>단면 렌더링

대부분의 렌더러는 성능을 향상시키기 위해 [백페이스 컬링을](https://en.wikipedia.org/wiki/Back-face_culling) 사용합니다. 그러나 메쉬가 [절단 평면으로](cut-planes.md)열리면 사용자는 종종 삼각형의 뒷면을 볼 수 있습니다. 이러한 삼각형이 멀리 컬링되면 결과가 설득력있게 보이지 않습니다.

이 문제를 안정적으로 방지하는 방법은 삼각형을 *양면으로*렌더링하는 것입니다. 백페이스 컬링을 사용하지 않는 것은 성능에 영향을 미치므로 기본적으로 Azure Remote Rendering은 절단 평면과 교차하는 메시에 대해 양면 렌더링으로만 전환됩니다.

*단면 렌더링* 설정을 사용하면 이 동작을 사용자 지정할 수 있습니다.

> [!CAUTION]
> 단면 렌더링 설정은 실험피쳐입니다. 나중에 다시 제거될 수 있습니다. 응용 프로그램에서 중요한 문제를 실제로 해결하지 않는 한 기본 설정을 변경하지 마십시오.

## <a name="prerequisites"></a>사전 요구 사항

단면 렌더링 설정은 `opaqueMaterialDefaultSidedness` 옵션을 로 설정하여 [변환된](../../how-tos/conversion/configure-model-conversion.md) 메시에만 영향을 미칩니다. `SingleSided` 기본적으로 이 옵션은 `DoubleSided`로 설정됩니다.

## <a name="single-sided-rendering-setting"></a>단면 렌더링 설정

세 가지 모드가 있습니다.

**보통:** 이 모드에서는 메시들이 변환될 때 항상 렌더링됩니다. 즉, 설정으로 `opaqueMaterialDefaultSidedness` `SingleSided` 변환된 메싱은 절단 평면과 교차하는 경우에도 백페이스 컬링이 활성화되어 항상 렌더링됩니다.

**동적더블사이딩:** 이 모드에서는 절단 평면이 메시와 교차하면 자동으로 양면 렌더링으로 전환됩니다. 이 모드는 기본 모드입니다.

**항상 양쪽면:** 모든 단면 형상을 항상 양면으로 렌더링하도록 강제합니다. 이 모드는 대부분 노출되므로 단면 렌더링과 양면 렌더링 간의 성능 영향을 쉽게 비교할 수 있습니다.

단면 렌더링 설정을 다음과 같이 변경할 수 있습니다.

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

* [절단 평면](cut-planes.md)
* [모델 변환 구성](../../how-tos/conversion/configure-model-conversion.md)
