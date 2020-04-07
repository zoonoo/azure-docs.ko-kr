---
title: 모델 형식에 대한 재질 매핑
description: 모델 소스 형식에서 PBR 재질로의 기본 변환에 대해 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680389"
---
# <a name="material-mapping-for-model-formats"></a>모델 형식에 대한 재질 매핑

원본 자산을 [모델로 변환하면](../how-tos/conversion/model-conversion.md)변환기는 각 [메시에](../concepts/meshes.md)대한 [재질을](../concepts/materials.md) 만듭니다. 재질을 작성하는 방법을 [재정의할](../how-tos/conversion/override-materials.md)수 있습니다. 그러나 기본적으로 변환은 [PBR 재질을](../overview/features/pbr-materials.md)만듭니다. FBX와 같은 모든 소스 파일 형식은 자체 규칙을 사용하여 재질을 정의하므로 이러한 규칙을 Azure 원격 렌더링의 PBR 재질 매개 변수에 매핑해야 합니다. 

이 문서에서는 재질을 원본 자산에서 런타임 재질로 변환하는 데 사용되는 정확한 매핑을 나열합니다.

## <a name="gltf"></a>glTF

glTF 2.0 사양의 거의 모든 것은 *EmissiveFactor* 및 *Emissive Texture를*제외한 Azure 원격 렌더링에서 지원됩니다.

다음 표에서 이러한 매핑을 보여줍니다.

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   베이스컬러 팩터   |   알베도컬러              |
|   베이스컬러 텍스처  |   알베도맵                |
|   메탈릭 팩터    |   금속성                |
|   메탈릭텍스처   |   메탈니스맵             |
|   거칠기 요소   |   거칠기                |
|   거칠기 텍스처  |   거칠기맵             |
|   오클루전 팩터   |   폐색                |
|   오클루전 텍스처  |   오클루전맵             |
|   일반 텍스처     |   노멀맵                |
|   알파 컷오프       |   알파클립임계값       |
|   알파모드.불투명  |   alphaClipEnabled = 거짓, 투명 = 거짓 |
|   알파모드.마스크    |   alphaClipEnabled = true, 투명 = 거짓  |
|   알파모드.블렌드   |   는투명 = true     |
|   이중 면       |   는양쪽으로            |
|   방사 인자    |   -                        |
|   방사 질감   |   -                        |

glTF의 각 텍스처에는 `texCoord` Azure 원격 렌더링 재질에서도 지원되는 값이 있을 수 있습니다.

### <a name="embedded-textures"></a>임베디드 텍스처

.bin 또는 * \*.glb* 파일에 포함된 텍스처가 지원됩니다. * \**

### <a name="supported-gltf-extension"></a>지원되는 glTF 확장

또한 기본 기능 집합에 Azure 원격 렌더링은 다음과 같은 glTF 확장을 지원합니다.

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): [색상 재료에](../overview/features/color-materials.md)해당합니다. *방사 재료의* 경우 이 확장을 사용하는 것이 좋습니다.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): 금속 성 거칠기 텍스처 대신 확산 - 스페큘러 광택 질감을 제공 할 수 있습니다. Azure 원격 렌더링 구현은 확장의 변환 수식을 직접 따릅니다.

## <a name="fbx"></a>Fbx

FBX 형식은 폐쇄 소스이며 FBX 재료는 일반적으로 PBR 재료와 호환되지 않습니다. FBX는 많은 고유한 매개 변수 및 속성이 있는 표면에 대한 복잡한 설명을 사용하며 Azure 원격 렌더링 파이프라인에서 모든 서피스를 **사용하지는 않습니다.**

> [!IMPORTANT]
> Azure 원격 렌더링 모델 변환 파이프라인은 **FBX 2011 이상만**지원합니다.

FBX 형식은 재료에 대한 보수적 인 접근 방식을 정의하며 공식 FBX 사양에는 두 가지 유형만 있습니다.

