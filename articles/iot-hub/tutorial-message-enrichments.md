---
title: 자습서-Azure IoT Hub 메시지 강화를 사용 하 여
description: Azure IoT Hub 메시지에 대 한 메시지 강화를 사용 하는 방법을 보여 주는 자습서
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259076"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>자습서: Azure IoT Hub 메시지 강화 (미리 보기)를 사용 하 여

*강화 메시지* IoT Hub의 기능은 *스탬프* 메시지는 지정 된 끝점에 전송 되기 전에 추가 정보를 사용 하 여 메시지입니다. 메시지 강화를 사용 하는 이유 중 하나는 다운스트림 처리를 간소화 하기 위해 사용할 수 있는 데이터를 포함 하는 것입니다. 예를 들어, 장치 쌍 태그를 사용 하 여 장치 원격 분석 메시지를 보강 합니다.이 정보에 대 한 API를 호출 하는 장치 쌍을 확인 하는 고객에 게 부하를 줄일 수 있습니다. 자세한 내용은 참조는 [메시지 원칙이 개요](iot-hub-message-enrichments-overview.md)합니다.

이 자습서에서는 리소스를 설정 하려면 Azure CLI를 사용-두 개의 다른 저장소 컨테이너를 가리키는 두 개의 끝점을 포함 하 여 **보강** 하 고 **원래**합니다. 사용 하 여 합니다 [Azure portal](https://portal.azure.com) 끝점에 보낸 메시지에만 적용할 메시지 강화를 구성 하는 **보강** 저장소 컨테이너입니다. 메시지를 보냅니다는 IoT Hub에는 두 저장소 컨테이너에 라우팅됩니다. 끝점에 보낸 메시지만 합니다 **보강** 저장소 컨테이너를 보강 합니다.

이 자습서를 완료 하려면 수행 하는 태스크는 다음과 같습니다.

**IoT Hub 메시지 강화를 사용 하 여**
> [!div class="checklist"]
> * Azure CLI를 사용 하는 IoT hub, 두 개의 끝점을 사용 하는 저장소 계정 및 라우팅 구성 리소스를 만듭니다.
> * 메시지 강화를 구성 하려면 Azure portal을 사용 합니다.
> * 허브에 메시지를 보내는 IoT 장치를 시뮬레이션 하는 앱을 실행 합니다.
> * 결과 확인 하 고 메시지 원칙이 예상 대로 작동 하는지 확인 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* [Visual Studio](https://www.visualstudio.com/)를 설치합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>샘플 코드를 검색 합니다.

다운로드 [IoT 장치 시뮬레이션](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) 압축을 풉니다. 이 리포지토리에 IoT hub에 메시지를 전송 하는 데 사용할 것을 포함 하 여, 여러 응용 프로그램입니다.

이 다운로드에는 또한 메시지 강화 테스트를 사용 하는 리소스를 만드는 스크립트를 포함 합니다. 스크립트가 /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli 인지 합니다. 이제 스크립트를 살펴볼 수 있으며 사용 하 여 키를 누릅니다. 또한이 문서에서 직접 스크립트를 복사할 수 있습니다.

테스트를 시작할 준비가 때에 IoT hub에 메시지를 보낼 장치 시뮬레이션 응용 프로그램에서이 다운로드를 사용 합니다.

## <a name="set-up-and-configure-resources"></a>설정 하 고 리소스를 구성 합니다.

필요한 리소스를 만드는 것 외에도 Azure CLI 스크립트 또한 끝점을 별도 저장소 컨테이너에 대 한 두 경로 구성 합니다. 라우팅 구성에 대 한 자세한 내용은 참조는 [라우팅 자습서](tutorial-routing.md)합니다. 사용할 리소스를 설정 합니다 [Azure portal](https://portal.azure.com) 각 끝점에 대 한 메시지 강화를 구성 및 테스트 단계를 계속 합니다.

> [!NOTE]
> 양쪽 끝점에 모든 메시지가 라우팅되는 하지만 구성 된 메시지 강화를 사용 하 여 끝점으로 이동 메시지만 보강 됩니다.
>

아래 스크립트를 사용 하거나 다운로드 한 리포지토리의 /resources 폴더에 스크립트를 열 수 있습니다. 스크립트는 수행 하는 단계는 다음과 같습니다.

* IoT Hub를 만듭니다.
* 저장소 계정을 만듭니다.
* 메시지 보강 되지 됩니다에 대 한 풍부한 메시지에 대 한 개와-저장소 계정에 두 개의 컨테이너를 만듭니다.
* 두 개의 저장소 계정에 대 한 라우팅을 설정 합니다.
    * 각 저장소 계정 컨테이너에 대 한 끝점을 만듭니다.
    * 각 저장소 계정 컨테이너 끝점에 대 한 경로 만듭니다.

몇 가지 리소스 이름(예: IoT Hub 이름 및 스토리지 계정 이름)은 전역적으로 고유해야 합니다. 스크립트를 보다 쉽게 실행 되도록 해당 리소스 이름을 호출 하는 임의의 영숫자 값을 사용 하 여 추가 됩니다 *했습니다*합니다. randomValue는 스크립트의 맨 위에 한 번 생성되고 전체 스크립트에서 필요에 따라 리소스에 추가됩니다. 임의로 설정하지 않으려면 빈 문자열이나 특정 값으로 설정할 수 있습니다.

이미 않았다면 엽니다는 [bash Cloud Shell 창.](https://shell.azure.com)합니다. 압축 푼된 저장소에서 스크립트를 열거나, 사용 하 여 Ctrl + A를 모두 선택 하려면 다음을 복사 하 고 Ctrl + C입니다. 또는 다음 CLI 스크립트를 복사 하 하거나이 cloud shell에서 직접 열 수 있습니다. 명령줄에서 마우스 오른쪽 단추로 클릭 하 고 선택 하 여 Azure cloud shell 창에 스크립트를 붙여 넣습니다 **붙여넣기**합니다. 스크립트를 한 번에 하나씩을 실행 됩니다. 스크립트 실행이 중지 되 면 선택 **Enter** 마지막 명령이 실행 되도록 합니다. 다음 코드 블록에는 무엇이 설명 주석과 함께 사용 되는 스크립트를 보여 줍니다.

스크립트에서 만든 리소스는 다음과 같습니다. **보강** 강화를 사용 하 여 메시지에 대 한 리소스를 의미 합니다. **원래** 보강 되지 되는 메시지에 대 한 리소스를 의미 합니다.

| 이름 | Value |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 컨테이너 이름 | 원문 언어  |
| 컨테이너 이름 | 보강  |
| IoT 장치 이름 | Contoso-테스트 장치 |
| IoT Hub 이름 | ContosoTestHubMsgEn |
| 저장소 계정 이름 | contosostorage |
| 끝점 이름 1 | ContosoStorageEndpointOriginal |
| 끝점 이름 2 | ContosoStorageEndpointEnriched|
| 경로 이름 1 | ContosoStorageRouteOriginal |
| 경로 이름 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

이 시점에서 리소스를 모두 설정 되어 및 라우팅 구성 되어 있습니다. 메시지 라우팅 구성 포털에서 볼 수 있으며 메시지에 대 한 메시지 강화를 설정 합니다 **보강** 저장소 컨테이너입니다.

### <a name="view-routing-and-configure-the-message-enrichments"></a>라우팅 보기 및 메시지 강화를 구성 합니다.

1. 선택 하 여 IoT Hub로 이동 **리소스 그룹**를 선택한 다음이 자습서에 대 한 설정의 리소스 그룹 (**ContosoResources_MsgEn**). 목록에서 IoT Hub를 찾아 선택 합니다. 선택 *메시지 라우팅** Iot Hub에 대 한 합니다.

   ![메시지 라우팅 선택](./media/tutorial-message-enrichments/select-iot-hub.png)

   라우팅 창에는 세 개의 탭-메시지 **경로**를 **사용자 지정 끝점**, 및 **메시지를 보강**합니다. 스크립트에서 설정 구성을 표시 하려면 먼저 두 개의 탭을 찾아볼 수 있습니다. 세 번째 탭을 사용 하 여 메시지 강화를 추가 합니다. 라는 저장소 컨테이너에 대 한 끝점으로 이동 하는 메시지를 보강 보겠습니다 **보강**합니다. 이름 및 값을 입력 하 고 선택한 끝점 **ContosoStorageEndpointEnriched** 드롭다운 목록에서. 보강 메시지에 IoT Hub 이름을 추가 하는 설정의 예는 다음과 같습니다.

   ![첫 번째 보강 추가](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. ContosoStorageEndpointEnriched 끝점에 대 한 목록에 이러한 값을 추가 합니다.

   | 이름 | Value | 끝점 (드롭다운 목록) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | 디바이스 위치 | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |CustomerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 장치는 쌍이 없는 경우 여기에 넣는 값 메시지 강화에 값을 문자열로 표시 됩니다. 쌍 정보, 포털에서 허브로 이동 하는 장치를 확인 하려면 선택한 **IoT 장치**장치를 선택한 다음 선택 **장치 쌍** 페이지의 맨 위에 있는 합니다.
   >
   > (위치)과 같은 태그를 추가 하려는 경우 특정 값으로 설정 하는 쌍 정보를 편집할 수 있습니다. 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](iot-hub-devguide-device-twins.md)을 참조하세요.

3. 완료 되 면, 창에이 이미지와 비슷하게 표시 됩니다.

   ![추가 하는 모든 원칙이 포함 된 테이블](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 선택 **적용** 변경 내용을 저장 합니다.

## <a name="send-messages-to-the-iot-hub"></a>IoT Hub에 메시지 보내기

IoT Hub에 메시지를 보낼 시뮬레이션 된 장치 응용 프로그램을 실행 하는 끝점에 대 한 메시지 강화 구성 됩니다. 허브를 설정한 다음을 수행 하는 규칙을 사용 하 여:

* ContosoStorageEndpointOriginal 저장소 끝점으로 라우팅된 메시지를 보강 되지 됩니다 및 저장소 컨테이너에 저장 됩니다 `original`합니다.

* ContosoStorageEndpointEnriched 저장소 끝점으로 라우팅된 메시지를 보강 되며 저장소 컨테이너에 저장 된 `enriched`합니다.

시뮬레이션 된 장치 응용 프로그램 압축 푼된 다운로드에 응용 프로그램 중 하나입니다. 응용 프로그램은 각 메서드의 다양 한 메시지 라우팅에 대 한 메시지를 보내는 합니다 [라우팅 자습서](tutorial-routing.md); 여기에 Azure Storage.

솔루션 파일(IoT_SimulatedDevice.sln)을 두 번 클릭하여 Visual Studio에서 코드를 연 다음, Program.cs를 엽니다. 대체 `{your hub name}` IoT hub 이름입니다. IoT hub 호스트 이름 형식이 **{hub name}.azure devices.net**합니다. 이 자습서에서는 허브 호스트 이름은 **ContosoTestHubMsgEn.azure devices.net**합니다. 다음으로, 대체 `{device key}` 장치 키를 사용 하 여 리소스를 만드는 스크립트를 실행할 때 이전 저장 합니다.

장치 키가 없는 경우 포털에서 검색할 수 있습니다. 로그인 한 후로 이동 **리소스 그룹**, 리소스 그룹을 선택한 다음 IoT Hub를 선택 합니다. 아래를 봅니다 **IoT 장치** 테스트 장치에 대 한 장치를 선택 합니다. 복사 아이콘 옆에 선택 **기본 키** 클립보드에 복사 합니다.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>실행 및 테스트

콘솔 애플리케이션을 실행합니다. 잠시 기다립니다. 전송 중인 메시지는 응용 프로그램의 콘솔 화면에 표시 됩니다.

앱은 1초마다 새로운 디바이스-클라우드 메시지를 IoT Hub에 보냅니다. 메시지에는 디바이스 ID, 온도, 습도 및 메시지 수준(기본값이 `normal`)과 함께 JSON 직렬화된 개체가 포함됩니다. 수준을 임의로 할당 `critical` 또는 `storage`, 메시지의 저장소 계정 또는 기본 끝점으로 라우팅할 수 있습니다. 전송 된 메시지를 **보강** 저장소 계정에 컨테이너를 보강 합니다.

여러 저장소 메시지를 보낸 후에 데이터를 봅니다.

1. 선택 **리소스 그룹**, 그런 다음 리소스 그룹 (ContosoResourcesMsgEn)를 찾아 선택 합니다.

2. 저장소 계정 (contosostorage)를 선택 합니다. 선택한 **저장소 탐색기 (미리 보기)** 왼쪽의 선택 창에서.

   ![Storage 탐색기를 선택 합니다.](./media/tutorial-message-enrichments/select-storage-explorer.png)

   선택 **BLOB 컨테이너** 사용할 수 있는 두 개의 컨테이너를 확인 합니다.

   ![저장소 계정에 컨테이너를 참조 하세요.](./media/tutorial-message-enrichments/show-blob-containers.png)

컨테이너의 메시지 호출 **보강** 메시지에 포함 된 메시지 강화 했습니다. 컨테이너의 메시지 호출 **원래** 없습니다 강화를 사용 하 여 원시 메시지를 갖습니다. 드릴 다운 컨테이너 중 하나가 아래쪽 및 가장 최근 메시지 파일을 연 후 해당 컨테이너의 메시지에 추가 되지 않습니다 원칙이 있는지 확인 하려면 다른 컨테이너에 대해 동일한 작업을 수행 합니다.

메시지를 제공 하도록 향상 되었습니다 보면 "내 IoT Hub" 허브 이름 뿐만 아니라 위치와 같이 고객 ID, 표시 됩니다.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

및 unenriched 메시지는 다음과 같습니다. "" IoT Hub 내 ","장치 위치"및"customerID"표시 되지 않습니다 여기서이 끝점이 없는 원칙이 있어.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 모두 제거 하려는 경우 리소스 그룹을 삭제 합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우 IoT Hub, 스토리지 계정 및 리소스 그룹 자체가 제거됩니다.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Azure CLI를 사용하여 리소스 정리

리소스 그룹을 제거하려면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용합니다. `$resourceGroup`은 이 자습서의 시작 부분에서 **ContosoResources**로 다시 설정되었습니다.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 구성 하 고 테스트 하려면 다음 단계를 사용 하 여 IoT Hub 메시지 추가 메시지 강화 합니다.

**IoT Hub 메시지 강화를 사용 하 여**
> [!div class="checklist"]
> * Azure CLI를 사용 하는 IoT hub, 두 enendpoints 사용 하 여 저장소 계정 및 라우팅 구성 리소스를 만듭니다.
> * 메시지 강화를 구성 하려면 Azure portal을 사용 합니다.
> * 허브는 IoT 장치 보내는 메시지를 시뮬레이션 하는 앱을 실행 합니다.
> * 결과 확인 하 고 메시지 원칙이 예상 대로 작동 하는지 확인 합니다.

메시지 강화 하는 방법에 대 한 자세한 내용은 참조는 [메시지 원칙이 개요](iot-hub-message-enrichments-overview.md)합니다.

메시지 라우팅에 대 한 자세한 내용은 다음이 문서를 참조 하세요.

* [다른 끝점으로 장치-클라우드 메시지를 보내도록 IoT Hub 메시지 라우팅 사용](iot-hub-devguide-messages-d2c.md)

* [자습서: IoT Hub 라우팅](tutorial-routing.md)