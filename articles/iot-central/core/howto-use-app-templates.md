---
title: Azure IoT Central |에서 응용 프로그램 템플릿 사용 Microsoft Docs
description: 운영자로서 Azure IoT Central 애플리케이션에서 디바이스 집합을 사용하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3cc6f82676f426240fba4cc4910246073aa9a556
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982463"
---
# <a name="use-application-templates"></a>애플리케이션 템플릿 사용

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

이 문서에서는 솔루션 관리자로 서 응용 프로그램 템플릿을 만들고 사용 하는 방법을 설명 합니다.

Azure IoT Central 응용 프로그램을 만드는 경우 기본 제공 샘플 템플릿 중에서 선택할 수 있습니다. 기존 IoT Central 응용 프로그램에서 사용자 고유의 응용 프로그램 템플릿을 만들 수도 있습니다. 그런 다음 새 응용 프로그램을 만들 때 사용자 고유의 응용 프로그램 템플릿을 사용할 수 있습니다.

응용 프로그램 템플릿을 만들면 기존 응용 프로그램에서 다음 항목이 포함 됩니다.

- 대시보드 레이아웃 및 정의한 모든 타일을 포함 하는 기본 응용 프로그램 대시보드
- 측정, 설정, 속성, 명령 및 대시보드를 포함 한 장치 템플릿입니다.
- 규칙. 모든 규칙 정의가 포함 되어 있습니다. 그러나 전자 메일 작업을 제외한 작업은 포함 되지 않습니다.
- 장치 집합은 해당 조건 및 대시보드를 포함 합니다.

> [!WARNING]
> 대시보드는 특정 장치에 대 한 정보를 표시 하는 타일을 포함 하는 경우 요청 된 리소스를 새 응용 프로그램에서 **찾을 수 없는** 타일로 표시 합니다. 새 응용 프로그램의 장치에 대 한 정보를 표시 하려면 이러한 타일을 다시 구성 해야 합니다.

응용 프로그램 템플릿을 만들 때 다음 항목은 포함 되지 않습니다.

- 디바이스
- 사용자
- 작업 정의
- 연속 데이터 내보내기 정의

응용 프로그램 템플릿에서 만든 모든 응용 프로그램에 이러한 항목을 수동으로 추가 합니다.

## <a name="create-an-application-template"></a>애플리케이션 템플릿 만들기

기존 IoT Central 응용 프로그램에서 응용 프로그램 템플릿을 만들려면 다음을 수행 합니다.

1. 응용 프로그램의 **관리** 섹션으로 이동 합니다.
1. **응용 프로그램 템플릿 내보내기**를 선택 합니다.
1. **응용 프로그램 템플릿 내보내기** 페이지에서 템플릿의 이름 및 설명을 입력 합니다.
1. 응용 프로그램 템플릿을 만들려면 **내보내기** 단추를 선택 합니다. 이제 다른 사용자가 템플릿에서 새 응용 프로그램을 만들 수 있도록 **공유 가능한 링크** 를 복사할 수 있습니다.

![애플리케이션 템플릿 만들기](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>응용 프로그램 템플릿 사용

응용 프로그램 템플릿을 사용 하 여 새 IoT Central 응용 프로그램을 만들려면 이전에 만든 공유 가능 **링크가**필요 합니다. 공유 가능한 **링크** 를 브라우저의 주소 표시줄에 붙여 넣습니다. **응용 프로그램 만들기** 페이지가 표시 되 고 사용자 지정 응용 프로그램 템플릿이 선택 됩니다.

![템플릿에서 응용 프로그램 만들기](media/howto-use-app-templates/create-app.png)

가격 책정 계획을 선택 하 고 폼의 다른 필드를 작성 합니다. 그런 다음 **만들기** 를 선택 하 여 응용 프로그램 템플릿에서 새 IoT Central 응용 프로그램을 만듭니다.

## <a name="manage-application-templates"></a>응용 프로그램 템플릿 관리

**응용 프로그램 템플릿 내보내기** 페이지에서 응용 프로그램 템플릿을 삭제 하거나 업데이트할 수 있습니다.

응용 프로그램 템플릿을 삭제 하는 경우 더 이상 이전에 생성 된 공유 가능 링크를 사용 하 여 새 응용 프로그램을 만들 수 없습니다.

응용 프로그램 템플릿을 업데이트 하려면 **응용 프로그램 템플릿 내보내기** 페이지에서 템플릿 이름이 나 설명을 변경 합니다. 그런 다음 **내보내기** 단추를 다시 선택 합니다. 이 작업은 공유 가능한 새 **링크** 를 생성 하 고 이전에 **공유 가능한 링크** URL을 무효화 합니다.

## <a name="next-steps"></a>다음 단계

이제 응용 프로그램 템플릿을 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 [Azure Portal에서 IoT Central를 관리](howto-manage-iot-central-from-portal.md) 하는 방법을 배우는 것입니다.
