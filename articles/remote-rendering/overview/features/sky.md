---
title: 하늘 반사
description: 하늘 반사에 대한 환경 맵을 설정하는 방법에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680610"
---
# <a name="sky-reflections"></a>하늘 반사

Azure 원격 렌더링에서 하늘 텍스처는 오브젝트를 사실적으로 라이트화하는 데 사용됩니다. 증강 현실 응용 프로그램의 경우 이 텍스처는 실제 환경과 유사해야 개체가 설득력 있게 보이게 됩니다. 이 문서에서는 하늘 텍스처를 변경하는 방법에 대해 설명합니다.

> [!NOTE]
> 하늘 텍스처를 *환경 맵이라고도*합니다. 이러한 용어는 상호 교환적으로 사용됩니다.

## <a name="object-lighting"></a>오브젝트 조명

Azure 원격 렌더링은 사실적인 조명 계산을 위해 물리적 기반 렌더링(PBR)을 사용합니다. *physically based rendering* 장면에 [광원을](lights.md) 추가할 수 있지만 좋은 하늘 텍스처를 사용하면 가장 큰 영향을 미칩니다.

아래 이미지는 하늘 텍스처를 통해서만 다른 표면에 조명을 비추는 결과를 보여 준다.

| 거칠기  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 비금속  | ![유전체0](media/dielectric-0.png)   | ![그린포인트파크](media/dielectric-0.25.png)  | ![그린포인트파크](media/dielectric-0.5.png)  | ![그린포인트파크](media/dielectric-0.75.png)  | ![그린포인트파크](media/dielectric-1.png)  |
| Metal      | ![그린포인트파크](media/metallic-0.png)  | ![그린포인트파크](media/metallic-0.25.png)    | ![그린포인트파크](media/metallic-0.5.png)    | ![그린포인트파크](media/metallic-0.75.png)    | ![그린포인트파크](media/metallic-1.png)    |

조명 모델에 대한 자세한 내용은 [재질](../../concepts/materials.md) 장을 참조하십시오.

> [!IMPORTANT]
> Azure 원격 렌더링은 조명 모델에만 하늘 텍스처를 사용합니다. 증강 현실 응용 프로그램은 이미 적절한 배경을 가지고 있기 때문에 그것은 배경으로 하늘을 렌더링하지 않습니다 - 현실 세계.

## <a name="changing-the-sky-texture"></a>하늘 텍스처 변경

환경 맵을 변경하려면 [텍스처를 로드하고](../../concepts/textures.md) 세션의 `SkyReflectionSettings`다음 을 변경하기만 하면 됩니다.

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

기본 제공 `LoadTextureFromSASAsync` 텍스처가 로드되므로 변형이 위의 경우에 사용됩니다. [연결된 Blob 저장소에서 로드하는](../../how-tos/create-an-account.md#link-storage-accounts)경우 `LoadTextureAsync` 변형을 사용합니다.

## <a name="sky-texture-types"></a>하늘 텍스처 유형

*[큐브맵과](https://en.wikipedia.org/wiki/Cube_mapping)* *2D 텍스처를* 환경 맵으로 사용할 수 있습니다.

모든 텍스처는 [지원되는 텍스처 형식이어야](../../concepts/textures.md#supported-texture-formats)합니다. 하늘 텍스처에 밉맵을 제공할 필요가 없습니다.

### <a name="cube-environment-maps"></a>큐브 환경 맵

참조하기 위해 래핑되지 않은 큐브맵은 다음과 같습니다.

![래핑되지 않은 큐브맵](media/Cubemap-example.png)

큐브맵 텍스처를 로드하는 데 사용합니다. `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` `TextureType.CubeMap`

### <a name="sphere-environment-maps"></a>구 환경 맵

2D 텍스처를 환경 맵으로 사용하는 경우 이미지는 [구형 좌표 공간에](https://en.wikipedia.org/wiki/Spherical_coordinate_system)있어야 합니다.

![구형 좌표의 하늘 이미지](media/spheremap-example.png)

구형 `TextureType.Texture2D` 환경 맵을 로드하는 데 사용합니다. `AzureSession.Actions.LoadTextureAsync`

## <a name="built-in-environment-maps"></a>기본 제공 환경 맵

Azure 원격 렌더링은 항상 사용할 수 있는 몇 가지 기본 제공 환경 맵을 제공합니다. 모든 기본 제공 환경 맵은 큐브맵입니다.

|ID                         | 설명                                              | 그림                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | 스트라이프 조명의 다양한, 밝은 실내 베이스 조명    | ![자동 상점](media/autoshop.png)
|builtin://BoilerRoom               | 밝은 실내 조명 설정, 여러 개의 창 조명      | ![보일러룸](media/boiler-room.png)
|builtin://ColorfulStudio           | 중간 정도의 조명 실내 설정에서 다양한 색상의 조명  | ![컬러풀 스튜디오](media/colorful-studio.png)
|builtin://Hangar                   | 적당히 밝은 주변 홀 조명                     | ![스몰한가르](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 밝은 어두운 대비로 어두운 실내 설정              | ![인더스트리파이프앤밸브](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 주간 주변 방 조명, 밝은 창 영역 조명     | ![레봄보](media/lebombo.png)
|builtin://SataraNight              | 어두운 밤 하늘과 많은 주변 조명과 지상   | ![사타라나이트](media/satara-night.png)
|builtin://SunnyVondelpark          | 밝은 햇빛과 그림자 대비                      | ![서니본델파크](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 적당한 지면 조명으로 맑은 하늘광            | ![시퍼폰테인](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 적당히 변화하는 태양과 그늘                         | ![눈물의스틸브리지](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 황혼에 접근하는 저녁 일몰 빛                    | ![베니스 선셋](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 밝은, 무성한 녹색, 백색 광원, 희미한 지면 | ![휘플크릭 리저널파크](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 밝은 주변 접지 조명으로 낮시간                 | ![윈터리버](media/winter-river.png)
|builtin://DefaultSky               | 눈물과 동일Of스틸브리지                               | ![디폴드스카이](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>다음 단계

* [조명](../../overview/features/lights.md)
* [원자재](../../concepts/materials.md)
* [질감](../../concepts/textures.md)
* [텍스콩프 커맨드 라인 도구](../../resources/tools/tex-conv.md)
