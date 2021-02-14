---
ms.openlocfilehash: 2afe3e29c5717eb8d4921f82f35083ecbb14828c
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531604"
---
1. https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 에서 리포지토리를 복제합니다.
1. Visual Studio Code에서 리포지토리를 다운로드한 폴더를 엽니다.
1. Visual Studio Code에서 *src/cloud-to-device-console-app* 으로 이동합니다. 여기에서 파일을 만들고 이름을 *appsettings.json* 으로 지정합니다. 이 파일에는 프로그램을 실행하는 데 필요한 설정이 포함됩니다.
1. 이 빠른 시작의 앞부분에서 만든 *~/clouddrive/lva-sample/appsettings.json* 파일의 콘텐츠를 복사합니다.

    텍스트는 다음 출력과 비슷합니다.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. *src/edge* 폴더로 이동하고, *.env* 라는 파일을 만듭니다.
1. */clouddrive/lva-sample/edge-deployment/.env* 파일의 콘텐츠를 복사합니다. 텍스트는 다음 코드와 비슷합니다.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    