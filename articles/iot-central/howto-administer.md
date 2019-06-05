---
title: Azure IoT Central 애플리케이션 관리 | Microsoft Docs
description: 관리자로 서 Azure IoT Central 응용 프로그램을 관리 하는 방법
author: viv-liu
ms.author: viviali
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 87ed31836fcda922b085ec951eb6d9d14542db6a
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467546"
---
# <a name="administer-your-iot-central-application"></a>IoT Central 애플리케이션 관리

IoT Central 애플리케이션을 만든 후 **관리** 섹션으로 이동할 수 있습니다.

- 응용 프로그램 설정 관리
- 사용자 관리
- 역할 관리
- 청구서 보기
- 평가판을 종량제로 변환
- 데이터 내보내기
- 디바이스 연결 관리
- 개발자 도구에 대 한 액세스 토큰을 사용 합니다.
- 응용 프로그램의 UI 사용자 지정
- 응용 프로그램에서 도움말 링크를 사용자 지정
- IoT Central을 프로그래밍 방식으로 관리

**관리** 섹션에 액세스하여 사용하려면 Azure IoT Central 애플리케이션에서 **관리자** 역할이어야 합니다. Azure IoT Central 애플리케이션을 만드는 사용자에게는 자동으로 해당 애플리케이션의 **관리자** 역할이 할당됩니다. 이 문서의 [사용자 관리](#manage-users) 섹션에서는 다른 사용자에게 **관리자** 역할을 할당하는 방법을 자세히 설명합니다.

## <a name="manage-application-settings"></a>응용 프로그램 설정 관리

### <a name="change-application-name-and-url"></a>애플리케이션 이름 및 URL 변경
**애플리케이션 설정** 페이지에서 애플리케이션의 이름 및 URL을 변경한 다음, **저장**을 선택합니다.

![애플리케이션 설정 페이지](media/howto-administer/image0-a.png)

이 페이지를 숨기는 옵션이 포함 되어 있습니다 관리자 응용 프로그램에 대 한 사용자 지정 테마를 만드는 경우 합니다 **응용 프로그램 이름** UI에 있습니다. 사용자 지정 테마에 응용 프로그램 로고 응용 프로그램 이름을 포함 하는 경우에 유용 합니다. 자세한 내용은 [Azure IoT Central UI 사용자 지정](./howto-customize-ui.md)합니다.

> [!Note]
> URL을 변경하면 이전 URL을 다른 Azure IoT Central 고객이 사용할 수 있습니다. 이 경우 해당 URL을 더 이상 사용할 수 없습니다. URL을 변경하면 이전 URL이 더 이상 작동하지 않으며 사용자에게 새 URL을 사용하라고 알려야 합니다.

### <a name="prepare-and-upload-image"></a>이미지 준비 및 업로드
애플리케이션 이미지를 변경하려면 [이미지를 준비하여 Azure IoT Central 애플리케이션에 업로드](howto-prepare-images.md)를 참조하세요.

### <a name="copy-an-application"></a>애플리케이션 복사
모든 장치 인스턴스, 장치 데이터 기록 및 사용자 데이터를 제외한 모든 애플리케이션의 복사본을 만들 수 있습니다. 복사에 대 한 청구는 종 량 제 응용 프로그램을 됩니다. 이 방식으로는 평가판 애플리케이션을 만들 수 없습니다.

선택 **복사**합니다. 대화 상자에서 새 종량제 애플리케이션에 대한 세부 정보를 입력합니다. 선택한 **복사** 계속할 것인지를 확인 합니다. [애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작에서 이 형식의 필드에 대해 자세히 알아보세요.

![애플리케이션 설정 페이지](media/howto-administer/appcopy2.png)

앱 복사 작업에 성공한 후 링크를 사용 하 여 새 응용 프로그램을 이동할 수 있습니다.

![애플리케이션 설정 페이지](media/howto-administer/appcopy3a.png)

> [!Note]
> 애플리케이션을 복사하면 규칙 및 작업의 정의도 복사됩니다. 그러나 원래 앱에 대한 액세스 권한이 있는 사용자는 복사한 앱에 복사되지 않으므로 사용자가 필수 구성 요소인 이메일 등의 작업에 사용자를 수동으로 추가해야 합니다. 일반적으로 새 앱에서 규칙 및 작업이 최신 상태인지 확인하는 것이 좋습니다.

### <a name="delete-an-application"></a>애플리케이션 삭제

> [!Note]
> 애플리케이션을 삭제하려면 애플리케이션을 만들 때 선택한 Azure 구독의 리소스를 삭제할 수 있는 권한도 필요합니다. 자세한 내용은 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 참조하세요.

IoT Central 애플리케이션을 영구적으로 삭제하려면 **삭제** 단추를 사용합니다. 이 작업 응용 프로그램과 연결 된 모든 데이터를 영구적으로 삭제 합니다.

## <a name="manage-users"></a>사용자 관리

### <a name="add-users"></a>사용자 추가

모든 사용자는 Azure IoT Central 애플리케이션에 로그인하고 액세스하려면 사용자 계정이 있어야 합니다. Azure IoT Central에서 지원되는 계정은 MSA(Microsoft 계정) 및 Azure AD(Azure Active Directory) 계정입니다. Azure Active Directory 그룹은 현재 Azure IoT Central에서 지원되지 않습니다.

자세한 내용은 [Microsoft 계정 도움말](https://support.microsoft.com/products/microsoft-account?category=manage-account) 및 [빠른 시작: Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)를 참조하세요.

1. IoT Central 애플리케이션에 사용자를 추가하려면 **관리** 섹션에 있는 **사용자** 페이지로 이동합니다.

    ![사용자 목록](media/howto-administer/image1.png)

1. 사용자를 추가하려면 **사용자** 페이지에서 **+ 사용자 추가**를 선택합니다.

1. **역할** 드롭다운 메뉴에서 사용자의 역할을 선택합니다. 이 문서의 [역할 관리](#manage-roles) 섹션에서 역할에 대해 자세히 알아보세요.

    ![역할 선택](media/howto-administer/image3.png)

    > [!NOTE]
    >  사용자를 대량으로 추가하려면 세미콜론을 구분 기호로 사용하여 추가할 모든 사용자의 사용자 ID를 입력합니다. **역할** 드롭다운 메뉴에서 역할을 선택합니다. 그런 다음 **저장**을 선택합니다.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>사용자에게 지정된 역할 편집

역할이 할당된 후에는 변경할 수 없습니다. 사용자에게 할당된 역할을 변경하려면 사용자를 삭제하고 다른 역할의 사용자를 추가해야 합니다.

### <a name="delete-users"></a>사용자 삭제

사용자를 삭제하려면 **사용자** 페이지에서 하나 이상의 확인란을 선택합니다. 그런 다음, **삭제**를 선택합니다.

## <a name="manage-roles"></a>역할 관리

역할을 통해 조직 내에서 다양한 IoT Central 작업을 수행할 수 있는 사용자를 제어할 수 있습니다. 애플리케이션 사용자에게 세 가지 역할을 할당할 수 있습니다.

### <a name="administrator"></a>관리자

**관리자** 역할의 사용자는 애플리케이션의 모든 기능에 액세스할 수 있습니다.

애플리케이션을 만드는 사용자에게는 자동으로 **관리자** 역할이 할당됩니다. **관리자** 역할의 사용자가 항상 1명 이상 있어야 합니다.

### <a name="application-builder"></a>애플리케이션 빌더

**애플리케이션 작성자** 역할의 사용자는 애플리케이션에서 애플리케이션 관리를 제외한 모든 일을 할 수 있습니다. 작성기 수 만들기, 편집 및 삭제 장치 템플릿 및 장치, 장치 집합을 관리 및 분석 및 작업을 실행 합니다. 작성자는 애플리케이션의 **관리** 섹션에 액세스할 수 없습니다.

### <a name="application-operator"></a>애플리케이션 운영자

**애플리케이션 운영자** 역할의 사용자는 디바이스 템플릿을 변경할 수 없으며 애플리케이션을 관리할 수 없습니다. 연산자 수 추가 삭제 장치, 장치 집합을 관리 하 고 분석 및 작업을 실행 합니다. 운영자는 **애플리케이션 작성자** 및 **관리** 페이지에 액세스할 수 없습니다.

## <a name="view-your-bill"></a>청구서 보기

청구서를 보려면 **관리** 섹션의 **청구** 페이지로 이동합니다. 새 탭에서 Azure 청구 페이지가 열리고, 여기서 각 Azure IoT Central 애플리케이션에 대한 청구서를 볼 수 있습니다.

### <a name="convert-your-trial-to-pay-as-you-go"></a>평가판을 종량제로 변환

평가판 애플리케이션을 종량제 애플리케이션으로 변환할 수 있습니다. 이러한 유형의 애플리케이션 간의 차이는 다음과 같습니다.

- **평가판** 만료 되기 전에 7 일 동안 응용 프로그램은 무료입니다. 만료되기 전에 언제든지 종량제로 변환할 수 있습니다.
- **종 량 제** 무료 처음 5 개 장치를 사용 하 여 장치, 응용 프로그램 요금이 청구 됩니다.

[Azure IoT Central 가격 책정 페이지](https://azure.microsoft.com/pricing/details/iot-central/)에서 가격 책정에 대해 자세히 알아보세요.

이 셀프 서비스 프로세스를 완료하려면 다음 단계를 수행합니다.

1. **관리** 섹션의 **청구** 페이지로 이동합니다.

    ![평가판 상태](media/howto-administer/freetrialbilling.png)

1. 선택 **종 량 제로 변환**합니다.

    ![평가판 변환](media/howto-administer/convert.png)

1. 적절한 Azure Active Directory를 선택한 다음, 종량제 애플리케이션에 사용할 Azure 구독을 선택합니다.

1. 선택한 후 **변환**, 응용 프로그램은 이제 종 량 제 응용 프로그램 및 요금이 청구를 시작 합니다.

## <a name="export-data"></a>데이터 내보내기

**연속 데이터 내보내기**를 사용하도록 설정하여 측정, 디바이스 및 디바이스 템플릿 데이터를 Azure Blob 스토리지 계정에 내보낼 수 있습니다. [데이터를 내보내는](howto-export-data.md) 방법에 대해 자세히 알아보세요.

## <a name="manage-device-connection"></a>디바이스 연결 관리

다음 키 및 인증서를 사용하여 애플리케이션에서 규모에 맞게 디바이스를 연결합니다. [디바이스 연결](concepts-connectivity.md)에 대해 자세히 알아보세요.

## <a name="use-access-tokens"></a>액세스 토큰 사용

개발자 도구에서 사용하기 위한 액세스 토큰을 생성합니다. 현재 사용할 수 있는 유일한 개발자 도구는 장치 메시지를 모니터링 하 고 속성 및 설정의 변경 내용에 대 한 IoT Central 탐색기입니다. [IoT Central 탐색기](howto-use-iotc-explorer.md)에 대해 자세히 알아보세요.

## <a name="customize-your-application"></a>응용 프로그램을 사용자 지정

응용 프로그램에서 아이콘과 색을 변경 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure IoT Central UI 사용자 지정](./howto-customize-ui.md)합니다.

## <a name="customize-help"></a>도움말을 사용자 지정

응용 프로그램에서 사용자 지정 도움말 링크를 추가 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure IoT Central UI 사용자 지정](./howto-customize-ui.md)합니다.

## <a name="manage-programatically"></a>프로그래밍 방식으로 관리

IoT Central Azure Resource Manager SDK 패키지는 Node, Python, C#, Ruby, Java 및 Go에 사용할 수 있습니다. 만들기, 나열, 업데이트 또는 IoT Central 응용 프로그램을 삭제 하려면 이러한 패키지를 사용할 수 있습니다. 패키지 인증, 오류 처리를 관리 하는 도우미를 포함 합니다.

[https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)에서 Azure Resource Manager SDK 사용 방법의 예제를 찾을 수 있습니다.

자세한 내용은 다음 GitHub 리포지토리 및 패키지를 참조 하세요.

| 언어 | 리포지토리 | 패키지 |
| ---------| ---------- | ------- |
| 노드 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [디바이스 템플릿 설정](howto-set-up-template.md)
