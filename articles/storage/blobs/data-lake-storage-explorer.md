---
title: Azure Data Lake Storage Gen2에서 Azure Storage 탐색기 사용
description: Azure Storage 탐색기를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정의 디렉터리 및 파일 및 디렉터리 ACL (액세스 제어 목록)을 관리 합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652278"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure Storage 탐색기를 사용 하 여 Azure Data Lake Storage Gen2의 디렉터리 및 파일 관리

이 문서에서는 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 디렉터리와 파일을 만들고 관리 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](../common/storage-account-create.md)을 수행합니다.

- 로컬 컴퓨터에 설치 된 Azure Storage 탐색기 Windows, Macintosh 또는 Linux용 Azure Storage Explorer를 설치하려면 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 참조하세요.

> [!NOTE]
> Storage 탐색기 Azure Data Lake Storage Gen2로 작업할 때 Blob (blob) & Data Lake Storage Gen2 (dfs) [끝점](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) 을 사용 합니다. 전용 끝점을 사용 하 여 Azure Data Lake Storage Gen2에 대 한 액세스를 구성 하는 경우 저장소 계정에 대해 두 개의 개인 끝점을 만듭니다. 하나는 대상 하위 리소스이 `blob` 고 다른 하나는 대상 하위 리소스 `dfs` 입니다.

## <a name="sign-in-to-storage-explorer"></a>Storage Explorer에 로그인

Storage Explorer를 처음 시작할 때 **Microsoft Azure Storage Explorer - 연결** 창이 나타납니다. Storage Explorer에서는 몇 가지 방법으로 스토리지 계정에 연결할 수 있지만, 현재는 ACL을 관리하는 한 가지 방법만 지원됩니다.

|Task|목적|
|---|---|
|Azure 계정 추가 | Azure에 인증하기 위해 조직 로그인 페이지로 리디렉션합니다. ACL을 관리하고 설정하려는 경우 현재 유일하게 지원되는 인증 방법입니다.|
|연결 문자열 또는 공유 액세스 서명 URI 사용 | SAS 토큰 또는 공유 연결 문자열이 있는 컨테이너 또는 스토리지 계정에 직접 액세스하는 데 사용할 수 있습니다. |
|스토리지 계정 이름 및 키 사용| 스토리지 계정 이름과 스토리지 계정 키를 사용하여 Azure Storage에 연결합니다.|

**Azure 계정 추가** 를 선택하고 **로그인..** 을 클릭합니다. 화면 상의 메시지에 따라 Azure 계정에 로그인합니다.

![Microsoft Azure Storage 탐색기 표시 하 고 Azure 계정 추가 옵션 및 로그인 단추를 강조 표시 하는 스크린샷](media/storage-quickstart-blobs-storage-explorer/connect.png)

작업이 완료되면 Azure Storage Explorer는 표시된 **탐색기** 탭을 로드합니다. 이 보기는 [Azure Storage 에뮬레이터](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 계정 또는 [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 환경을 통해 구성된 로컬 스토리지뿐만 아니라 모든 Azure Storage 계정에 대한 정보를 제공합니다.

![Microsoft Azure Storage Explorer - 연결 창](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 디렉터리와 파일을 보관 합니다. 하나를 만들려면 계속 진행 단계에서 만든 저장소 계정을 확장 합니다. **Blob 컨테이너** 를 선택 하 고 마우스 오른쪽 단추를 클릭 한 다음 **blob 컨테이너 만들기** 를 선택 합니다. 컨테이너의 이름을 입력 합니다. 컨테이너 이름 지정에 대 한 규칙 및 제한 사항 목록은 [컨테이너 만들기](storage-quickstart-blobs-dotnet.md#create-a-container) 섹션을 참조 하세요. 완료 되 면 **enter** 키를 눌러 컨테이너를 만듭니다. 컨테이너가 성공적으로 만들어지면 선택한 저장소 계정에 대 한 **Blob 컨테이너** 폴더 아래에 표시 됩니다.

![Microsoft Azure Storage 탐색기-컨테이너 만들기](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>디렉터리 만들기

디렉터리를 만들려면 진행 단계에서 만든 컨테이너를 선택 합니다. 컨테이너 리본에서 **새 폴더** 단추를 선택 합니다. 디렉터리의 이름을 입력 합니다. 완료 되 면 **enter** 키를 눌러 디렉터리를 만듭니다. 디렉터리가 성공적으로 만들어지면 편집기 창에 표시 됩니다.

![Microsoft Azure Storage 탐색기-디렉터리 만들기](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>디렉터리에 Blob 업로드

디렉터리 리본에서 **업로드** 단추를 선택 합니다. 이 작업에서는 폴더나 파일을 업로드할 수 있는 옵션이 제공됩니다.

업로드할 파일 또는 폴더를 선택합니다.

![Microsoft Azure Storage Explorer - Blob 업로드](media/data-lake-storage-explorer/upload-file.png)

**확인** 을 선택하면 선택한 파일들이 업로드 대기 상태가 되고 파일 하나하나가 업로드됩니다. 업로드가 완료되면 결과가 **활동** 창에 표시됩니다.

## <a name="view-blobs-in-a-directory"></a>디렉터리에서 Blob 보기

**Azure Storage Explorer** 애플리케이션에서 스토리지 계정 아래에 있는 디렉터리를 선택합니다. 기본 창에는 선택한 디렉터리의 Blob 목록이 표시됩니다.

![Microsoft Azure Storage Explorer - 디렉터리의 Blob 나열](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Blob 다운로드

**Azure Storage 탐색기** 를 사용 하 여 파일을 다운로드 하려면 파일을 선택한 상태로 리본에서 **다운로드** 를 선택 합니다. 파일 대화 상자가 열리면 파일 이름을 입력할 수 있습니다. **저장** 을 선택 하 여 로컬 위치에 대 한 파일 다운로드를 시작 합니다.

## <a name="next-steps"></a>다음 단계

Acl (액세스 제어 목록)을 설정 하 여 파일 및 디렉터리 권한을 관리 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Storage 탐색기를 사용 하 여 Azure Data Lake Storage Gen2에서 Acl 관리](./data-lake-storage-explorer-acl.md)
