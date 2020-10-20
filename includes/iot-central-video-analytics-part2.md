---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 383cd286f89bde13f5e557792e980f0455e00917
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876752"
---
## <a name="deploy-and-configure-azure-media-services"></a>Azure Media Services 배포 및 구성

이 솔루션에서는 Azure Media Services 계정을 사용하여 IoT Edge 게이트웨이 디바이스에서 만든 개체 감지 비디오 클립을 저장합니다.

Media Services 계정을 만들 때 다음과 같이 해야 합니다.

- 계정 이름, Azure 구독, 위치, 리소스 그룹 및 스토리지 계정을 입력해야 합니다. Media Services 계정을 만들 때 기본 설정을 사용하여 새 스토리지 계정을 만듭니다.

- 지역으로 **미국 동부**를 선택합니다.

- **미국 동부** 지역에 Media Services 및 스토리지 계정에 사용할 *lva-rg*라는 새 리소스 그룹을 만듭니다. 자습서를 마친 후 *lva-rg* 리소스 그룹을 삭제하여 간단하게 모든 리소스를 제거할 수 있습니다.

[Azure Portal에서 Media Services 계정](https://portal.azure.com/?r=1#create/Microsoft.MediaService)을 만듭니다.

> [!TIP]
> 이 자습서의 모든 예제에서 **미국 동부** 지역을 사용합니다. 원한다면 본인과 가장 가까운 지역을 사용해도 됩니다.

*scratchpad.txt* 파일에 **Media Services** 계정 이름을 기록해 둡니다.

배포가 완료되면 **Media Services** 계정의 **속성** 페이지로 이동합니다. *scratchpad.txt* 파일에 **리소스 ID**를 기록해 둡니다. 나중에 IoT Edge 모듈을 구성할 때 이 값을 사용합니다.

다음으로, Media Services 리소스에 대한 Azure Active Directory 서비스 주체를 구성합니다. **API 액세스**를 선택한 다음, **서비스 주체 인증**을 선택합니다. Media Services 리소스와 동일한 이름을 사용하여 새 Azure Active Directory 앱을 만들고, *IoT Edge 액세스*라는 설명을 사용하여 비밀을 만듭니다.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Azure Media Services용 Azure AD 앱 구성":::

비밀이 생성되면 **서비스 보안 주체 애플리케이션에 연결하기 위해 자격 증명 복사** 섹션이 나올 때까지 아래로 스크롤합니다. **JSON**을 선택합니다. 여기서 모든 자격 증명 정보를 한 번에 복사할 수 있습니다. 이 정보를 *scratchpad.txt* 파일에 기록해 둡니다. 나중에 IoT Edge 디바이스를 구성할 때 이 정보를 사용합니다.

> [!WARNING]
> 비밀을 보고 저장할 수 있는 기회는 지금밖에 없습니다. 비밀을 분실하면 다른 비밀을 생성해야 합니다.

## <a name="create-the-azure-iot-central-application"></a>Azure IoT Central 애플리케이션 만들기

이 섹션에서는 템플릿에서 새 Azure IoT Central 애플리케이션을 만듭니다. 자습서 시리즈 전체에서 이 애플리케이션을 사용하여 완전한 솔루션을 빌드합니다.

새로운 Azure IoT Central 애플리케이션을 만들려면:

1. [Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트로 이동합니다.

1. Azure 구독에 액세스하는 데 사용하는 자격 증명으로 로그인합니다.

1. 새 Azure IoT Central 애플리케이션 만들기를 시작하려면 **빌드** 페이지에서 **새 애플리케이션**을 선택합니다.

1. **소매**를 선택합니다. 소매 페이지는 여러 소매점 애플리케이션 템플릿을 표시합니다.

새 비디오 분석 애플리케이션을 만드는 방법은 다음과 같습니다.

1. **비디오 분석 - 개체 및 동작 감지** 애플리케이션 템플릿을 선택합니다. 이 템플릿에는 이 자습서에서 사용되는 디바이스용 디바이스 템플릿이 포함되어 있습니다. 템플릿에는 운영자가 카메라 모니터링 및 관리와 같은 작업을 수행하는 데 사용할 수 있는 샘플 대시보드가 포함되어 있습니다.

1. 필요에 따라 친숙한 **애플리케이션 이름**을 선택합니다. 이 애플리케이션은 Northwind Traders라는 가상의 소매점을 기반으로 합니다. 이 자습서에서는 *Northwind Traders 비디오 분석*이라는 **애플리케이션 이름**을 사용합니다.

    > [!NOTE]
    > 친숙한 **애플리케이션 이름**을 사용하는 경우에도 애플리케이션 **URL**에 고유한 값을 사용해야 합니다.

1. Azure 구독이 있는 경우 해당하는 **디렉터리** 및 **Azure 구독**을 선택하고, **위치**로 **미국**을 선택합니다. 구독이 없는 경우 **7일 평가판**을 사용하도록 설정하고 필요한 연락처 정보를 작성할 수 있습니다. 이 자습서에서는 두 개의 디바이스(카메라 2대, IoT Edge 디바이스 하나)를 사용하므로, 평가판을 사용하지 않으면 사용량에 따라 요금이 청구됩니다.

    디렉터리, 구독 및 위치에 대한 자세한 내용은 [애플리케이션 만들기 빠른 시작](../articles/iot-central/core/quick-deploy-iot-central.md)을 참조하세요.

1. **만들기**를 선택합니다.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Azure Media Services용 Azure AD 앱 구성":::

### <a name="retrieve-the-configuration-data"></a>구성 데이터 검색

이 자습서의 뒷부분에서 IoT Edge 게이트웨이를 구성할 때 IoT Central 애플리케이션의 구성 데이터가 필요합니다. IoT Edge 디바이스를 애플리케이션에 등록하고 연결하려면 이 정보가 필요합니다.

**관리** 섹션에서 **사용자의 애플리케이션**을 선택하고 **애플리케이션 URL** 및 **애플리케이션 ID**를 *scratchpad.txt* 파일에 기록해 둡니다.

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="Azure Media Services용 Azure AD 앱 구성":::

**API 토큰**을 선택하고 **운영자** 역할에 대한 **LVAEdgeToken**이라는 새 토큰을 생성합니다.

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Azure Media Services용 Azure AD 앱 구성":::

나중에 사용할 수 있도록 *scratchpad.txt* 파일에 토큰을 기록해 둡니다. 대화 상자가 닫힌 후에는 토큰을 다시 볼 수 없습니다.

**관리** 섹션에서 **디바이스 연결**을 선택한 다음, **SAS-IoT-Devices**를 선택합니다.

*scratchpad.txt* 파일에 디바이스의 **기본 키**를 기록해 둡니다. 나중에 IoT Edge 디바이스를 구성할 때 이 *기본 그룹 공유 액세스 서명 토큰*을 사용합니다.
