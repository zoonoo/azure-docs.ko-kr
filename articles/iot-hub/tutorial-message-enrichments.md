---
title: 자습서 - Azure IoT Hub 메시지 보강 사용
description: Azure IoT Hub 메시지에 메시지 보강을 사용하는 방법을 보여 주면 자습서
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: c812e00699cd8f8cfbaf32feea1b43866ffb0990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674364"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>자습서: Azure IoT 허브 메시지 보강 사용

*메시지 보강은* Azure IoT Hub가 메시지를 지정된 끝점으로 보내기 전에 추가 정보를 사용하여 메시지를 *스탬프하는* 기능을 설명합니다. 메시지 보강을 사용하는 한 가지 이유는 다운스트림 처리를 단순화하는 데 사용할 수 있는 데이터를 포함하기 위해서입니다. 예를 들어 장치 쌍 태그로 장치 원격 분석 메시지를 보강하면 고객의 부하를 줄여 이 정보에 대한 장치 쌍 API 호출을 할 수 있습니다. 자세한 내용은 [메시지 보강 개요를](iot-hub-message-enrichments-overview.md)참조하십시오.

이 자습서에서는 IoT 허브에 대한 메시지 보강을 테스트하는 데 필요한 리소스를 만들고 구성하는 두 가지 방법을 볼 수 있습니다. 리소스에는 두 개의 저장소 컨테이너가 있는 하나의 저장소 계정이 포함됩니다. 한 컨테이너는 보강된 메시지를 담고 다른 컨테이너는 원본 메시지를 보유합니다. 또한 메시지를 수신하고 강화 여부에 따라 적절한 저장소 컨테이너로 라우팅하는 IoT 허브도 포함되어 있습니다.

