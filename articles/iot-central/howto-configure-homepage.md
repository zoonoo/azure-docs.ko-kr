---
title: Azure IoT Central 응용 프로그램 대시보드 구성 | Microsoft Docs
description: 작성기로 기본 Azure IoT Central 응용 프로그램 대시보드를 구성 하는 방법에 대해 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850209"
---
# <a name="configure-the-application-dashboard"></a>응용 프로그램 대시보드 구성

**대시보드** 는 응용 프로그램에 대 한 액세스 권한이 있는 사용자가 응용 프로그램의 URL로 이동할 때 로드 되는 페이지입니다. **샘플 Contoso** 또는 **샘플 devkits** 응용 프로그램 템플릿을 선택 하 여 응용 프로그램을 만든 경우 응용 프로그램에 미리 정의 된 대시보드가 있습니다. **사용자 지정 응용 프로그램** 응용 프로그램 템플릿을 선택한 경우 대시보드는 비어 있습니다.

> [!NOTE]
> 사용자는 기본 응용 프로그램 대시보드 대신 사용할 [고유한 개인 대시보드를 만들](howto-personalize-dashboard.md) 수도 있습니다.

## <a name="add-tiles"></a>타일 추가

다음 스크린샷은 **샘플 Contoso** 템플릿에서 만든 응용 프로그램의 대시보드를 보여 줍니다. 응용 프로그램의 기본 대시보드를 사용자 지정 하려면 페이지의 오른쪽 위에 있는 **편집** 을 선택 합니다.

!["샘플 Contoso" 템플릿을 기반으로 하는 응용 프로그램 대시보드](media/howto-configure-homepage/image1a.png)

**편집**을 선택 하면 대시보드 라이브러리 패널이 열립니다. 라이브러리에는 대시보드를 사용자 지정 하는 데 사용할 수 있는 타일 및 대시보드 기본 형식이 포함 되어 있습니다.

![대시보드 라이브러리](media/howto-configure-homepage/image2a.png)

예를 들어 장치 **설정 및 속성** 타일을 추가 하 여 장치에 대 한 현재 설정 및 속성 값의 선택 항목을 표시할 수 있습니다. 이렇게 하려면 먼저 **디바이스 템플릿**을 선택한 다음, **디바이스 인스턴스**를 선택합니다. 타일에 제목을 지정한 후, 표시할 **설정** 또는 **속성**을 선택합니다. 다음 스크린샷은 타일에 추가 하도록 선택한 설정 및 속성을 보여 줍니다. **완료** 를 선택 하 여 변경 내용을 대시보드에 저장 합니다.

![설정 및 속성에 대한 세부 정보가 있는 "디바이스 세부 정보 구성" 양식](media/howto-configure-homepage/image3a.png)

이제 운영자가 기본 응용 프로그램 대시보드를 볼 때 장치에 대 한 **온도 설정** 설정이 포함 된 새 타일이 표시 됩니다.

![타일에 대한 설정 및 속성이 표시된 “대시보드” 탭](media/howto-configure-homepage/image4a.png)

라이브러리의 다른 타일 유형을 탐색 하 여 기본 응용 프로그램 대시보드를 추가로 사용자 지정 하는 방법을 찾을 수 있습니다.

Azure IoT Central에서 타일을 사용 하는 방법에 대해 자세히 알아보려면 [대시보드 타일 사용](howto-use-tiles.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 기본 응용 프로그램 대시보드를 구성 하는 방법을 배웠으므로 다음 작업을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [이미지 준비 및 업로드하는 방법 알아보기](howto-prepare-images.md)