* *램버트* - 일반적으로 이미 꽤 많은 시간 동안 사용되지 않지만, 여전히 변환 시간에 퐁으로 변환하여 지원됩니다.
* *Phong* - 거의 모든 재료와 대부분의 콘텐츠 도구가이 유형을 사용합니다.

퐁 모델은 더 정확하며 FBX 재질의 *유일한* 모델로 사용됩니다. 아래에서 *FBX 재질이라고*합니다.

> Maya는 재료의 PBR 및 가오리 유형에 대한 사용자 지정 속성을 정의하여 FBX에 대한 두 가지 사용자 지정 확장을 사용합니다. 이러한 세부 정보는 FBX 사양에 포함되지 않으므로 현재 Azure 원격 렌더링에서 지원되지 않습니다.

FBX 재질은 분산-Specular-SpecularLevel 개념을 사용하므로 분산 텍스처에서 알베도 맵으로 변환하려면 다른 매개변수를 계산하여 분산에서 빼야 합니다.

> FBX의 모든 색상과 텍스처는 sRGB 공간(감마 공간이라고도 함)에 있지만 Azure 원격 렌더링은 시각화 중에 선형 공간으로 작동하며 프레임 끝에는 모든 것을 sRGB 공간으로 다시 변환합니다. Azure 원격 렌더링 자산 파이프라인은 모든 것을 선형 공간으로 변환하여 준비된 데이터를 렌더러에 보냅니다.

이 표에서는 텍스처가 FBX 머티리얼에서 Azure 원격 렌더링 재질로 매핑되는 방법을 보여 주며, 텍스처가 매핑되는 방식을 보여 주시고 있습니다. 그 중 일부는 직접 사용되지 않지만 수식에 참여하는 다른 텍스처와 함께 (예 : 확산 텍스처)와 함께 사용됩니다.

| Fbx | Azure Remote Rendering |
|:-----|:----|
| 앰비언트 컬러 | 오클루전 지도   |
| 디퓨즈컬러 | *알베도, 금속에 사용* |
| 투명 색상 | *알베도의 알파 채널에 사용* |
| 투명도 | *알베도의 알파 채널에 사용* |
| 불투명도 | *알베도의 알파 채널에 사용* |
| 스페큘러 컬러 | *알베도, 금속, 거칠기에 사용* |
| 스페큘러 팩터| *알베도, 금속, 거칠기에 사용* |
| 샤이니시엑스포니트 | *알베도, 금속, 거칠기에 사용* |
| 노멀맵 | 노멀맵 |
| 범프 | *노멀맵으로 변환* |
| 방사색 | - |
| 방사 인자 | - |
| 반사색상 | - |
| 변위색상 | - |

위의 매핑은 재료 변환에서 가장 복잡한 부분입니다. 아래에서 이러한 가정에 대해 설명합니다.

아래에 사용되는 몇 가지 정의:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. 빨간색 0.2125 `Specular`+ . 녹색 으로 0.7154 + `Specular`. 파란색으로 0.0721
* `DiffuseBrightness`= 0.299 `Diffuse`* . 레드<sup>2</sup> + 0.587 * `Diffuse`. 녹색<sup>2</sup> + 0.114 * `Diffuse`. 블루<sup>2</sup>
* `SpecularBrightness`= 0.299 `Specular`* . 레드<sup>2</sup> + 0.587 * `Specular`. 녹색<sup>2</sup> + 0.114 * `Specular`. 블루<sup>2</sup>
* `SpecularStrength`= 최대(.`Specular` 빨간색, `Specular`. 녹색, `Specular`. 파란색)

