---
title: Azure IoT Central 응용 프로그램 관리 | Microsoft Docs
description: 관리자로서 Azure IoT Central 응용 프로그램을 관리하는 방법을 알아봅니다.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1bb0bc0aa7ad6bbbad502832ba8e0a96f36de428
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268308"
---
# <a name="administer-your-iot-central-application"></a>IOT Central 응용 프로그램 관리

Microsoft Azure IoT Central 응용 프로그램을 만든 후에는 Azure IoT Central 사용자 인터페이스의 **관리** 섹션을 사용하여 응용 프로그램을 관리할 수 있습니다. **관리** 섹션으로 이동하려면 왼쪽 탐색 메뉴에서 **관리**를 선택합니다.

**관리** 섹션에서 다음과 같은 일을 할 수 있습니다.

- 사용자 관리

- 역할 관리

- 대금 청구 정보 보기

- 응용 프로그램 설정 관리

- 평가판 제공

**관리** 섹션의 보조 탐색 메뉴에는 다양한 관리 작업의 링크가 있습니다.

**관리** 섹션에 액세스하여 사용하려면 Azure IoT Central 응용 프로그램에서 **관리자** 역할이어야 합니다. Azure IoT Central 응용 프로그램을 만드는 사용자에게는 자동으로 해당 응용 프로그램의 **관리자** 역할이 할당됩니다. 이 문서의 *사용자 관리* 섹션에서는 다른 사용자에게 **관리자** 역할을 할당하는 방법을 설명합니다.

## <a name="change-application-name"></a>응용 프로그램 이름 변경

응용 프로그램 이름을 변경하려면 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **응용 프로그램 설정** 페이지로 이동합니다.

**응용 프로그램 설정** 페이지에서 **응용 프로그램 이름** 필드에 원하는 이름을 입력합니다. 그런 다음 **저장**을 선택합니다.

## <a name="change-the-application-url"></a>응용 프로그램 URL 변경

응용 프로그램 URL을 변경하려면 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **응용 프로그램 설정** 페이지로 이동합니다.

![응용 프로그램 설정 페이지](media\howto-administer\image0-a.png)

**응용 프로그램 설정** 페이지에서 **URL** 필드에 원하는 URL을 입력한 다음, **저장**을 선택합니다. URL은 200자 이하여야 합니다. 사용할 수 없는 URL인 경우 유효성 검사 오류가 발생합니다.

> [!Note]
> URL을 변경하면 이전 URL을 다른 Azure IoT Central 고객이 사용할 수 있습니다. 이 경우 해당 URL을 더 이상 사용할 수 없습니다. URL을 변경하면 이전 URL이 더 이상 작동하지 않으며 사용자에게 새 URL을 사용하라고 알려야 합니다.

## <a name="change-the-application-image"></a>응용 프로그램 이미지 변경

Azure IoT Central 응용 프로그램에서 이미지를 사용하는 방법에 대한 자세한 내용은 [이미지를 준비하여 Azure IoT Central 응용 프로그램에 업로드](howto-prepare-images.md)를 참조하세요.

## <a name="copy-an-application"></a>응용 프로그램 복사

모든 장치 인스턴스, 장치 데이터 기록 및 사용자 데이터를 제외한 모든 응용 프로그램의 복사본을 만들 수 있습니다. 복사본은 요금이 청구되는 유료 응용 프로그램입니다. 다른 응용 프로그램을 복사하여 평가판 응용 프로그램을 만들 수 없습니다.

응용 프로그램을 복사하려면 **응용 프로그램 설정** 페이지로 이동합니다. 그런 다음, **복사** 단추를 선택합니다.

![응용 프로그램 설정 페이지](media\howto-administer\appCopy1.png)

**복사** 단추를 선택하면 복사하여 생성될 새 응용 프로그램에 대한 이름, URL, Azure AD 디렉터리, 구독 및 Azure 지역을 선택할 수 있는 대화 상자가 열립니다. 이러한 각 필드의 값을 선택합니다. 그런 다음, **복사** 단추를 클릭하여 진행 여부를 확인합니다. [응용 프로그램 만드는 방법](howto-create-application.md)에 대한 이 문서에서 해당 값의 입력 내용을 자세히 알아볼 수 있습니다.

