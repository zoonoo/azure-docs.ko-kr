---
title: 디바이스에 블록 Blob 저장 - Azure IoT Edge | Microsoft Docs
description: 계층화 및 라이브 라이브 기능 이해를 참조 하십시오. 지원 되는 blob storage 작업을 참조 하 고 blob storage 계정에 연결 합니다.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c5a27a8016202f7f8c9e256eaf6b3077fbef295b
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414522"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>IoT Edge(미리 보기)에서 Azure Blob Storage를 사용하여 에지에 데이터 저장

IoT Edge의 Azure Blog Storage는 에지에 [블록 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 스토리지 솔루션을 제공합니다. IoT Edge 장치의 blob storage 모듈은 블록 blob이 IoT Edge 장치에 로컬로 저장 된다는 점을 제외 하 고 Azure block blob service 처럼 동작 합니다. 동일한 Azure 저장소 SDK 메서드 또는 이미 익숙한 블록 Blob API 호출을 사용하여 Blob에 액세스할 수 있습니다. 이 문서에서는 IoT Edge 장치에서 Blob 서비스를 실행 하는 IoT Edge 컨테이너의 Azure Blob Storage 관련 된 개념을 설명 합니다.

이 모듈은 데이터를 처리 하거나 클라우드로 전송할 수 있을 때까지 데이터를 로컬로 저장 해야 하는 시나리오에서 유용 합니다. 이 데이터는 비디오, 이미지, 재무 데이터, 병원 데이터 또는 기타 구조화 되지 않은 데이터 일 수 있습니다.

> [!NOTE]
> IoT Edge의 Azure Blob Storage는 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다.

비디오에서 빠른 소개 보기
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

이 모듈은 **deviceToCloudUpload** 및 **deviceautodelete** 기능과 함께 제공 됩니다.

**deviceToCloudUpload** 는 구성 가능한 기능입니다. 이 함수는 임시 인터넷 연결을 지원 하 여 로컬 blob 저장소에서 Azure로 데이터를 자동으로 업로드 합니다. 다음 작업을 수행할 수 있습니다.

- DeviceToCloudUpload 기능을 설정/해제 합니다.
- NewestFirst 또는 OldestFirst와 같이 Azure에 데이터를 복사 하는 순서를 선택 합니다.
- 데이터를 업로드 하려는 Azure Storage 계정을 지정 합니다.
- Azure에 업로드 하려는 컨테이너를 지정 합니다. 이 모듈을 사용 하 여 원본 및 대상 컨테이너 이름을 모두 지정할 수 있습니다.
- 클라우드 저장소에 업로드가 완료 된 후 blob을 즉시 삭제할 수 있는 기능을 선택 합니다.
- 전체 blob 업로드 (작업 사용 `Put Blob` ) 및 블록 수준 업로드 (및 `Put Block List` 작업 `Put Block` 사용)를 수행 합니다.

이 모듈은 blob이 블록으로 구성 된 경우 블록 수준 업로드를 사용 합니다. 몇 가지 일반적인 시나리오는 다음과 같습니다.

- 응용 프로그램은 이전에 업로드 한 blob의 일부 블록을 업데이트 합니다 .이 모듈은 전체 blob이 아닌 업데이트 된 블록만 업로드 합니다.
- 모듈이 blob을 업로드 하는 중 이며, 연결이 다시 되 면 전체 blob이 아닌 나머지 블록만 업로드 합니다.

Blob을 업로드 하는 동안 예기치 않은 프로세스 종료 (예: 전원 오류)가 발생 하면 모듈이 다시 온라인 상태가 되 면 업로드에 대 한 모든 블록이 다시 업로드 됩니다.

**Deviceautodelete** 는 구성 가능한 기능입니다. 이 함수는 지정 된 기간 (분 단위)이 만료 될 때 로컬 저장소에서 blob을 자동으로 삭제 합니다. 다음 작업을 수행할 수 있습니다.

- DeviceAutoDelete 기능을 설정/해제 합니다.
- Blob이 자동으로 삭제 되는 시간 (분)을 지정 합니다 (deleteAfterMinutes).
- DeleteAfterMinutes 값이 만료 되는 경우 업로드 하는 동안 blob을 유지 하는 기능을 선택 합니다.


## <a name="prerequisites"></a>전제 조건

Azure IoT Edge 디바이스:

- [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)에 대 한 빠른 시작의 단계를 수행 하 여 개발 컴퓨터 또는 가상 컴퓨터를 IoT Edge 장치로 사용할 수 있습니다.

- IoT Edge 모듈의 Azure Blob Storage는 다음 디바이스 구성을 지원합니다.

  | 운영 체제 | AMD64 | ARM32v7 | ARM64 |
  | ---------------- | ----- | ----- | ---- |
  | Raspbian-stretch | 아니요 | 예 | 아니요 |  
  | Ubuntu Server 16.04 | 예 | 아니요 | 예 |
  | Ubuntu Server 18.04 | 예 | 아니요 | 예 |
  | Windows 10 IoT Enterprise, 빌드 17763 | 예 | 아니오 | 아니요 |
  | Windows Server 2019, 빌드 17763 | 예 | 아니오 | 아니요 |
  

클라우드 리소스:

Azure의 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 및 deviceAutoDelete 속성

모듈의 desired 속성을 사용 하 여 **deviceToCloudUploadProperties** 및 **deviceautodeleteproperties**를 설정 합니다. 원하는 속성은 배포 중에 설정 하거나 나중에 다시 배포할 필요 없이 모듈 쌍을 편집 하 여 변경할 수 있습니다. 값이 올바르게 전파 되도록 및 `reported configuration` `configurationValidation` 의 "모듈 쌍"을 확인 하는 것이 좋습니다.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

이 설정의 이름은입니다.`deviceToCloudUploadProperties`

| 필드 | 가능한 값 | 설명 | 환경 변수 |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | 기본적으로 `false` 로 설정 됩니다. 이 기능을 설정 하려면이 필드를로 `true`설정 합니다. | `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | 데이터를 Azure로 복사 하는 순서를 선택할 수 있습니다. 기본적으로 `OldestFirst` 로 설정 됩니다. 순서는 Blob의 마지막 수정 시간에 의해 결정 됩니다. | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`데이터를 업로드 하려는 Azure Storage 계정을 지정할 수 있는 연결 문자열입니다. , `Azure Storage Account Name` ,`Azure Storage Account Key` 를`End point suffix`지정 합니다. 데이터가 업로드 되는 Azure의 적절 한 EndpointSuffix를 추가 합니다 .이는 글로벌 Azure, 정부 Azure 및 Microsoft Azure Stack에 따라 달라 집니다. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Azure에 업로드 하려는 컨테이너 이름을 지정할 수 있습니다. 이 모듈을 사용 하 여 원본 및 대상 컨테이너 이름을 모두 지정할 수 있습니다. 대상 컨테이너 이름을 지정 하지 않으면 컨테이너 이름이로 `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`자동 할당 됩니다. 대상 컨테이너 이름에 대 한 템플릿 문자열을 만들고 가능한 값 열을 체크 아웃할 수 있습니다. <br>*% h-> IoT Hub 이름 (3-50 자)입니다. <br>*% d-> IoT Edge 장치 ID (1 ~ 129 자)입니다. <br>*% m-> 모듈 이름 (1 ~ 64 자)입니다. <br>*% c-> 원본 컨테이너 이름 (3 ~ 63 자) <br><br>컨테이너 이름의 최대 크기는 63 자입니다. 컨테이너 크기가 63 자를 초과 하는 경우 대상 컨테이너 이름을 자동으로 할당 하는 동안 각 섹션 (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName)은 15로 잘립니다. 자를. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | 기본적으로 `false` 로 설정 됩니다. 로 `true`설정 되 면 클라우드 저장소에 업로드가 완료 되 면 데이터가 자동으로 삭제 됩니다. | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

이 설정의 이름은입니다.`deviceAutoDeleteProperties`

| 필드 | 가능한 값 | 설명 | 환경 변수 |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | 기본적으로 `false` 로 설정 됩니다. 이 기능을 설정 하려면이 필드를로 `true`설정 합니다. | `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 시간을 분 단위로 지정 합니다. 이 값이 만료 되 면 모듈이 로컬 저장소에서 blob을 자동으로 삭제 합니다. | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | 기본적으로로 `true`설정 되며, deleteAfterMinutes가 만료 되는 경우 클라우드 저장소에 업로드 하는 동안 blob을 유지 합니다. 로 `false` 설정할 수 있으며, deleteAfterMinutes 만료 되는 즉시 데이터를 삭제 합니다. 참고: 이 속성을 work로 설정 하려면 uploadOn를 true로 설정 해야 합니다.| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>로그 파일 구성

모듈에 대 한 로그 파일을 구성 하는 방법에 대 한 자세한 내용은 이러한 [프로덕션 모범 사례](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)를 참조 하세요.

## <a name="connect-to-your-blob-storage-module"></a>Blob Storage 모듈에 연결

모듈에 대해 구성한 계정 이름 및 계정 키를 사용하여 IoT Edge 장치의 Blob Storage에 액세스할 수 있습니다.

만드는 저장소 요청의 Blob 엔드포인트로 IoT Edge 디바이스를 지정합니다. 구성한 IoT Edge 디바이스 정보 및 계정 이름을 사용하여 [명시적 저장소 엔드포인트에 대한 연결 문자열을 만들 수 있습니다](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint).

- IoT Edge 모듈의 Azure Blob Storage와 동일한 장치에 배포 된 모듈의 경우 Blob 끝점은 `http://<module name>:11002/<account name>`입니다.
- 다른 장치에서 실행 되는 모듈 또는 응용 프로그램의 경우 네트워크에 적합 한 끝점을 선택 해야 합니다. 네트워크 설정에 따라 외부 모듈이 나 응용 프로그램의 데이터 트래픽이 IoT Edge 모듈의 Azure Blob Storage를 실행 하는 장치에 도달할 수 있도록 끝점 형식을 선택 합니다. 이 시나리오에 대 한 blob 끝점은 다음 중 하나입니다.
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage 빠른 시작 샘플

Azure Blob Storage 설명서에는 여러 언어의 빠른 시작 샘플 코드가 포함 되어 있습니다. 이러한 샘플을 실행 하면 blob 끝점을 변경 하 여 로컬 blob Storage 모듈에 연결 하 여 IoT Edge에서 Azure Blob Storage를 테스트할 수 있습니다.

다음 퀵 스타트 샘플에서는 IoT Edge 에서도 지원 되는 언어를 사용 하므로 blob storage 모듈과 함께 IoT Edge 모듈로 배포할 수 있습니다.

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Azure Storage 탐색기를 사용 하 여 로컬 저장소에 연결

[Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 를 사용 하 여 로컬 저장소 계정에 연결할 수 있습니다.

1. Azure Storage 탐색기 다운로드 및 설치

1. 연결 문자열을 사용 하 여 Azure Storage에 연결

1. 연결 문자열 제공:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 연결 단계를 진행 합니다.

1. 로컬 저장소 계정 내에 컨테이너 만들기

1. 블록 blob으로 파일 업로드를 시작 합니다.
   > [!NOTE]
   > 이 모듈은 페이지 blob을 지원 하지 않습니다.

1. Storage 탐색기에서 Azure storage 계정을 연결 하도록 선택할 수 있습니다. 이 구성은 로컬 저장소 계정 및 Azure storage 계정 모두에 대 한 단일 보기를 제공 합니다.

## <a name="supported-storage-operations"></a>지원되는 저장소 작업

IoT Edge의 blob storage 모듈은 Azure Storage Sdk를 사용 하며, 블록 blob 끝점에 대 한 Azure Storage API의 2017-04-17 버전과 일치 합니다. 

IoT Edge Azure Blob Storage에서 모든 Azure Blob Storage 작업을 지원 하지 않기 때문에이 섹션에는 각 작업의 상태가 나열 됩니다.

### <a name="account"></a>계정

지원됨:

- 컨테이너 나열

지원되지 않음:

- Blob 서비스 속성을 가져오고 설정
- 실행 전 Blob 요청
- Blob 서비스 통계 가져오기
- 계정 정보 가져오기

### <a name="containers"></a>컨테이너

지원됨:

- 컨테이너 만들기 및 삭제
- 컨테이너 속성 및 메타데이터 가져오기
- BLOB 나열
- 컨테이너 ACL 가져오기 및 설정
- 컨테이너 메타데이터 설정

지원되지 않음:

- 컨테이너 임대

### <a name="blobs"></a>Blob

지원됨:

- Blob 배치, 가져오기 및 삭제
- Blob 속성 가져오기 및 설정
- Blob 메타데이터 가져오기 및 설정

지원되지 않음:

- Blob 임대
- Blob 스냅샷
- Blob 복사 및 중단
- Blob 삭제 취소
- Blob 계층 설정

### <a name="block-blobs"></a>블록 Blob

지원됨:

- 블록 배치
- 블록 목록 배치 및 가져오기

지원되지 않음:

- URL에서 블록 배치

## <a name="release-notes"></a>릴리스 정보

이 모듈에 대 한 [docker 허브의 릴리스 정보](https://hub.docker.com/_/microsoft-azure-blob-storage) 는 다음과 같습니다.

## <a name="feedback"></a>사용자 의견

사용자 의견은이 모듈과 기능을 유용 하 고 사용 하기 쉽도록 하는 데 중요 합니다. 피드백을 공유 하 고 개선할 수 있는 방법을 알려주세요.

다음에 연결할 수 있습니다.absiotfeedback@microsoft.com

## <a name="next-steps"></a>다음 단계

[IoT Edge에서 Azure Blob Storage 배포](how-to-deploy-blob.md) 에 대해 자세히 알아보세요.