Specular강도 수식은 여기에서 얻을 수 [있습니다.](https://en.wikipedia.org/wiki/Luma_(video))
밝기 수식은 이 [사양에](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)설명되어 있습니다.

### <a name="roughness"></a>거칠기

`Roughness`[이 수식에서](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf) `Specular` 계산됩니다. `ShininessExponent` 수식은 퐁 스페큘러 지수의 거칠기 근사치입니다.

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>금속성

`Metalness`glTF `Diffuse` 사양에서 이 수식을 `Specular` 사용하여 [계산됩니다.](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)

Ax<sup>2</sup> + Bx + C = 0 : 여기서 아이디어는 우리가 방정식을 해결한다는 것입니다.
기본적으로 유전체 표면은 반사 방식으로 빛의 약 4%를 반사하고 나머지는 확산됩니다. 금속 표면은 확산 방식으로 빛을 반사하지 않지만 모든 것은 반사방식으로 반사됩니다.
이 공식은 광택 플라스틱과 광택 금속 표면을 구별 할 수있는 방법이 없기 때문에 몇 가지 단점이 있습니다. 우리는 표면이 금속 특성을 가지고 대부분의 시간을 가정하고, 따라서 광택 플라스틱 / 고무 표면이 예상대로 보이지 않을 수 있습니다.
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>반사율

`Albedo`에서 `Diffuse` `Specular`계산됩니다. `Metalness`

금속성 섹션에 설명된 바와 같이 유전체 표면은 빛의 약 4%를 반사합니다.  
여기서 아이디어는 값을 요소로 사용하여 `Dielectric` `Metal` `Metalness` 색상과 색상을 선형으로 보간하는 것입니다. 금속이 `0.0`있는 경우 스페큘러에 따라 어두운 색상(스페큘러가 높으면) 확산이 변경되지 않습니다(스페큘러가 없는 경우). 금속성이 큰 값인 경우 분산 색상은 스페큘러 색상을 선호하여 사라집니다.

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`위의 수식에 의해 계산되었지만 알파 채널에는 추가 계산이 필요합니다. FBX 형식은 투명성에 대해 모호하며 이를 정의하는 여러 가지 방법이 있습니다. 콘텐츠 도구마다 다른 방법을 사용합니다. 여기서 아이디어는 하나의 수식으로 통합하는 것입니다. 그러나 일부 자산이 일반적인 방식으로 만들어지지 않으면 일부 자산이 투명하게 표시되지 않습니다.

이 것에서 `TransparentColor` `TransparencyFactor` `Opacity`계산됩니다.

정의된 `Opacity` 경우 직접 사용하십시오. `AlbedoAlpha`  =  `Opacity`  
정의된 `TransparencyColor` 경우 `AlbedoAlpha` = 1.0 -`TransparentColor`(( 빨간색 `TransparentColor`+ . 녹색 `TransparentColor`+ . 파란색) / 3.0) 기타  
if `TransparencyFactor`, `AlbedoAlpha` = 1.0 -`TransparencyFactor`

최종 `Albedo` 색상에는 `AlbedoRGB` `AlbedoAlpha`4개의 채널이 있으며.

### <a name="summary"></a>요약

여기서 `Albedo` 요약하면 0에 가까운 경우 `Diffuse` `Specular` 원본에 매우 가깝습니다. 그렇지 않으면 서피스가 금속 표면처럼 보이고 분산 색상이 손실됩니다. 표면은 충분히 `Specular` 크고 밝은 경우 `ShininessExponent` 더 연마하고 반사 볼 것이다. 그렇지 않으면 표면이 거칠게 보이고 환경을 거의 반영하지 않습니다.

### <a name="known-issues"></a>알려진 문제

* 간단한 색상 형상에는 현재 수식이 잘 작동하지 않습니다. 충분히 `Specular` 밝은 경우 모든 형상은 색상없이 반사 금속 표면이됩니다. 여기서 해결 방법은 원본에서 30%로 낮추거나 `Specular` 변환 [설정fbxAssumeMetallic을](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)사용하는 것입니다.
* PBR 재료는 최근에 `Maya` `3DS Max` 추가되었으며 콘텐츠 제작 도구도 추가되었습니다. 사용자 정의 블랙박스 속성을 사용하여 FBX에 전달합니다. Azure 원격 렌더링은 문서화되지 않고 형식이 닫힌 소스이기 때문에 이러한 추가 속성을 읽지 않습니다.

## <a name="next-steps"></a>다음 단계

* [모델 변환](../how-tos/conversion/model-conversion.md)
* [모델 변환 중 재질 재질 재이상](../how-tos/conversion/override-materials.md)
