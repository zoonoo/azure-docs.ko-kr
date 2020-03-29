---
title: 디바이스에 블록 Blob 저장 - Azure IoT Edge | Microsoft Docs
description: 계층화 및 실시간 기능을 이해하고, 지원되는 Blob 저장소 작업을 확인하고, Blob 저장소 계정에 연결합니다.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509821"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>IoT Edge에서 Azure Blob Storage를 사용하여 에지에 데이터 저장

IoT Edge의 Azure Blob 저장소는 [에지에서 블록 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 및 [부속 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) 저장소 솔루션을 제공합니다. Blob이 IoT Edge 장치에 로컬로 저장되는 경우를 제외하고 IoT Edge 장치의 Blob 저장소 모듈은 Azure Blob 서비스처럼 작동합니다. 이미 사용 하 고 있는 동일한 Azure 저장소 SDK 메서드 또는 Blob API 호출을 사용 하 여 Blob에 액세스할 수 있습니다. 이 문서에서는 IoT Edge 장치에서 Blob 서비스를 실행하는 IoT Edge 컨테이너의 Azure Blob 저장소와 관련된 개념을 설명합니다.

이 모듈은 시나리오에서 유용합니다.

* 데이터를 처리하거나 클라우드로 전송할 때까지 로컬에 저장해야 하는 경우 이 데이터는 비디오, 이미지, 재무 데이터, 병원 데이터 또는 기타 구조화되지 않은 데이터일 수 있습니다.
* 연결이 제한된 장소에 있는 경우.
* 데이터를 로컬로 효율적으로 처리하여 데이터에 대한 대기 시간 지연 시간을 줄이려는 경우 가능한 한 빨리 비상 사태에 대응할 수 있습니다.
* 대역폭 비용을 절감하고 테라바이트 규모의 데이터를 클라우드로 전송하지 않으려는 경우 데이터를 로컬로 처리하고 처리된 데이터만 클라우드로 보낼 수 있습니다.

빠른 소개를 위해 비디오 보기
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

이 모듈은 장치와 함께 **제공ToCloudUpload** 및 **deviceAutoDelete** 기능.

**deviceToCloudUpload는** 구성 가능한 기능입니다. 이 기능은 간헐적인 인터넷 연결 지원을 통해 로컬 Blob 저장소의 데이터를 Azure에 자동으로 업로드합니다. 그것은 당신이 할 수 있습니다 :

* 장치ToCloudUpload 기능을 켜고 끕니다.
* 데이터가 Azure에 복사되는 순서를 선택합니다.(예: NewestFirst 또는 가장 오래 된 첫 번째)
* 데이터를 업로드할 Azure Storage 계정을 지정합니다.
* Azure에 업로드할 컨테이너를 지정합니다. 이 모듈을 사용하면 소스 및 대상 컨테이너 이름을 모두 지정할 수 있습니다.
* 클라우드 저장소에 업로드가 완료된 후 Blob을 즉시 삭제할 수 있는 기능을 선택합니다.
* `Put Blob` 전체 Blob 업로드(작업 사용) 및 블록 `Put Block` `Put Block List` 수준 `Append Block` 업로드(사용 및 작업 사용)를 수행합니다.

이 모듈에서는 Blob이 블록으로 구성된 블록 수준 업로드를 사용합니다. 다음은 몇 가지 일반적인 시나리오입니다.

* 응용 프로그램은 이전에 업로드 된 블록 Blob의 일부 블록을 업데이트하거나 추가 Blob에 새 블록을 추가하면이 모듈은 전체 Blob이 아닌 업데이트 된 블록만 업로드합니다.
* 모듈은 Blob을 업로드하고 인터넷 연결이 사라집니다.

Blob 업로드 중에 예기치 않은 프로세스 종료(예: 정전)가 발생하면 모듈이 다시 온라인 상태가 되면 업로드예정이었던 모든 블록이 다시 업로드됩니다.

**deviceAutoDelete는** 구성 가능한 기능입니다. 이 기능은 지정된 기간(분 단위로 측정)이 만료되면 로컬 저장소에서 Blob을 자동으로 삭제합니다. 그것은 당신이 할 수 있습니다 :

* 장치 자동 삭제 기능을 켜고 끕니다.
* 시간(deleteAfterMinutes)을 지정한 후 Blob이 자동으로 삭제됩니다.
* deleteAfterMinutes 값이 만료되는 경우 업로드하는 동안 Blob을 유지하는 기능을 선택합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure IoT Edge 디바이스:

* [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)장치의 빠른 시작 단계에 따라 개발 컴퓨터 또는 가상 컴퓨터를 IoT Edge 장치로 사용할 수 있습니다.

* 지원되는 운영 체제 및 아키텍처 목록은 [Azure IoT Edge 지원 시스템을](support.md#operating-systems) 참조하십시오. IoT Edge의 Azure Blob 저장소 모듈은 다음과 같은 아키텍처를 지원합니다.
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * 리눅스 ARM64 (미리보기)

클라우드 리소스:

Azure의 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloud업로드 및 장치자동 삭제 속성

모듈의 원하는 속성을 사용하여 **장치ToCloudUploadProperties** 및 **장치자동 삭제 속성을**설정합니다. 원하는 속성은 배포 하는 동안 설정 하거나 모듈 쌍 재배포 하지 않고 편집 하 여 나중에 변경할 수 있습니다. "모듈 트윈"을 `reported configuration` `configurationValidation` 확인하고 값이 올바르게 전파되었는지 확인하는 것이 좋습니다.

### <a name="devicetoclouduploadproperties"></a>디바이스토클라우드업로드프로퍼티

이 설정의 이름은 `deviceToCloudUploadProperties`입니다. IoT Edge 시뮬레이터를 사용하는 경우 설명 섹션에서 찾을 수 있는 이러한 속성에 대한 관련 환경 변수로 값을 설정합니다.

| 속성 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| 업로드 켜기 | true, false | 기본적으로 `false` 설정합니다. 피쳐를 켜려면 이 필드를 로 `true`설정합니다. <br><br> 환경 변수: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| 업로드 순서 | 최신첫번째, 가장 오래된 첫 번째 | 데이터를 Azure에 복사하는 순서를 선택할 수 있습니다. 기본적으로 `OldestFirst` 설정합니다. 순서는 Blob의 마지막으로 수정된 시간에 의해 결정됩니다. <br><br> 환경 변수: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| 클라우드스토리지커넥션스트링 |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`는 데이터를 업로드할 저장소 계정을 지정할 수 있는 연결 문자열입니다. 을 `Azure Storage Account Name` `Azure Storage Account Key`지정합니다. `End point suffix` 데이터가 업로드될 Azure의 적절한 EndpointSuffix를 추가하면 전역 Azure, 정부 Azure 및 Microsoft Azure 스택에 따라 다릅니다. <br><br> 여기에서 Azure 저장소 SAS 연결 문자열을 지정하도록 선택할 수 있습니다. 그러나 만료될 때 이 속성을 업데이트해야 합니다. <br><br> 환경 변수: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| 스토리지컨테이너업로드 | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Azure에 업로드할 컨테이너 이름을 지정할 수 있습니다. 이 모듈을 사용하면 소스 및 대상 컨테이너 이름을 모두 지정할 수 있습니다. 대상 컨테이너 이름을 지정하지 않으면 컨테이너 이름이 자동으로 로 `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`할당됩니다. 대상 컨테이너 이름에 대한 템플릿 문자열을 만들고 가능한 값 열을 체크 아웃할 수 있습니다. <br>* %h -> IoT 허브 이름 (3-50 자). <br>* %d -> IoT 에지 장치 ID (1 ~ 129 문자). <br>* %m -> 모듈 이름 (1 ~ 64 자). <br>* %c -> 소스 컨테이너 이름 (3 ~ 63 자). <br><br>컨테이너 이름의 최대 크기는 63자이며 컨테이너 크기가 63자를 초과하는 경우 자동으로 대상 컨테이너 이름을 할당하면 각 섹션(IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName)을 15로 트리밍합니다. 문자. <br><br> 환경 변수: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| 삭제후업로드 | true, false | 기본적으로 `false` 설정합니다. `true`로 설정하면 클라우드 저장소에 업로드가 완료되면 데이터가 자동으로 삭제됩니다. <br><br> **주의**: 추가 Blob을 사용하는 경우 이 설정은 성공적으로 업로드한 후 로컬 저장소에서 추가 Blob을 삭제하고 해당 Blob에 대한 향후 추가 블록 작업이 실패합니다. 이 설정을 신중하게 사용하거나 응용 프로그램에서 자주 부속 작업을 수행하거나 지속적인 부가 작업을 지원하지 않는 경우 이 설정을 사용하지 마십시오.<br><br> 환경 변수: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`. |

### <a name="deviceautodeleteproperties"></a>장치자동 삭제 속성

이 설정의 이름은 `deviceAutoDeleteProperties`입니다. IoT Edge 시뮬레이터를 사용하는 경우 설명 섹션에서 찾을 수 있는 이러한 속성에 대한 관련 환경 변수로 값을 설정합니다.

| 속성 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| 삭제 켜기 | true, false | 기본적으로 `false` 설정합니다. 피쳐를 켜려면 이 필드를 로 `true`설정합니다. <br><br> 환경 변수: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| 삭제애프터분 | `<minutes>` | 시간을 분으로 지정합니다. 이 값이 만료되면 모듈은 로컬 저장소에서 Blob을 자동으로 삭제합니다. <br><br> 환경 변수: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| 유지동안업로드 | true, false | 기본적으로 `true`는 로 설정되며 deleteAfterMinutes이 만료되면 클라우드 저장소에 업로드하는 동안 Blob이 유지됩니다. 당신은 그것을 설정할 `false` 수 있습니다 그리고 삭제즉시 데이터를 삭제합니다AfterMinutes 만료. 참고: 이 속성이 업로드작업을 하려면On을 true로 설정해야 합니다.  <br><br> **주의**: 추가 Blob을 사용하는 경우 이 설정은 값이 만료될 때 로컬 저장소에서 추가 Blob을 삭제하고 해당 Blob에 대한 향후 추가 블록 작업이 실패합니다. 응용 프로그램에서 수행하는 부속 작업의 예상 빈도에 대해 만료 값이 충분히 큰지 확인할 수 있습니다.<br><br> 환경 변수: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>SMB 공유를 로컬 저장소로 사용

이 모듈의 Windows 컨테이너를 Windows 호스트에 배포할 때 SMB 공유를 로컬 저장소 경로로 제공할 수 있습니다.

SMB 공유 및 IoT 장치가 상호 신뢰할 수 있는 도메인에 있는지 확인합니다.

PowerShell `New-SmbGlobalMapping` 명령을 실행하여 Windows를 실행하는 IoT 장치에서 SMB 공유를 로컬로 매핑할 수 있습니다.

구성 단계는 다음과 같습니다.

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

예를 들어:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

이 명령은 자격 증명을 사용하여 원격 SMB 서버로 인증합니다. 그런 다음 원격 공유 경로를 G: 드라이브 문자(사용 가능한 다른 드라이브 문자일 수 있음)에 매핑합니다. 이제 IoT 장치에 데이터 볼륨이 G: 드라이브의 경로에 매핑됩니다.

IoT 장치의 사용자가 원격 SMB 공유를 읽고 쓸 수 있는지 확인합니다.

배포의 `<storage mount>` 경우 **G:/ContainerData:C:/BlobRoot**의 값이 될 수 있습니다.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Linux에서 컨테이너 사용자에게 디렉터리 액세스 권한 부여

Linux 컨테이너에 대한 만들기 옵션에서 [저장소에 볼륨 마운트를](https://docs.docker.com/storage/volumes/) 사용한 경우 추가 단계를 수행할 필요가 없지만 [바인드 마운트를](https://docs.docker.com/storage/bind-mounts/) 사용한 경우 이러한 단계를 사용하여 서비스를 올바르게 실행해야 합니다.

사용자가 작업을 수행하는 데 필요한 최소 사용 권한으로 액세스 권한을 제한하는 최소 권한 원칙에 따라 이 모듈에는 사용자(이름: absie, ID: 11000) 및 사용자 그룹(이름: absie, ID: 11000)이 포함됩니다. 컨테이너가 **루트로** 시작되면(기본 사용자는 **루트)** 권한이 낮은 **absie** 사용자로 서비스가 시작됩니다.

이 동작은 호스트 경로에 대한 사용 권한의 구성이 서비스가 올바르게 작동하도록 바인딩하는 데 중요하게 만들며, 그렇지 않으면 서비스가 액세스 거부 오류와 충돌합니다. 디렉터리 바인딩에 사용되는 경로는 컨테이너 사용자가 액세스할 수 있어야 합니다(예: absie 11000). 호스트에서 아래 명령을 실행하여 컨테이너 사용자에게 디렉터리에 대한 액세스 권한을 부여할 수 있습니다.

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

예를 들어:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

**absie**이외의 사용자로 서비스를 실행해야 하는 경우 배포 매니페스트의 "User" 속성 아래에 createOptions에서 사용자 지정 사용자 ID를 지정할 수 있습니다. 이 경우 기본 또는 루트 그룹 `0`ID를 사용해야 합니다.

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

이제 컨테이너 사용자에게 디렉터리에 대한 액세스 권한을 부여합니다.

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>로그 파일 구성

모듈에 대한 로그 파일 구성에 대한 자세한 내용은 다음 [프로덕션 모범 사례를](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)참조하십시오.

## <a name="connect-to-your-blob-storage-module"></a>Blob Storage 모듈에 연결

모듈에 대해 구성한 계정 이름 및 계정 키를 사용하여 IoT Edge 디바이스의 Blob Storage에 액세스할 수 있습니다.

만드는 스토리지 요청의 Blob 엔드포인트로 IoT Edge 디바이스를 지정합니다. 구성한 IoT Edge 디바이스 정보 및 계정 이름을 사용하여 [명시적 스토리지 엔드포인트에 대한 연결 문자열을 만들 수 있습니다](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint).

* IoT Edge 모듈의 Azure Blob 저장소가 실행 중인 것과 동일한 장치에 배포되는 모듈의 `http://<module name>:11002/<account name>`경우 Blob 끝점은 다음과 같습니다.
* 다른 장치에서 실행되는 모듈 또는 응용 프로그램의 경우 네트워크에 적합한 끝점을 선택해야 합니다. 네트워크 설정에 따라 외부 모듈 또는 응용 프로그램의 데이터 트래픽이 IoT Edge 모듈의 Azure Blob Storage를 실행하는 장치에 도달할 수 있도록 끝점 형식을 선택합니다. 이 시나리오의 Blob 끝점은 다음 중 하나입니다.
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 저장소 빠른 시작 샘플

Azure Blob Storage 설명서에는 여러 언어로 된 빠른 시작 샘플 코드가 포함되어 있습니다. 이러한 샘플을 실행하여 Blob 끝점을 변경하여 로컬 Blob 저장소 모듈에 연결하여 IoT Edge에서 Azure Blob 저장소를 테스트할 수 있습니다.

다음 빠른 시작 샘플은 IoT Edge에서 지원하는 언어를 사용하므로 Blob 저장소 모듈과 함께 IoT Edge 모듈로 배포할 수 있습니다.

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * 파이썬 SDK의 V2.1 이전의 버전에는 모듈이 Blob 생성 시간을 반환하지 않는 알려진 문제가 있습니다. 이 문제 때문에 목록 Blob과 같은 일부 메서드가 작동하지 않습니다. 해결 방법을 사용하여 Blob 클라이언트의 API 버전을 '2017-04-17'로 명시적으로 설정합니다. 예제: `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Blob 샘플 부속](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [루비](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [이동](../storage/blobs/storage-quickstart-blobs-go.md)
* [Php](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Azure 저장소 탐색기를 사용하여 로컬 저장소에 연결

[Azure 저장소 탐색기를](https://azure.microsoft.com/features/storage-explorer/) 사용하여 로컬 저장소 계정에 연결할 수 있습니다.

1. Azure Storage Explorer 다운로드 및 설치

1. 연결 문자열을 사용하여 Azure 저장소에 연결

1. 연결 문자열 제공:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 연결하는 단계를 진행합니다.

1. 로컬 저장소 계정 내에서 컨테이너 만들기

1. 블록 Blob 또는 Blob을 부가형으로 파일을 업로드하기 시작합니다.
   > [!NOTE]
   > 이 모듈은 페이지 Blob을 지원하지 않습니다.

1. 저장소 탐색기에서도 Azure 저장소 계정을 연결하도록 선택할 수 있습니다. 이 구성은 로컬 저장소 계정과 Azure 저장소 계정에 대한 단일 보기를 제공합니다.

## <a name="supported-storage-operations"></a>지원되는 스토리지 작업

IoT Edge의 Blob 저장소 모듈은 Azure 저장소 SDK를 사용하며 블록 Blob 끝점에 대한 Azure 저장소 API의 2017-04-17 버전과 일치합니다.

IoT Edge의 Azure Blob 저장소에서 모든 Azure Blob 저장소 작업을 지원하지 는 않으므로 이 섹션에서는 각 작업의 상태를 나열합니다.

### <a name="account"></a>계정

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
* 컨테이너 ACL 가져오기 및 설정
* 컨테이너 메타데이터 설정

지원되지 않음:

* 컨테이너 임대

### <a name="blobs"></a>Blob

지원됨:

* Blob 배치, 가져오기 및 삭제
* Blob 속성 가져오기 및 설정
* Blob 메타데이터 가져오기 및 설정

지원되지 않음:

* Blob 임대
* Blob 스냅샷
* Blob 복사 및 중단
* Blob 삭제 취소
* Blob 계층 설정

### <a name="block-blobs"></a>블록 Blob

지원됨:

* 블록 배치
* 블록 목록 배치 및 가져오기

지원되지 않음:

* URL에서 블록 배치

### <a name="append-blobs"></a>추가 Blob

지원됨:

* 블록 부속

지원되지 않음:

* URL에서 블록 부속

## <a name="event-grid-on-iot-edge-integration"></a>IoT 에지 통합의 이벤트 그리드

> [!CAUTION]
> IoT 에지의 이벤트 그리드와의 통합이 미리 보기 상태입니다.

이제 IoT Edge의 Azure Blob 저장소 모듈을 통해 IoT 에지의 이벤트 그리드와의 통합을 제공합니다. 이 통합에 대한 자세한 내용은 [모듈을 배포하고 이벤트를 게시하고 이벤트 배달을 확인하는 자습서를](../event-grid/edge/react-blob-storage-events-locally.md)참조하십시오.

## <a name="release-notes"></a>릴리스 정보

이 모듈의 docker 허브에 있는 [릴리스 노트는](https://hub.docker.com/_/microsoft-azure-blob-storage) 다음과 같습니다.

## <a name="feedback"></a>사용자 의견

귀하의 의견은이 모듈과 그 기능을 유용하고 사용하기 쉽게 하기 위해 우리에게 중요합니다. 의견을 공유하고 개선 할 수있는 방법을 알려주십시오.

당신은 에서 저희에게 연락 할 수 있습니다absiotfeedback@microsoft.com

## <a name="next-steps"></a>다음 단계

[IoT 에지에 Azure Blob 저장소를 배포하는](how-to-deploy-blob.md) 방법 알아보기

[IoT Edge 블로그의 Azure Blob Storage에서](https://aka.ms/abs-iot-blogpost) 최신 업데이트 및 공지 사항으로 최신 상태
