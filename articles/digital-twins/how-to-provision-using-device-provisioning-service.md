---
title: 장치 프로 비전 서비스를 사용 하 여 장치 자동 관리
titleSuffix: Azure Digital Twins
description: 장치 프로 비전 서비스 (DPS)를 사용 하 여 Azure Digital Twins에서 IoT 장치를 프로 비전 하 고 사용 중지 하는 자동화 된 프로세스를 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a571d92dd9663c7d2d0a576b59e5cd2b3352cb76
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951104"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>DPS (장치 프로 비전 서비스)를 사용 하 여 Azure Digital Twins의 장치 자동 관리

이 문서에서는 Azure Digital Twins를 [DPS (장치 프로 비전 서비스)](../iot-dps/about-iot-dps.md)와 통합 하는 방법에 대해 알아봅니다.

이 문서에서 설명 하는 솔루션을 통해 장치 프로 비전 서비스를 사용 하 여 Azure Digital Twins에서 IoT Hub 장치를 **_프로 비전_** 하 고 사용 **_중지_** 하는 프로세스를 자동화할 수 있습니다. 

_프로 비전_ 및 사용 _중지_ 단계에 대 한 자세한 내용 및 모든 엔터프라이즈 IoT 프로젝트에 공통적인 일반적인 장치 관리 단계 집합을 보다 잘 이해 하려면 IoT Hub의 장치 관리 설명서에서 [ *장치 수명 주기* 섹션](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) 을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

프로 비전을 설정 하려면 먼저 다음을 설정 해야 합니다.
* **Azure Digital Twins 인스턴스** [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md) 의 지침에 따라 Azure digital 쌍 인스턴스를 만듭니다. Azure Portal ([명령](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))에서 인스턴스의 **_호스트 이름을_** 수집 합니다.
* **IoT hub**. 지침은이 [IoT Hub 빠른](../iot-hub/quickstart-send-telemetry-cli.md)시작의 *IoT Hub 만들기* 섹션을 참조 하세요.
* IoT Hub 데이터를 기반으로 디지털 쌍 정보를 업데이트 하는 [**Azure 함수**](../azure-functions/functions-overview.md) 입니다. [*방법: IoT hub 데이터 수집*](how-to-ingest-iot-hub-data.md) 에 설명 된 지침에 따라이 Azure 함수를 만듭니다. 이 문서에서 사용할 함수 **_이름을_** 수집 합니다.

또한이 샘플에서는 장치 프로 비전 서비스를 사용 하 여 프로 비전을 포함 하는 **장치 시뮬레이터** 를 사용 합니다. 장치 시뮬레이터는 [Azure Digital Twins 및 IoT Hub 통합 샘플](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)에 있습니다. 샘플 링크로 이동 하 고 제목 아래에서 *ZIP 다운로드* 단추를 선택 하 여 컴퓨터에서 샘플 프로젝트를 가져옵니다. 다운로드 한 폴더의 압축을 풉니다.

