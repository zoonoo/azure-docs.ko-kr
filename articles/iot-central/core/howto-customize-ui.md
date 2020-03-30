---
title: Azure IoT 중앙 UI 사용자 지정 | 마이크로 소프트 문서
description: Azure IoT 중앙 응용 프로그램에 대한 테마 및 도움말 링크를 사용자 지정하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f76f143b6c6a26b88b78e20d8d5d8ae1ae48553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158017"
---
# <a name="customize-the-azure-iot-central-ui"></a>Azure IoT 중앙 UI 사용자 지정

이 문서에서는 사용자 지정 테마를 적용하고 사용자 지정 도움말 링크를 수정하여 사용자 지정 도움말 리소스를 지정하여 응용 프로그램의 UI를 사용자 지정하는 방법을 설명합니다. 



다음 스크린샷은 표준 테마를 사용하는 페이지를 보여 주었습니다.

![표준 IoT 중앙 테마](./media/howto-customize-ui/standard-ui.png)

다음 스크린샷은 사용자 지정 UI 요소가 강조 표시된 사용자 지정 스크린샷을 사용하는 페이지를 보여 주며, 이 화면은 다음과 같은 것입니다.

![사용자 정의 IoT 중앙 테마](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>테마 만들기

사용자 지정 테마를 만들려면 **관리** 섹션의 **응용 프로그램 사용자 지정** 페이지로 이동합니다.

![IoT 센트럴 테마](./media/howto-customize-ui/themes.png)

이 페이지에서 응용 프로그램의 다음 측면을 사용자 지정할 수 있습니다.

### <a name="application-logo"></a>응용 프로그램 로고

투명한 배경을 가진 PNG 이미지(1MB 이하)입니다. 이 로고는 IoT 중앙 응용 프로그램 제목 표시줄의 왼쪽에 표시됩니다.

로고 이미지에 응용 프로그램 이름이 포함된 경우 응용 프로그램 이름 텍스트를 숨길 수 있습니다. 자세한 내용은 [응용 프로그램 관리를](howto-administer.md#change-application-name-and-url)참조하십시오.

### <a name="browser-icon-favicon"></a>브라우저 아이콘(파비콘)

투명한 배경을 가진 32 x 32 픽셀 이하의 PNG 이미지입니다. 웹 브라우저는 주소 표시줄, 기록, 북마크 및 브라우저 탭에서 이 이미지를 사용할 수 있습니다.

### <a name="browser-colors"></a>브라우저 색상

페이지 헤더의 색상과 악센트 버튼 및 기타 강조 표시에 사용되는 색상을 변경할 수 있습니다. 형식에서 `##ff6347`6자 육감 색상 값을 사용합니다. **HEX 값** 색상 표기에 대한 자세한 내용은 [HTML 색상](https://www.w3schools.com/html/html_colors.asp)을 참조하십시오.

> [!NOTE]
> 언제든지 응용 프로그램 사용자 지정 페이지의 기본 옵션으로 되돌릴 **수 있습니다.**

### <a name="changes-for-operators"></a>연산자 변경 사항

관리자가 사용자 지정 테마를 만드는 경우 운영자 와 응용 프로그램의 다른 사용자는 더 이상 **설정에서**테마를 선택할 수 없습니다.

## <a name="replace-help-links"></a>도움말 링크 바꾸기

운영자 및 다른 사용자에게 사용자 지정 도움말 정보를 제공하려면 응용 프로그램 **도움말** 메뉴의 링크를 수정할 수 있습니다.

도움말 링크를 수정하려면 **관리** 섹션의 **사용자 지정 도움말** 페이지로 이동합니다.

![IoT 중앙 도움말 링크 사용자 지정](./media/howto-customize-ui/help-links.png)

도움말 메뉴에 새 항목을 추가하고 기본 항목을 제거할 수도 있습니다.

![맞춤형 IoT 중앙 도움말](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> **언제든지 사용자 지정** 도움말 페이지의 기본 도움말 링크로 되돌릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 IoT Central 응용 프로그램에서 UI를 사용자 지정하는 방법을 배웠으니 다음과 같은 몇 가지 다음 단계가 제안되었습니다.

- [애플리케이션 관리](./howto-administer.md)
- [대시보드에 타일 추가](howto-add-tiles-to-your-dashboard.md)