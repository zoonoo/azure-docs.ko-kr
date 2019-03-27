---
title: Azure Storage 탐색기를 사용하여 Data Lake Storage Gen2에 대한 권한 설정
description: 이 방법에서는 Azure Storage 탐색기를 사용하여 Azure Data Lake Storage Gen2 지원 스토리지 계정 내의 파일 및 디렉터리에 대해 사용 권한을 설정하는 방법을 알아봅니다.
services: storage
author: roygara
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: d1e941e41871de4a11641254f40c53285b096807
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546314"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2와 함께 Azure Storage 탐색기를 사용하여 파일 및 디렉터리 수준 사용 권한 설정

Azure Data Lake Storage Gen2에 저장된 파일은 세분화된 사용 권한 및 ACL(액세스 제어 목록) 관리를 지원합니다. 세분화된 사용 권한 및 ACL 관리를 함께 사용하면 매우 세부적인 수준에서 데이터 액세스를 관리할 수 있습니다.

이 문서에서는 Azure Storage 탐색기를 사용하여 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 파일 수준 사용 권한 설정
> * 디렉터리 수준 사용 권한 설정
> * 액세스 제어 목록에 사용자 또는 그룹 추가

## <a name="prerequisites"></a>필수 조건

프로세스를 가장 잘 설명하려면 [Azure Storage 탐색기 빠른 시작](data-lake-storage-Explorer.md)을 완료해야 합니다. 이렇게 하면 스토리지 계정이 가장 적합한 상태가 됩니다(파일 시스템이 생성되고 데이터가 업로드됨).

## <a name="managing-access"></a>액세스 관리

파일 시스템의 루트에서 사용 권한을 설정할 수 있습니다. 이렇게 하려면 해당 권한이 있는 개인 계정으로 Azure Storage 탐색기에 로그인해야 합니다(연결 문자열을 사용하는 경우와 다름). 파일 시스템을 마우스 오른쪽 단추로 클릭하고 **사용 권한 관리**를 선택하여 **사용 권한 관리** 대화 상자를 표시합니다.

![Microsoft Azure Storage 탐색기 - 디렉터리 액세스 관리](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

**사용 권한 관리** 대화 상자에서는 소유자 및 소유자 그룹의 사용 권한을 관리할 수 있습니다. 또한 사용 권한을 관리할 수 있는 액세스 제어 목록에 새 사용자 및 그룹을 추가할 수 있습니다.

액세스 제어 목록에 새 사용자 또는 그룹을 추가하려면 **사용자 또는 그룹 추가** 필드를 선택합니다.

목록에 추가할 해당 AAD(Azure Active Directory) 항목을 입력하고 **추가**를 선택합니다.

이제 사용자 또는 그룹이 **사용자 및 그룹** 필드에 표시되며, 사용 권한 관리를 시작할 수 있습니다.

> [!NOTE]
> AAD에서 보안 그룹을 만들고 개별 사용자가 아닌 그룹에서 사용 권한을 유지 관리하는 것이 모범 사례이며 권장됩니다. 이 권장 사항에 대한 자세한 내용은 [Data Lake Storage Gen2에 대한 모범 사례](data-lake-storage-best-practices.md)를 참조하세요.

할당할 수 있는 사용 권한의 두 가지 범주는 액세스 ACL 및 기본 ACL입니다.

* **액세스**: 액세스 ACL은 개체에 대한 액세스를 제어합니다. 파일과 디렉터리 둘 다에 액세스 ACL이 있습니다.

* **기본**: 디렉터리와 연결된 ACL 템플릿으로, 해당 디렉터리 아래에 생성된 모든 자식 항목에 대한 액세스 ACL을 결정합니다. 파일에는 기본 ACL이 없습니다.

두 범주에 모두, 파일 또는 디렉터리에 할당할 수 있는 **읽기**, **쓰기** 및 **실행**의 세 가지 사용 권한이 있습니다.

>[!NOTE]
> 여기에서 선택하면 현재 디렉터리에 있는 기존 항목에는 사용 권한이 설정되지 않습니다. 파일이 이미 있는 경우 각 개별 항목으로 이동한 다음, 사용 권한을 수동으로 설정해야 합니다.

개별 파일은 물론 개별 디렉터리에 대한 사용 권한을 관리할 수 있으므로 세분화된 액세스 제어가 가능합니다. 디렉터리와 파일 둘 다에서, 사용 권한 관리 프로세스는 위에서 설명한 프로세스와 동일합니다. 사용 권한을 관리하려는 파일 또는 디렉터리를 마우스 오른쪽 단추로 클릭하고 동일한 프로세스를 따릅니다.

## <a name="next-steps"></a>다음 단계

이 방법에서는 **Azure Storage 탐색기**를 사용하여 파일 및 디렉터리에 대한 사용 권한을 설정하는 방법을 알아보았습니다. 기본 ACL, 액세스 ACL, 해당 동작 및 사용 권한을 포함하여 ACL에 대해 자세히 알아보려면 이 주제에 대한 개념 문서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2의 액세스 제어](data-lake-storage-access-control.md)
