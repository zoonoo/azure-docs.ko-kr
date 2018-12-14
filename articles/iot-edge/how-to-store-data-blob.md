---
title: Azure IoT Edge 디바이스의 Azure Blob Storage | Microsoft Docs
description: 에지에 데이터를 저장하도록 IoT Edge 디바이스에 Azure Blob Storage 모듈을 배포합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 10/03/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fa88ff46b4fb93d55aa0087cca0e6184f3e087a0
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567284"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>IoT Edge(미리 보기)에서 Azure Blob Storage를 사용하여 에지에 데이터 저장

IoT Edge의 Azure Blog Storage는 에지에 [블록 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 저장소 솔루션을 제공합니다. IoT Edge 디바이스의 Blob 저장소 모듈은 Azure 블록 Blob 서비스처럼 작동하지만, 블록 Blob은 IoT Edge 디바이스에 로컬로 저장됩니다. 동일한 Azure 저장소 SDK 메서드 또는 이미 익숙한 블록 Blob API 호출을 사용하여 Blob에 액세스할 수 있습니다. 

비디오, 이미지, 재무 데이터, 병원 데이터 또는 로컬에 저장해야 하는 모든 데이터 관련 시나리오는 이 모듈을 사용하여 나중에 로컬로 처리하거나 클라우드로 전송할 수 있는 좋은 예입니다.

이 문서에서는 IoT Edge 디바이스에서 Blob service를 실행하는 Azure Blob Storage를 IoT Edge 컨테이너에 배포하기 위한 지침을 제공합니다. 

>[!NOTE]
>IoT Edge의 Azure Blob Storage는 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다. 

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 디바이스:

* [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)의 빠른 시작에 설명된 단계에 따라 개발 머신 또는 가상 머신을 Edge 장치로 사용할 수 있습니다.
* IoT Edge 모듈의 Azure Blob Storage는 다음 디바이스 구성을 지원합니다.

   | 운영 체제 | 아키텍처 |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core(10월 업데이트) | AMD64 |
   | Windows 10 IoT Enterprise(10월 업데이트) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

클라우드 리소스:

* Azure의 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md). 


## <a name="deploy-blob-storage-to-your-device"></a>디바이스에 Blob 저장소 배포

IoT Edge 디바이스에 모듈을 배포하는 여러 가지 방법이 있는데, 모든 방법이 IoT Edge 모듈의 Azure Blob Storage에서 작동합니다. 가장 간단한 두 가지 방법은 Azure Portal 또는 Visual Studio Code 템플릿을 사용하는 것입니다. 

### <a name="azure-portal"></a>Azure portal

#### <a name="find-the-module"></a>모듈 찾기

다음 두 방법 중 하나를 선택하여 Blob 저장소 모듈을 찾을 수 있습니다.

1. Azure Portal에서 "IoT Edge의 Azure Blob Storage"를 검색합니다. 검색 결과 항목을 **선택**합니다.
2. Azure Portal에서 Marketplace로 이동한 다음, "사물 인터넷"을 클릭합니다. "IoT Edge 모듈" 섹션에서 "IoT Edge의 Azure Blob 저장소"를 선택합니다. **만들기**를 클릭합니다.

#### <a name="steps-to-deploy"></a>배포 단계

**IoT Edge 모듈의 대상 장치**

1. IoT Hub가 배포되는 "구독"을 선택합니다.
2. 해당하는 "IoT Hub"를 선택합니다.
3. 이 모듈을 배포할 "IoT Edge 디바이스 이름"을 입력합니다. "디바이스 찾기"를 사용하도록 선택하여 디바이스를 찾을 수 있습니다.
4. **만들기**를 클릭합니다.

**모듈 설정**

