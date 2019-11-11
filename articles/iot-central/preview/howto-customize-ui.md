---
title: Azure IoT Central UI 사용자 지정 | Microsoft Docs
description: Azure IoT central 응용 프로그램의 테마 및 도움말 링크를 사용자 지정 하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1c9f400c1712c4826044354ead27ecaf597ee311
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894943"
---
# <a name="customize-the-azure-iot-central-ui-preview-features"></a>Azure IoT Central UI 사용자 지정 (미리 보기 기능)

이 문서에서는 관리자가 사용자 지정 테마를 적용 하 고 사용자 지정 도움말 리소스를 가리키도록 도움말 링크를 수정 하 여 응용 프로그램의 UI를 사용자 지정할 수 있는 방법을 설명 합니다. 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

다음 스크린샷은 표준 테마를 사용 하는 페이지를 보여 줍니다.

![표준 IoT Central 테마](./media/howto-customize-ui/standard-ui.png)

다음 스크린샷은 사용자 지정 된 UI 요소가 강조 표시 된 사용자 지정 스크린샷을 사용 하는 페이지를 보여 줍니다.

![사용자 지정 IoT Central 테마](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>테마 만들기

사용자 지정 테마를 만들려면 **관리** 섹션의 **응용 프로그램 사용자 지정** 페이지로 이동 합니다.

![IoT Central 테마](./media/howto-customize-ui/themes.png)

이 페이지에서 응용 프로그램의 다음 측면을 사용자 지정할 수 있습니다.

### <a name="application-logo"></a>응용 프로그램 로고

투명 한 배경을 사용 하 여 1mb 보다 크지 않은 PNG 이미지입니다. 이 로고는 IoT Central 응용 프로그램 제목 표시줄의 왼쪽에 표시 됩니다.

응용 프로그램 이름을 로고 이미지에 포함 하는 경우 응용 프로그램 이름 텍스트를 숨길 수 있습니다. 자세한 내용은 [응용 프로그램 관리](./howto-administer.md#change-application-name-and-url)를 참조 하세요.

### <a name="browser-icon-favicon"></a>브라우저 아이콘 (favicon)

투명 한 배경을 사용 하 여 32 x 32 픽셀 보다 크지 않은 PNG 이미지입니다. 웹 브라우저는 주소 표시줄, 기록, 책갈피 및 브라우저 탭에서이 이미지를 사용할 수 있습니다.

### <a name="browser-colors"></a>브라우저 색

페이지 머리글의 색과 accenting 단추 및 기타 강조 표시에 사용 되는 색을 변경할 수 있습니다. `##ff6347`형식으로 6 자 16 진수 색 값을 사용 합니다. **16 진수 값** 색 표기법에 대 한 자세한 내용은 [HTML 색](https://www.w3schools.com/html/html_colors.asp)을 참조 하세요.

> [!NOTE]
> 언제 든 지 **응용 프로그램 사용자 지정** 페이지에서 기본 옵션으로 되돌릴 수 있습니다.

### <a name="changes-for-operators"></a>연산자의 변경 내용

관리자가 사용자 지정 테마를 만들 경우 운영자와 응용 프로그램의 다른 사용자가 더 이상 **설정**에서 테마를 선택할 수 없습니다.

## <a name="replace-help-links"></a>도움말 링크 바꾸기

사용자 지정 도움말 정보를 운영자 및 다른 사용자에 게 제공 하기 위해 응용 프로그램 **도움말** 메뉴에서 링크를 수정할 수 있습니다.

도움말 링크를 수정 하려면 **관리** 섹션에서 **사용자 지정 도움말** 페이지로 이동 합니다.

![IoT Central 도움말 링크 사용자 지정](./media/howto-customize-ui/help-links.png)

도움말 메뉴에 새 항목을 추가 하 고 기본 항목을 제거할 수도 있습니다.

![사용자 지정 된 IoT Central 도움말](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> 언제 든 지 **도움말 사용자 지정** 페이지에서 기본 도움말 링크를 다시 되돌릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Central 응용 프로그램에서 UI를 사용자 지정 하는 방법을 배웠으므로 이제 몇 가지 제안 된 다음 단계를 참조 하세요.

- [애플리케이션 관리](./howto-administer.md)
- [대시보드에 타일 추가](../core/howto-add-tiles-to-your-dashboard.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)