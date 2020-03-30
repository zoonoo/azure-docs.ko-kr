---
title: Storage Explorer 시작 | Microsoft Docs
description: Storage Explorer로 Azure 스토리지 리소스 관리
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279794"
---
# <a name="get-started-with-storage-explorer"></a>Storage Explorer 시작

## <a name="overview"></a>개요

Microsoft Azure 저장소 탐색기는 Windows, macOS 및 Linux에서 Azure 저장소 데이터로 쉽게 작업할 수 있는 독립 실행형 앱입니다. 이 문서에서는 Azure 저장소 계정에 연결하고 관리하는 몇 가지 방법을 알아봅니다.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>사전 요구 사항

# <a name="windows"></a>[Windows](#tab/windows)

다음 버전의 Windows는 저장소 탐색기를 지원합니다.

* Windows 10(권장)
* Windows 8
* Windows 7

모든 버전의 Windows에서 저장소 탐색기에는 .NET Framework 4.6.2 이상이 필요합니다.

# <a name="macos"></a>[macOS](#tab/macos)

다음 버전의 macOS는 저장소 탐색기를 지원합니다.

* 맥 OS 10.12 시에라 및 이후 버전

# <a name="linux"></a>[Linux](#tab/linux)

저장소 탐색기는 리눅스의 가장 일반적인 배포판에 대 한 [스냅 스토어에서](https://snapcraft.io/storage-explorer) 사용할 수 있습니다. 이 설치에 대 한 스냅 저장소를 권장 합니다. 저장소 탐색기 스냅은 새 버전이 스냅 스토어에 게시될 때 모든 종속성 및 업데이트를 설치합니다.

지원되는 배포판의 경우 [스냅 설치 페이지를](https://snapcraft.io/docs/installing-snapd)참조하십시오.

저장소 탐색기는 암호 관리자를 사용해야 합니다. 암호 관리자에 수동으로 연결해야 할 수 있습니다. 다음 명령을 실행하여 저장소 탐색기를 시스템의 암호 관리자에 연결할 수 있습니다.

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

저장 탐색기는 *.tar.gz* 다운로드로도 사용할 수 있습니다. 종속성을 수동으로 설치해야 합니다. 리눅스 지원 *.tar.gz* 설치의 다음 배포판:

* 우분투 18.04 x64
* 우분투 16.04 x64
* Ubuntu 14.04 x64

*.tar.gz* 설치는 다른 배포판에서 작동할 수 있지만 나열된 배포판만 공식적으로 지원됩니다.

Linux에 저장소 탐색기를 설치하는 데 더 많은 도움이 되면 Azure 저장소 탐색기 문제 해결 가이드에서 [Linux 종속성을](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) 참조하세요.

---

## <a name="download-and-install"></a>다운로드 및 설치

저장소 탐색기를 다운로드하고 설치하려면 [Azure 저장소 탐색기를](https://www.storageexplorer.com)참조하십시오.

## <a name="connect-to-a-storage-account-or-service"></a>스토리지 계정 또는 서비스에 연결

Storage Explorer는 Storage 계정에 연결하는 여러 방법을 제공합니다. 일반적으로 다음 중 하나를 수행할 수 있습니다.

* [Azure에 로그인하여 구독 및 해당 리소스에 액세스](#sign-in-to-azure)
* [특정 저장소 또는 CosmosDB 리소스 연결](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Azure에 로그인

> [!NOTE]
> 로그인한 후 리소스에 완전히 액세스하려면 저장소 탐색기에는 관리(Azure Resource Manager)와 데이터 계층 권한이 모두 필요합니다. 즉, 저장소 계정, 계정의 컨테이너 및 컨테이너의 데이터에 대한 액세스를 제공하는 Azure Active Directory(Azure AD) 권한이 필요합니다. 데이터 계층에만 사용 권한이 있는 경우 Azure AD 를 [통해 리소스를 추가하는](#add-a-resource-via-azure-ad)것이 좋습니다. 저장소 탐색기에서 요구하는 특정 권한에 대한 자세한 내용은 [Azure 저장소 탐색기 문제 해결 가이드를](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)참조하십시오.

1. 저장소 탐색기에서**계정 관리** **보기를** > 선택하거나 **계정 관리** 단추를 선택합니다.

    ![계정 관리][1]

1. 이제 **계정 관리에** 로그인한 모든 Azure 계정이 표시됩니다. 다른 계정에 연결하려면 **계정 추가를**선택합니다.

1. **Azure 저장소에 연결에서** **Azure 환경에서** Azure 클라우드를 선택하여 국가 클라우드 또는 Azure 스택에 로그인합니다. 환경을 선택한 후 **다음을**선택합니다.

    ![로그인 옵션][2]

    저장소 탐색기는 로그인할 페이지를 엽니다. 자세한 내용은 [Azure 스택 구독 또는 저장소 계정에 저장소 탐색기 연결을](/azure-stack/user/azure-stack-storage-connect-se)참조 하십시오.

1. Azure 계정으로 성공적으로 로그인하면 해당 계정과 연결된 계정 및 Azure 구독이 **계정 관리**에 나타납니다. **모든 구독을** 선택하여 나열된 Azure 구독 중에서 선택 항목을 전환하거나 전혀 전환하지 않습니다. 작업하려는 Azure 구독을 선택한 후 **적용**을 선택합니다.

    ![Azure 구독 선택][3]

    **EXPLORER는** 선택한 Azure 구독과 연결된 저장소 계정을 표시합니다.

    ![선택한 Azure 구독][4]

### <a name="attach-a-specific-resource"></a>특정 리소스 연결

저장소 탐색기의 리소스에 연결하는 방법에는 여러 가지가 있습니다.

* [Azure AD를 통해 리소스를 추가합니다.](#add-a-resource-via-azure-ad) 데이터 계층에만 사용 권한이 있는 경우 이 옵션을 사용하여 Blob 컨테이너 또는 Azure Data Lake Storage Gen2 Blob 저장소 컨테이너를 추가합니다.
* [연결 문자열을 사용합니다.](#use-a-connection-string) 저장소 계정에 연결 문자열이 있는 경우 이 옵션을 사용합니다. 저장소 탐색기는 키 및 [공유 액세스 서명](storage/common/storage-dotnet-shared-access-signature-part-1.md) 연결 문자열을 모두 지원합니다.
* [공유 액세스 시그니처 URI를 사용합니다.](#use-a-shared-access-signature-uri) Blob 컨테이너, 파일 공유, 큐 또는 테이블에 [대한 공유 액세스 서명 URI가](storage/common/storage-dotnet-shared-access-signature-part-1.md) 있는 경우 이를 사용하여 리소스에 연결합니다. 공유 액세스 서명 URI를 얻으려면 [저장소 탐색기](#generate-a-sas-in-storage-explorer) 또는 [Azure 포털을](https://portal.azure.com)사용할 수 있습니다.
* [이름과 키를 사용합니다.](#use-a-name-and-key) 저장소 계정의 계정 키를 알고 있는 경우 이 옵션을 사용하여 빠르게 연결할 수 있습니다. [Azure 포털에서](https://portal.azure.com) **설정** > **액세스 키를** 선택하여 저장소 계정 페이지에서 키를 찾습니다.
* [로컬 에뮬레이터에 연결합니다.](#attach-to-a-local-emulator) 사용 가능한 Azure Storage 에뮬레이터 중 하나를 사용하는 경우 이 옵션을 사용하여 에뮬레이터에 쉽게 연결할 수 있습니다.
* [연결 문자열을 사용하여 Azure Cosmos DB 계정에 연결합니다.](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string) CosmosDB 인스턴스에 연결 문자열이 있는 경우 이 옵션을 사용합니다.
* [URI를 통해 Azure 데이터 레이크 저장소에 연결합니다.](#connect-to-azure-data-lake-store-by-uri) Azure Data Lake 스토어에 URI가 있는 경우 이 옵션을 사용합니다.

#### <a name="add-a-resource-via-azure-ad"></a>Azure AD를 통해 리소스 추가

1. **Azure 저장소에 연결**연결을 열려면 **연결** 기호를 선택합니다.

    ![Azure Storage 옵션에 연결][9]

1. 아직 수행하지 않은 경우 **Azure 계정 추가** 옵션을 사용하여 리소스에 대한 액세스 권한이 있는 Azure 계정에 로그인합니다. 로그인한 후 Azure **저장소에 연결로**돌아갑니다.

1. **Azure Active Directory(Azure AD)를 통해 리소스 추가를**선택한 다음 다음 을 **선택합니다.**

1. Azure 계정 및 테넌트를 선택합니다. 이러한 값은 첨부할 저장소 리소스에 액세스할 수 있어야 합니다. **다음**을 선택합니다.

1. 연결할 리소스 유형을 선택합니다. 연결하는 데 필요한 정보를 입력합니다. 

   이 페이지에 입력하는 정보는 추가하는 리소스 유형에 따라 다릅니다. 올바른 유형의 리소스를 선택해야 합니다. 필요한 정보를 입력한 후 **다음을**선택합니다.

1. 연결 **요약을** 검토하여 모든 정보가 올바른지 확인합니다. 이 경우 **연결을**선택합니다. 그렇지 않으면 **뒤로를** 선택하여 이전 페이지로 돌아가서 잘못된 정보를 수정합니다.

연결이 성공적으로 추가되면 리소스 트리는 연결을 나타내는 노드로 이동합니다. 리소스가 **로컬 & 연결된** > **저장소 계정(연결된** > **저장소)** > **Blob 컨테이너**아래에 나타납니다. 저장소 탐색기 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure 저장소 탐색기 문제 해결 가이드를](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)참조하십시오.

#### <a name="use-a-connection-string"></a>연결 문자열 사용

1. **Azure 저장소에 연결**연결을 열려면 **연결** 기호를 선택합니다.

    ![Azure Storage 옵션에 연결][9]

1. **연결 문자열 사용을**선택한 다음 **다음**을 선택합니다.

1. 연결에 대한 표시 이름을 선택하고 연결 문자열을 입력합니다. 그런 다음 **을 선택합니다.**

1. 연결 **요약을** 검토하여 모든 정보가 올바른지 확인합니다. 이 경우 **연결을**선택합니다. 그렇지 않으면 **뒤로를** 선택하여 이전 페이지로 돌아가서 잘못된 정보를 수정합니다.

연결이 성공적으로 추가되면 리소스 트리는 연결을 나타내는 노드로 이동합니다. 리소스가 **로컬 & 연결된** > **저장소 계정**아래에 나타납니다. 저장소 탐색기 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure 저장소 탐색기 문제 해결 가이드를](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)참조하십시오.

#### <a name="use-a-shared-access-signature-uri"></a>공유 액세스 시그니처 URI 사용

1. **Azure 저장소에 연결**연결을 열려면 **연결** 기호를 선택합니다.

    ![Azure Storage 옵션에 연결][9]

1. **SAS(공유 액세스 서명) URI 사용을**선택한 다음 **다음**을 선택합니다.

1. 연결에 대한 표시 이름을 선택하고 공유 액세스 서명 URI를 입력합니다. 첨부하는 리소스 유형에 대한 서비스 끝점은 자동 채워야 합니다. 사용자 지정 끝점을 사용하는 경우 그렇지 않을 수 있습니다. **다음**을 선택합니다.

1. 연결 **요약을** 검토하여 모든 정보가 올바른지 확인합니다. 이 경우 **연결을**선택합니다. 그렇지 않으면 **뒤로를** 선택하여 이전 페이지로 돌아가서 잘못된 정보를 수정합니다.

연결이 성공적으로 추가되면 리소스 트리는 연결을 나타내는 노드로 이동합니다. 리소스는*연결한 컨테이너 유형에 대한 서비스 노드의* **로컬 & 연결된** > 저장소**계정(연결된 저장소** > **계정)** > 아래에 나타납니다. 저장소 탐색기 연결을 추가할 수 없는 경우 [Azure 저장소 탐색기 문제 해결 가이드를 참조하세요.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 문제 해결 가이드를 참조하세요.

#### <a name="use-a-name-and-key"></a>이름과 키 사용

1. **Azure 저장소에 연결**연결을 열려면 **연결** 기호를 선택합니다.

    ![Azure Storage 옵션에 연결][9]

1. **저장소 계정 이름 및 키 사용을**선택한 다음 **다음**을 선택합니다.

1. 연결의 표시 이름을 선택합니다.

1. 저장소 계정 이름과 액세스 키 중 하나를 입력합니다.

1. 사용할 **저장소 도메인을** 선택한 다음 **다음**을 선택합니다.

1. 연결 **요약을** 검토하여 모든 정보가 올바른지 확인합니다. 이 경우 **연결을**선택합니다. 그렇지 않으면 **뒤로를** 선택하여 이전 페이지로 돌아가서 잘못된 정보를 수정합니다.

연결이 성공적으로 추가되면 리소스 트리는 연결을 나타내는 노드로 이동합니다. 리소스가 **로컬 & 연결된** > **저장소 계정**아래에 나타납니다. 저장소 탐색기 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure 저장소 탐색기 문제 해결 가이드를](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)참조하십시오.

#### <a name="attach-to-a-local-emulator"></a>로컬 에뮬레이터에 연결

스토리지 탐색기는 현재 두 개의 공식 저장소 에뮬레이터를 지원합니다.

* [Azure 저장소 에뮬레이터(Windows만)](storage/common/storage-use-emulator.md)
* [아주라이트](https://github.com/azure/azurite) (윈도우, 맥 OS, 또는 리눅스)

에뮬레이터가 기본 포트에서 수신 대기 중이면 **에뮬레이터 - 기본 포트** 노드를 사용하여 에뮬레이터에 액세스할 수 있습니다. **에뮬레이터 -** 로컬 & **연결된** > **저장소 계정**에서 기본 포트를 찾습니다.

연결에 다른 이름을 사용하거나 에뮬레이터가 기본 포트에서 실행되고 있지 않은 경우 다음 단계를 따르세요.

1. 에뮬레이터를 시작합니다. 각 서비스 `AzureStorageEmulator.exe status` 유형에 대한 포트를 표시하는 명령을 입력합니다.

   > [!IMPORTANT]
   > 저장소 탐색기는 에뮬레이터를 자동으로 시작하지 않습니다. 수동으로 시작해야 합니다.

1. **Azure 저장소에 연결**연결을 열려면 **연결** 기호를 선택합니다.

    ![Azure Storage 옵션에 연결][9]

1. **로컬 에뮬레이터에 연결한**다음 **다음을**선택합니다.

1. 연결의 표시 이름을 선택하고 에뮬레이터가 각 서비스 유형에 대해 수신 대기 중이던 포트를 입력합니다. **로컬 에뮬레이터에 연결하면** 대부분의 에뮬레이터에 대한 기본 포트 값이 제안됩니다. 공식 에뮬레이터 중 어느 것도 현재 파일 서비스를 지원하지 않으므로 **파일 포트는** 비어 있습니다. 사용 중이면 파일을 지원하는 경우 사용할 포트를 입력할 수 있습니다. 그런 다음 **을 선택합니다.**

1. 연결 **요약을** 검토하고 모든 정보가 올바른지 확인합니다. 이 경우 **연결을**선택합니다. 그렇지 않으면 **뒤로를** 선택하여 이전 페이지로 돌아가서 잘못된 정보를 수정합니다.

연결이 성공적으로 추가되면 리소스 트리는 연결을 나타내는 노드로 이동합니다. 노드는 로컬 **& 연결된** > **저장소 계정**아래에 표시되어야 합니다. 저장소 탐색기 연결을 추가할 수 없거나 연결을 성공적으로 추가한 후 데이터에 액세스할 수 없는 경우 [Azure 저장소 탐색기 문제 해결 가이드를](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)참조하십시오.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>연결 문자열을 사용하여 Azure Cosmos DB 계정에 연결

Azure 구독을 통해 Azure Cosmos DB 계정을 관리하는 대신 연결 문자열을 사용하여 Azure Cosmos DB에 연결할 수 있습니다. 연결하려면 다음 단계를 따릅니다.

1. **EXPLORER에서** **로컬 & 연결,** 마우스 오른쪽 단추 클릭 **코스모스 DB 계정**확장 및 Azure **코스모스 DB에 연결**선택 .

    ![연결 문자열을 사용하여 Azure Cosmos DB에 연결][21]

1. Azure Cosmos DB API를 선택하고 **연결 문자열** 데이터를 입력한 다음 **확인을** 선택하여 Azure Cosmos DB 계정을 연결합니다. 연결 문자열을 검색하는 방법에 대한 자세한 내용은 [Azure Cosmos 계정 관리를](https://docs.microsoft.com/azure/cosmos-db/manage-account)참조하십시오.

    ![연결 문자열][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>URI로 Azure Data Lake Store에 연결

구독에 없는 리소스에 액세스할 수 있습니다. 리소스 URI를 제공 하려면 해당 리소스에 대 한 액세스 권한이 있는 사람이 있어야 합니다. 로그인한 후 URI를 사용하여 데이터 레이크 스토어에 연결합니다. 연결하려면 다음 단계를 따릅니다.

1. **탐색기에서** **로컬 & 연결합니다.**

1. **데이터 레이크 스토리지 Gen1을**마우스 오른쪽 단추로 클릭하고 데이터 레이크 스토리지 **Gen1에 연결 선택합니다.**

    ![데이터 레이크 스토어 컨텍스트 메뉴에 연결](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. URI를 입력한 다음 **확인을**선택합니다. 데이터 레이크 저장소가 **데이터 레이크 저장소**아래에 나타납니다.

    ![데이터 레이크 스토어 결과에 연결](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

이 예제에서는 데이터 레이크 저장소 Gen1을 사용합니다. Azure 데이터 레이크 저장소 Gen2를 사용할 수 있습니다. 자세한 내용은 [Azure 데이터 레이크 저장소 Gen1을](./data-lake-store/data-lake-store-overview.md)참조하세요.

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>저장소 탐색기에서 공유 액세스 서명 생성<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>계정 수준 공유 액세스 서명

1. 공유하려는 저장소 계정을 마우스 오른쪽 단추로 클릭한 다음 **공유 액세스 서명 받기를**선택합니다.

    ![공유 액세스 서명 컨텍스트 메뉴 옵션 받기][14]

1. **공유 액세스 서명에서**계정에 대해 원하는 시간 프레임 및 권한을 지정한 다음 **을**선택합니다.

    ![공유 액세스 서명 받기][15]

1. **연결 문자열** 또는 원시 **쿼리 문자열을** 클립보드에 복사합니다.

### <a name="service-level-shared-access-signature"></a>서비스 수준 공유 액세스 서명

서비스 수준에서 공유 액세스 서명을 받을 수 있습니다. 자세한 내용은 [Blob 컨테이너에 대한 SAS 받기를](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)참조하십시오.

## <a name="search-for-storage-accounts"></a>스토리지 계정 검색

저장소 리소스를 찾으려면 **EXPLORER** 창에서 검색할 수 있습니다.

검색 상자에 텍스트를 입력하면 저장소 탐색기는 해당 시점까지 입력한 검색 값과 일치하는 모든 리소스를 표시합니다. 이 예제에서는 끝점에 대한 검색을 보여 주며 다음과 같은 작업을 보여 **주며**

![Storage 계정 검색][23]

> [!NOTE]
> 검색 속도를 높이려면 **계정 관리를** 사용하여 검색하려는 항목이 포함되지 않은 구독을 선택 해제합니다. 노드를 마우스 오른쪽 단추로 클릭하고 **여기에서 검색을** 선택하여 특정 노드에서 검색을 시작할 수도 있습니다.
>
>

## <a name="next-steps"></a>다음 단계

* [저장소 탐색기를 통해 Azure Blob 저장소 리소스 관리](vs-azure-tools-storage-explorer-blobs.md)
* [Azure Storage Explorer 를 사용하여 데이터 작업](./cosmos-db/storage-explorer.md)
* [Storage Explorer를 사용하여 Azure Data Lake Store 리소스 관리](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
