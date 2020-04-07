---
title: 텍스콘프 - 텍스처 변환 도구
description: 텍스콩프 명령줄 도구용 사용 설명서
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680025"
---
# <a name="texconv---texture-conversion-tool"></a>텍스콘프 - 텍스처 변환 도구

TexConv는 PNG, TGA, JPEG 및 DDS와 같은 일반적인 입력 형식의 텍스처를 런타임 소비를 위해 최적화된 형식으로 처리하는 명령줄 도구입니다.
가장 일반적인 시나리오는 단일 입력 파일을 `A.xxx` 최적화된 `B.yyy`형식으로 변환하는 것이지만 이 도구에는 고급 용도에 대한 많은 추가 옵션이 있습니다.

## <a name="command-line-help"></a>명령줄 도움말

매개 변수를 사용하여 TexConv.exe를 `--help` 실행하면 사용 가능한 모든 옵션이 나열됩니다. 또한 TexConv는 실행 될 때 사용된 옵션을 인쇄하여 수행 내용을 이해하는 데 도움이됩니다. 자세한 내용은 이 출력을 참조하십시오.

## <a name="general-usage"></a>일반 사용법

텍스콘프는 항상 **정확히 하나의 출력** 파일을 생성합니다. **여러 입력** 파일을 사용하여 출력을 어셈블할 수 있습니다. 어셈블리의 경우 입력 파일을 가져오고 출력 이미지의 채널로 이동하는*채널(빨간색, 녹색, 파란색* 또는 *알파)을*알려주는 **채널 매핑도**필요합니다.

