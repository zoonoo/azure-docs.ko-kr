---
title: TexConv-질감 변환 도구
description: TexConv 명령줄 도구에 대 한 사용자 설명서
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80680025"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv-질감 변환 도구

TexConv는 PNG, TGA, JPEG 및 DDS와 같은 일반적인 입력 형식에서 런타임 사용을 위해 최적화 된 형식으로 질감을 처리 하는 명령줄 도구입니다.
가장 일반적인 시나리오는 단일 입력 파일을 최적화 된 형식으로 변환 하는 것입니다 .이 도구에는 `A.xxx` `B.yyy` 고급 사용을 위한 추가 옵션이 많이 있습니다.

## <a name="command-line-help"></a>명령줄 도움말

매개 변수를 사용 하 여 TexConv.exe를 실행 `--help` 하면 사용 가능한 모든 옵션이 나열 됩니다. 또한 TexConv는 실행 되는 작업을 이해 하는 데 사용 되는 옵션을 인쇄 합니다. 자세한 내용은이 출력을 참조 하세요.

## <a name="general-usage"></a>일반 사용법

TexConv는 항상 **정확히 하나의 출력** 파일을 생성 합니다. **여러 입력** 파일을 사용 하 여에서 출력을 조합할 수 있습니다. 어셈블리의 경우 입력 파일에서 가져와서 출력 이미지의 채널로 이동 하는 채널 (*빨강, 녹색, 파랑* 또는 *알파*)을 알려주는 **채널 매핑도**필요 합니다.

