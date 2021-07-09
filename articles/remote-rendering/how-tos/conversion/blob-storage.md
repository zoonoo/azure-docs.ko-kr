---
title: 모델 변환에 Azure Blob Storage 사용
description: 모델 변환을 위해 Blob 스토리지를 설정하고 사용하는 일반적인 단계에 대해 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 742bf5eb7e33f7121325216d8880f77dadc85155
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803983"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>모델 변환에 Azure Blob Storage 사용

[모델 변환](model-conversion.md) 서비스는 입력 데이터를 검색하고 출력 데이터를 저장하기 위해 Azure Blob Storage에 액세스해야 합니다. 이 문서에서는 가장 일반적인 단계를 수행하는 방법에 대해 설명합니다.

## <a name="prepare-azure-storage-accounts"></a>Azure Storage 계정 준비

- 스토리지 계정(StorageV2) 만들기
- 스토리지 계정에 입력 Blob 컨테이너(예: "arrinput") 만들기
- 스토리지 계정에 출력 Blob 컨테이너(예: "arroutput") 만들기

> [!TIP]
> 스토리지 계정을 설정하는 방법에 대한 단계별 지침은 [빠른 시작: 렌더링을 위한 모델 변환](../../quickstarts/convert-model.md)을 참조하세요.

스토리지 계정 및 Blob 컨테이너 만들기는 다음 도구 중 하나를 사용하여 수행할 수 있습니다.

- [Azure Portal](https://portal.azure.com)
- [az 명령줄](/cli/azure/install-azure-cli)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- SDK(C#, Python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Azure Remote Rendering에서 스토리지 계정에 액세스 할 수 있는지 확인

Azure Remote Rending은 스토리지 계정에서 모델 데이터를 검색하고 데이터를 해당 계정에 다시 써야 합니다.

다음 두 가지 방법으로 스토리지 계정에 대한 Azure Remote Rendering 액세스 권한을 부여할 수 있습니다.

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Azure Storage 계정을 Azure Remote Rendering 계정에 연결

[계정 만들기](../create-an-account.md#link-storage-accounts) 섹션에 나와 있는 단계를 따릅니다.

### <a name="retrieve-sas-for-the-storage-containers"></a>스토리지 컨테이너에 대한 SAS 검색

SAS(저장된 액세스 서명)는 입력에 대한 읽기 액세스 권한 및 출력에 대한 쓰기 액세스 권한을 부여하는 데 사용됩니다. 모델이 변환될 때마다 새 URI를 생성하는 것이 좋습니다. URI는 일정 시간이 지나면 만료되므로 더 오래 유지하면 애플리케이션이 예기치 않게 중단될 수 있습니다.

SAS에 대한 자세한 내용은 [SAS 설명서](../../../storage/common/storage-sas-overview.md)를 참조하세요.

SAS URI는 다음 중 하나를 사용하여 생성할 수 있습니다.

- az PowerShell 모듈
  - [예제 PowerShell 스크립트](../../samples/powershell-example-scripts.md) 참조
- [az 명령줄](/cli/azure/install-azure-cli)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
  - 마우스 오른쪽 단추로 "공유 액세스 서명 가져오기" 컨테이너(읽기, 입력 컨테이너에 대한 목록 액세스, 출력 컨테이너에 대한 쓰기 액세스)를 클릭합니다.
- SDK(C#, Python...)

자산 변환에서 공유 액세스 서명을 사용하는 예제는 [Powershell 예제 스크립트](../../samples/powershell-example-scripts.md#script-conversionps1)의 Conversion.ps1에 나와 있습니다.

## <a name="upload-an-input-model"></a>입력 모델 업로드

모델 변환을 시작하려면 다음 옵션 중 하나를 사용하여 모델을 업로드해야 합니다.

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) - Azure Blob Storage에서 파일을 업로드/다운로드/관리하는 편리한 UI
- [Azure 명령줄](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Azure PowerShell 모듈](/powershell/azure/install-az-ps)
  - [예제 PowerShell 스크립트](../../samples/powershell-example-scripts.md) 참조
- [스토리지 SDK 사용(Python, C# ...)](../../../storage/index.yml)
- [Azure Storage REST API 사용](/rest/api/storageservices/blob-service-rest-api)

변환할 데이터를 업로드하는 방법에 대한 예제는 [Powershell 예제 스크립트](../../samples/powershell-example-scripts.md#script-conversionps1)의 Conversion.ps1을 참조하세요.

> [!Note]
> 입력 모델을 업로드하는 경우 서비스에서 [Windows 경로 길이 제한](/windows/win32/fileio/maximum-file-path-limitation) 문제를 방지하기 위해 긴 파일 이름 및/또는 폴더 구조를 사용하지 않도록 주의해야 합니다. 

## <a name="get-a-sas-uri-for-the-converted-model"></a>변환된 모델에 대한 SAS URI 가져오기

이 단계는 [스토리지 컨테이너에 대한 SAS를 검색](#retrieve-sas-for-the-storage-containers)하는 단계와 비슷합니다. 그러나 이번에는 출력 컨테이너에 기록된 모델 파일에 대한 SAS URI를 검색해야 합니다.

예를 들어 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 통해 SAS URI를 검색하려면 마우스 오른쪽 단추로 모델 파일을 클릭하고 "공유 액세스 서명 가져오기"를 선택합니다.

스토리지 계정을 Azure Remote Rendering 계정에 연결하지 않은 경우 모델을 로드하기 위한 SAS(공유 액세스 서명)가 필요합니다. 계정을 연결하는 방법은 [계정 만들기](../create-an-account.md#link-storage-accounts)에서 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환 구성](configure-model-conversion.md)
- [모델 변환 REST API](conversion-rest-api.md)
