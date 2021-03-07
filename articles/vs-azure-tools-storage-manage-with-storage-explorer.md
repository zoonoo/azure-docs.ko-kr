---
title: Storage Explorer 시작 | Microsoft Docs
description: Storage 탐색기를 사용 하 여 Azure storage 리소스 관리를 시작 합니다. Azure Storage 탐색기를 다운로드 하 여 설치 하 고, 저장소 계정 또는 서비스에 연결 합니다.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441591"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer 시작

## <a name="overview"></a>개요

Microsoft Azure Storage Explorer는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 쉽게 사용할 수 있게 하는 독립 실행형 앱입니다.

이 문서에서는 Azure storage 계정에 연결 하 고 관리 하는 몇 가지 방법을 알아봅니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer" source="./vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>필수 구성 요소

# <a name="windows"></a>[Windows](#tab/windows)

다음 버전의 Windows에서는 Storage 탐색기을 지원 합니다.

* Windows 10(권장)
* Windows 8
* Windows 7

모든 버전의 Windows에서는 Storage 탐색기에 최소한 .NET Framework 4.7.2 필요 합니다.

# <a name="macos"></a>[macOS](#tab/macos)

다음 버전의 macOS 지원 Storage 탐색기:

* macOS 10.12 시에라리온 이상 버전

# <a name="linux"></a>[Linux](#tab/linux)