컴퓨터에 [**Node.js**](https://nodejs.org/download) 설치 되어 있어야 합니다. 장치 시뮬레이터는 **Node.js** 버전 10.0. x 이상을 기반으로 합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

아래 이미지는 장치 프로 비전 서비스와 함께 Azure Digital Twins를 사용 하는이 솔루션의 아키텍처를 보여 줍니다. 장치 프로 비전 및 사용 중지 흐름이 모두 표시 됩니다.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="종단 간 시나리오에서 장치 및 여러 Azure 서비스의 다이어그램 데이터는 자동 온도 조절기 장치와 DPS 사이에서 앞뒤로 흐릅니다. 또한 데이터는 DPS에서 IoT Hub로, ' 할당 ' 레이블이 지정 된 Azure 함수를 통해 Azure Digital Twins로 흐릅니다. 수동 ' 장치 삭제 ' 작업의 데이터는 IoT Hub > Event Hubs > Azure Functions Azure Digital Twins를 통해 흐릅니다." lightbox="media/how-to-provision-using-dps/flows.png":::

이 문서는 다음 두 개의 섹션으로 구분됩니다.
* [*장치 프로 비전 서비스를 사용 하 여 장치 자동 프로 비전*](#auto-provision-device-using-device-provisioning-service)
* [*IoT Hub 수명 주기 이벤트를 사용 하 여 장치 자동 사용 중지*](#auto-retire-device-using-iot-hub-lifecycle-events)

아키텍처의 각 단계에 대 한 자세한 설명은이 문서의 뒷부분에 있는 개별 섹션을 참조 하십시오.

## <a name="auto-provision-device-using-device-provisioning-service"></a>장치 프로 비전 서비스를 사용 하 여 장치 자동 프로 비전

이 섹션에서는 장치 프로 비전 서비스를 Azure Digital Twins에 연결 하 여 아래 경로를 통해 장치를 자동으로 프로 비전 합니다. 이는 [앞](#solution-architecture)에서 설명한 전체 아키텍처에서 발췌 한 것입니다.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="프로 비전 흐름의 다이어그램-흐름의 레이블이 지정 된 숫자 레이블 섹션의 솔루션 아키텍처 다이어그램에 대 한 발췌 한 것입니다. 자동 온도 조절기 장치와 DPS 간에 데이터 흐름이 전달 됩니다 (장치 > DPS의 경우 1, DPS > 장치의 경우 5). 또한 데이터는 DPS에서 IoT Hub (4)로 이동 하 고 ' 할당 ' 이라는 레이블이 지정 된 Azure 함수 (2)를 통해 Azure Digital Twins (3)로 흐릅니다." lightbox="media/how-to-provision-using-dps/provision.png":::

프로세스 흐름에 대 한 설명은 다음과 같습니다.
1. 장치는 DPS 끝점에 연결 하 고 식별 정보를 전달 하 여 id를 증명 합니다.
2. DPS는 등록 목록에 대해 등록 ID 및 키의 유효성을 검사 하 여 장치 id의 유효성을 검사 하 고 [Azure function](../azure-functions/functions-overview.md) 을 호출 하 여 할당을 수행 합니다.
3. Azure function은 장치에 대 한 Azure Digital Twins [에 새 쌍](concepts-twins-graph.md) 을 만듭니다. 쌍은 장치의 **등록 ID** 와 동일한 이름을 갖습니다.
4. DPS는 IoT hub에 장치를 등록 하 고 장치의 원하는 쌍 상태를 채웁니다.
5. IoT hub는 장치 ID 정보 및 IoT hub 연결 정보를 장치에 반환 합니다. 이제 장치에서 IoT hub에 연결할 수 있습니다.

다음 섹션에서는이 자동 프로 비전 장치 흐름을 설정 하는 단계를 안내 합니다.

### <a name="create-a-device-provisioning-service"></a>Device Provisioning Service 만들기

장치 프로 비전 서비스를 사용 하 여 새 장치를 프로 비전 하는 경우 등록 ID와 동일한 이름을 사용 하 여 Azure Digital Twins에서 해당 장치에 대 한 새 쌍을 만들 수 있습니다.

IoT 장치를 프로 비전 하는 데 사용 되는 장치 프로 비전 서비스 인스턴스를 만듭니다. 아래 Azure CLI 지침을 사용 하거나 Azure Portal를 사용할 수 있습니다. 빠른 시작 [*: Azure Portal*](../iot-dps/quick-setup-auto-provision.md)를 사용 하 여 IoT Hub Device Provisioning Service를 설정 합니다.

다음 Azure CLI 명령은 장치 프로 비전 서비스를 만듭니다. 장치 프로 비전 서비스 이름, 리소스 그룹 및 지역을 지정 해야 합니다. 장치 프로 비전 서비스를 지 원하는 지역을 확인 하려면 [*지역별 사용 가능한 Azure 제품*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)을 방문 하세요.
[컴퓨터에](/cli/azure/install-azure-cli)Azure CLI가 설치 되어 있는 경우이 명령은 [Cloud Shell](https://shell.azure.com)또는 로컬로 실행할 수 있습니다.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>장치 프로 비전 서비스에 사용할 함수를 추가 합니다.

[필수 구성 요소](#prerequisites) 섹션에서 만든 함수 앱 프로젝트 내에서 장치 프로 비전 서비스에 사용할 새 함수를 만듭니다. 이 함수는 [사용자 지정 할당 정책의](../iot-dps/how-to-use-custom-allocation-policies.md) 장치 프로 비전 서비스에서 새 장치를 프로 비전 하는 데 사용 됩니다.

먼저 컴퓨터의 Visual Studio에서 함수 앱 프로젝트를 열고 다음 단계를 수행 합니다.

#### <a name="step-1-add-a-new-function"></a>1 단계: 새 함수 추가 

Visual Studio의 함수 앱 프로젝트에 *HTTP 트리거* 형식의 새 함수를 추가 합니다.

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="함수 앱 프로젝트에서 Http 트리거 형식의 Azure 함수를 추가 하기 위한 Visual Studio 뷰의 스크린샷" lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>2 단계: 함수 코드 입력

프로젝트에 새 NuGet 패키지를 추가 [합니다..](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) 코드에 사용 된 패키지가 프로젝트에 아직 포함 되지 않은 경우 프로젝트에도 더 많은 패키지를 추가 해야 할 수 있습니다.

새로 만든 함수 코드 파일에서 다음 코드를 붙여넣고 함수 이름을 *DpsAdtAllocationFunc* 로 바꾼 후 파일을 저장 합니다.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>3 단계: Azure에 함수 앱 게시

*DpsAdtAllocationFunc* 함수를 사용 하 여 프로젝트를 Azure의 함수 앱에 게시 합니다.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>장치 프로 비전 등록 만들기

다음으로 **사용자 지정 할당 함수** 를 사용 하 여 장치 프로 비전 서비스에서 등록을 만들어야 합니다. 장치 프로 비전 서비스 설명서의 사용자 지정 할당 정책 문서의 [*등록 만들기*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) 섹션에서 지침에 따라이 작업을 수행 합니다.

이 흐름을 진행 하는 동안 다음 옵션을 선택 하 여 등록을 방금 만든 함수에 연결 해야 합니다.

* **허브에 장치를 할당 하는 방법을 선택** 합니다. 사용자 지정 (Azure 함수 사용).
* **이 그룹을 할당할 수 있는 IoT hub를 선택 합니다.** IoT hub 이름을 선택 하거나 *새 iot Hub 연결* 단추를 선택 하 고 드롭다운에서 iot hub를 선택 합니다.

그런 다음 *새 함수 선택* 단추를 선택 하 여 함수 앱을 등록 그룹에 연결 합니다. 그런 다음, 다음 값을 입력 합니다.

* **구독**: Azure 구독이 자동으로 채워집니다. 올바른 구독 인지 확인 합니다.
* **함수 앱**: 함수 앱 이름을 선택 합니다.
* **함수**: DpsAdtAllocationFunc를 선택 합니다.

세부 정보를 저장 합니다.                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="사용자 지정 (Azure 함수 사용) 및 IoT hub 이름을 선택 하는 세관 등록 그룹 세부 정보 창의 스크린샷 섹션에서 허브에 장치를 할당 하는 방법 선택 및이 그룹을 할당할 수 있는 IoT hub 선택 또한 드롭다운에서 구독, 함수 앱을 선택 하 고 DpsAdtAllocationFunc를 선택 해야 합니다." lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

등록을 만든 후 등록에 대 한 **기본 키는** 나중에이 문서에 대 한 장치 시뮬레이터를 구성 하는 데 사용 됩니다.

### <a name="set-up-the-device-simulator"></a>장치 시뮬레이터 설정

이 샘플에서는 장치 프로 비전 서비스를 사용 하 여 프로 비전을 포함 하는 장치 시뮬레이터를 사용 합니다. 장치 시뮬레이터는 [Azure Digital Twins 및 IoT Hub 통합 샘플](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)에 있습니다. 샘플을 아직 다운로드 하지 않은 경우 샘플 링크로 이동 하 여 제목 아래에 있는 *ZIP 다운로드* 단추를 선택 하 여 지금 가져옵니다. 다운로드 한 폴더의 압축을 풉니다.

#### <a name="upload-the-model"></a>모델 업로드

장치 시뮬레이터는이 ID로 모델을 사용 하는 자동 온도 조절기 형식 장치 `dtmi:contosocom:DigitalTwins:Thermostat;1` 입니다. 장치에 대해이 유형의 쌍을 만들려면 먼저이 모델을 Azure Digital Twins에 업로드 해야 합니다.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

모델에 대 한 자세한 내용은 [*방법: 모델 관리*](how-to-manage-model.md#upload-models)를 참조 하세요.

#### <a name="configure-and-run-the-simulator"></a>시뮬레이터 구성 및 실행

명령 창에서 다운로드 한 샘플 *Azure Digital Twins* 로 이동 하 IoT Hub 고 이전에 압축을 푼 다음, *장치 시뮬레이터* 디렉터리로 이동 합니다. 다음으로 다음 명령을 사용 하 여 프로젝트에 대 한 종속성을 설치 합니다.

```cmd
npm install
```

그런 다음, 장치 시뮬레이터 디렉터리에서 env 파일을 env 라는 새 파일에 복사 하 고 다음 값을 수집 하 여 설정을 채웁니다.

* PROVISIONING_IDSCOPE:이 값을 얻으려면 [Azure Portal](https://portal.azure.com/)에서 장치 프로 비전 서비스로 이동한 다음 메뉴 옵션에서 *개요* 를 선택 하 고 필드 *ID 범위* 를 찾습니다.

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="ID 범위 값을 복사 하기 위한 장치 프로 비전 개요 페이지의 Azure Portal 보기 스크린샷" lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: 장치에 대 한 등록 ID를 선택할 수 있습니다.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: 이전에 설정한 등록의 기본 키입니다. 이 값을 다시 얻으려면 Azure Portal에서 장치 프로 비전 서비스로 이동 하 고 *등록 관리* 를 선택한 다음 이전에 만든 등록 그룹을 선택 하 고 *기본 키* 를 복사 합니다.

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="SAS 기본 키 값을 복사 하는 장치 프로 비전 서비스 등록 관리 페이지 Azure Portal 보기의 스크린샷" lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

이제 위의 값을 사용 하 여 env 파일 설정을 업데이트 합니다.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

파일을 저장하고 닫습니다.

### <a name="start-running-the-device-simulator"></a>장치 시뮬레이터 실행 시작

명령 창의 *device 시뮬레이터* 디렉터리에서 다음 명령을 사용 하 여 장치 시뮬레이터를 시작 합니다.

```cmd
node .\adt_custom_register.js
```

장치를 등록 하 고 IoT Hub에 연결한 다음 메시지 보내기를 시작 해야 합니다.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="장치 등록 및 메시지 보내기를 보여 주는 명령 창의 스크린샷" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>유효성 검사

이 문서에서 설정한 흐름의 결과로 장치가 Azure Digital Twins에 자동으로 등록 됩니다. 다음 [Azure Digital TWINS CLI](how-to-use-cli.md) 명령을 사용 하 여 만든 Azure Digital twins 인스턴스에서 장치의 쌍을 찾습니다.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Azure Digital Twins 인스턴스에서 발견 되는 장치의 쌍이 표시 되어야 합니다.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="새로 만든 쌍을 보여 주는 명령 창의 스크린샷" lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>IoT Hub 수명 주기 이벤트를 사용 하 여 장치 자동 사용 중지

이 섹션에서는 Azure Digital Twins에 IoT Hub 수명 주기 이벤트를 연결 하 여 아래 경로를 통해 장치의 자동 사용을 중지 합니다. 이는 [앞](#solution-architecture)에서 설명한 전체 아키텍처에서 발췌 한 것입니다.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="사용 중지 장치 흐름의 다이어그램-솔루션 아키텍처 다이어그램의 발췌 이며, 흐름의 레이블 지정 섹션에는 숫자가 있습니다. 자동 온도 조절기 장치는 다이어그램의 Azure 서비스에 연결 되지 않은 상태로 표시 됩니다. 수동 ' 장치 삭제 ' 작업의 데이터는 IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital Twins (3)를 통해 흐릅니다." lightbox="media/how-to-provision-using-dps/retire.png":::

프로세스 흐름에 대 한 설명은 다음과 같습니다.
1. 외부 또는 수동 프로세스는 IoT Hub에서 장치 삭제를 트리거합니다.
2. IoT Hub는 장치를 삭제 하 고 [이벤트 허브](../event-hubs/event-hubs-about.md)로 라우팅되는 [장치 수명 주기](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) 이벤트를 생성 합니다.
3. Azure function은 Azure Digital Twins에서 장치 쌍을 삭제 합니다.

다음 섹션에서는이 자동 사용 중지 장치 흐름을 설정 하는 단계를 안내 합니다.

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

다음으로 IoT Hub 수명 주기 이벤트를 수신 하는 Azure [이벤트 허브](../event-hubs/event-hubs-about.md) 를 만듭니다. 

[*이벤트 허브 만들기*](../event-hubs/event-hubs-create.md) 빠른 시작에 설명 된 단계를 따릅니다. 이벤트 허브의 이름을 *lifecycleevents* 로 합니다. 다음 섹션에서 IoT Hub 경로와 Azure 함수를 설정할 때이 이벤트 허브 이름을 사용 합니다.

아래 스크린샷에서는 이벤트 허브를 만드는 방법을 보여 줍니다.
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="Lifecycleevents 이름으로 이벤트 허브를 만드는 Azure Portal 창의 스크린샷" lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>이벤트 허브에 대 한 SAS 정책 만들기

그런 다음 함수 앱을 사용 하 여 이벤트 허브를 구성 하는 [SAS (공유 액세스 서명) 정책을](../event-hubs/authorize-access-shared-access-signature.md) 만들어야 합니다.
이 작업을 수행하려면
1. Azure Portal에서 방금 만든 이벤트 허브로 이동 하 고 왼쪽의 메뉴 옵션에서 **공유 액세스 정책** 을 선택 합니다.
2. **추가** 를 선택합니다. *SAS 정책 추가* 창이 열리면 원하는 정책 이름을 입력 하 고 *수신 대기* 확인란을 선택 합니다.
3. **만들기** 를 선택합니다.
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="이벤트 허브 SAS 정책을 추가 하는 Azure Portal의 스크린샷" lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>함수 앱을 사용 하 여 이벤트 허브 구성

그런 다음 [필수 구성 요소](#prerequisites) 섹션에서 설정한 Azure 함수 앱을 구성 하 여 새 이벤트 허브를 사용 합니다. 이렇게 하려면 이벤트 허브의 연결 문자열을 사용 하 여 함수 앱 내에 환경 변수를 설정 합니다.

1. 방금 만든 정책을 열고 **연결 문자열-기본 키** 값을 복사 합니다.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="연결 문자열-기본 키를 복사 하는 Azure Portal의 스크린샷" lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. 다음 Azure CLI 명령을 사용 하 여 함수 앱 설정에서 연결 문자열을 변수로 추가 합니다. [컴퓨터에](/cli/azure/install-azure-cli)Azure CLI가 설치 되어 있는 경우이 명령은 [Cloud Shell](https://shell.azure.com)또는 로컬로 실행할 수 있습니다.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>IoT Hub 수명 주기 이벤트를 사용 하 여 사용 중지 하는 함수 추가

[전제 조건](#prerequisites) 섹션에서 만든 함수 앱 프로젝트 내에서 IoT Hub 수명 주기 이벤트를 사용 하 여 기존 장치를 사용 중지 하는 새 함수를 만듭니다.

수명 주기 이벤트에 대 한 자세한 내용은 [*IoT Hub 비 원격 분석 이벤트*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조 하세요. Azure 함수에서 Event Hubs를 사용 하는 방법에 대 한 자세한 내용은 [*Azure Functions에 대 한 azure Event Hubs 트리거*](../azure-functions/functions-bindings-event-hubs-trigger.md)를 참조 하세요.

먼저 컴퓨터의 Visual Studio에서 함수 앱 프로젝트를 열고 다음 단계를 수행 합니다.

#### <a name="step-1-add-a-new-function"></a>1 단계: 새 함수 추가
     
*Event Hub 트리거* 형식의 새 함수를 Visual Studio의 함수 앱 프로젝트에 추가 합니다.

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="함수 앱 프로젝트에서 Event Hub 트리거 형식의 Azure 함수를 추가 하기 위한 Visual Studio 창의 스크린샷." lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>2 단계: 함수 코드 입력

새로 만든 함수 코드 파일에서 다음 코드를 붙여넣고, 함수의 이름을로 바꾸고 `DeleteDeviceInTwinFunc.cs` , 파일을 저장 합니다.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>3 단계: Azure에 함수 앱 게시

*Deletedeviceintwinfunc .cs* 함수를 사용 하 여 프로젝트를 Azure의 함수 앱에 게시 합니다.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>수명 주기 이벤트에 대 한 IoT Hub 경로 만들기

이제 장치 수명 주기 이벤트를 라우팅하기 위해 IoT Hub 경로를 설정 합니다. 이 경우에는에 의해 식별 되는 장치 삭제 이벤트를 특별히 수신 대기 `if (opType == "deleteDeviceIdentity")` 합니다. 그러면 디지털 쌍 항목의 삭제를 트리거하고 장치와 디지털 쌍의 사용 중지를 종료 합니다.

먼저 IoT hub에서 이벤트 허브 끝점을 만들어야 합니다. 그런 다음이 이벤트 허브 끝점에 수명 주기 이벤트를 보내는 IoT hub의 경로를 추가 합니다.
이벤트 허브 끝점을 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 [전제 조건](#prerequisites) 섹션에서 만든 IoT hub로 이동 하 고 왼쪽의 메뉴 옵션에서 **메시지 라우팅** 을 선택 합니다.
2. **사용자 지정 엔드포인트** 탭을 선택합니다.
3. **+ 추가** 를 선택 하 고 event **hubs** 를 선택 하 여 event hubs 유형 끝점을 추가 합니다.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="이벤트 허브 사용자 지정 끝점을 추가 하기 위한 Visual Studio 창의 스크린샷." lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. 열리는 *이벤트 허브 끝점 추가* 창에서 다음 값을 선택 합니다.
    * **끝점 이름**: 끝점 이름을 선택 합니다.
    * **이벤트 허브 네임 스페이스**: 드롭다운 목록에서 이벤트 허브 네임 스페이스를 선택 합니다.
    * **이벤트 허브 인스턴스**: 이전 단계에서 만든 이벤트 허브 이름을 선택 합니다.
5. **만들기** 를 선택합니다. 다음 단계에서 경로를 추가 하려면이 창을 열어 둡니다.

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="이벤트 허브 끝점을 추가 하기 위한 Visual Studio 창의 스크린샷." lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

다음으로, 위의 단계에서 만든 끝점에 연결 하는 경로를 추가 하 고 삭제 이벤트를 보내는 라우팅 쿼리를 추가 합니다. 경로를 만들려면 다음 단계를 따르세요.

1. *경로 탭으로* 이동 하 고 **추가** 를 선택 하 여 경로를 추가 합니다.

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="이벤트를 보낼 경로를 추가 하는 Visual Studio 창의 스크린샷." lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. 열리는 *경로 추가* 페이지에서 다음 값을 선택 합니다.

   * **이름**: 경로 이름을 선택 합니다. 
   * **끝점**: 드롭다운에서 이전에 만든 event hubs 끝점을 선택 합니다.
   * **데이터 원본**: *장치 수명 주기 이벤트* 를 선택 합니다.
   * **라우팅 쿼리**: `opType='deleteDeviceIdentity'` 를 입력 합니다. 이 쿼리는 장치 수명 주기 이벤트를 삭제 이벤트만 전송 하도록 제한 합니다.

3. **저장** 을 선택합니다.

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="수명 주기 이벤트를 보내는 경로를 추가 하는 Azure Portal 창의 스크린샷" lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

이 흐름을 완료 한 후에는 모든 것이 종단 간 장치 사용 중지로 설정 됩니다.

### <a name="validate"></a>유효성 검사

사용 중지 프로세스를 트리거하려면 IoT Hub에서 장치를 수동으로 삭제 해야 합니다.

[Azure CLI 명령](/cli/azure/ext/azure-iot/iot/hub/module-identity#ext_azure_iot_az_iot_hub_module_identity_delete) 또는 Azure Portal를 사용 하 여이 작업을 수행할 수 있습니다. Azure Portal에서 장치를 삭제 하려면 다음 단계를 수행 합니다.

1. IoT hub로 이동 하 고 왼쪽의 메뉴 옵션에서 **iot 장치** 를 선택 합니다. 
2. [이 문서의 처음 절반](#auto-provision-device-using-device-provisioning-service)에서 선택한 장치 등록 ID가 포함 된 장치가 표시 됩니다. 또는 장치를 삭제 한 후 쌍이 자동으로 삭제 되는지 확인할 수 있도록 Azure Digital Twins에 쌍이 있는 한 다른 모든 장치를 선택할 수 있습니다.
3. 장치를 선택 하 고 **삭제** 를 선택 합니다.

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="IoT 장치에서 장치 쌍을 삭제 하는 Azure Portal의 스크린샷" lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Azure Digital Twins에 반영 된 변경 내용을 확인 하는 데 몇 분 정도 걸릴 수 있습니다.

다음 [Azure Digital TWINS CLI](how-to-use-cli.md) 명령을 사용 하 여 Azure Digital twins 인스턴스에서 장치 쌍이 삭제 되었는지 확인 합니다.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

장치 쌍이 더 이상 Azure Digital Twins 인스턴스에서 검색 되지 않는 것을 볼 수 있습니다.

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="쌍을 찾을 수 없는 명령 창의 스크린샷" lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 더 이상 필요 하지 않은 경우 다음 단계에 따라 삭제 합니다.

Azure Cloud Shell 또는 로컬 Azure CLI를 사용 하 여 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용 하 여 리소스 그룹의 모든 Azure 리소스를 삭제할 수 있습니다. 그러면 리소스 그룹이 제거 됩니다. Azure Digital Twins 인스턴스 IoT hub 및 허브 장치 등록 event grid 토픽 및 관련 구독 event hubs 네임 스페이스 및 저장소와 같은 관련 된 리소스를 포함 하 여 Azure Functions 앱입니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

그런 다음 로컬 컴퓨터에서 다운로드 한 프로젝트 샘플 폴더를 삭제 합니다.

## <a name="next-steps"></a>다음 단계

장치에 대해 생성 된 디지털 쌍은 Azure digital 쌍에 플랫 계층으로 저장 되지만 모델 정보 및 조직에 대 한 다중 수준 계층 구조를 사용 하 여 보강 수 있습니다. 이 개념에 대 한 자세한 내용은 다음을 참조 하세요.

* [*개념: 디지털 쌍 및 쌍 그래프*](concepts-twins-graph.md)

Azure 함수에서 HTTP 요청을 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

* [*Azure Functions에 대 한 Azure Http 요청 트리거*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Azure Digital Twins에 이미 저장 된 모델 및 그래프 데이터를 사용 하 여이 정보를 자동으로 제공 하는 사용자 지정 논리를 작성할 수 있습니다. 쌍 그래프에서 정보를 관리, 업그레이드 및 검색 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

* [*방법: 디지털 쌍 관리*](how-to-manage-twin.md)
* [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)