가장 직접적인 전달 명령줄은 다음과 같습니다.

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`출력 파일 및 형식을 지정 합니다.
- `-in0`첫 번째 입력 이미지를 지정 합니다.
- `-rgba`출력 이미지가 4 개의 채널을 모두 사용 하 고 입력 이미지에서 1:1를 가져와야 함을 나타냅니다.

## <a name="multiple-input-files"></a>여러 입력 파일

여러 입력 파일에서 출력을 조합 하려면 다음 옵션을 사용 하 여 각 입력 파일을 숫자로 지정 합니다 `-in` .

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

2d 질감에서 큐브 맵를 어셈블할 때,,,, 또는,,,,,를 사용할 수도 있습니다 `-right` `-left` `-top` `-bottom` `-front` `-back` `-px` `-nx` `-py` `-ny` `-pz` `-nz` .

이러한 입력을 출력 파일에 매핑하려면 적절 한 채널 매핑이 필요 합니다.

## <a name="channel-mappings"></a>채널 매핑

채널 매핑 옵션은 지정 된 출력 채널을 채울 입력을 지정 합니다. 각 채널에 대 한 입력을 다음과 같이 개별적으로 지정할 수 있습니다.

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

여기서 출력의 RGB 채널은 첫 번째 입력 이미지를 사용 하 여 채워져 있지만 red 및 blue는 교체 됩니다. 출력의 알파 채널은 두 번째 입력 이미지의 빨간색 채널 값으로 채워집니다.

각 채널에 대 한 매핑을 별도로 지정 하면 유연성이 가장 큽니다. 편의를 위해 "재구성" 연산자를 사용 하 여 동일한를 작성할 수 있습니다.

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>출력 채널

사용할 수 있는 채널 매핑 옵션은 다음과 같습니다.

- `-r`, `-g` , `-b` , `-a` : 단일 채널 할당을 지정 합니다.
- `-rg`: 빨강 및 녹색 채널 할당을 지정 합니다.
- `-rgb`: 빨강, 녹색 및 파랑 채널 할당을 지정 합니다.
- `-rgba`: 4 개의 채널 할당을 모두 지정 합니다.

R, RG 또는 RGB 채널만 언급 하 고 TexConv는 각각 1, 2 또는 3 개의 채널만 있는 출력 파일을 만들도록 지시 합니다.

### <a name="input-swizzling"></a>입력 재구성

어떤 입력 질감이 출력 채널을 채워야 하는지 지정 하면 입력을 swizzle 수 있습니다.

- `-rgba in0`는 `-rgba in0.rgba`와 같습니다.
- `-rgba in0.bgra`입력 채널을 swizzle 합니다.
- `-rgb in0.rrr`빨간색 채널이 모든 채널에 복제 됩니다.

채널을 검정색이 나 흰색으로 채울 수도 있습니다.

- `-rgb in0 -a white`4 채널 출력 질감을 만들지만 알파를 완전히 불투명 하 게 설정 합니다.
- `-rg black -b white`완전히 파란색 질감을 만듭니다.

## <a name="common-options"></a>일반 옵션

가장 흥미로운 옵션은 다음과 같습니다. 에는 추가 옵션이 나열 되어 `TexConv --help` 있습니다.

### <a name="output-type"></a>출력 형식

- `-type 2D`: 출력은 일반 2D 이미지가 됩니다.
- `-type Cubemap`: 출력은 큐브 맵 이미지가 됩니다. DDS 출력 파일에만 지원 됩니다. 이를 지정 하면 6 개의 일반 2D 입력 이미지에서 큐브 맵을 조합할 수 있습니다.

### <a name="image-compression"></a>이미지 압축

- `-compression none`: 출력 이미지는 압축 되지 않습니다.
- `-compression medium`: 지원 되는 경우 출력 이미지는 너무 많은 품질을 저하 시 키 지 않고 압축을 사용 합니다.
- `-compression high`: 지원 되는 경우 출력 이미지는 더 작은 파일의 압축 및 사용 품질 저하를 사용 합니다.

### <a name="mipmaps"></a>Mip 맵을

기본적으로 TexConv는 출력 형식이 지 원하는 경우 mip 맵을을 생성 합니다.

- `-mipmaps none`: Mip 맵을이 생성 되지 않습니다.
- `-mipmaps Linear`: 지원 되는 경우 mip 맵을는 box 필터를 사용 하 여 생성 됩니다.

### <a name="usage-srgb--gamma-correction"></a>사용법 (sRGB/감마 보정)

`-usage`옵션은 출력의 용도를 지정 하 여 TexConv 입력 및 출력 파일에 감마 보정을 적용할지 여부를 알려 줍니다. 사용은 RGB 채널에만 영향을 줍니다. 알파 채널은 항상 ' 선형 ' 값을 포함 하는 것으로 간주 됩니다. 사용법을 지정 하지 않은 경우 ' auto ' 모드는 첫 번째 입력 이미지의 형식 및 파일 이름에서 사용을 검색 합니다. 예를 들어 단일 및 이중 채널 출력 형식은 항상 선형입니다. 출력을 확인 하 여 결정 TexConv 확인 합니다.

- `-usage Linear`: 출력 이미지에 색을 나타내지 않는 값이 포함 되어 있습니다. 이는 일반적으로 금속성 및 거친 질감 및 모든 종류의 마스크에 대 한 경우입니다.

- `-usage Color`: 출력 이미지는 확산/albedo 맵과 같은 색을 나타냅니다. SRGB 플래그는 출력 DDS 헤더에 설정 됩니다.

- `-usage HDR`: 출력 파일은 인코딩에 대해 픽셀당 8 비트 이상 사용 해야 합니다. 따라서 모든 값은 선형 공간에 저장 됩니다. HDR 질감의 경우 데이터가 색 또는 기타 데이터를 나타내는지 여부는 중요 하지 않습니다.

- `-usage NormalMap`: 출력 이미지는 탄젠트 공간 법선 지도를 나타냅니다. 값이 정규화 되 고 밉 맵 계산이 약간 최적화 됩니다.

- `-usage NormalMap_Inverted`: 출력은 Y가 입력 보다 반대 방향을 가리키는 탄젠트 공간 법선 맵입니다.

### <a name="image-rescaling"></a>이미지 크기 조정

- `-minRes 64`: 출력의 최소 해상도를 지정 합니다. 입력 이미지가 더 작은 경우에는 크기 조정이 시작 됩니다.
- `-maxRes 1024`: 출력의 최대 해상도를 지정 합니다. 입력 이미지가 더 크면 축소 됩니다.
- `-downscale 1`:이 값이 0 보다 크면 입력 이미지가 해상도에서 N 번 반 됩니다. 전반적인 품질 감소를 적용 하려면이를 사용 합니다.

## <a name="examples"></a>예

### <a name="convert-a-color-texture"></a>색 질감 변환

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>일반 맵 변환

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>HDR 큐브 맵 만들기

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

HDR cubemaps의 좋은 소스는 [hdrihaven.com](https://hdrihaven.com/hdris/)입니다.

### <a name="bake-multiple-images-into-one"></a>여러 이미지를 하나로 굽기

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>단일 채널 추출

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