Storage 탐색기은 Linux의 가장 일반적인 배포를 위해 [Snap 저장소](https://snapcraft.io/storage-explorer) 에서 사용할 수 있습니다. 이 설치에 대해 Snap Store를 권장 합니다. Storage 탐색기 snap는 새 버전이 Snap 저장소에 게시 될 때 모든 종속성 및 업데이트를 설치 합니다.

지원 되는 배포에 대해서는 [ `snapd` 설치 페이지](https://snapcraft.io/docs/installing-snapd)를 참조 하세요.

Storage 탐색기 암호 관리자를 사용 해야 합니다. 수동으로 암호 관리자에 연결 해야 할 수도 있습니다. 다음 명령을 실행 하 여 시스템의 암호 관리자에 Storage 탐색기를 연결할 수 있습니다.

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage 탐색기는 *release.tar.gz* 다운로드로도 사용할 수 있습니다. *Release.tar.gz* 를 사용 하는 경우 종속성을 수동으로 설치 해야 합니다. Release.tar.gz 설치는 다음과 같은 Linux의 배포를 지원 *합니다* .

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

*Release.tar.gz* 설치는 다른 배포판에서 작동할 수 있지만 나열 된 배포만 공식적으로 지원 됩니다.

Linux에 Storage 탐색기를 설치 하는 데 대 한 자세한 내용은 Azure Storage 탐색기 문제 해결 가이드에서 [linux 종속성](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) 을 참조 하세요.

---

## <a name="download-and-install"></a>다운로드 및 설치

Storage 탐색기를 다운로드 하 고 설치 하려면 [Azure Storage 탐색기](https://www.storageexplorer.com)를 참조 하세요.

## <a name="connect-to-a-storage-account-or-service"></a>스토리지 계정 또는 서비스에 연결

Storage 탐색기은 Azure 리소스에 연결 하는 여러 방법을 제공 합니다.

* [Azure에 로그인 하 여 구독 및 해당 리소스에 액세스](#sign-in-to-azure)
* [개별 Azure Storage 리소스에 연결](#attach-to-an-individual-resource)
* [CosmosDB 리소스에 연결](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Azure에 로그인

> [!NOTE]
> 로그인 한 후 리소스에 완전히 액세스 하려면 Storage 탐색기 관리 (Azure Resource Manager)와 데이터 계층 권한이 모두 필요 합니다. 즉, 저장소 계정, 계정의 컨테이너 및 컨테이너의 데이터에 액세스할 수 있는 Azure Active Directory (Azure AD) 권한이 있어야 합니다. 데이터 계층 에서만 사용 권한이 있는 경우 리소스에 연결할 때 **Azure Active Directory (AZURE AD)를 사용 하 여 로그인** 옵션을 선택 하는 것이 좋습니다. Storage 탐색기 필요한 특정 사용 권한에 대 한 자세한 내용은 [Azure Storage 탐색기 문제 해결 가이드](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues)를 참조 하세요.

1. Storage 탐색기에서   >  **계정 관리** 보기를 선택 하거나 **계정 관리** 단추를 선택 합니다.

    :::image type="content" alt-text="계정 관리" source ="./vs-storage-explorer-manage-accounts.png":::

1. 이제 **계정 관리** 에서 로그인 한 모든 Azure 계정을 표시 합니다. 다른 계정에 연결 하려면 **계정 추가 ...** 를 선택 합니다.

1. **Azure Storage에 연결** 대화 상자가 열립니다. **리소스 선택** 패널에서 **구독** 을 선택 합니다.

    :::image type="content" alt-text="연결 대화 상자" source="./vs-storage-explorer-connect-dialog.png":::

1. **Azure 환경 선택** 패널에서 로그인 할 azure 환경을 선택 합니다. 글로벌 Azure, 국가 클라우드 또는 Azure Stack 인스턴스에 로그인 할 수 있습니다. 그런 후 **다음** 을 선택합니다.

    :::image type="content" alt-text="로그인 옵션" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Azure Stack에 대 한 자세한 내용은 [Azure Stack 구독 또는 저장소 계정에 Storage 탐색기 연결](/azure-stack/user/azure-stack-storage-connect-se)을 참조 하세요.

1. Storage 탐색기는 로그인 할 웹 페이지를 엽니다.

1. Azure 계정으로 성공적으로 로그인 하면 해당 계정과 연결 된 계정 및 Azure 구독이 **계정 관리** 아래에 나타납니다. 작업하려는 Azure 구독을 선택한 후 **적용** 을 선택합니다.

    :::image type="content" alt-text="Azure 구독 선택" source="./vs-storage-explorer-account-panel.png":::

1. **탐색기** 선택한 Azure 구독과 연결 된 저장소 계정을 표시 합니다.

    :::image type="content" alt-text="선택한 Azure 구독" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>개별 리소스에 연결

Storage 탐색기를 사용 하면 다양 한 인증 방법을 사용 하 여 Azure Data Lake Storage Gen2 컨테이너와 같은 개별 리소스에 연결할 수 있습니다. 일부 인증 방법은 특정 리소스 종류에 대해서만 지원 됩니다.

| 리소스 종류    | Azure AD | 계정 이름 및 키 | 공유 액세스 서명(SAS)  | 공용 (익명) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Storage 계정 | 예      | 예                  | 예 (연결 문자열 또는 URL) | No                 |
| Blob 컨테이너  | 예      | 예                   | 예 (URL)                      | 예                |
| Gen2 컨테이너  | 예      | 예                   | 예 (URL)                      | 예                |
| Gen2 디렉터리 | 예      | 예                   | 예 (URL)                      | 예                |
| 파일 공유      | 예       | 예                   | 예 (URL)                      | No                 |
| 큐           | 예      | 예                   | 예 (URL)                      | No                 |
| 테이블           | 예       | 예                   | 예 (URL)                      | No                 |
 
Storage 탐색기 에뮬레이터의 구성 된 포트를 사용 하 여 [로컬 저장소 에뮬레이터](#local-storage-emulator) 에 연결할 수도 있습니다.

개별 리소스에 연결 하려면 왼쪽 도구 모음에서 **연결** 단추를 선택 합니다. 그런 다음 연결 하려는 리소스 종류에 대 한 지침을 따릅니다.

:::image type="content" alt-text="Azure Storage 옵션에 연결" source="./vs-storage-explorer-connect-button.png":::

저장소 계정에 대 한 연결이 성공적으로 추가 되 면 **로컬 & 연결 된**  >  **저장소 계정** 아래에 새 트리 노드가 표시 됩니다.

다른 리소스 유형의 경우에는 **로컬 & 연결** 된  >  **저장소 계정**  >  **(연결 된 컨테이너)** 아래에 새 노드가 추가 됩니다. 노드가 해당 유형과 일치 하는 그룹 노드 아래에 나타납니다. 예를 들어 Azure Data Lake Storage Gen2 컨테이너에 대 한 새 연결이 **Blob 컨테이너** 아래에 표시 됩니다.

Storage 탐색기 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure Storage 탐색기 문제 해결 가이드](./storage/common/storage-explorer-troubleshooting.md)를 참조 하세요.

다음 섹션에서는 개별 리소스에 연결 하는 데 사용할 수 있는 다양 한 인증 방법을 설명 합니다.

#### <a name="azure-ad"></a>Azure AD

Storage 탐색기 Azure 계정을 사용 하 여 다음 리소스 유형에 연결할 수 있습니다.
* Blob 컨테이너
* Azure Data Lake Storage Gen2 컨테이너
* Azure Data Lake Storage Gen2 디렉터리
* 큐
 
리소스에 대 한 데이터 계층 액세스는 있지만 관리 계층 액세스가 없는 경우 Azure AD가 기본 설정 된 옵션입니다.

1. [위에 설명 된 단계](#sign-in-to-azure)를 사용 하 여 하나 이상의 Azure 계정에 로그인 합니다.
1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 **Blob 컨테이너**, **ADLS Gen2 컨테이너** 또는 **큐** 를 선택 합니다.
1. **Azure Active Directory (AZURE AD)를 사용 하 여 로그인** 을 선택 하 고 **다음** 을 선택 합니다.
1. Azure 계정 및 테 넌 트를 선택 합니다. 계정 및 테 넌 트는 연결 하려는 저장소 리소스에 대 한 액세스 권한이 있어야 합니다. **다음** 을 선택합니다.
1. 연결에 대 한 표시 이름 및 리소스의 URL을 입력 합니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토 합니다. 연결 정보가 올바르면 **연결** 을 선택 합니다.

#### <a name="account-name-and-key"></a>계정 이름 및 키

저장소 계정의 이름 및 키를 사용 하 여 저장소 계정에 연결할 수 Storage 탐색기.

[Azure Portal](https://portal.azure.com)에서 계정 키를 찾을 수 있습니다. 저장소 계정 페이지를 열고 **설정**  >  **액세스 키** 를 선택 합니다.

1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 **저장소 계정** 을 선택 합니다.
1. **계정 이름 및 키** 를 선택 하 고 **다음** 을 선택 합니다.
1. 연결에 대 한 표시 이름, 계정 이름 및 계정 키 중 하나를 입력 합니다. 적절 한 Azure 환경을 선택 합니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토 합니다. 연결 정보가 올바르면 **연결** 을 선택 합니다.

#### <a name="shared-access-signature-sas-connection-string"></a>SAS (공유 액세스 서명) 연결 문자열

SAS (공유 액세스 서명)가 포함 된 연결 문자열을 사용 하 여 저장소 계정에 연결할 수 Storage 탐색기. SAS 연결 문자열은 다음과 같습니다.

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 **저장소 계정** 을 선택 합니다.
1. **SAS (공유 액세스 서명)** 를 선택 하 고 **다음** 을 선택 합니다.
1. 연결에 대 한 표시 이름 및 저장소 계정에 대 한 SAS 연결 문자열을 입력 합니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토 합니다. 연결 정보가 올바르면 **연결** 을 선택 합니다.

#### <a name="shared-access-signature-sas-url"></a>SAS (공유 액세스 서명) URL

SAS URI를 사용 하 여 다음 리소스 유형에 연결할 수 Storage 탐색기.
* Blob 컨테이너
* Azure Data Lake Storage Gen2 컨테이너 또는 디렉터리
* 파일 공유
* 큐
* 테이블

SAS URI는 다음과 같습니다.

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 연결 하려는 리소스를 선택 합니다.
1. **SAS (공유 액세스 서명)** 를 선택 하 고 **다음** 을 선택 합니다.
1. 연결에 대 한 표시 이름 및 리소스에 대 한 SAS URI를 입력 합니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토 합니다. 연결 정보가 올바르면 **연결** 을 선택 합니다.

#### <a name="local-storage-emulator"></a>로컬 저장소 에뮬레이터

Storage 탐색기 Azure Storage 에뮬레이터에 연결할 수 있습니다. 현재는 두 가지 지원 되는 에뮬레이터가 있습니다.

* [Azure Storage 에뮬레이터](storage/common/storage-use-emulator.md) (Windows에만 해당)
* [Azurite](https://github.com/azure/azurite) (Windows, Macos 또는 Linux)

에뮬레이터가 기본 포트에서 수신 대기 하는 경우 **로컬 & 연결 된**  >  **저장소 계정**  >  **에뮬레이터-기본 포트** 노드를 사용 하 여 에뮬레이터에 액세스할 수 있습니다.

연결에 다른 이름을 사용 하려는 경우 또는 에뮬레이터가 기본 포트에서 실행 되지 않는 경우:

1. 에뮬레이터를 시작 합니다.

   > [!IMPORTANT]
   > Storage 탐색기 에뮬레이터를 자동으로 시작 하지 않습니다. 수동으로 시작 해야 합니다.

1. **Azure Storage에 연결** 대화 상자의 **리소스 선택** 패널에서 **로컬 저장소 에뮬레이터** 를 선택 합니다.
1. 연결에 대 한 표시 이름 및 사용 하려는 에뮬레이트된 각 서비스의 포트 번호를 입력 합니다. 서비스를 사용 하지 않으려면 해당 포트를 비워 둡니다. **다음** 을 선택합니다.
1. **요약** 패널에서 연결 정보를 검토 합니다. 연결 정보가 올바르면 **연결** 을 선택 합니다.

### <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB에 연결

Storage 탐색기는 Azure Cosmos DB 리소스에 대 한 연결도 지원 합니다.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>연결 문자열을 사용하여 Azure Cosmos DB 계정에 연결

Azure 구독을 통해 Azure Cosmos DB 계정을 관리 하는 대신 연결 문자열을 사용 하 여 Azure Cosmos DB에 연결할 수 있습니다. 연결하려면 다음 단계를 따릅니다.

1. **탐색기** 에서 **로컬 & 연결** 됨을 확장 하 고 **Cosmos DB 계정** 을 마우스 오른쪽 단추로 클릭 한 다음 **Azure Cosmos DB에 연결** 을 선택 합니다.

    ![연결 문자열을 사용하여 Azure Cosmos DB에 연결][21]

1. Azure Cosmos DB API를 선택 하 고 **연결 문자열** 데이터를 입력 한 다음 **확인** 을 선택 하 여 Azure Cosmos DB 계정을 연결 합니다. 연결 문자열을 검색 하는 방법에 대 한 자세한 내용은 [Azure Cosmos 계정 관리](./cosmos-db/how-to-manage-database-account.md)를 참조 하세요.

    ![연결 문자열][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>URI로 Azure Data Lake Store에 연결

구독에 없는 리소스에 액세스할 수 있습니다. 리소스 URI를 제공 하기 위해 해당 리소스에 대 한 액세스 권한이 있는 사용자가 필요 합니다. 로그인 한 후 URI를 사용 하 여 Data Lake Store에 연결 합니다. 연결하려면 다음 단계를 따릅니다.

1. **탐색기** 에서 **로컬 & 연결** 됨을 확장 합니다.

1. **Data Lake Storage Gen1** 를 마우스 오른쪽 단추로 클릭 하 고 **Data Lake Storage Gen1에 연결을** 선택 합니다.

    ![Data Lake Store 상황에 맞는 메뉴에 연결](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. URI를 입력 한 다음 **확인** 을 선택 합니다. Data Lake Store **Data Lake Storage** 아래에 나타납니다.

    ![Data Lake Store 결과에 연결](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

이 예제에서는 Data Lake Storage Gen1를 사용 합니다. 이제 Azure Data Lake Storage Gen2를 사용할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1 정의](./data-lake-store/data-lake-store-overview.md)를 참조 하세요.

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Storage 탐색기에서 공유 액세스 서명 생성<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>계정 수준 공유 액세스 서명

1. 공유 하려는 저장소 계정을 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기** 를 선택 합니다.

    ![공유 액세스 서명 상황에 맞는 메뉴 옵션 가져오기][14]

1. **공유 액세스 서명** 에서 계정에 대해 원하는 시간 프레임 및 사용 권한을 지정 하 고 **만들기** 를 선택 합니다.

    ![공유 액세스 서명 가져오기][15]

1. **연결 문자열** 또는 원시 **쿼리 문자열** 을 클립보드에 복사 합니다.

### <a name="service-level-shared-access-signature"></a>서비스 수준 공유 액세스 서명

서비스 수준에서 공유 액세스 서명을 가져올 수 있습니다. 자세한 내용은 [blob 컨테이너에 대 한 SAS 가져오기](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)를 참조 하세요.

## <a name="search-for-storage-accounts"></a>스토리지 계정 검색

저장소 리소스를 찾으려면 **탐색기** 창에서 검색할 수 있습니다.

검색 상자에 텍스트를 입력 하면 Storage 탐색기는 해당 시점까지 입력 한 검색 값과 일치 하는 모든 리소스를 표시 합니다. 다음 예제에서는 **끝점** 에 대 한 검색을 보여 줍니다.

![Storage 계정 검색][23]

> [!NOTE]
> 검색 속도를 높이려면 **계정 관리** 를 사용 하 여 검색 중인 항목이 포함 되지 않은 구독을 선택 취소 합니다. 노드를 마우스 오른쪽 단추로 클릭 하 고 **여기에서 검색** 을 선택 하 여 특정 노드에서 검색을 시작할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Storage 탐색기를 사용 하 여 Azure Blob storage 리소스 관리](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Storage Explorer 를 사용하여 데이터 작업](./cosmos-db/storage-explorer.md)
* [Storage Explorer를 사용하여 Azure Data Lake Store 리소스 관리](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
