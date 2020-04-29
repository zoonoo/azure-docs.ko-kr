---
title: 하늘 반사
description: 하늘 반사를 위한 환경 지도를 설정 하는 방법을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680610"
---
# <a name="sky-reflections"></a>하늘 반사

Azure 원격 렌더링에서 하늘 텍스처는 사실적 개체에 사용 됩니다. 확대 현실 응용 프로그램의 경우이 질감이 실제 환경에서 개체를 유도 하는 것 처럼 보이게 합니다. 이 문서에서는 하늘 텍스처를 변경 하는 방법을 설명 합니다.

> [!NOTE]
> 하늘 질감을 *환경 맵*이 라고도 합니다. 이러한 용어는 서로 바꿔 사용 됩니다.

## <a name="object-lighting"></a>개체 조명

Azure 원격 렌더링은 실제 조명 계산을 위해 .Pbr ( *물리적 기반 렌더링* )를 사용 합니다. 장면에 [광원](lights.md) 을 추가할 수 있지만 좋은 하늘 텍스처를 사용 하면 효과가 가장 큽니다.

아래 이미지는 하늘 질감이 있는 다른 표면의 조명 결과를 보여 줍니다.

| 거친  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 미 설치  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

조명 모델에 대 한 자세한 내용은 [자료](../../concepts/materials.md) 챕터를 참조 하세요.

> [!IMPORTANT]
> Azure 원격 렌더링에서는 조명 모델에만 하늘 질감이 사용 됩니다. 확대 된 현실 응용 프로그램은 이미 적절 한 배경을가지고 있으므로 지구를 배경으로 렌더링 하지 않습니다.

## <a name="changing-the-sky-texture"></a>하늘 질감 변경

환경 맵을 변경 하려면 [텍스처를 로드](../../concepts/textures.md) 하 고 세션을 변경 하기만 하면 됩니다 `SkyReflectionSettings`.

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

기본 제공 질감이 `LoadTextureFromSASAsync` 로드 되기 때문에 변형은 위에서 사용 됩니다. [연결된 Blob 스토리지](../../how-tos/create-an-account.md#link-storage-accounts)에서 로드하는 경우에는 `LoadTextureAsync` 변형을 사용합니다.

## <a name="sky-texture-types"></a>하늘 텍스처 유형

*[Cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* 및 *2d 텍스처* 를 모두 환경 맵으로 사용할 수 있습니다.

모든 질감은 [지원 되는 질감 형식](../../concepts/textures.md#supported-texture-formats)이어야 합니다. 하늘 질감에 대 한 mip 맵을를 제공할 필요가 없습니다.

### <a name="cube-environment-maps"></a>큐브 환경 맵

참조를 위해 다음은 래핑 해제 된 큐브 맵입니다.

![래핑 해제 된 큐브 맵](media/Cubemap-example.png)

을 `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` 사용 `TextureType.CubeMap` 하 여 큐브 맵 질감을 로드 합니다.

### <a name="sphere-environment-maps"></a>구 환경 맵

2D 텍스처를 환경 맵으로 사용 하는 경우 이미지는 [구면 좌표 공간](https://en.wikipedia.org/wiki/Spherical_coordinate_system)에 있어야 합니다.

![구면 좌표의 하늘 이미지](media/spheremap-example.png)

를 `AzureSession.Actions.LoadTextureAsync` 사용 `TextureType.Texture2D` 하 여 구형 환경 맵을 로드 합니다.

## <a name="built-in-environment-maps"></a>기본 제공 환경 맵

Azure 원격 렌더링은 항상 사용할 수 있는 몇 가지 기본 제공 환경 맵을 제공 합니다. 모든 기본 제공 환경 맵은 cubemaps입니다.

|ID                         | Description                                              | 그림                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | 다양 한 스트라이프 조명, 브라이트 실내 기본 조명    | ![자동 쇼핑](media/autoshop.png)
|builtin://BoilerRoom               | 밝은 실내 조명 설정, 여러 창 조명      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | 중간 조명 실내 설정에서 색이 지정 된 Varyingly  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | 중간 밝은 주변 홀 빛                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 옅은 검정 대비를 사용 하는 Dim 실내 설정              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 주간 실내 실내 조명, 밝은 창 영역 광원     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | 주변 조명이 많은 진한 야간 하늘 및 접지   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | 브라이트 햇빛 및 그림자 대비                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 중간 접지 조명이 있는 하늘 조명 지우기            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 보통의 다양 한 sun 및 음영                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 저녁 일몰 뿌연 근접                    | ![VeniceSunset 해제](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 밝은, lush-녹색, 흰색 밝은 톤, 흐린 접지 | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 밝은 주변광 조명이 있는 주간                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | TearsOfSteelBridge와 동일                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>다음 단계

* [조명](../../overview/features/lights.md)
* [재질](../../concepts/materials.md)
* [질감](../../concepts/textures.md)
* [TexConv 명령줄 도구](../../resources/tools/tex-conv.md)
