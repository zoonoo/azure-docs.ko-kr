---
title: Azure IoT Central 응용 프로그램 관리 | Microsoft Docs
description: 관리자는 응용 프로그램 이름을 변경 하 고, URL을 변경 하 고, 이미지를 업로드 하 고, 응용 프로그램을 복사 및 삭제 하 여 Azure IoT Central 응용 프로그램을 관리 하는 방법
author: viv-liu
ms.author: viviali
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bdb13ef0d4f05802e4ab0f383c1bf3d82d9ba632
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880970"
---
# <a name="manage-your-iot-central-application"></a>IoT Central 응용 프로그램 관리

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

이 문서에서는 관리자가 응용 프로그램 이름 및 URL을 변경 하 여 응용 프로그램을 관리 하 고, 이미지를 업로드 하는 방법을 설명 하 고, Azure IoT Central 응용 프로그램에서 응용 프로그램을 복사 및 삭제 하는 방법을 배울 수 있습니다.

**관리** 섹션에 액세스하여 사용하려면 Azure IoT Central 애플리케이션에서 **관리자** 역할이어야 합니다. Azure IoT Central 애플리케이션을 만드는 사용자에게는 자동으로 해당 애플리케이션의 **관리자** 역할이 할당됩니다. 

## <a name="change-application-name-and-url"></a>애플리케이션 이름 및 URL 변경

**애플리케이션 설정** 페이지에서 애플리케이션의 이름 및 URL을 변경한 다음, **저장**을 선택합니다.

![애플리케이션 설정 페이지](media/howto-administer-pnp/image0-a.png)

관리자가 응용 프로그램에 대 한 사용자 지정 테마를 만들 경우이 페이지에는 UI에서 **응용 프로그램 이름을** 숨기는 옵션이 포함 됩니다. 이 기능은 사용자 지정 테마의 응용 프로그램 로고가 응용 프로그램 이름을 포함 하는 경우에 유용 합니다. 자세한 내용은 [Azure IOT CENTRAL UI 사용자 지정](./howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)을 참조 하세요.

> [!Note]
> URL을 변경하면 이전 URL을 다른 Azure IoT Central 고객이 사용할 수 있습니다. 이 경우 해당 URL을 더 이상 사용할 수 없습니다. URL을 변경하면 이전 URL이 더 이상 작동하지 않으며 사용자에게 새 URL을 사용하라고 알려야 합니다.

## <a name="prepare-and-upload-image"></a>이미지 준비 및 업로드

애플리케이션 이미지를 변경하려면 [이미지를 준비하여 Azure IoT Central 애플리케이션에 업로드](howto-prepare-images.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)를 참조하세요.

## <a name="copy-an-application"></a>애플리케이션 복사

모든 디바이스 인스턴스, 디바이스 데이터 기록 및 사용자 데이터를 제외한 모든 애플리케이션의 복사본을 만들 수 있습니다. 이 복사본은 요금이 청구 되는 종 량 제 응용 프로그램입니다. 이 방식으로는 평가판 애플리케이션을 만들 수 없습니다.

**복사**를 선택 합니다. 대화 상자에서 새 종량제 애플리케이션에 대한 세부 정보를 입력합니다. 그런 다음 **복사** 를 선택 하 여 계속 하 시겠습니까를 확인 합니다. [애플리케이션 만들기](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 빠른 시작에서 이 형식의 필드에 대해 자세히 알아보세요.

![애플리케이션 설정 페이지](media/howto-administer-pnp/appcopy2.png)

앱 복사 작업이 성공 하면 링크를 사용 하 여 새 응용 프로그램으로 이동할 수 있습니다.

![애플리케이션 설정 페이지](media/howto-administer-pnp/appcopy3a.png)

응용 프로그램을 복사 하면 규칙 및 전자 메일 작업의 정의도 복사 됩니다. 흐름, Logic Apps 등의 일부 작업은 규칙 ID를 통해 특정 규칙에 연결 됩니다. 다른 응용 프로그램에 규칙을 복사 하는 경우 규칙 ID를 가져옵니다. 이 경우 사용자는 새 작업을 만든 후 새 작업을 연결 해야 합니다. 일반적으로 규칙 및 작업을 확인 하 여 새 앱에서 최신 상태 인지 확인 하는 것이 좋습니다.

> [!WARNING]
> 대시보드는 특정 장치에 대 한 정보를 표시 하는 타일을 포함 하는 경우 요청 된 리소스를 새 응용 프로그램에서 **찾을 수 없는** 타일로 표시 합니다. 새 응용 프로그램의 장치에 대 한 정보를 표시 하려면 이러한 타일을 다시 구성 해야 합니다.

## <a name="delete-an-application"></a>애플리케이션 삭제

IoT Central 애플리케이션을 영구적으로 삭제하려면 **삭제** 단추를 사용합니다. 이 작업을 수행 하면 응용 프로그램과 연결 된 모든 데이터가 영구적으로 삭제 됩니다.

> [!Note]
> 애플리케이션을 삭제하려면 애플리케이션을 만들 때 선택한 Azure 구독의 리소스를 삭제할 수 있는 권한도 필요합니다. 자세한 내용은 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 참조하세요.


## <a name="manage-programatically"></a>프로그래밍 방식으로 관리

IoT Central Azure Resource Manager SDK 패키지는 Node, Python, C#, Ruby, Java 및 Go에 사용할 수 있습니다. 이러한 패키지를 사용 하 여 IoT Central 응용 프로그램을 생성, 나열, 업데이트 또는 삭제할 수 있습니다. 패키지에는 인증 및 오류 처리를 관리 하는 도우미가 포함 되어 있습니다.

[https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)에서 Azure Resource Manager SDK 사용 방법의 예제를 찾을 수 있습니다.

자세히 알아보려면 다음 GitHub 리포지토리 및 패키지를 참조 하세요.

| 언어 | 리포지토리 | 패키지 |
| ---------| ---------- | ------- |
| 노드 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>다음 단계
 
이제 Azure IoT Central 응용 프로그램을 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 Azure IoT Central에서 [사용자 및 역할 관리](howto-manage-users-roles-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 에 대해 알아보는 것입니다.