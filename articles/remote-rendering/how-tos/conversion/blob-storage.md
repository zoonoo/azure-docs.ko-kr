---
title: 모델 변환에 Azure Blob 저장소 사용
description: 모델 변환에 Blob 저장소를 설정하고 사용하는 일반적인 단계를 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681650"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>모델 변환에 Azure Blob 저장소 사용

[모델 변환](model-conversion.md) 서비스는 입력 데이터를 검색하고 출력 데이터를 저장할 수 있도록 Azure Blob 저장소에 대한 액세스가 필요합니다. 이 문서에서는 가장 일반적인 단계를 수행하는 방법에 대해 설명합니다.

## <a name="prepare-azure-storage-accounts"></a>Azure 저장소 계정 준비

- 저장소 계정 만들기(StorageV2)
- 저장소 계정에 입력 Blob 컨테이너 만들기(예: "arrinput").
- 저장소 계정에서 출력 Blob 컨테이너 만들기(예: "arroutput").

> [!TIP]
> 저장소 계정을 설정하는 방법에 대한 단계별 지침은 [빠른 시작: 렌더링을 위한 모델 변환을](../../quickstarts/convert-model.md) 참조하세요.

저장소 계정 및 Blob 컨테이너를 만드는 작업은 다음 도구 중 하나로 수행할 수 있습니다.

- [Azure portal](https://portal.azure.com)
- [az 명령줄](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- SDK (C #, 파이썬 ... ) )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Azure 원격 렌더링이 저장소 계정에 액세스할 수 있는지 확인

Azure 원격 Rending 저장소 계정에서 모델 데이터를 검색 하 고 다시 데이터를 작성 해야 합니다.

다음 두 가지 방법으로 저장소 계정에 Azure 원격 렌더링 액세스 권한을 부여할 수 있습니다.

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Azure 저장소 계정을 Azure 원격 렌더링 계정과 연결

계정 만들기 섹션에서 제공된 단계를 [따릅니다.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>저장소 컨테이너에 대한 SAS 검색

저장된 액세스 서명(SAS)은 입력에 대한 읽기 액세스 권한을 부여하고 출력에 대한 쓰기 액세스를 부여하는 데 사용됩니다. 모델을 변환할 때마다 새 URI를 생성하는 것이 좋습니다. URI는 잠시 후 만료되므로 더 긴 기간 동안 유지하면 응용 프로그램이 예기치 않게 중단될 수 있습니다.

SAS에 대한 자세한 내용은 [SAS 설명서에서](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)확인할 수 있습니다.

SAS URI는 다음 중 하나를 사용하여 생성할 수 있습니다.

- 아즈 파워쉘 모듈
  - 예제 [PowerShell 스크립트](../../samples/powershell-example-scripts.md) 참조
- [az 명령줄](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
  - "공유 액세스 서명 받기"를 마우스 오른쪽 버튼으로 클릭합니다(읽기, 입력 컨테이너에 대한 목록 액세스, 출력 컨테이너에 대한 쓰기 액세스)
- SDK (C #, 파이썬 ... ) )

자산 변환에서 공유 액세스 서명을 사용하는 예제는 [Powershell 예제 스크립트의](../../samples/powershell-example-scripts.md#script-conversionps1)Convert.ps1에 표시됩니다.

## <a name="upload-an-input-model"></a>입력 모델 업로드

모델 변환을 시작하려면 다음 옵션 중 하나를 사용하여 모델을 업로드해야 합니다.

- [Azure 저장소 탐색기](https://azure.microsoft.com/features/storage-explorer/) - Azure Blob 저장소에서 파일을 업로드/다운로드/관리할 수 있는 편리한 UI
- [Azure 명령줄](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - 예제 [PowerShell 스크립트](../../samples/powershell-example-scripts.md) 참조
- [스토리지 SDK (파이썬, C # ... ) 를 사용 하 여](https://docs.microsoft.com/azure/storage/)
- [Azure 저장소 REST API 사용](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

변환을 위해 데이터를 업로드하는 방법의 예는 [Powershell 예제 스크립트의](../../samples/powershell-example-scripts.md#script-conversionps1)Convert.ps1을 참조하십시오.

## <a name="get-a-sas-uri-for-the-converted-model"></a>변환된 모델에 대한 SAS URI 받기

이 단계는 [저장소 컨테이너에 대한 SAS 검색과 유사합니다.](#retrieve-sas-for-the-storage-containers) 그러나 이번에는 출력 컨테이너에 기록 된 모델 파일에 대 한 SAS URI를 검색 해야 합니다.

예를 들어 [Azure 저장소 탐색기를](https://azure.microsoft.com/features/storage-explorer/)통해 SAS URI를 검색하려면 모델 파일을 마우스 오른쪽 단추로 클릭하고 "공유 액세스 서명 받기"를 선택합니다.

저장소 계정을 Azure 원격 렌더링 계정에 연결하지 않은 경우 모델을 로드하는 SAS(공유 액세스 서명)가 필요합니다. [계정 만들기에서](../create-an-account.md#link-storage-accounts)계정을 연결하는 방법을 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환 구성](configure-model-conversion.md)
- [모델 변환 REST API](conversion-rest-api.md)
