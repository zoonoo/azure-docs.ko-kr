---
title: 'Storage 탐색기: Azure Data Lake Storage Gen2 Acl 설정'
description: Azure Storage 탐색기를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 Acl (액세스 제어 목록)을 관리 합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654342"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Storage 탐색기를 사용 하 여 Azure Data Lake Storage Gen2에서 Acl 관리

이 문서에서는 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 acl (액세스 제어 목록)을 관리 하는 방법을 보여 줍니다.

Storage 탐색기를 사용 하 여 디렉터리 및 파일의 Acl을 확인 하 고 업데이트할 수 있습니다. 부모 디렉터리 아래에 생성 된 새 자식 항목에는 ACL 상속이 이미 사용할 수 있습니다. 그러나 각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에 ACL 설정을 재귀적으로 적용할 수도 있습니다. 

이 문서에서는 파일 또는 디렉터리의 ACL을 수정 하는 방법과 하위 디렉터리에 ACL 설정을 재귀적으로 적용 하는 방법을 보여 줍니다.

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

## <a name="manage-an-acl"></a>ACL 관리

컨테이너, 디렉터리 또는 파일을 마우스 오른쪽 단추로 클릭 한 다음 **Access Control 목록 관리** 를 클릭 합니다.  다음 스크린샷에서는 디렉터리를 마우스 오른쪽 단추로 클릭할 때 표시 되는 메뉴를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![Azure Storage 탐색기에서 디렉터리를 마우스 오른쪽 단추로 클릭](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

**액세스 관리** 대화 상자를 사용 하 여 소유자 및 소유자 그룹에 대 한 사용 권한을 관리할 수 있습니다. 또한 사용 권한을 관리할 수 있는 액세스 제어 목록에 새 사용자 및 그룹을 추가할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![액세스 관리 대화 상자](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

액세스 제어 목록에 새 사용자 또는 그룹을 추가 하려면 **추가** 단추를 선택 합니다. 그런 다음 목록에 추가할 해당 Azure Active Directory (Azure AD) 항목을 입력 하 고 **추가** 를 선택 합니다.  이제 사용자 또는 그룹이 **사용자 및 그룹** 필드에 표시되며, 사용 권한 관리를 시작할 수 있습니다.

> [!NOTE]
> Azure AD에서 보안 그룹을 만들고 개별 사용자가 아닌 그룹에 대 한 사용 권한을 유지 하는 것이 좋습니다. 이 권장 사항 및 기타 모범 사례에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-explorer-acl.md)을 참조 하세요.

확인란 컨트롤을 사용 하 여 액세스 및 기본 Acl을 설정 합니다. 이러한 Acl 유형 간의 차이점에 대 한 자세한 내용은 [Acl 유형](data-lake-storage-access-control.md#types-of-acls)을 참조 하세요.

## <a name="apply-acls-recursively"></a>재귀적으로 Acl 적용

각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에 ACL 항목을 재귀적으로 적용할 수 있습니다.

ACL 항목을 재귀적으로 적용 하려면 컨테이너 또는 디렉터리를 마우스 오른쪽 단추로 클릭 한 다음 **Access Control 목록 전파** 를 클릭 합니다.  다음 스크린샷에서는 디렉터리를 마우스 오른쪽 단추로 클릭할 때 표시 되는 메뉴를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![디렉터리를 마우스 오른쪽 단추로 클릭 하 고 액세스 제어 전파 설정을 선택 합니다.](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>다음 단계

Data Lake Storage Gen2 권한 모델에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2의 액세스 제어 모델](./data-lake-storage-access-control-model.md)