가장 똑바른 명령줄은 다음과 입니다.

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`출력 파일 및 형식을 지정합니다.
- `-in0`첫 번째 입력 이미지를 지정합니다.
- `-rgba`출력 이미지는 네 개의 채널을 모두 사용해야 하며 입력 이미지에서 1:1을 가져와야 한다고 알려줍니다.

## <a name="multiple-input-files"></a>여러 입력 파일

여러 입력 파일의 출력을 어셈블하려면 숫자를 `-in` 늘리는 옵션을 사용하여 각 입력 파일을 지정합니다.

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

2D 텍스처에서 큐브맵을 어셈블할 `-right`때 `-left` `-top`, `-bottom` `-front` `-back` `-px` `-nx` `-py` `-ny` `-pz` `-nz`

이러한 입력을 출력 파일에 매핑하려면 적절한 채널 매핑이 필요합니다.

## <a name="channel-mappings"></a>채널 매핑

채널 매핑 옵션은 지정된 출력 채널을 채울 입력을 지정합니다. 다음과 같이 각 채널의 입력을 개별적으로 지정할 수 있습니다.

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

여기서 출력의 RGB 채널은 첫 번째 입력 이미지를 사용하여 채워지지만 빨간색과 파란색은 바뀝니다. 출력의 알파 채널은 두 번째 입력 이미지의 빨간색 채널의 값으로 채워질 것입니다.

각 채널에 대한 매핑을 별도로 지정하면 유연성이 가장 큰 이면에 있습니다. 편의를 위해 "swizzling"연산자를 사용하여 동일한 작성할 수 있습니다.

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>출력 채널

다음 채널 매핑 옵션을 사용할 수 있습니다.

- `-r``-a` . `-g` `-b`
- `-rg`: 빨간색 및 녹색 채널 할당을 지정합니다.
- `-rgb`: 빨간색, 녹색 및 파란색 채널 할당을 지정합니다.
- `-rgba`: 네 개의 채널 할당을 모두 지정합니다.

R, RG 또는 RGB 채널만 언급하면 TexConv는 각각 1채널, 2채널 또는 3개 채널만 있는 출력 파일을 생성하도록 지시합니다.

### <a name="input-swizzling"></a>입력 스위즐링

어떤 입력 텍스처가 어떤 출력 채널을 채워야 하는지 진술할 때 입력을 스위즐할 수 있습니다.

- `-rgba in0`는 `-rgba in0.rgba`와 같습니다.
- `-rgba in0.bgra`입력 채널을 스위즐
- `-rgb in0.rrr`모든 채널에 빨간색 채널을 복제합니다.

하나는 또한 검은 색 또는 흰색 으로 채널을 채울 수 있습니다.

- `-rgb in0 -a white`4 채널 출력 텍스처를 만들지 만 알파를 완전히 불투명하게 설정합니다.
- `-rg black -b white`완전히 파란색 텍스처를 만듭니다.

## <a name="common-options"></a>일반 옵션

가장 흥미로운 옵션은 다음과 같습니다. 더 많은 옵션이 `TexConv --help`다음으로 나열됩니다.

### <a name="output-type"></a>출력 형식

- `-type 2D`: 출력은 일반 2D 이미지가 됩니다.
- `-type Cubemap`: 출력은 큐브맵 이미지가 됩니다. DDS 출력 파일에서만 지원됩니다. 이 것을 지정하면 6개의 일반 2D 입력 이미지에서 큐브맵을 어셈블할 수 있습니다.

### <a name="image-compression"></a>이미지 압축

- `-compression none`: 출력 이미지가 압축되지 않습니다.
- `-compression medium`: 지원되는 경우 출력 이미지는 너무 많은 품질을 희생하지 않고 압축을 사용합니다.
- `-compression high`: 지원되는 경우 출력 이미지는 더 작은 파일을 위해 압축 및 희생 품질을 사용합니다.

### <a name="mipmaps"></a>밉

기본적으로 TexConv는 출력 형식이 지원하는 경우 밉맵을 생성합니다.

- `-mipmaps none`: 밉맵이 생성되지 않습니다.
- `-mipmaps Linear`: 지원되는 경우 상자 필터를 사용하여 밉맵이 생성됩니다.

### <a name="usage-srgb--gamma-correction"></a>사용법(sRGB/감마 보정)

이 `-usage` 옵션은 출력의 목적을 지정하므로 TexConv가 입력 및 출력 파일에 감마 보정을 적용할지 여부를 알려줍니다. 사용량은 RGB 채널에만 영향을 줍니다. 알파 채널은 항상 '선형' 값을 포함하는 것으로 간주됩니다. 사용량을 지정하지 않으면 '자동' 모드가 첫 번째 입력 이미지의 형식및 파일 이름에서 사용량을 감지하려고 시도합니다. 예를 들어 단일 및 이중 채널 출력 형식은 항상 선형입니다. 텍스콘브가 어떤 결정을 내렸는지 확인하려면 출력을 확인하십시오.

- `-usage Linear`: 출력 이미지에는 색상을 나타내지 않는 값이 포함되어 있습니다. 이것은 일반적으로 금속 및 거칠기 텍스처뿐만 아니라 모든 종류의 마스크의 경우입니다.

- `-usage Color`: 출력 이미지는 분산/알베도 맵과 같은 색상을 나타냅니다. sRGB 플래그는 출력 DDS 헤더에 설정됩니다.

- `-usage HDR`: 출력 파일은 인코딩을 위해 픽셀당 8비트 이상을 사용해야 합니다. 따라서 모든 값은 선형 공간에 저장됩니다. HDR 텍스처의 경우 데이터가 색상또는 다른 데이터를 나타내는지 여부는 중요하지 않습니다.

- `-usage NormalMap`: 출력 이미지는 접선 공간 법선 맵을 나타냅니다. 값이 정규화되고 밉맵 계산이 약간 최적화됩니다.

- `-usage NormalMap_Inverted`: 출력은 입력과 반대 방향으로 Y를 가리키는 접선 공간 법선 맵입니다.

### <a name="image-rescaling"></a>이미지 크기 조정

- `-minRes 64`: 출력의 최소 해상도를 지정합니다. 입력 이미지가 작으면 고급화됩니다.
- `-maxRes 1024`: 출력의 최대 해상도를 지정합니다. 입력 이미지가 크면 축소됩니다.
- `-downscale 1`: 0보다 크면 입력 이미지가 해상도 N배로 반으로 줄어듭니다. 전체 품질 감소를 적용하려면 이 방법을 사용합니다.

## <a name="examples"></a>예

### <a name="convert-a-color-texture"></a>색상 텍스처 변환

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>일반 맵 변환

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>HDR 큐브맵 만들기

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

HDR 큐브맵에 대한 훌륭한 소스는 [hdrihaven.com.](https://hdrihaven.com/hdris/)

### <a name="bake-multiple-images-into-one"></a>여러 이미지를 하나로 베이크

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>단일 채널 추출

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