![응용 프로그램 설정 페이지](media\howto-administer\appCopy2.png)

앱 복사 작업이 성공하면 응용 프로그램을 복사하여 만들어진 새 응용 프로그램으로 이동할 수 있습니다. 앱으로 이동하려면 **응용 프로그램 설정** 페이지에 나타나는 링크를 선택합니다.

![응용 프로그램 설정 페이지](media\howto-administer\appCopy3.png)

> [!Note]
> 응용 프로그램을 복사하면 규칙 또는 작업의 정의도 복사됩니다. 그러나 원래 앱에 대한 액세스 권한이 있는 사용자는 복사한 앱에 복사되지 않으므로 사용자가 필수 구성 요소인 메일 등의 작업에 사용자를 수동으로 추가해야 합니다.

## <a name="delete-an-application"></a>응용 프로그램 삭제

응용 프로그램을 삭제하려면 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **응용 프로그램 설정** 페이지로 이동합니다.

**삭제**를 선택합니다.

> [!Note]
> 응용 프로그램을 삭제하면 해당 응용 프로그램과 연결된 모든 데이터가 영구적으로 삭제됩니다.  응용 프로그램을 삭제하려면 응용 프로그램을 만들 때 선택한 Azure 구독의 리소스를 삭제할 수 있는 권한도 필요합니다. 자세한 내용은 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 참조하세요.

## <a name="roles-in-azure-iot-central"></a>Azure IoT Central의 역할

역할을 통해 조직 내에서 다양한 Azure IoT Central 작업을 수행할 수 있는 사용자를 제어할 수 있습니다. Azure IoT Central에서는 응용 프로그램 사용자에게 세 가지 역할을 할당할 수 있습니다. 역할은 각 응용 프로그램을 통해 할당됩니다. 동일한 사용자가 서로 다른 응용 프로그램에서 여러 역할을 가질 수 있습니다. 동일한 사용자를 응용 프로그램 내의 여러 역할에 할당할 수 있습니다.

### <a name="administrator"></a>관리자

**관리자** 역할의 사용자는 Azure IoT Central 응용 프로그램의 모든 기능에 액세스할 수 있습니다.

응용 프로그램을 만드는 사용자에게는 자동으로 **관리자** 역할이 할당됩니다. **관리자** 역할의 사용자가 항상 1명 이상 있어야 합니다.

### <a name="application-builder"></a>응용 프로그램 작성자

**응용 프로그램 작성자** 역할의 사용자는 Azure IoT Central 응용 프로그램에서 응용 프로그램 관리를 제외한 모든 일을 할 수 있습니다.

### <a name="application-operator"></a>응용 프로그램 운영자

**응용 프로그램 운영자** 역할의 사용자는 **응용 프로그램 작성자** 페이지에 액세스할 수 없습니다. 이들은 응용 프로그램을 관리할 수 없습니다.

## <a name="manage-users"></a>사용자 관리

응용 프로그램 관리자는 응용 프로그램의 역할에 사용자를 할당할 수 있습니다.

### <a name="add-users"></a>사용자 추가

모든 사용자는 Azure IoT Central 응용 프로그램에 로그인하고 액세스하려면 사용자 계정이 있어야 합니다. Azure IoT Central에서 지원되는 계정은 MSA(Microsoft 계정) 및 Azure AD(Azure Active Directory) 계정입니다. Azure Active Directory 그룹은 현재 Azure IoT Central에서 지원되지 않습니다.