1. "모듈 추가" 섹션의 "배포 모듈" 아래에서는 해당 모듈이 "AzureBlobStorageonIoTEdge"로 시작하는 이름으로 이미 나열된 것을 볼 수 있습니다. 
2. "배포 모듈" 목록에서 Blob 저장소 모듈을 **선택**합니다. "IoT Edge 사용자 지정 모듈" 측면 패널이 열립니다.
3. **이름**: 여기에 모듈 이름을 변경할 수 있습니다.
4. **이미지 URI**: URI를 **mcr.microsoft.com/azure-blob-storage:latest**로 바꿉니다.
5. **컨테이너 만들기 옵션**: 포털 페이지에서 아래의 JSON 값을 각자 해당하는 값으로 편집하고 JSON으로 바꿉니다.
   
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
   
    * `<your storage account name>`를 업데이트합니다. 계정 이름은 3~24자 사이여야 하고 소문자와 숫자만 사용해야 합니다.
    * `<your storage account key>`를 64바이트 base64 키로 업데이트합니다. [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob 저장소에 액세스하려면 이러한 자격 증명을 사용합니다.
    * `<storage directory bind>`를 업데이트합니다. 컨테이너 운영 체제에 따라 다릅니다. IoT Edge 디바이스에서 Blob 모듈이 데이터를 저장할 [볼륨](https://docs.docker.com/storage/volumes/) 이름 또는 디렉터리 절대 경로를 제공합니다.  

       * Linux 컨테이너: **\<storage path>:/blobroot**. 예: /srv/containerdata:/blobroot. 또는 my-volume:/blobroot. 
       * Windows 컨테이너: **\<storage path>:C:/BlobRoot**. 예: C:/ContainerData:C:/BlobRoot. 또는 my-volume:C:/blobroot.
   
   > [!CAUTION]
   > **\<Storage directory bind>** 값에 대한 "/blobroot"(Linux인 경우) 및 "C:/BlobRoot"(Windows인 경우)를 변경하지 마세요.

    ![모듈 값 업데이트](./media/how-to-store-data-blob/edit-module.png)

6. "IoT Edge 사용자 지정 모듈"에서 값을 **저장**합니다.
7. "모듈 설정" 섹션에서 **다음**을 클릭합니다.
8. "경로 지정" 섹션에서 **다음**을 클릭합니다.
9. 검토 후 "배포 검토" 섹션에서 **제출**을 클릭합니다.
10. IoT Hub에서 디바이스가 Blob 저장소 모듈을 실행하고 있는지 확인합니다. 

### <a name="visual-studio-code-templates"></a>Visual Studio Code 템플릿

Azure IoT Edge는 Visual Studio Code에 에지 솔루션 개발을 도와주는 템플릿을 제공합니다. 다음 단계를 수행하려면 개발 머신에 [Visual Studio Code](https://code.visualstudio.com/)가 설치되어 있고 [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)이 구성되어 있어야 합니다.

다음 단계에 따라 Blob 저장소 모듈을 사용하여 새 IoT Edge 솔루션을 만들고, 배포 매니페스트를 구성합니다. 

1. **보기** > **명령 팔레트**를 선택합니다. 

2. 명령 팔레트에서 **Azure IoT Edge: 새로운 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 

3. 프롬프트에 따라 새 솔루션을 만듭니다. 

   1. **폴더 선택** - 새 솔루션을 만들 폴더로 이동합니다.  
   
   2. **솔루션 이름 제공** - 솔루션 이름을 제공하거나 기본값을 그대로 적용합니다.
   
   3. **모듈 템플릿 선택** - **기존 모듈**을 선택합니다(전체 이미지 URL 입력).
   
   4. **모듈 이름 제공** - 인식 가능한 모듈 이름을 입력합니다(예: **azureBlobStorage**).
   
   5. **모듈의 Docker 이미지 제공** - 이미지 URI **mcr.microsoft.com/azure-blob-storage:latest**를 제공합니다.

VS Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만든 다음, 새 창에서 로드합니다. 

솔루션 템플릿은 Blob 저장소 모듈 이미지를 포함하는 배포 매니페스트 템플릿을 만들지만, 모듈의 만들기 옵션을 구성해야 합니다. 

1. 새 솔루션 작업 영역에서 **deployment.template.json**을 열고 **모듈** 섹션을 찾습니다. 

2. 이 배포에 필요 없는 **tempSensor** 모듈을 삭제합니다. 

3. 다음 코드를 복사하여 Blob 저장소 모듈의 **createOptions** 필드에 붙여넣습니다. 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![모듈 만들기 옵션 업데이트](./media/how-to-store-data-blob/create-options.png)

4. 만들기 옵션 JSON에서 컨테이너 운영 체제에 따라 `<storage directory bind>`를 업데이트합니다. IoT Edge 디바이스에서 Blob 모듈이 데이터를 저장할 [볼륨](https://docs.docker.com/storage/volumes/) 이름 또는 디렉터리 절대 경로를 제공합니다.  

   * Linux 컨테이너: **\<storage path>:/blobroot**. 예: /srv/containerdata:/blobroot. 또는 my-volume:/blobroot.
   * Windows 컨테이너: **\<storage path>:C:/BlobRoot**. 예: C:/ContainerData:C:/BlobRoot. 또는 my-volume:C:/blobroot.
   
   > [!CAUTION]
   > **\<Storage directory bind>** 값에 대한 "/blobroot"(Linux인 경우) 및 "C:/BlobRoot"(Windows인 경우)를 변경하지 마세요.

5. **deployment.template.json**을 저장합니다.

6. 솔루션 작업 영역에서 **.env**를 엽니다. 

7. Blob 저장소 이미지의 컨테이너 레지스트리 값은 공개적으로 제공되므로 입력할 필요가 없습니다. 대신, 다음 두 환경 변수를 새로 추가합니다. 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. `STORAGE_ACCOUNT_NAME`의 값을 제공합니다. 계정 이름은 3~24자 사이여야 하고 소문자와 숫자만 사용해야 합니다. `STORAGE_ACCOUNT_KEY`의 64바이트 base64 키도 제공합니다. [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob 저장소에 액세스하려면 이러한 자격 증명을 사용합니다. 

9. **.env**를 저장합니다. 

10. **deployment.template.json**을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 배포 매니페스트 생성**을 선택합니다. 

Visual Studio Code는 deployment.template.json 및 .env에 제공된 정보를 사용하여 새로운 배포 매니페스트 파일을 만듭니다. 솔루션 작업 영역의 새 **config** 폴더에 배포 매니페스트가 만들어집니다. 이 파일이 생겼으면 [Visual Studio Code에서 Azure IoT Edge 모듈 배포](how-to-deploy-modules-vscode.md) 또는 [Azure CLI 2.0을 사용하여 Azure IoT Edge 모듈 배포](how-to-deploy-modules-cli.md)의 단계를 수행할 수 있습니다.

## <a name="connect-to-your-blob-storage-module"></a>Blob 저장소 모듈에 연결

모듈에 대해 구성한 계정 이름 및 계정 키를 사용하여 IoT Edge 디바이스의 Blob 저장소에 액세스할 수 있습니다. 

만드는 저장소 요청의 Blob 엔드포인트로 IoT Edge 디바이스를 지정합니다. 구성한 IoT Edge 디바이스 정보 및 계정 이름을 사용하여 [명시적 저장소 엔드포인트에 대한 연결 문자열을 만들 수 있습니다](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint). 

1. "IoT Edge에서 Azure Blob Storage"가 실행되는 동일한 에지 디바이스에 배포되는 모듈의 경우 Blob 엔드포인트는 `http://<Module Name>:11002/<account name>`입니다. 
2. "IoT Edge에서 Azure Blob Storage"가 실행되는 에지 디바이스가 아닌 다른 에지 디바이스에 배포되는 모듈의 경우 설정에 따라 Blob 엔드포인트는 `http://<device IP >:11002/<account name>`, `http://<IoT Edge device hostname>:11002/<account name>` 또는 `http://<FQDN>:11002/<account name>`입니다.

## <a name="logs"></a>로그

컨테이너 내부의 다음 위치에서 로그를 찾을 수 있습니다. 
* Linux: /blobroot/logs/platformblob.log

## <a name="deploy-multiple-instances"></a>여러 인스턴스 배포

IoT Edge에 Azure Blob Storage 인스턴스를 여러 개 배포하려면 모듈이 바인딩되는 HostPort만 변경하면 됩니다. Blob 저장소 모듈은 항상 컨테이너에서 11002 포트를 공개하지만, 호스트에서 모듈이 바인딩될 포트를 선언할 수도 있습니다. 

모듈 만들기 옵션을 편집하여 HostPort 값을 변경할 수 있습니다.

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

추가 Blob 저장소 모듈에 연결할 때 업데이트된 호스트 포트를 가리키도록 엔드포인트를 변경합니다. 

### <a name="try-it-out"></a>체험

Azure Blob Storage 설명서에는 여러 언어로 샘플 코드를 제공하는 빠른 시작이 포함되어 있습니다. 이러한 샘플을 실행하여 Blob 저장소 모듈을 가리키도록 Blob 엔드포인트를 변경하는 방법으로 IoT Edge의 Azure Blob Storage를 테스트할 수 있습니다.

다음 빠른 시작에서는 IoT Edge에서도 지원되는 언어를 사용하므로, Blob 저장소 모듈과 함께 IoT Edge 모듈로 배포할 수 있습니다.

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.JS](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>지원되는 저장소 작업

IoT Edge의 Blob 저장소 모듈은 동일한 Azure Storage SDK를 사용하며, 블록 Blob 엔드포인트용 Azure Storage API의 2018-03-28 버전과 일치합니다. 이후 릴리스는 고객의 요구에 따라 다릅니다. 

일부 Azure Blob Storage 작업은 IoT Edge의 Azure Blob Storage에서 지원되지 않습니다. 다음 섹션에서는 지원되지 않는 작업에 대해 설명합니다. 

### <a name="account"></a>계좌

지원됨: 
* 컨테이너 나열

지원되지 않음: 
* Blob 서비스 속성을 가져오고 설정
* 실행 전 Blob 요청
* Blob 서비스 통계 가져오기
* 계정 정보 가져오기

### <a name="containers"></a>컨테이너

지원됨: 
* 컨테이너 만들기 및 삭제
* 컨테이너 속성 및 메타데이터 가져오기
* Blob 나열

지원되지 않음: 
* 컨테이너 ACL 가져오기 및 설정
* 컨테이너 임대
* 컨테이너 메타데이터 설정

### <a name="blobs"></a>Blob

지원됨: 
* Blob 배치, 가져오기 및 삭제
* Blob 속성 가져오기 및 설정
* Blob 메타데이터 가져오기 및 설정

지원되지 않음: 
* Blob 임대
* Blob 스냅숏
* Blob 복사 및 중단
* Blob 삭제 취소
* Blob 계층 설정

### <a name="block-blobs"></a>블록 Blob

지원됨: 
* 블록 배치: 블록 크기는 4MB 이하여야 합니다.
* 블록 목록 배치 및 가져오기

지원되지 않음:
* URL에서 블록 배치

## <a name="next-steps"></a>다음 단계

[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)에 대해 자세히 알아보기

