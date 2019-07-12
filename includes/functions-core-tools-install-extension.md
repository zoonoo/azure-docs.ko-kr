---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ffd9e54c0f39b4256dbc83a336328797a8b53c45
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608276"
---
## <a name="register-extensions"></a>확장 등록

HTTP 및 타이머 트리거를 제외 하 고 바인딩을 함수 런타임 버전 2.x 확장 패키지로 구현 됩니다. 버전에서의 Azure Functions 런타임 2.x를 명시적으로 함수에 사용 되는 바인딩 형식에 대 한 확장을 등록 해야 합니다. 이 예외는 HTTP 바인딩 및 타이머 트리거를 확장 하지 않아도 됩니다.

바인딩 확장을 개별적으로 설치 하도록 선택할 수 있습니다 또는 host.json 프로젝트 파일에 대 한 확장 번들 참조를 추가할 수 있습니다. 확장 번들에는 여러 바인딩 형식을 사용 하는 경우 패키지 호환성 문제가 있을 가능성이 제거 합니다. 권장 되는 바인딩 확장 등록 방법 이며 확장 번들에는 또한.NET Core를 설치 하는 요구 사항이 제거 2.x SDK. 

### <a name="extension-bundles"></a>확장 번들

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

자세한 내용은 참조 하세요 [등록 하는 Azure Functions 바인딩 확장](../articles/azure-functions/functions-bindings-register.md#extension-bundles)합니다. 바인딩 functions.json 파일에 추가 하기 전에 확장 번들을 host.json에 추가 해야 합니다.

### <a name="register-individual-extensions"></a>개별 확장 등록

번들에 없는 확장을 설치 해야 할 경우 특정 바인딩에 대 한 개별 확장 패키지를 수동으로 등록할 수 있습니다. 

> [!NOTE]
> 사용 하 여 확장을 수동으로 등록 `func extensions install`,.NET Core 있어야 합니다. 2.x SDK가 설치 되어 있습니다.

함수에 필요한 모든 바인딩을 포함하도록 *function.json* 파일을 업데이트한 후에 프로젝트 폴더에서 다음 명령을 실행합니다.

```bash
func extensions install
```

이 명령은 *function.json* 파일을 읽어 필요한 패키지를 확인하고 설치한 후 확장 프로젝트를 다시 빌드합니다. 현재 버전에서 새 바인딩을 추가하되, 기존 바인딩을 업데이트하지는 않습니다. 새 바인딩을 설치할 때 기존 바인딩을 최신 버전으로 업데이트하려면 `--force` 옵션을 사용합니다.