자세한 내용은 [Microsoft 계정 도움말](https://support.microsoft.com/products/microsoft-account?category=manage-account) 및 [빠른 시작: Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)를 참조하세요.

1. Azure IoT Central 응용 프로그램에 사용자 계정을 추가하려면 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **사용자** 페이지로 이동합니다.

    ![사용자 목록](media\howto-administer\image1.png)

1. 사용자를 추가하려면 **사용자** 페이지에서 **+ 사용자 추가**를 선택합니다.

    ![사용자 추가](media\howto-administer\image2.png)

1. **역할** 드롭다운 메뉴에서 사용자의 역할을 선택합니다. 역할에 대한 자세한 정보는 이 문서의 *Azure IoT Central의 역할* 섹션을 참조하세요.

    ![역할 선택](media\howto-administer\image3.png)

    > [!NOTE]
    >  사용자를 대량으로 추가하려면 세미콜론을 구분 기호로 사용하여 추가할 모든 사용자의 사용자 ID를 입력합니다. **역할** 드롭다운 메뉴에서 역할을 선택합니다. 그런 다음 **저장**을 선택합니다.

1. 사용자를 추가하면 **사용자** 페이지에 해당 사용자에 대한 항목이 표시됩니다.

    ![사용자 목록](media\howto-administer\image4.png)

### <a name="edit-the-roles-that-are-assigned-to-users"></a>사용자에게 지정된 역할 편집

역할이 할당된 후에는 변경할 수 없습니다. 사용자에게 할당된 역할을 변경하려면 사용자를 삭제하고 다른 역할의 사용자를 추가해야 합니다.

### <a name="delete-users"></a>사용자 삭제

사용자를 삭제하려면 **사용자** 페이지에서 하나 이상의 확인란을 선택합니다. 그런 다음, **삭제**를 선택합니다.

## <a name="view-your-bill"></a>청구서 보기

청구서를 보려면 **관리** 섹션의 **청구** 페이지로 이동합니다. 그런 다음, **청구**를 선택합니다. 새 탭에서 Azure 청구 페이지가 열리고, 여기서 각 Azure IoT Central 응용 프로그램에 대한 청구서를 볼 수 있습니다.

## <a name="convert-your-trial-to-a-paid-application"></a>평가판을 유료 응용 프로그램으로 변환

IoT Central을 평가한 후 평가판을 유료 응용 프로그램으로 변환할 수 있습니다. 이 셀프 서비스 프로세스를 완료하려면 다음 단계를 수행합니다.

1. 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **청구** 페이지로 이동합니다. 평가판을 연장하지 않은 경우 다음 스크린샷과 같은 페이지가 표시됩니다.

    ![평가판 상태](media/howto-administer/freetrial.png)

2. **유료 응용 프로그램으로 변환**을 선택합니다. 평가판을 연장하지 않은 경우 다음 스크린샷과 같은 팝업 창이 표시됩니다.

    ![평가판 연장](media/howto-administer/extend.png)

3. 팝업 창에서 적절한 Azure Active Directory 테넌트를 선택한 다음, IoT Central 응용 프로그램에 사용할 Azure 구독을 선택합니다.

3. **변환**을 선택하면 평가판이 유료 응용 프로그램으로 변환되고 대금 청구가 시작됩니다.

## <a name="extend-your-free-trial"></a>평가판 연장

기본적으로 모든 평가판은 7일 동안 사용할 수 있습니다. 평가판 기간을 30일로 연장하려면 다음 단계를 수행합니다.

1. 보조 탐색 메뉴를 사용하여 **관리** 섹션의 **청구** 페이지로 이동합니다.

1. **평가판 연장**을 선택합니다. 팝업 창에서 적절한 Azure Active Directory 테넌트를 선택한 다음, IoT Central 응용 프로그램에 사용할 Azure 구독을 선택합니다.

1. 그런 다음, **연장**을 선택합니다. 이제 평가판이 30일 동안 유효합니다.

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>제어 평면 작업에 Azure SDK 사용

IoT Central Azure Resource Manager SDK 패키지는 Node, Python, C#, Ruby, Java 및 Go에 사용할 수 있습니다. 이러한 라이브러리는 IoT Central의 제어 평면 작업을 지원하므로, IoT Central 응용 프로그램을 만들거나 나열, 업데이트 또는 삭제할 수 있습니다. 또한 각 언어에 특정한 인증 및 오류 처리를 다루기 위한 도우미를 제공합니다. 

[https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)에서 Azure Resource Manager SDK 사용 방법의 예제를 찾을 수 있습니다.

자세한 내용을 보려면 GitHub에서 이러한 패키지를 살펴보세요.

| 언어 | 리포지토리 | 패키지 |
| ---------| ---------- | ------- |
| 노드 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| 자바 | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램을 관리하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [장치 템플릿 설정](howto-set-up-template.md)