* 첫 번째 방법은 Azure CLI를 사용하여 리소스를 만들고 메시지 라우팅을 구성하는 것입니다. 그런 다음 [Azure 포털을](https://portal.azure.com)사용하여 보강을 수동으로 정의합니다.

* 두 번째 방법은 Azure Resource Manager 템플릿을 사용하여 메시지 라우팅 및 메시지 보강에 대한 *리소스와* 구성을 모두 만드는 것입니다.

메시지 라우팅 및 메시지 보강에 대한 구성이 완료되면 응용 프로그램을 사용하여 IoT 허브로 메시지를 보냅니다. 그런 다음 허브는 두 저장소 컨테이너로 라우팅합니다. **보강된** 저장소 컨테이너에 대한 끝점으로 전송된 메시지만 보강됩니다.

이 자습서를 완료하기 위해 수행하는 작업은 다음과 같습니다.

**IoT 허브 메시지 보강 사용**
> [!div class="checklist"]
> * 첫 번째 방법: Azure CLI를 사용하여 리소스를 만들고 메시지 라우팅을 구성합니다. [Azure 포털을](https://portal.azure.com)사용하여 메시지 보강을 수동으로 구성합니다.
> * 두 번째 방법: 리소스 관리자 템플릿을 사용하여 리소스를 만들고 메시지 라우팅 및 메시지 보강을 구성합니다. 
> * 허브에 메시지를 보내는 IoT 장치를 시뮬레이션하는 앱을 실행합니다.
> * 결과를 보고 메시지 보강이 예상대로 작동하는지 확인합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Visual Studio](https://www.visualstudio.com/)를 설치합니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 자습서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>IoT C# 샘플 리포지토리 검색

GitHub에서 [IoT C# 샘플을](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) 다운로드하고 압축을 풀어 보십시오. 이 리포지토리에는 여러 응용 프로그램, 스크립트 및 리소스 관리자 템플릿이 있습니다. 이 자습서에 사용할 것 들은 다음과 같습니다.

* 수동 메서드의 경우 리소스를 만드는 데 사용되는 CLI 스크립트가 있습니다. 이 스크립트는 /azure-iot 샘플-csharp/iot-hub/자습서/라우팅/시뮬레이션장치/리소스/iothub_msgenrichment_cli.azcli에 있습니다. 이 스크립트는 리소스를 만들고 메시지 라우팅을 구성합니다. 이 스크립트를 실행한 후 [Azure 포털](https://portal.azure.com)을 사용하여 메시지 보강을 수동으로 만듭니다.
* 자동화된 메서드의 경우 Azure 리소스 관리자 템플릿이 있습니다. 템플릿은 /azure-iot 샘플-csharp/iot 허브/자습서/라우팅/시뮬레이션장치/리소스/template_msgenrichments.json에 있습니다. 이 템플릿은 리소스를 만들고 메시지 라우팅을 구성한 다음 메시지 보강을 구성합니다.
* 사용하는 세 번째 응용 프로그램은 IoT 허브에 메시지를 보내고 메시지 보강을 테스트하는 데 사용하는 장치 시뮬레이션 앱입니다.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Azure CLI를 사용하여 수동으로 설정하고 구성

필요한 리소스를 만드는 것 외에도 Azure CLI 스크립트는 별도의 저장소 컨테이너인 끝점에 대한 두 경로를 구성합니다. 메시지 라우팅을 구성하는 방법에 대한 자세한 내용은 [라우팅 자습서를](tutorial-routing.md)참조하십시오. 리소스를 설정한 후 Azure [포털을](https://portal.azure.com) 사용하여 각 끝점에 대한 메시지 보강을 구성합니다. 그런 다음 테스트 단계를 계속합니다.

> [!NOTE]
> 모든 메시지는 두 끝점으로 라우팅되지만 구성된 메시지 보강이 있는 끝점으로 가는 메시지만 보강됩니다.
>

다음에 오는 스크립트를 사용하거나 다운로드한 리포지토리의 /resources 폴더에서 스크립트를 열 수 있습니다. 스크립트에서 수행하는 단계는 다음과 같습니다.

* IoT Hub를 만듭니다.
* 스토리지 계정을 만듭니다.
* 저장소 계정에 두 개의 컨테이너를 만듭니다. 한 컨테이너는 보강된 메시지에 대한 것이고 다른 컨테이너는 보강되지 않은 메시지에 대한 것입니다.
* 두 개의 서로 다른 저장소 계정에 대한 라우팅 설정:
    * 각 저장소 계정 컨테이너에 대한 끝점을 만듭니다.
    * 각 저장소 계정 컨테이너 끝점에 대한 경로를 만듭니다.

IoT 허브 이름 및 저장소 계정 이름과 같이 전역적으로 고유해야 하는 여러 리소스 이름이 있습니다. 스크립트를 보다 쉽게 실행할 수 있도록 이러한 리소스 이름에 *randomValue*라는 임의의 가상 값으로 추가 됩니다. 임의의 값은 스크립트 맨 위에 한 번 생성됩니다. 스크립트 전체에서 필요에 따라 리소스 이름에 추가됩니다. 값을 임의로 지정하지 않으려면 빈 문자열 또는 특정 값으로 설정할 수 있습니다.

아직 수행하지 않은 경우 Azure 클라우드 [셸 창을](https://shell.azure.com) 열고 Bash로 설정되어 있는지 확인합니다. 압축 해제된 리포지토리에서 스크립트를 열고 Ctrl+A를 선택하여 모두 선택한 다음 Ctrl+C를 선택하여 복사합니다. 또는 다음 CLI 스크립트를 복사하거나 Cloud Shell에서 직접 열 수 있습니다. 명령줄을 마우스 오른쪽 단추로 클릭하고 **붙여넣기를**선택하여 Cloud Shell 창에 스크립트를 붙여넣습니다. 스크립트는 한 번에 하나의 문을 실행합니다. 스크립트 실행이 중지된 후 **Enter를** 선택하여 마지막 명령을 실행했는지 확인합니다. 다음 코드 블록에는 사용되는 스크립트와 스크립트가 수행하는 작업을 설명하는 주석이 표시됩니다.

스크립트에서 만든 리소스는 다음과 같습니다. *보강은* 리소스가 보강이 있는 메시지에 대한 것이라는 것을 의미합니다. *원래* 리소스는 보강되지 않은 메시지에 대한 것입니다.

| 이름 | 값 |
|-----|-----|
| resourceGroup | 콘토소리소스임스임스젠 |
| 컨테이너 이름 | 오리지널  |
| 컨테이너 이름 | 농축  |
| IoT 장치 이름 | 콘토소 테스트 장치 |
| IoT Hub 이름 | 콘토소테스트허브스젠 |
| 저장소 계정 이름 | 콘토소스토리지 |
| 끝점 이름 1 | 콘토소스토리지엔드포인트오리지널 |
| 끝점 이름 2 | 콘토소스토리지엔드포인트농축|
| 경로 이름 1 | 콘토소스토리지오리지널 |
| 경로 이름 2 | 콘토소스토리지루트농축 |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

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
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

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

# This is the endpoint for the first container, for endpoint messages that are not enriched.
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

# This is the endpoint for the second container, for endpoint messages that are enriched.
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

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

이 시점에서 리소스가 모두 설정되고 메시지 라우팅이 구성됩니다. 포털에서 메시지 라우팅 구성을 보고 **보강된** 저장소 컨테이너로 이동 하는 메시지에 대 한 메시지 보강을 설정할 수 있습니다.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Azure 포털을 사용하여 메시지 보강을 수동으로 구성합니다.

1. **리소스 그룹을**선택하여 IoT 허브로 이동합니다. 그런 다음 이**자습서(ContosoResourcesMsgEn)에**대해 설정된 리소스 그룹을 선택합니다. 목록에서 IoT 허브를 찾아 선택합니다. IoT 허브에 대한 **메시지 라우팅을** 선택합니다.

   ![메시지 라우팅 선택](./media/tutorial-message-enrichments/select-iot-hub.png)

   메시지 라우팅 창에는 **경로,** 사용자 지정 **끝점**및 **메시지 보강으로**레이블이 지정된 세 개의 탭이 있습니다. 처음 두 탭을 탐색하여 스크립트에서 설정한 구성을 확인합니다. 세 번째 탭을 사용하여 메시지 보강을 추가합니다. **보강이라고**하는 저장소 컨테이너의 끝점으로 가는 메시지를 보강해 보겠습니다. 이름과 값을 입력한 다음 드롭다운 목록에서 **ContosoStorageEndpoint가 강화된** 끝점을 선택합니다. 다음은 메시지에 IoT 허브 이름을 추가하는 보강을 설정하는 방법의 예입니다.

   ![첫 번째 농축 추가](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. ContosoStorageEndpoint농축 끝점의 목록에 이러한 값을 추가합니다.

   | Key | 값 | 끝점(드롭다운 목록) |
   | ---- | ----- | -------------------------|
   | 마이오트허브 | $iothubname | AzureStorage컨테이너 > ContosoStorageEndpointEnriched |
   | 장치 위치 | $twin.tags.location | AzureStorage컨테이너 > ContosoStorageEndpointEnriched |
   |Customerid | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorage컨테이너 > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 장치에 쌍이 없는 경우 여기에 입력한 값은 메시지 보강값의 문자열로 스탬프됩니다. 장치 쌍 정보를 보려면 포털의 허브로 이동하여 **IoT 장치를**선택합니다. 기기를 선택한 다음 페이지 상단에서 **장치 쌍(Device twin)을** 선택합니다.
   >
   > 트윈 정보를 편집하여 위치와 같은 태그를 추가하고 특정 값으로 설정할 수 있습니다. 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](iot-hub-devguide-device-twins.md)을 참조하세요.

3. 완료되면 창이 이 이미지와 비슷해야 합니다.

   ![모든 농축물을 추가한 테이블](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 변경 내용을 저장하려면 **적용을** 선택합니다. [테스트 메시지 보강](#test-message-enrichments) 섹션으로 건너뜁니다.

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 만들기 및 구성
리소스 관리자 템플릿을 사용하여 리소스, 메시지 라우팅 및 메시지 보강을 만들고 구성할 수 있습니다.

1. Azure Portal에 로그인합니다. **+ 리소스 만들기를** 선택하여 검색 상자를 불러올 수 있습니다. *템플릿 배포를*입력하고 검색합니다. 결과 창에서 템플릿 **배포(사용자 지정 템플릿을 사용하여 배포)를**선택합니다.

   ![Azure 포털의 템플릿 배포](./media/tutorial-message-enrichments/template-select-deployment.png)

1. **템플릿 배포** 창에서 **만들기를** 선택합니다.

1. 사용자 **지정 배포** **창에서 편집기에서 사용자 고유의 템플릿 빌드를 선택합니다.**

1. 템플릿 **편집** 창에서 파일 **로드를**선택합니다. 윈도우 탐색기가 나타납니다. **/iot 허브/자습서/라우팅/시뮬레이션장치/리소스의**압축 해제된 리포지토리 파일에서 **template_messageenrichments.json** 파일을 찾습니다. 

   ![로컬 컴퓨터에서 템플릿 선택](./media/tutorial-message-enrichments/template-select.png)

1. 로컬 컴퓨터에서 템플릿 파일을 로드하려면 **열기를** 선택합니다. 로드되고 편집 창에 나타납니다.

   이 템플릿은 기본 이름 끝에 임의의 값을 추가하여 전역적으로 고유한 IoT 허브 이름 및 저장소 계정 이름을 사용하도록 설정되어 있으므로 템플릿을 변경하지 않고 사용할 수 있습니다.

   템플릿을 로드하여 만든 리소스는 다음과 같습니다. **보강은** 리소스가 보강이 있는 메시지에 대한 것이라는 것을 의미합니다. **원래** 리소스는 보강되지 않은 메시지에 대한 것입니다. Azure CLI 스크립트에 사용된 것과 동일한 값입니다.

   | 이름 | 값 |
   |-----|-----|
   | resourceGroup | 콘토소리소스임스임스젠 |
   | 컨테이너 이름 | 오리지널  |
   | 컨테이너 이름 | 농축  |
   | IoT 장치 이름 | 콘토소 테스트 장치 |
   | IoT Hub 이름 | 콘토소테스트허브스젠 |
   | 저장소 계정 이름 | 콘토소스토리지 |
   | 끝점 이름 1 | 콘토소스토리지엔드포인트오리지널 |
   | 끝점 이름 2 | 콘토소스토리지엔드포인트농축|
   | 경로 이름 1 | 콘토소스토리지오리지널 |
   | 경로 이름 2 | 콘토소스토리지루트농축 |

1. **저장**을 선택합니다. **사용자 지정 배포** 창이 나타나고 템플릿에서 사용하는 모든 매개 변수를 표시합니다. 설정해야 하는 유일한 필드는 **리소스 그룹입니다.** 새 것을 만들거나 드롭다운 목록에서 하나를 선택합니다.

   **다음은 사용자 지정 배포** 창의 위쪽 절반입니다. 리소스 그룹을 채우는 위치를 확인할 수 있습니다.

   ![사용자 지정 배포 창의 맨 위 절반](./media/tutorial-message-enrichments/template-deployment-top.png)

1. **다음은 사용자 지정 배포** 창의 아래쪽 절반입니다. 나머지 매개 변수및 이용 약관을 볼 수 있습니다. 

   ![사용자 지정 배포 창의 아래쪽 절반](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. 이용 약관에 동의하려면 확인란을 선택합니다. 그런 다음 **구입을** 선택하여 템플릿 배포를 계속합니다.

1. 템플릿이 완전히 배포될 때까지 기다립니다. 화면 상단의 벨 아이콘을 선택하여 진행 상황을 확인합니다. 작업이 완료되면 테스트 메시지 보강 섹션으로 계속 [이동합니다.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>테스트 메시지 보강

메시지 보강을 보려면 리소스 **그룹을**선택합니다. 그런 다음 이 자습서에 사용 하는 리소스 그룹을 선택 합니다. 리소스 목록에서 IoT 허브를 선택하고 **메시징**으로 이동합니다. 메시지 라우팅 구성 및 구성된 보강이 나타납니다.

이제 메시지 보강이 끝점에 대해 구성되었으므로 시뮬레이션된 장치 응용 프로그램을 실행하여 IoT 허브로 메시지를 보냅니다. 허브는 다음 작업을 수행하는 설정으로 설정되었습니다.

* 저장소 끝점으로 라우팅된 메시지 ContosoStorageEndpointOriginal은 보강되지 않으며 저장소 컨테이너에 `original`저장됩니다.

* 저장소 끝점으로 라우팅된 메시지 ContosoStorageEndpointEnriched 저장소 컨테이너에 `enriched`보강 되고 저장됩니다.

시뮬레이션 된 장치 응용 프로그램은 압축해제 된 다운로드의 응용 프로그램 중 하나입니다. 응용 프로그램은 Azure Storage를 포함하는 [라우팅 자습서의](tutorial-routing.md)각 다른 메시지 라우팅 메서드에 대한 메시지를 보냅니다.

솔루션 파일 **IoT_SimulatedDevice.sln을** 두 번 클릭하여 Visual Studio에서 코드를 연 다음 **Program.cs**엽니다. IoT 허브 이름을 마커로 `{your hub name}`대체합니다. IoT 허브 호스트 이름의 형식은 **{허브 이름}.azure-devices.net.** 이 자습서에서는 허브 호스트 이름이 ContosoTestHubMsgEn.azure-devices.net. 그런 다음 스크립트를 실행했을 때 이전에 저장한 장치 키를 `{your device key}`대체하여 마커에 대한 리소스를 만듭니다.

장치 키가 없는 경우 포털에서 검색할 수 있습니다. 로그인한 후 리소스 **그룹으로**이동하여 리소스 그룹을 선택한 다음 IoT 허브를 선택합니다. **IoT 장치** 아래에서 테스트 장치를 살펴보고 장치를 선택합니다. **기본 키** 옆에 있는 복사 아이콘을 선택하여 클립보드에 복사합니다.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>실행 및 테스트

콘솔 응용 프로그램을 몇 분 동안 실행합니다. 전송되는 메시지는 응용 프로그램의 콘솔 화면에 표시됩니다.

앱은 1초마다 새로운 디바이스-클라우드 메시지를 IoT Hub에 보냅니다. 메시지에는 디바이스 ID, 온도, 습도 및 메시지 수준(기본값이 `normal`)과 함께 JSON 직렬화된 개체가 포함됩니다. 저장소 계정 또는 기본 `critical` 끝점으로 메시지를 라우팅하도록 하는 수준을 `storage`임의로 할당합니다. 저장소 계정의 **보강된** 컨테이너로 전송된 메시지가 보강됩니다.

여러 저장소 메시지를 보낸 후 데이터를 봅니다.

1. **리소스 그룹을**선택합니다. 리소스 그룹인 **ContosoResourcesMsgEn을**찾아 선택합니다.

2. **저장소 계정(contosostorage)을**선택합니다. 그런 다음 왼쪽 창에서 **저장소 탐색기(미리 보기)를** 선택합니다.

   ![저장소 탐색기 선택](./media/tutorial-message-enrichments/select-storage-explorer.png)

   **BLOB 컨테이너를** 선택하여 사용할 수 있는 두 개의 컨테이너를 확인합니다.

   ![저장소 계정의 컨테이너 보기](./media/tutorial-message-enrichments/show-blob-containers.png)

**농축이라는** 컨테이너의 메시지에는 메시지에 포함된 메시지 보강이 있습니다. **원본이라는** 컨테이너의 메시지에는 보강이 없는 원시 메시지가 있습니다. 맨 아래에 도달할 때까지 컨테이너 중 하나로 드릴다운하고 가장 최근 메시지 파일을 엽니다. 그런 다음 다른 컨테이너에 대해동일한 작업을 수행하여 해당 컨테이너의 메시지에 보강이 추가되지 않았는지 확인합니다.

보강된 메시지를 보면 다음과 같이 허브 이름과 위치 및 고객 ID가 있는 "my IoT Hub"가 표시됩니다.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

다음은 농축되지 않은 메시지입니다. 이러한 필드는 보강에 의해 추가되므로 "내 IoT Hub", "장치 위치" 및 "customerID"는 여기에 표시되지 않습니다. 이 끝점에는 보강이 없습니다.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 모든 리소스를 제거하려면 리소스 그룹을 삭제합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우 IoT Hub, 스토리지 계정 및 리소스 그룹 자체가 제거됩니다.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Azure CLI를 사용하여 리소스 정리

리소스 그룹을 제거하려면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용합니다. 이 `$resourceGroup` 자습서의 시작 부분에서 **ContosoResourcesMsgEn으로** 설정된 것을 기억하십시오.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 단계를 사용하여 IoT Hub 메시지에 메시지 보강을 구성하고 테스트했습니다.

**IoT 허브 메시지 보강 사용**
> [!div class="checklist"]
> * 첫 번째 방법: Azure CLI를 사용하여 리소스를 만들고 메시지 라우팅을 구성합니다. [Azure 포털을](https://portal.azure.com)사용하여 메시지 보강을 수동으로 구성합니다.
> * 두 번째 방법: Azure 리소스 관리자 템플릿을 사용하여 리소스를 만들고 메시지 라우팅 및 메시지 보강을 구성합니다.
> * 허브에 메시지를 보내는 IoT 장치를 시뮬레이션하는 앱을 실행합니다.
> * 결과를 보고 메시지 보강이 예상대로 작동하는지 확인합니다.

메시지 보강에 대한 자세한 내용은 [메시지 보강 개요를](iot-hub-message-enrichments-overview.md)참조하십시오.

메시지 라우팅에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [IoT Hub 메시지 라우팅을 사용하여 다양한 엔드포인트에 디바이스-클라우드 메시지 보내기](iot-hub-devguide-messages-d2c.md)
* [자습서: IoT 허브 라우팅](tutorial-routing.md)
