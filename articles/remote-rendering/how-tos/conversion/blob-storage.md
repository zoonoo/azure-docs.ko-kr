---
title: 모델 변환에 Azure Blob Storage 사용
description: 모델 변환을 위해 blob storage를 설정 및 사용 하는 일반적인 단계에 대해 설명 합니다.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80681650"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>모델 변환에 Azure Blob Storage 사용

[모델 변환](model-conversion.md) 서비스는 입력 데이터를 검색 하 고 출력 데이터를 저장할 수 있도록 Azure blob storage에 액세스 해야 합니다. 이 문서에서는 가장 일반적인 단계를 수행 하는 방법을 설명 합니다.

## <a name="prepare-azure-storage-accounts"></a>Azure Storage 계정 준비

- 저장소 계정 만들기 (StorageV2)
- 저장소 계정에 입력 blob 컨테이너를 만듭니다 (예: "arrinput").
- 저장소 계정에 출력 blob 컨테이너를 만듭니다 (예: "arroutput").

> [!TIP]
> 저장소 계정을 설정 하는 방법에 대 한 단계별 지침은 [빠른 시작: 렌더링을 위한 모델 변환](../../quickstarts/convert-model.md) 을 참조 하세요.

저장소 계정 및 blob 컨테이너 만들기는 다음 도구 중 하나를 사용 하 여 수행할 수 있습니다.

- [Azure Portal](https://portal.azure.com)
- [az command line](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- Sdk (c #, Python ...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Azure 원격 렌더링이 저장소 계정에 액세스할 수 있는지 확인

Azure 원격 렌더링는 저장소 계정에서 모델 데이터를 검색 하 고 다시 데이터를 써야 합니다.

다음 두 가지 방법으로 Azure 원격 렌더링에 저장소 계정에 대 한 액세스 권한을 부여할 수 있습니다.

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Azure 원격 렌더링 계정에 Azure Storage 계정 연결

[계정 만들기](../create-an-account.md#link-storage-accounts) 섹션에 설명 된 단계를 따릅니다.

### <a name="retrieve-sas-for-the-storage-containers"></a>저장소 컨테이너에 대 한 SAS 검색

저장 된 액세스 서명 (SAS)은 입력에 대 한 읽기 액세스 권한을 부여 하 고 출력에 대 한 쓰기 권한을 부여 하는 데 사용 됩니다. 모델을 변환할 때마다 새 Uri를 생성 하는 것이 좋습니다. Uri는 일정 시간 후에 만료 되므로 긴 기간 동안 유지 하면 응용 프로그램이 예기치 않게 중단 될 수 있습니다.

Sas에 대 한 자세한 내용은 [sas 설명서](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)에서 찾을 수 있습니다.

SAS URI는 다음 중 하나를 사용 하 여 생성할 수 있습니다.

- az PowerShell module
  - [예제 PowerShell 스크립트](../../samples/powershell-example-scripts.md) 를 참조 하세요.
- [az command line](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
  - 컨테이너 "공유 액세스 서명 가져오기"를 마우스 오른쪽 단추로 클릭 (읽기, 입력 컨테이너에 대 한 액세스 나열, 출력 컨테이너에 대 한 쓰기 권한)
- Sdk (c #, Python ...)

자산 변환에서 공유 액세스 서명을 사용 하는 예는 [Powershell 예제 스크립트](../../samples/powershell-example-scripts.md#script-conversionps1)의 Conversion.ps1에 나와 있습니다.

## <a name="upload-an-input-model"></a>입력 모델 업로드

모델 변환을 시작 하려면 다음 옵션 중 하나를 사용 하 여 모델을 업로드 해야 합니다.

- [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) -Azure blob Storage에서 파일을 업로드/다운로드/관리 하는 편리한 UI
- [Azure 명령줄](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - [예제 PowerShell 스크립트](../../samples/powershell-example-scripts.md) 를 참조 하세요.
- [저장소 SDK 사용 (Python, c # ...)](https://docs.microsoft.com/azure/storage/)
- [Azure Storage REST Api 사용](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

변환을 위해 데이터를 업로드 하는 방법에 대 한 예는 [Powershell 예제 스크립트](../../samples/powershell-example-scripts.md#script-conversionps1)의 Conversion.ps1을 참조 하세요.

## <a name="get-a-sas-uri-for-the-converted-model"></a>변환 된 모델에 대 한 SAS URI 가져오기

이 단계는 [저장소 컨테이너에 대 한 SAS를 검색](#retrieve-sas-for-the-storage-containers)하는 것과 비슷합니다. 그러나 이번에는 출력 컨테이너에 기록 된 모델 파일에 대 한 SAS URI를 검색 해야 합니다.

예를 들어 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 통해 SAS URI를 검색 하려면 모델 파일을 마우스 오른쪽 단추로 클릭 하 고 "공유 액세스 서명 가져오기"를 선택 합니다.

저장소 계정을 Azure 원격 렌더링 계정에 연결 하지 않은 경우 모델을 로드 하기 위한 SAS (공유 액세스 서명)가 필요 합니다. 계정 [만들기](../create-an-account.md#link-storage-accounts)에서 계정을 연결 하는 방법을 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환 구성](configure-model-conversion.md)
- [모델 변환 REST API](conversion-rest-api.md)
