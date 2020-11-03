---
title: 도구를 사용 하 여 DTDL 모델 작성 및 유효성 검사 | Microsoft Docs
description: Visual Studio Code 또는 Visual Studio 2019 용 DTDL 편집기를 설치 하 고이 편집기를 사용 하 여 IoT 플러그 앤 플레이 모델을 작성 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 40d1ae4da07e159c24970c065d1c39e22b89a29a
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280202"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>DTDL authoring tools 설치 및 사용

[DTDL (디지털 Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 모델은 JSON 파일입니다. Visual Studio code 또는 Visual Studio 2019 용 확장을 사용 하 여 이러한 모델 파일을 작성 하 고 유효성을 검사할 수 있습니다.

## <a name="install-and-use-the-vs-code-extension"></a>VS Code 확장 설치 및 사용

VS Code 용 DTDL 확장은 다음 DTDL 제작 기능을 추가 합니다.

- DTDL v2 구문 유효성 검사입니다.
- 자동 완성 기능을 포함 하는 Intellisense를 사용 하 여 언어 구문을 지원할 수 있습니다.
- 명령 팔레트에서 인터페이스를 만들 수 있습니다.

DTDL 확장을 설치 하려면 [Visual Studio Code 용 dtdl 편집기](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)로 이동 합니다. VS Code의 확장 뷰에서 **Dtdl** 을 검색할 수도 있습니다.

확장을 설치한 경우에는이 확장을 사용 하 여 VS code에서 DTDL 모델 파일을 작성 하는 데 도움이 됩니다.

- 확장은 DTDL 모델 파일에서 구문 유효성 검사를 제공 하 고 다음 스크린샷에 표시 된 대로 오류를 강조 표시 합니다.

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="VS Code의 모델 유효성 검사":::

- DTDL 모델을 편집 하는 경우 intellisense 및 자동 완성을 사용 합니다.

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="VS Code에서 DTDL 모델에 대해 intellisense 사용":::

- 새 DTDL 인터페이스를 만듭니다. **Dtdl: Create interface** 명령은 새 인터페이스를 사용 하 여 JSON 파일을 만듭니다. 인터페이스에는 원격 분석, 속성 및 명령 정의 예가 포함 되어 있습니다.

## <a name="install-and-use-the-visual-studio-extension"></a>Visual Studio 확장 설치 및 사용

Visual Studio 2019 용 DTDL 확장은 다음 DTDL 제작 기능을 추가 합니다.

- DTDL v2 구문 유효성 검사입니다.
- 자동 완성 기능을 포함 하는 Intellisense를 사용 하 여 언어 구문을 지원할 수 있습니다.

DTDL 확장을 설치 하려면 [VS 2019에 대 한 Dtdl 언어 지원](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport)으로 이동 합니다. Visual Studio에서 **확장 관리** 에서 **dtdl** 을 검색할 수도 있습니다.

확장을 설치한 경우에는이 확장을 사용 하 여 Visual Studio에서 DTDL 모델 파일을 작성 하는 데 도움이 됩니다.

- 확장은 DTDL 모델 파일에서 구문 유효성 검사를 제공 하 고 다음 스크린샷에 표시 된 대로 오류를 강조 표시 합니다.

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Visual Studio의 모델 유효성 검사":::

- DTDL 모델을 편집 하는 경우 intellisense 및 자동 완성을 사용 합니다.

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Visual Studio에서 DTDL 모델에 대해 intellisense 사용":::

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Visual Studio Code 및 Visual Studio 2019 용 DTDL 확장을 사용 하 여 DTDL 모델 파일을 작성 하 고 유효성을 검사 하는 방법에 대해 알아보았습니다. 제안 된 다음 단계는 [모델과 장치에서 Azure IoT 탐색기](./howto-use-iot-explorer.md)를 사용 하는 방법을 배우는 것입니다.
