---
title: Storage Explorer 시작 | Microsoft Docs
description: Storage Explorer로 Azure 스토리지 리소스 관리
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508746"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer 시작

## <a name="overview"></a>개요

Azure Storage Explorer는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 손쉽게 사용할 수 있는 독립 실행형 앱입니다. 이 문서에서는 Azure Storage 계정에 연결하고 관리하는 몇 가지 방법을 알아봅니다.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>필수 조건

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Azure Storage Explorer는 다음 버전의 Windows에서 지원됩니다.

* Windows 10(권장)
* Windows 8
* Windows 7

모든 버전의 Windows에서는 .NET Framework 4.6.2 이상 버전이 필요합니다.

[Storage 탐색기 다운로드 및 설치](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Azure Storage Explorer는 다음 버전의 macOS에서 지원됩니다.

* macOS 10.12 "Sierra" 이상 버전

[Storage 탐색기 다운로드 및 설치](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Azure Storage Explorer는 다음 Linux 배포에서 지원됩니다.

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Azure Storage Explorer는 다른 배포에서 작동할 수도 있지만 위에 나열된 항목만 공식적으로 지원됩니다.

Linux에서 Storage 탐색기를 설치 하는 자세한 도움말을 참조 하세요. 합니다 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)합니다.

Azure Storage Explorer [릴리스 정보](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)는 일부 배포에 대한 특정 단계를 포함합니다.

[Storage 탐색기 다운로드 및 설치](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>저장소 계정 또는 서비스에 연결

Storage Explorer는 Storage 계정에 연결하는 여러 방법을 제공합니다. 일반적으로 다음과 같은 작업을 하거나 수행할 수 있습니다.

* [Azure에 로그인 하 여 구독 및 리소스 액세스](#sign-in-to-azure)
* [특정 저장소 또는 CosmosDB 리소스 연결](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Azure에 로그인

> [!NOTE]
> 리소스에 로그인 한 후에 완전히 액세스 하려면 저장소 탐색기 (ARM) 관리 및 데이터 계층 권한이 모두 필요 합니다. 이 저장소 계정, 계정에서 컨테이너 및 컨테이너의 데이터에 액세스할 수 있는 Azure AD 권한이 필요 하는 것을 의미 합니다. 데이터 계층에만 권한이 있는 경우 사용을 고려 [Azure AD 사용 하 여 연결](#add-a-resource-via-azure-ad)합니다. Storage 탐색기에 필요한 정확한 권한에 대 한 자세한 내용은 참조는 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues)합니다.
>
>

1. Storage Explorer에서 **계정 관리**를 선택하여 **계정 관리 패널**로 이동합니다.

    ![계정 관리][1]

2. 이제 왼쪽 창에는 사용자가 로그인한 모든 Azure 계정이 표시됩니다. 다른 계정에 연결하려면 **계정 추가**를 선택합니다.

3. 국내 클라우드 또는 Azure Stack에 로그인하려는 경우 **Azure 환경** 드롭다운을 클릭하여 사용하려는 Azure 클라우드를 선택합니다. 환경을 선택했으면 **로그인...** 단추를 클릭합니다. Azure Stack에 로그인하는 경우 자세한 내용은 [Azure Stack 구독에 Storage Explorer 연결](/azure-stack/user/azure-stack-storage-connect-se)을 참조하세요.

    ![로그인 옵션][2]

4. Azure Stack 계정으로 성공적으로 로그인하면 계정과 계정에 연결된 Azure Stack 구독이 왼쪽 창에 추가됩니다. 작업하려는 Azure 구독을 선택한 후 **적용**(**모든 구독:** 을 선택하면 나열된 Azure 구독의 전체 선택 및 선택 취소가 전환됨)을 선택합니다.

    ![Azure 구독 선택][3]

    왼쪽 창은 선택한 Azure 구독과 연결된 저장소 계정을 표시합니다.

    ![선택한 Azure 구독][4]

### <a name="attach-a-specific-resource"></a>특정 리소스를 연결 합니다.
    
Storage 탐색기에 리소스 연결에 대 한 옵션을 여러 가지가 있습니다. 다음을 수행할 수 있습니다.

* [Azure AD 통해 리소스 추가](#add-a-resource-via-azure-ad): 데이터 계층에만 권한이 있는 경우 Blob 컨테이너 또는 ADLS Gen2 Blob 컨테이너를 추가 하려면이 옵션을 사용할 수 있습니다.
* [연결 문자열을 사용 하 여](#use-a-connection-string): 저장소 계정에 연결 문자열에 있으면 합니다. Storage 탐색기는 모두 키를 지원 하 고 [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) 연결 문자열입니다.
* [SAS URI를 사용 하 여](#use-a-sas-uri): 있는 경우는 [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI Blob 컨테이너, 파일 공유, 큐 또는 테이블입니다. 사용할 수 있습니다 하는 SAS URI를 가져오려면 [Storage 탐색기](#generate-a-sas-in-storage-explorer) 또는 [Azure portal](https://portal.azure.com)합니다.
* [이름 및 키를 사용 하 여](#use-a-name-and-key): 저장소 계정의 계정 키 중 하나를 알고 있는 경우 신속 하 게 연결 하려면이 옵션을 사용할 수 있습니다. 저장소 계정에 대 한 키 저장소 계정에 위치한 **액세스 키** 에서 블레이드는 [Azure portal](https://portal.azure.com)합니다.
* [로컬 에뮬레이터에 연결할](#attach-to-a-local-emulator): 사용 가능한 Azure Storage 에뮬레이터 중 하나를 사용 하는 경우이 옵션을 사용 하 여 에뮬레이터에 쉽게 연결 하 합니다.
* [연결 문자열을 사용하여 Azure Cosmos DB 계정에 연결](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): CosmosDB 인스턴스를 연결 문자열에 있으면 합니다.
* [URI로 Azure Data Lake Store에 연결할](#connect-to-azure-data-lake-store-by-uri): URI는 Azure Data Lake Store 해야 할 경우.

#### <a name="add-a-resource-via-azure-ad"></a>Azure AD 통해 리소스를 추가 합니다.

1. 엽니다는 **연결 대화 상자** 를 클릭 하 여는 **연결 단추** 왼쪽 세로 도구 모음에서.

    ![Azure Storage 옵션에 연결][9]

2. 아직 수행 하는 경우 사용 합니다 **Azure 계정 추가** 리소스에 대 한 액세스 권한이 있는 Azure 계정에 로그인 하는 옵션입니다. 반환 시 로그인 후 합니다 **연결 대화 상자**합니다.

3. 선택 된 **Azure Active Directory (Azure AD)를 통해 리소스 추가** 옵션을 클릭 **다음**합니다.

4. 연결 하려는 저장소 리소스 및 리소스에 있는 구독에 대 한 액세스 권한이 있는 Azure 계정을 선택 하 고 클릭 **다음**합니다.

5. 연결 하려는 리소스 유형을 선택 하 고 연결 하는 데 필요한 정보를 입력 합니다. 이 페이지에서 입력을 추가 하는 리소스의 유형에 따라 변경 됩니다. 올바른 유형의 리소스를 선택 해야 합니다. 필요한 정보를 입력 했으면 **다음**합니다.

6. 연결 요약을 검토 하 고 모든 정보가 정확한 지 확인 합니다. 모든 정보가 올바르게 표시 되 면 클릭 **Connect**를 사용 하 여 이전 페이지로 돌아가서이 고, 그렇지는 **다시** 잘못 된 정보를 수정 하는 단추입니다.

연결이 성공적으로 추가 되 면 리소스 트리의 해당 연결을 나타내는 노드에 자동으로 이동 됩니다. 어떤 이유로, 다르게 아래 **로컬 및 연결 됨** → **저장소 계정** → **(연결 된 컨테이너)** → **Blob 컨테이너** . Storage 탐색기에서 연결을 추가할 수 없는 경우 또는 연결을 성공적으로 추가한 후 데이터에 액세스 참조 수 없는 경우는 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 도움말에 대 한 합니다.

#### <a name="use-a-connection-string"></a>연결 문자열 사용

1. 엽니다는 **연결 대화 상자** 를 클릭 하 여는 **연결 단추** 왼쪽 세로 도구 모음에서.

    ![Azure Storage 옵션에 연결][9]

2. 선택 된 **연결 문자열을 사용 하 여** 옵션을 클릭 **다음**합니다.

3. 연결에 대 한 표시 이름을 선택 하 고 연결 문자열에 입력 합니다. 그런 후 **Next** 를 클릭합니다.

4. 연결 요약을 검토 하 고 모든 정보가 정확한 지 확인 합니다. 모든 정보가 올바르게 표시 되 면 클릭 **Connect**를 사용 하 여 이전 페이지로 돌아가서이 고, 그렇지는 **다시** 잘못 된 정보를 수정 하는 단추입니다.

연결이 성공적으로 추가 되 면 리소스 트리의 해당 연결을 나타내는 노드에 자동으로 이동 됩니다. 어떤 이유로, 다르게 아래 **로컬 및 연결 됨** → **저장소 계정**합니다. Storage 탐색기에서 연결을 추가할 수 없는 경우 또는 연결을 성공적으로 추가한 후 데이터에 액세스 참조 수 없는 경우는 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 도움말에 대 한 합니다.

#### <a name="use-a-sas-uri"></a>SAS URI 사용

1. 엽니다는 **연결 대화 상자** 를 클릭 하 여는 **연결 단추** 왼쪽 세로 도구 모음에서.

    ![Azure Storage 옵션에 연결][9]

2. 선택 된 **공유 액세스 서명 (SAS) URI 사용 하 여** 옵션을 클릭 **다음**합니다.

3. 연결에 대 한 표시 이름을 선택 하 고 SAS URI를 입력 합니다. 서비스 끝점을 연결 하는 리소스의 형식에 대 한 자동 채우기를 해야 합니다. 사용자 지정 끝점을 사용 하는 경우 다음 있기 하지 않을 수 있습니다. **다음**을 클릭합니다.

4. 연결 요약을 검토 하 고 모든 정보가 정확한 지 확인 합니다. 모든 정보가 올바르게 표시 되 면 클릭 **Connect**를 사용 하 여 이전 페이지로 돌아가서이 고, 그렇지는 **다시** 잘못 된 정보를 수정 하는 단추입니다.

연결이 성공적으로 추가 되 면 리소스 트리의 해당 연결을 나타내는 노드에 자동으로 이동 됩니다. 어떤 이유로, 다르게 아래 **로컬 및 연결 됨** → **저장소 계정** → **(연결 된 컨테이너)** → **유형에 대 한 서비스 노드 컨테이너 연결**합니다. Storage 탐색기에서 연결을 추가할 수 없는 경우 또는 연결을 성공적으로 추가한 후 데이터에 액세스 참조 수 없는 경우는 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 도움말에 대 한 합니다.

#### <a name="use-a-name-and-key"></a>이름 및 키 사용

1. 엽니다는 **연결 대화 상자** 를 클릭 하 여는 **연결 단추** 왼쪽 세로 도구 모음에서.

    ![Azure Storage 옵션에 연결][9]

2. 선택 된 **저장소 계정 이름과 키를 사용 하 여** 옵션을 클릭 **다음**합니다.

3. 연결의 표시 이름을 선택 합니다.

4. 저장소 계정 이름 및 해당 액세스 키 중 하나를 입력 합니다.

5. 선택 된 **저장소 도메인** 를 클릭 한 다음 **다음**합니다.

4. 연결 요약을 검토 하 고 모든 정보가 정확한 지 확인 합니다. 모든 정보가 올바르게 표시 되 면 클릭 **Connect**를 사용 하 여 이전 페이지로 돌아가서이 고, 그렇지는 **다시** 잘못 된 정보를 수정 하는 단추입니다.

연결이 성공적으로 추가 되 면 리소스 트리의 해당 연결을 나타내는 노드에 자동으로 이동 됩니다. 어떤 이유로, 다르게 아래 **로컬 및 연결 됨** → **저장소 계정**합니다. Storage 탐색기에서 연결을 추가할 수 없는 경우 또는 연결을 성공적으로 추가한 후 데이터에 액세스 참조 수 없는 경우는 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 도움말에 대 한 합니다.

#### <a name="attach-to-a-local-emulator"></a>로컬 에뮬레이터에 연결

Storage 탐색기는 모든 플랫폼에서 에뮬레이터를 지원합니다. 두 현재 사용 가능한 공식 에뮬레이터 다음과 같습니다.
* [Azure storage 에뮬레이터](storage/common/storage-use-emulator.md) (Windows만 해당)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS 또는 Linux)

에뮬레이터에는 기본 포트에서 실행 되는 경우 사용할 수 있습니다 합니다 **에뮬레이터-기본 포트** 노드 아래에서 항상 확인할 수 있습니다 **로컬 및 연결 됨** → **저장소 계정** 을 에뮬레이터에 빠르게 액세스할 수 있습니다. 연결에 다른 이름을 사용 하거나 기본 포트에 대해 에뮬레이터를 실행 하지 않는 경우 다음 하려는 경우는 아래 단계입니다.

1. 에뮬레이터를 시작 합니다. 포트의 참고를 수행 하는 경우 에뮬레이터는 각 서비스 유형에 대해에서 수신 합니다. 나중에이 정보를 확인 해야 합니다.

   > [!IMPORTANT]
   > Storage Explorer는 자동으로 에뮬레이터를 시작하지 않습니다. 사용자가 직접 시작해야 합니다.

2. 엽니다는 **연결 대화 상자** 를 클릭 하 여는 **연결 단추** 왼쪽 세로 도구 모음에서.

    ![Azure Storage 옵션에 연결][9]

3. 선택 된 **로컬 에뮬레이터에 연결** 옵션을 클릭 **다음**합니다.

4. 연결에 대 한 표시 이름을 선택 하 고 에뮬레이터에는 각 서비스 형식에 대 한 수신 대기 포트를 입력 합니다. 기본적으로 입력란 대부분의 에뮬레이터에 대 한 기본 포트 값이 포함 됩니다. **포트 파일** 도 비어 기본적으로 모두 공식 에뮬레이터 현재 파일 서비스를 지원 합니다. 사용 중인 에뮬레이터는 지원 하지만, 경우에 사용 되는 포트에 입력할 수 있습니다. **다음**을 클릭합니다.

5. 연결 요약을 검토 하 고 모든 정보가 정확한 지 확인 합니다. 모든 정보가 올바르게 표시 되 면 클릭 **Connect**를 사용 하 여 이전 페이지로 돌아가서이 고, 그렇지는 **다시** 잘못 된 정보를 수정 하는 단추입니다.

연결이 성공적으로 추가 되 면 리소스 트리의 해당 연결을 나타내는 노드에 자동으로 이동 됩니다. 어떤 이유로, 다르게 아래 **로컬 및 연결 됨** → **저장소 계정**합니다. Storage 탐색기에서 연결을 추가할 수 없는 경우 또는 연결을 성공적으로 추가한 후 데이터에 액세스 참조 수 없는 경우는 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 도움말에 대 한 합니다.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>연결 문자열을 사용하여 Azure Cosmos DB 계정에 연결

Azure 구독을 통해 Azure Cosmos DB 계정을 관리하는 것 외에, Azure Cosmos DB에 연결하는 또 다른 방법은 연결 문자열을 사용하는 것입니다. 연결 문자열을 사용하여 연결하려면 다음 단계를 따르세요.

1. 왼쪽 트리에서 **Local and Attached**(로컬 및 연결)를 찾아 **Azure Cosmos DB 계정**을 마우스 오른쪽 단추로 클릭한 후 **Connect to Azure Cosmos DB...** (Azure Cosmos DB에 연결...)를 선택합니다.

    ![연결 문자열을 사용하여 Azure Cosmos DB에 연결][21]

2. Azure Cosmos DB API를 선택하고 **연결 문자열**을 붙여 넣은 후 **확인**을 클릭하여 Azure Cosmos DB 계정을 연결합니다. 연결 문자열 검색에 대한 자세한 내용은 [Get the connection string](https://docs.microsoft.com/azure/cosmos-db/manage-account)(연결 문자열 가져오기)을 참조하세요.

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>URI로 Azure Data Lake Store에 연결

구독에 없는 리소스에 액세스하려고 합니다. 하지만 리소스에 대한 URI를 가져올 수 있도록 다른 사용자가 권한을 부여합니다. 이 경우 로그인한 후 URI를 사용하여 Data Lake Store에 연결할 수 있습니다. 다음 단계를 참조하세요.

1. Storage Explorer를 엽니다.
2. 왼쪽 창에서 **로컬 및 연결됨**을 펼칩니다.
3. **Data Lake Store**를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **Data Lake Store에 연결...** 을 선택합니다.

    ![Data Lake Store 바로 가기 메뉴에 연결](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. URI를 입력하면 도구가 방금 입력한 URL의 위치로 이동합니다.

    ![Data Lake Store에 연결 팝업 대화 상자](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Data Lake Store에 연결 결과](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Storage 탐색기에서 SAS를 생성 합니다.

### <a name="account-level-sas"></a>계정 수준 SAS

1. 저장소 계정을 공유 하 고 선택한 마우스 오른쪽 단추로 클릭 **공유 액세스 서명 가져오기...** .

    ![SAS 상황에 맞는 메뉴 옵션 가져오기][14]

2. **공유 액세스 서명 생성** 대화 상자에서 계정에 대해 원하는 시간 프레임 및 권한을 지정하고 **만들기** 단추를 클릭합니다.

    ![SAS 가져오기 대화 상자][15]

3. 수행할 수 있습니다. 복사 하거나 합니다 **연결 문자열** 또는 원시 **쿼리 문자열** 클립보드에 합니다.

### <a name="service-level-sas"></a>서비스 수준 SAS

[Storage 탐색기에서 blob 컨테이너에 대 한 SAS를 가져오는 방법](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>저장소 계정 검색

저장소 리소스를 찾아야 하며 위치를 잘 모를 경우, 왼쪽 창 위쪽에 있는 검색 상자를 사용하여 리소스를 검색할 수 있습니다.

검색 상자에 내용을 입력하면 왼쪽 창에 해당 시점까지 사용자가 입력한 검색 값과 일치하는 모든 리소스가 표시됩니다. 예를 들어 **엔드포인트** 검색 결과는 다음 스크린샷에 표시됩니다.

![Storage 계정 검색][23]

> [!NOTE]
> 검색 실행 시간 개선을 위해, **계정 관리 패널**을 사용하여 검색하는 항목이 포함되지 않은 모든 구독은 선택 취소합니다. 노드를 마우스 오른쪽 단추로 클릭하고 **여기에서 검색**을 선택하여 특정 노드에서 검색을 시작할 수도 있습니다.
>
>

## <a name="next-steps"></a>다음 단계

* [Storage 탐색기를 사용하여 Azure Blob Storage 리소스 관리](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Storage 탐색기에서 Azure Cosmos DB 관리(미리 보기)](./cosmos-db/storage-explorer.md)
* [Storage 탐색기를 사용하여 Azure Data Lake Store 리소스 관리](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
