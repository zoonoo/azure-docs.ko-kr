---
title: Azure IoT Edge 장치를 Azure Blob Storage 모듈 배포 | Microsoft Docs
description: 에지에 데이터를 저장하도록 IoT Edge 디바이스에 Azure Blob Storage 모듈을 배포합니다.
author: kgremban
ms.author: kgremban
ms.date: 05/21/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: philmea
ms.openlocfilehash: d844e81de9cfb556e91ab5c0d5a8074c822cce0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65990462"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>장치에 IoT Edge 모듈에서 Azure Blob Storage 배포

IoT Edge 장치에 모듈을 배포 하는 방법은 여러 가지가 있습니다 및 모든 IoT Edge 모듈에서 Azure Blob Storage에 대 한 작동 합니다. 가장 간단한 두 가지 방법은 Azure Portal 또는 Visual Studio Code 템플릿을 사용하는 것입니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)
- IoT Edge 런타임이 설치된 [IoT Edge 디바이스](how-to-register-device-portal.md)
- [Visual Studio Code](https://code.visualstudio.com/) 하며 [Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Visual Studio Code에서 배포 하는 경우.

## <a name="deploy-from-the-azure-portal"></a>Azure portal에서 배포

Azure portal을 사용 하면 배포 매니페스트 만들기 및 배포는 IoT Edge 장치에 푸시를 통해 안내 합니다.

### <a name="select-your-device"></a>디바이스 선택

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 메뉴에서 **IoT Edge**를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. **모듈 설정**을 선택합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure portal에 JSON 문서를 수동으로 작성 하는 대신 배포 매니페스트를 만드는 과정을 안내 하는 마법사를 있습니다. 여기에는 **모듈 추가**, **경로 지정** 및 **배포 검토**의 세 가지 단계가 있습니다.

#### <a name="add-modules"></a>모듈 추가

1. 페이지의 **배포 모듈** 섹션에서는 **추가**를 선택합니다.

1. 드롭다운 목록에서 모듈의 형식에서 선택 **IoT Edge 모듈**합니다.

1. 모듈에 대 한 이름을 제공 하 고 컨테이너 이미지를 지정 합니다.

   - **이름** -azureblobstorageoniotedge
   - **이미지 URI** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge는 모듈에 대 한 호출을 확인 하 고 Storage SDK를 소문자로 기본적으로 대/소문자 구분 합니다. 하지만의 모듈 이름을 합니다 [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) 는 **AzureBlobStorageonIoTEdge**, 소문자로 IoT Edge 모듈에서 Azure Blob Storage에 연결 하는 데 도움이 하 여 이름을 변경 중단 되지 않습니다.

1. 기본값 **컨테이너 만들기 옵션** 값이 컨테이너에 필요한 포트 바인딩을 정의 하지만 장치에서 저장소 계정 정보 및 저장소 디렉터리에 대 한 바인딩을 추가 해야 합니다. 아래의 JSON을 사용 하 여 포털에서 기본을 JSON을 바꿉니다.

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. 다음 정보로 복사한 JSON을 업데이트합니다.

   - `<your storage account name>`을 기억하기 쉬운 이름으로 바꿉니다. 계정 이름은 3 ~ 24 자, 소문자와 숫자만 사용 해야 합니다. 공백이 없어야 합니다.

   - `<your storage account key>`를 64바이트 base64 키로 바꿉니다. [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob Storage에 액세스하려면 이러한 자격 증명을 사용합니다.

   - 대체 `<storage directory bind>` 컨테이너 운영 체제에 따라 합니다. IoT Edge 디바이스에서 Blob 모듈이 데이터를 저장할 [볼륨](https://docs.docker.com/storage/volumes/) 이름 또는 디렉터리 절대 경로를 제공합니다. 스토리지 디렉터리 바인드는 사용자가 제공한 디바이스의 위치를 모듈에서 설정된 위치로 매핑합니다.

     - 형식은 Linux 컨테이너용  *\<저장소 경로 >: / blobroot*합니다. 예를 들어 **/srv/containerdata: / blobroot** 하거나 **볼륨 내: / blobroot**합니다.
     - 형식은 Windows 컨테이너용  *\<저장소 경로 >: C: / BlobRoot*합니다. 예를 들어 **c: / ContainerData:C: / BlobRoot** 하거나 **내-볼륨: C: / blobroot**합니다.

     > [!IMPORTANT]
     > 스토리지 디렉터리 바인드 값의 두 번째 절반을 변경하지 마세요. 이는 모듈의 특정 위치를 가리킵니다. 항상 스토리지 디렉터리 바인드는 Linux 컨테이너의 경우 **:/blobroot**로 끝나고 Windows 컨테이너의 경우 **:C:/BlobRoot**로 끝납니다.

    ![업데이트 모듈 컨테이너 만들기 옵션 - 포털](./media/how-to-store-data-blob/edit-module.png)

1. 설정 [계층화](how-to-store-data-blob.md#tiering-properties) 하 고 [time to live](how-to-store-data-blob.md#time-to-live-properties) 다음 JSON을 복사 하 여에 붙여넣으면 모듈에 대 한 속성을 **집합 모듈 쌍의 desired 속성** 상자입니다. 적절 한 값을 사용 하 여 각 속성을 구성, 저장 및 배포를 사용 하 여 계속 합니다.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

      ```

   ![계층화 및 time to live 속성 설정](./media/how-to-store-data-blob/iotedge_custom_module.png)

   모듈을 배포한 후 계층화 및 TTL 구성에 대 한 자세한 내용은 [모듈 쌍 편집](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)합니다. Desired 속성에 대 한 자세한 내용은 참조 하세요. [desired 속성을 정의 또는 업데이트](module-composition.md#define-or-update-desired-properties)합니다.

1. **저장**을 선택합니다.

1. **다음**을 선택하여 경로 섹션을 계속합니다.

#### <a name="specify-routes"></a>경로 지정

기본 경로 유지 하 고 선택 **다음** 검토 섹션을 계속 합니다.

#### <a name="review-deployment"></a>배포 검토

검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. 이 밖에도 추가 하지 않은 두 개의 모듈 선언: **$edgeAgent** 하 고 **$edgeHub**합니다. 이 두 개의 모듈은 [IoT Edge 런타임](iot-edge-runtime.md)을 구성하며 모든 배포에서 필수 기본값입니다.

배포 정보를 검토한 다음 **제출**을 선택합니다.

### <a name="verify-your-deployment"></a>배포 확인

배포를 제출한 후 IoT Hub의 **IoT Edge** 페이지로 돌아갑니다.

1. 해당 세부 정보를 열려면 배포할 대상으로 지정한 IoT Edge 디바이스를 선택합니다.
1. 디바이스 세부 정보에서 Blob Storage 모듈이 **배포에 지정됨** 및 **디바이스에서 보고됨** 모두로 나열되어 있는지를 확인합니다.

모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.

## <a name="deploy-from-visual-studio-code"></a>Visual Studio Code에서 배포

Azure IoT Edge는 Visual Studio Code에 에지 솔루션 개발을 도와주는 템플릿을 제공합니다. Blob 저장소 모듈을 사용 하 여 새 IoT Edge 솔루션을 만들려면 및 배포 매니페스트를 구성 하려면 다음 단계를 사용 합니다.

1. **보기** > **명령 팔레트**를 선택합니다.

1. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다.

   ![New IoT Edge Solution 실행](./media/how-to-develop-csharp-module/new-solution.png)

   명령 팔레트의 프롬프트에 따라 솔루션을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | 솔루션 파일을 만들려면 Visual Studio Code에 대 한 개발 컴퓨터의 위치를 선택 합니다. |
   | 솔루션 이름 제공 | 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값 **EdgeSolution**을 적용합니다. |
   | 모듈 템플릿 선택 | **기존 모듈(전체 이미지 URL 입력)** 을 선택합니다. |
   | 모듈 이름 제공 | 모듈에 대한 이름을 모두 소문자로 입력합니다(예: **azureblobstorage**).<br /><br />IoT Edge 모듈에서 Azure Blob Storage에 사용할 이름은 반드시 소문자로 사용해야 합니다. IoT Edge는 모듈을 참조할 때 대/소문자를 구분하며 Storage SDK는 기본적으로 소문자로 설정됩니다. |
   | 모듈의 Docker 이미지 제공 | 이미지 URI를 **mcr.microsoft.com/azure-blob-storage:latest**로 입력합니다. |

   Visual Studio Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만든 다음, 새 창에서 로드합니다. 솔루션 템플릿은 Blob Storage 모듈 이미지를 포함하는 배포 매니페스트 템플릿을 만들지만, 모듈의 만들기 옵션을 구성해야 합니다.

1. 새 솔루션 작업 영역에서 *deployment.template.json*을 열고 **모듈** 섹션을 찾습니다. 다음 구성을 변경합니다.

   1. 이 배포에 필요 없는 **tempSensor** 모듈을 삭제합니다.

   1. 다음 코드를 복사한는 `createOptions` 필드:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![모듈 createOptions-Visual Studio Code를 업데이트 합니다.](./media/how-to-store-data-blob/create-options.png)

1. `<your storage account name>`을 기억하기 쉬운 이름으로 바꿉니다. 계정 이름은 3 ~ 24 자, 소문자와 숫자만 사용 해야 합니다. 공백이 없어야 합니다.

1. `<your storage account key>`를 64바이트 base64 키로 바꿉니다. [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob Storage에 액세스하려면 이러한 자격 증명을 사용합니다.

1. 대체 `<storage directory bind>` 컨테이너 운영 체제에 따라 합니다. IoT Edge 디바이스에서 Blob 모듈이 데이터를 저장할 [볼륨](https://docs.docker.com/storage/volumes/) 이름 또는 디렉터리 절대 경로를 제공합니다. 스토리지 디렉터리 바인드는 사용자가 제공한 디바이스의 위치를 모듈에서 설정된 위치로 매핑합니다.  

      - 형식은 Linux 컨테이너용  *\<저장소 경로 >: / blobroot*합니다. 예를 들어 **/srv/containerdata: / blobroot** 하거나 **볼륨 내: / blobroot**합니다.
      - 형식은 Windows 컨테이너용  *\<저장소 경로 >: C: / BlobRoot*합니다. 예를 들어 **c: / ContainerData:C: / BlobRoot** 하거나 **내-볼륨: C: / blobroot**합니다.

      > [!IMPORTANT]
      > 스토리지 디렉터리 바인드 값의 두 번째 절반을 변경하지 마세요. 이는 모듈의 특정 위치를 가리킵니다. 항상 스토리지 디렉터리 바인드는 Linux 컨테이너의 경우 **:/blobroot**로 끝나고 Windows 컨테이너의 경우 **:C:/BlobRoot**로 끝납니다.

1. 구성 [계층화](how-to-store-data-blob.md#tiering-properties) 하 고 [time to live](how-to-store-data-blob.md#time-to-live-properties) 다음 JSON을 추가 하 여 모듈에 대 한 속성을 *deployment.template.json* 파일입니다. 적절 한 값을 사용 하 여 각 속성을 구성 하 고 파일을 저장 합니다.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }
   ```

   ![azureblobstorageoniotedge-Visual Studio Code에 대 한 desired 속성 설정](./media/how-to-store-data-blob/tiering_ttl.png)

   모듈을 배포한 후 계층화 및 TTL 구성에 대 한 자세한 내용은 [모듈 쌍 편집](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)합니다. 컨테이너에 대 한 자세한 정보에 대 한 만들기 옵션, 정책 및 원하는 상태 참조를 다시 시작 [edge 에이전트 desired 속성](module-edgeagent-edgehub.md#edgeagent-desired-properties)합니다.

1. *deployment.template.json* 파일을 저장합니다.

1. **deployment.template.json**을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 배포 매니페스트 생성**을 선택합니다.

1. 제공한 정보를 사용 하는 visual Studio Code *deployment.template.json* 새 배포 매니페스트 파일을 만들려면 사용 합니다. 솔루션 작업 영역의 새 **config** 폴더에 배포 매니페스트가 만들어집니다. 이 파일이 생겼으면 [Visual Studio Code에서 Azure IoT Edge 모듈 배포](how-to-deploy-modules-vscode.md) 또는 [Azure CLI 2.0을 사용하여 Azure IoT Edge 모듈 배포](how-to-deploy-modules-cli.md)의 단계를 수행할 수 있습니다.

## <a name="deploy-multiple-module-instances"></a>모듈의 여러 인스턴스 배포

IoT Edge 모듈에서 Azure Blob Storage의 여러 인스턴스를 배포 하려는 경우 다른 저장소 경로 제공 하 고 변경 하는 `HostPort` 모듈에 바인딩되는 값입니다. Blob Storage 모듈은 항상 컨테이너에서 11002 포트를 공개하지만, 호스트에서 모듈이 바인딩될 포트를 선언할 수도 있습니다.

편집할 **컨테이너 만들기 옵션** (Azure 포털)에서 또는 **createOptions** 필드 (에 *deployment.template.json* Visual Studio Code에서 파일) 를변경하려면`HostPort` 값:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

추가 Blob Storage 모듈에 연결할 때 업데이트된 호스트 포트를 가리키도록 엔드포인트를 변경합니다.

## <a name="next-steps"></a>다음 단계

배포 매니페스트의 작동 방식 및 생성 방법에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.
