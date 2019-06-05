---
title: 디바이스에 블록 Blob 저장 - Azure IoT Edge | Microsoft Docs
description: 계층화 및 time to live 기능을 이해 하 고 지원 되는 blob storage 작업을 보고 blob 저장소 계정에 연결 합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 396af2dfd9fc53c080163a27e376328c1369d5e1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991462"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>IoT Edge(미리 보기)에서 Azure Blob Storage를 사용하여 에지에 데이터 저장

IoT Edge의 Azure Blog Storage는 에지에 [블록 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 스토리지 솔루션을 제공합니다. IoT Edge 장치의 Blob Storage 모듈은 Azure 블록 Blob 서비스처럼 작동하지만, 블록 Blob은 IoT Edge 장치에 로컬로 저장됩니다. 동일한 Azure 저장소 SDK 메서드 또는 이미 익숙한 블록 Blob API 호출을 사용하여 Blob에 액세스할 수 있습니다.

> [!NOTE]
> IoT Edge의 Azure Blob Storage는 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다.

이 모듈을 함께 **계층화** 하 고 **-time-to-live** 기능입니다.

> [!NOTE]
> 현재 계층화 및 time to live 기능 에서만 사용할 Linux AMD64 및 Linux ARM32 합니다.

**계층화** 는 일시적인 인터넷 연결 지원 사용 하 여 Azure에 로컬 blob storage에서 데이터를 자동으로 업로드할 수 있는 기능을 구성할 수 있습니다. 할 수 있습니다.

- / 사용 안 함 계층 기능
- NewestFirst 또는 OldestFirst와 같은 Azure에 데이터를 복사 하는 순서를 선택 합니다.
- 데이터 업로드 하려면 Azure Storage 계정을 지정 합니다.
- Azure에 업로드 하려면 컨테이너를 지정 합니다. 이 모듈을 사용 하면 소스와 대상에 해당 하는 컨테이너의 이름을 지정할 수 있습니다.
- Blob 계층화 전체 수행 (사용 하 여 `Put Blob` 작업) 수준 계층화를 차단 하 고 (사용 하 여 `Put Block` 및 `Put Block List` 작업).

이 모듈에는 블록 수준 계층화의 블록 blob 구성 되어 있을 때 사용 합니다. 몇 가지 일반적인 시나리오는 다음과 같습니다.

- 이전에 업로드 된 blob의 일부 요소를 업데이트 하는 응용 프로그램, 업데이트 된 블록만 및는 전체 blob이 아니라이 모듈 업로드 합니다.
- 모듈은 blob을 업로드 및 인터넷 연결이 사라진, 연결 되 면 다시 다시 나머지 블록만 하지 전체 blob을 업로드 합니다.

Blob 업로드 하는 동안 문제가 발생 하면 (예: 정전) 예기치 않은 프로세스 종료를 모듈을 다시 온라인 상태가 되 면 업로드에 예정 된 모든 블록을 다시 업로드 됩니다.

**Time to live** (TTL)가 있는 모듈을 자동으로 blob의 로컬 저장소에서 지정 된 기간 (분 단위로 측정) 만료 되 면 삭제 기능을 구성할 수 있습니다. TTL을 사용 하면 수 있습니다.

- / 사용 안 함 계층 기능
- TTL을 분 단위로 지정 합니다.

비디오, 이미지, 재무 데이터, 병원 데이터 또는 모든 데이터는 나중에 로컬로 저장 해야 하는 경우 로컬로 처리 하거나 클라우드로 전송할 수 있는 데이터의 같은 위치 하는 시나리오는이 모듈을 사용 하는 좋은 예입니다.

이 문서에서는 IoT Edge 디바이스에서 Blob service를 실행하는 Azure Blob Storage를 IoT Edge 컨테이너에 배포하기 위한 지침을 제공합니다.

> [!NOTE]
> 용어 "자동 계층화" 및 "자동 만료" 비디오에 사용 되는 "계층" 및 "time to live"으로 대체 되었습니다.

간략 한 소개 비디오를 시청 하세요.
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 장치:

- 으로 사용할 수 있습니다 개발 컴퓨터 또는 가상 컴퓨터는 IoT Edge 장치에 대 한 빠른 시작의 단계에 따라 [Linux](quickstart-linux.md) 하거나 [Windows 장치](quickstart.md)합니다.

- IoT Edge 모듈의 Azure Blob Storage는 다음 디바이스 구성을 지원합니다.

  | 운영 체제 | 아키텍처 |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Core(10월 업데이트) | AMD64 |
  | Windows 10 IoT Enterprise(10월 업데이트) | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian-stretch | ARM32 |

클라우드 리소스:

Azure의 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

## <a name="tiering-and-time-to-live-properties"></a>계층화 및 time to live 속성

계층화를 설정 하는 desired 속성 및 time to live 속성을 사용 합니다. 배포 중에 설정 또는 다시 배포 하지 않아도 모듈 쌍을 편집 하 여 나중에 변경할 수 있습니다. 에 대 한 "모듈 쌍" 확인을 좋습니다 `reported configuration` 및 `configurationValidation` 값 올바르게 전파 되도록 합니다.

### <a name="tiering-properties"></a>계층 속성

이 설정의 이름은 `tieringSettings`

| 필드 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| tieringOn | true, false | 기본적으로 설정은 `false`설정 하려는 경우에 설정 `true`|
| backlogPolicy | NewestFirst를 OldestFirst | Azure에 데이터를 복사 하는 순서를 선택할 수 있습니다. 기본적으로 설정은 `OldestFirst`합니다. 순서는 Blob의 마지막 수정된 시간 기준 |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` 원하는 데이터를 Azure Storage 계정을 지정할 수 있도록 연결 문자열을 업로드 합니다. 지정할 `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`합니다. 전역 Azure, Azure Government 및 Microsoft Azure Stack에 대 한 다릅니다 적절 한 EndpointSuffix Azure 데이터가 업로드 수를 추가 합니다. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Azure에 업로드 하려면 컨테이너 이름을 지정할 수 있습니다. 이 모듈을 사용 하면 소스와 대상에 해당 하는 컨테이너의 이름을 지정할 수 있습니다. 대상 컨테이너 이름을 지정 하지 않으면, 컨테이너 이름으로 자동으로 할당 됩니다 것 `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`입니다. 대상 컨테이너 이름에 대 한 템플릿 문자열을 만들을 수 있는 값 열을 확인 합니다. <br>* %h IoT Hub 이름 (3-50 자)-> 합니다. <br>* %d IoT 장치 ID (129 자 1)-> 합니다. <br>* %m 모듈 이름 (1 ~ 64 자)-> 합니다. <br>* %c 원본 컨테이너 이름 (63 자가 하 3)-> 합니다. <br><br>컨테이너 이름의 최대 크기는 63 자 할당 하는 동안 자동으로 대상 컨테이너 이름은 컨테이너 크기에서 제거 하는 63 자를 초과 하는 경우 각 섹션 (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) 15 자입니다. |

### <a name="time-to-live-properties"></a>Time to live 속성

이 설정의 이름은 `ttlSettings`

| 필드 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| ttlOn | true, false | 기본적으로 설정은 `false`설정 하려는 경우에 설정 `true`|
| timeToLiveInMinutes | `<minutes>` | TTL을 분 단위로 지정 합니다. TTL 만료 되 면 모듈을 로컬 저장소에서 자동으로 blob 삭제 됩니다. |

## <a name="configure-log-files"></a>로그 파일 구성

이러한 모듈에 대 한 로그 파일 구성에 대 한 자세한 내용은 참조 [프로덕션에 대 한 유용한 정보](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)합니다.

## <a name="connect-to-your-blob-storage-module"></a>Blob Storage 모듈에 연결

모듈에 대해 구성한 계정 이름 및 계정 키를 사용하여 IoT Edge 장치의 Blob Storage에 액세스할 수 있습니다.

만드는 저장소 요청의 Blob 엔드포인트로 IoT Edge 디바이스를 지정합니다. 구성한 IoT Edge 디바이스 정보 및 계정 이름을 사용하여 [명시적 저장소 엔드포인트에 대한 연결 문자열을 만들 수 있습니다](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint).

- Blob 끝점은 IoT Edge 모듈에서 Azure Blob Storage에서 실행 하는 동일한 장치에서 배포 된 모듈의 경우: `http://<module name>:11002/<account name>`합니다.
- IoT Edge 모듈에서 Azure Blob Storage는를 실행 하 고 blob 설정에 따라 여기서 보다 다른 장치에 배포 된 모듈에 대 한 끝점 중 하나입니다.
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 저장소 빠른 시작 샘플

Azure Blob Storage 설명서에는 여러 언어로 샘플 코드를 제공하는 빠른 시작이 포함되어 있습니다. 이 샘플을 IoT Edge에서 Azure Blob Storage에 blob 저장소 모듈에 연결 하는 blob 끝점을 변경 하 여 테스트를 실행할 수 있습니다.

다음 빠른 시작에서는 IoT Edge에서도 지원되는 언어를 사용하므로, Blob Storage 모듈과 함께 IoT Edge 모듈로 배포할 수 있습니다.

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.JS](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Azure Storage 탐색기를 사용 하 여 로컬 저장소에 연결

사용할 수 있습니다 **Azure Storage 탐색기** 로컬 저장소 계정에 연결 합니다. 이 이용 [Azure Storage 탐색기 버전 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0)합니다.

> [!NOTE]
> 예: 로컬 저장소 계정에 대 한 연결을 추가 또는 로컬 저장소 계정에서 컨테이너를 만드는 다음 단계를 수행 하는 동안 오류가 발생할 수 있습니다. 무시 하 고 새로 고침 하세요.

1. Azure Storage 탐색기 다운로드 및 설치

1. 연결 문자열을 사용 하 여 Azure Storage에 연결

1. 연결 문자열을 제공 합니다. `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 연결 하는 단계를 진행 합니다.

1. 로컬 저장소 계정 내의 컨테이너 만들기

1. 블록 blob으로 파일 업로드를 시작 합니다.
   > [!NOTE]
   > 이 모듈에서 페이지 blob을 지원 하지 않습니다.

1. 여기서 데이터를 업로드 하는 Azure storage 계정 연결을 선택할 수 있습니다. 제공 단일 뷰를 로컬 저장소 계정 및 Azure storage 계정 모두에 대 한

## <a name="supported-storage-operations"></a>지원되는 저장소 작업

Blob 저장소 IoT Edge의 동일한 Azure Storage Sdk를 사용 하 여 모듈과의 블록 blob 끝점에 대 한 Azure Storage API 2017-04-17 버전과 일치 합니다. 이후 릴리스는 고객의 요구에 따라 다릅니다.

일부 Azure Blob Storage 작업에서는 IoT Edge의 Azure Blob Storage에서 지원 되므로이 섹션에서는 각각의 상태를 나열 합니다.

### <a name="account"></a>계좌

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
- Blob 나열
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

## <a name="feedback"></a>사용자 의견

여러분의 의견은 유용 하 고 쉽게 사용할 수 있도록이 모듈 및 해당 기능 소중 합니다. 사용자 의견 및 개선 하는 방법에 대해 알려주세요.

에 연결할 수 있습니다 수 absiotfeedback@microsoft.com

## <a name="next-steps"></a>다음 단계

자세한 내용은 [IoT Edge에서 Azure Blob Storage 배포](how-to-deploy-blob.md)
