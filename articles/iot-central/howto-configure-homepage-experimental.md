---
title: Azure IoT Central 애플리케이션의 홈 페이지 구성 | Microsoft Docs
description: 빌더로서 Azure IoT Central 애플리케이션의 홈 페이지를 구성하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 599616f046b2eccee009e8a08e8a2f51cf968644
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772766"
---
# <a name="configuring-homepage"></a>홈 페이지 구성

홈 페이지는 애플리케이션에 대한 액세스 권한이 있는 사용자가 애플리케이션의 URL로 이동할 때 로드되는 페이지입니다. 애플리케이션을 만드는 동안 “샘플 Contoso” 또는 “샘플 Devkits” 애플리케이션 템플릿 중 하나를 선택한 경우 애플리케이션에 미리 정의된 홈 페이지가 있습니다. 반면 “사용자 지정 애플리케이션” 애플리케이션 템플릿을 선택한 경우 홈 페이지는 비어 있게 됩니다.

## <a name="add-tiles"></a>타일 추가

예를 들어 다음은 “샘플 Contoso” 템플릿을 기반으로 하는 애플리케이션에 대한 홈 페이지입니다. 애플리케이션에 대한 홈페이지를 사용자 지정하려면 먼저 오른쪽 위에 있는 **편집**을 선택합니다. 

![“샘플 Contoso” 템플릿을 기반으로 하는 애플리케이션에 대한 홈 페이지](media/howto-configure-homepage-experimental/image1.png)

**편집**을 선택하면 왼쪽 패널에서 대시보드 라이브러리가 열립니다. 홈 페이지를 사용자 지정하기 위해 추가할 수 있는 다양한 유형의 타일 및 대시보드 기본 형식이 있습니다.

![대시보드 라이브러리](media/howto-configure-homepage-experimental/image2.png)

예를 들어 **설정 및 속성** 타일을 추가하여 현재 설정 및 속성 값의 선택 항목을 표시할 수 있습니다. 이렇게 하려면 먼저 **디바이스 템플릿**을 선택한 다음, **디바이스 인스턴스**를 선택합니다. 타일에 제목을 지정한 후, 표시할 **설정** 또는 **속성**을 선택합니다. 이 경우에는 **온도 설정**을 선택했습니다. **완료**를 클릭하면 이 타일이 홈페이지에 표시됩니다.

![설정 및 속성에 대한 세부 정보가 있는 "디바이스 세부 정보 구성" 양식](media/howto-configure-homepage-experimental/image3.png)

이제 운영자가 홈 페이지를 볼 때 디바이스의 속성 또는 설정을 표시하는 이 타일을 확인할 수 있습니다.

![타일에 대한 설정 및 속성이 표시된 “대시보드” 탭](media/howto-configure-homepage-experimental/image4.png)

라이브러리에 있는 다른 다양한 타일 형식을 통해 애플리케이션의 홈 페이지를 한층 더 사용자 지정할 수 있는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 홈 페이지를 구성하는 방법을 배웠으므로 이제 다음 작업을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [이미지 준비 및 업로드하는 방법 알아보기](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
