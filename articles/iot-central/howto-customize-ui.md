---
title: Azure IoT Central UI를 사용자 지정 | Microsoft Docs
description: Azure IoT central 응용 프로그램에 대 한 테마 및 도움말 링크를 사용자 지정 하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4d385f1e8c883453b4153ca4c9119d3be0a608bb
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495553"
---
# <a name="customize-the-azure-iot-central-ui"></a>Azure IoT Central UI를 사용자 지정 

*이 문서는 관리자에 게 적용 됩니다.*

IoT Central를 사용 하면 사용자 지정 테마를 적용 하 고 사용자 고유의 사용자 지정 도움말 리소스를 가리키도록 도움말 링크를 수정 하 여 응용 프로그램의 UI를 사용자 지정할 수 있습니다. 다음 스크린 샷에서 표준 테마를 사용 하는 페이지를 보여 줍니다.

![표준 IoT Central 테마](./media/howto-customize-ui/standard-ui.png)

다음 스크린샷에서 강조 표시 된 사용자 지정된 UI 요소를 사용 하 여 사용자 지정 스크린샷을 사용 하는 페이지를 보여 줍니다.

![사용자 지정 IoT Central 테마](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>테마 만들기

사용자 지정 테마를 만들려면로 이동 합니다 **응용 프로그램을 사용자 지정** 페이지에서 **관리** 섹션:

![IoT Central 테마](./media/howto-customize-ui/themes.png)

이 페이지에서는 응용 프로그램의 다음 측면을 사용자 지정할 수 있습니다.

### <a name="application-logo"></a>응용 프로그램 로고

PNG 이미지를 투명 한 배경의 1MB 미만. 이 로고는 IoT Central 응용 프로그램 제목 표시줄의 왼쪽에 표시 됩니다.

로고 이미지에 응용 프로그램의 이름에 포함 된 경우에 응용 프로그램 이름 입력란을 숨길 수 있습니다. 자세한 내용은 [응용 프로그램 설정 관리](./howto-administer.md#manage-application-settings)합니다.

### <a name="browser-icon-favicon"></a>브라우저 아이콘 (favicon)

PNG 이미지에서 투명 한 배경의 32 x 32 픽셀 보다 크지 합니다. 웹 브라우저 주소 표시줄, 기록, 책갈피 및 브라우저 탭에서이 이미지를 사용할 수 있습니다.

### <a name="browser-colors"></a>브라우저 색

단추 및 기타 주요 내용 악센트 사용 되는 색 및 색 페이지 머리글을 변경할 수 있습니다. 형식의 6 문자 16 진수 색 값을 사용 하 여 `##ff6347`입니다. 에 대 한 자세한 내용은 **16 진수 값** 표기법 색을 참조 하십시오 [HTML 색](https://www.w3schools.com/html/html_colors.asp)합니다.

> [!NOTE]
> 기본 옵션으로 되돌릴 수 있습니다 합니다 **응용 프로그램을 사용자 지정** 페이지입니다.

### <a name="changes-for-operators"></a>연산자에 대 한 변경 내용

관리자가 사용자 지정 테마를 만들 경우 연산자 응용 프로그램의 다른 사용자가 더 이상 선택할 수에서 테마 **설정을**합니다.

## <a name="replace-help-links"></a>도움말 링크를 대체 합니다.

운영자 및 다른 사용자에 게 사용자 지정 도움말 정보를 제공 하려면 응용 프로그램에 대 한 링크를 수정할 수 있습니다 **도움말** 메뉴.

도움말 링크를 수정 하려면로 이동 합니다 **도움말을 사용자 지정할** 페이지에 **관리** 섹션:

![IoT Central 도움말 링크를 사용자 지정](./media/howto-customize-ui/help-links.png)

또한 도움말 메뉴에 새 항목을 추가 하 고 기본 항목을 제거할 수 있습니다.

![사용자 지정된 IoT Central 도움말](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> 기본 도움말 링크를 다시 되돌릴 수 있습니다 합니다 **도움말을 사용자 지정할** 페이지입니다.

## <a name="next-steps"></a>다음 단계

이제 IoT Central 응용 프로그램에서 UI 사용자 지정 하는 방법을 배웠으므로 제안 된 다음 단계 다음과 같습니다.

- [애플리케이션 관리](./howto-administer.md)
- [응용 프로그램 대시보드를 구성 합니다.](./howto-configure-homepage.md)