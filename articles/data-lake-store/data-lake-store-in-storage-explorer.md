---
title: "Azure Storage 탐색기에서 Azure Data Lake Store 리소스 관리"
description: "사용자가 Azure Storage 탐색기에서 ADLS 데이터 및 리소스에 액세스하고 관리할 수 있도록 합니다."
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>Storage 탐색기(미리 보기)를 사용하여 Azure Data Lake Store 리소스 관리

[ADLS(Azure Data Lake Store)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)는 텍스트 또는 이진 데이터와 같이 많은 양의 구조화되지 않은 데이터를 저장하는 서비스입니다. 사용자는 HTTP 또는 HTTPS를 통해 어디서나 데이터에 액세스할 수 있습니다. Azure Storage 탐색기의 ADLS를 사용하면 사용자가 Blob 및 큐와 같은 다른 Azure 엔터티와 함께 ADLS 데이터 및 리소스에 액세스하고 관리할 수 있습니다. 이제 사용자는 동일한 도구를 사용하여 다양한 Azure 엔터티를 한 곳에서 관리할 수 있습니다.

또 다른 이점은 사용자에게 ADLS 데이터를 관리하기 위한 구독 권한이 필요하지 않다는 것입니다. Storage 탐색기에서 다른 사용자가 권한을 부여하는 한 ADLS 경로를 "로컬 및 연결됨" 노드에 연결할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 문서의 단계를 완료하려면 다음 필수 구성 요소가 필요합니다.

*   Azure 구독. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial)을 참조하세요.
*   Azure Data Lake Store 계정 만드는 방법에 대한 지침은 [Azure Data Lake Store 시작](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal)을 참조하세요.

## <a name="installation"></a>설치

[Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)에서 최신 Azure Storage 탐색기 비트를 설치합니다. 현재 Windows, Linux 및 MAC 버전이 지원됩니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

1. **Azure Storage 탐색기**를 설치한 후 왼쪽에 있는 **플러그 인** 아이콘을 클릭합니다. 다음 이미지를 참조하세요.
       
   ![플러그 인 아이콘](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. **Azure 계정 추가**를 선택하고 **로그인**을 클릭합니다.

   ![Azure 구독에 연결](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. **Azure 로그인** 대화 상자에서 **로그인**을 선택한 후에 Azure 자격 증명을 입력합니다.

    ![로그인](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. 목록에서 구독을 선택하고 **적용**을 클릭합니다.

    ![적용](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    탐색기 창이 업데이트되고, 선택한 구독에서 계정이 표시됩니다.

    ![계정 목록](./media/data-lake-store-in-storage-explorer/account-list.png)

    **Azure Data Lake Store**를 Azure 구독에 성공적으로 연결했습니다.

## <a name="connect-to-data-lake-store"></a>Data Lake 저장소에 연결
구독에 없는 리소스에 액세스하려고 합니다. 하지만 리소스에 대한 URI를 가져올 수 있도록 다른 사용자가 권한을 부여합니다. 이 경우 로그인한 후 URI를 사용하여 Data Lake Store에 연결할 수 있습니다. 다음 단계를 참조하세요.
1. 저장소 탐색기(미리 보기)를 엽니다.
2. 왼쪽 창에서 **로컬 및 연결됨**을 펼칩니다.
3. **Data Lake Store**를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **Data Lake Store에 연결...**을 선택합니다.

      ![Data Lake Store 바로 가기 메뉴에 연결](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. URI를 입력하면 도구가 방금 입력한 URL의 위치로 이동합니다.

      ![Data Lake Store에 연결 팝업 대화 상자](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Data Lake Store에 연결 결과](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Azure Data Lake Store 계정의 내용 보기
Azure Data Lake Store 계정의 리소스에는 폴더와 파일이 포함되어 있습니다.

다음 단계에서는 Storage 탐색기(미리 보기) 내에서 ADLS 계정의 내용을 보는 방법을 설명합니다.

1. 저장소 탐색기(미리 보기)를 엽니다.
2. 왼쪽 창에서 보려는 Azure Data Lake Store 계정이 포함된 구독을 펼칩니다.
3. **Data Lake Store**를 펼칩니다.
4. 보려는 Azure Data Lake Store 계정 노드를 마우스 오른쪽 단추로 클릭하고, 바로 가기 메뉴에서 **열기**를 선택합니다. 또한 ADLS 계정을 두 번 클릭하여 열 수도 있습니다. 
5. 기본 창에 ADLS 계정의 내용이 표시됩니다.

     ![기본 창](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Azure Data Lake Store 리소스 관리

Azure Data Lake Store 리소스는 다음 작업을 수행하여 관리할 수 있습니다.
*   여러 ADL 계정에서 ADLS 리소스를 탐색합니다.  
*   연결 문자열을 사용하여 ADLS에 직접 연결하고 관리합니다. 
*   [로컬 및 연결됨] 아래에 있는 ACL을 통해 다른 사용자가 공유한 ADLS 리소스를 봅니다.
*   파일/폴더 CRUD 작업 수행: 재귀 폴더 및 선택한 여러 개의 파일을 지원합니다. 
*   데스크톱 파일 탐색기 환경을 미러링하는 빠른 액세스 및 최근 위치에 폴더를 끌어서 놓거나 추가합니다. 
*   Storage 탐색기에서 한 번의 클릭으로 ADL 하이퍼링크를 복사하고 엽니다. 
*   오른쪽 아래의 창에 활동 로그를 표시하여 활동 상태를 표시합니다.
*   폴더 통계 및 파일 속성을 표시합니다.

## <a name="manage-resources-in-azure-storage-explorer"></a>Azure Storage 탐색기에서 리소스 관리
Azure Data Lake Store 계정이 만들어지면, 로컬 컴퓨터에서 폴더 및 파일을 업로드하고, 다운로드하고, 리소스를 열 수 있습니다. 그리고 빠른 액세스, 새 폴더, URL 복사, 모두 선택에 고정할 수 있습니다. 또한 복사, 붙여넣기, 이름 바꾸기, 삭제, 폴더 통계, 새로 고침을 수행할 수 있습니다.

다음 항목에서는 Azure Data Lake Store 계정 내에서 리소스를 관리하는 방법을 설명합니다. 수행하려는 작업에 따라서 다음 단계를 수행하세요.
   * **파일 업로드**

     1. 기본 창의 도구 모음에서 **업로드**를 선택하고, 드롭다운 메뉴에서 **파일 업로드...**를 선택합니다.

        ![파일 메뉴 업로드](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. **업로드할 파일 선택** 대화 상자에서 업로드하려는 파일을 선택합니다.

        ![폴더 업로드 대화 상자](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. **열기**를 선택하여 업로드를 시작합니다.
   * **폴더 업로드**

     1. 기본 창의 도구 모음에서 **업로드**를 선택하고, 드롭다운 메뉴에서 **폴더 업로드...**를 선택합니다.

        ![폴더 메뉴 업로드](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. **업로드할 폴더 선택** 대화 상자에서 업로드하려는 폴더를 선택합니다.

        ![폴더 업로드 대화 상자](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. **폴더 선택**을 선택하여 폴더 업로드를 시작합니다.

        ![폴더 업로드 대화 상자](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > 로컬 컴퓨터에 있는 폴더와 파일을 직접 끌어서 업로드를 구현할 수 있습니다. 
       
   * **로컬 컴퓨터에 폴더 또는 파일 다운로드**

     1. 다운로드하려는 폴더 또는 파일을 선택합니다.
     2. 기본 창 도구 모음에서 **다운로드**를 선택합니다.
     3. **다운로드한 파일을 저장할 폴더 선택** 대화 상자에서 폴더 또는 파일을 다운로드하려는 위치를 지정합니다. 그런 다음, 지정하려는 이름을 제공합니다.
     4. **저장**을 선택합니다.
   * **로컬 컴퓨터에서 폴더 또는 파일 열기**

     1. 열려는 폴더 또는 파일을 선택합니다.
     2. 기본 창의 도구 모음에서 **열기**를 선택하거나 선택한 폴더 또는 파일을 마우스 오른쪽 단추로 클릭하고, 바로 가기 메뉴에서 **열기**를 클릭합니다.
     3. 파일이 파일의 기본 파일 형식과 연결된 응용 프로그램을 통해 다운로드되고 열립니다. 또는 기본 창에서 폴더가 열립니다.

        ![파일 열기](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **클립보드에 폴더 또는 파일 복사**

     1. 복사하려는 폴더 또는 파일을 선택합니다.
     2. 기본 창의 도구 모음에서 **복사**를 선택하거나 선택한 폴더 또는 파일을 마우스 오른쪽 단추로 클릭하고, 바로 가기 메뉴에서 **복사**를 클릭합니다.
     3. 왼쪽 창에서 다른 ADLS 계정으로 이동하고 두 번 클릭하여 기본 창에서 봅니다.
     4. 기본 창의 도구 모음에서 **붙여넣기**를 선택하여 복사본을 만듭니다. 또는 대상에 있는 **붙여넣기** 바로 가기 메뉴를 클릭합니다.

        ![폴더 또는 파일 복사 및 붙여넣기](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + 저장소 유형 간 복사-붙여넣기는 **지원되지 않습니다**. ADLS 폴더 또는 파일을 복사하여 다른 ADLS 계정에 붙여넣을 수 있지만, Blob 저장소에 또는 다른 방법으로는 **붙여넣을 수 없습니다**. 
          > + 복사-붙여넣기는 폴더 또는 파일을 로컬로 다운로드한 다음, 대상에 업로드하여 작동합니다. 도구는 백 엔드에서 작업을 **수행하지 않습니다**. 큰 파일의 복사-붙여넣기는 느립니다. 고성능 파일 복사-이동 최적화는 현재 진행 중입니다.
   * **폴더 또는 파일 삭제**

     1. 삭제하려는 폴더 또는 파일을 선택합니다.
     2. 기본 창의 도구 모음에서 **삭제**를 선택하거나 선택한 폴더 또는 파일을 마우스 오른쪽 단추로 클릭하고, 바로 가기 메뉴에서 **삭제**를 클릭합니다.
     3. 확인 대화 상자에서 **예**를 선택합니다.

        ![폴더 또는 파일 복사 및 붙여넣기](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **빠른 액세스에 고정**

     1. 고정하려는 폴더를 선택합니다.
     2. 기본 창의 도구 모음에서 **빠른 액세스에 고정**을 선택합니다.
     3. 왼쪽 창에서 선택한 폴더가 **빠른 액세스** 노드에 추가된 것을 볼 수 있습니다.

        ![빠른 액세스에 고정](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. 빠른 액세스가 만들어지면 리소스에 쉽게 액세스할 수 있습니다.
   * **딥 링크**
     1. URL이 있는 경우 **파일 탐색기** 또는 브라우저의 주소 경로에 URL을 입력하기만 하면 됩니다.
     2. 그런 다음, **Storage Explorer.exe**가 자동으로 시작되어 방금 입력한 URL의 위치로 이동합니다.

        ![파일 탐색기의 딥 링크](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>다음 단계
* [최신 저장소 탐색기(미리 보기) 릴리스 정보 및 비디오](http://www.storageexplorer.com)를 보세요.
* [Azure Storage 탐색기에서 Azure Cosmos DB를 관리하는 방법](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)을 알아봅니다.
* [Storage 탐색기(미리 보기) 시작](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)에서 Storage 탐색기에 대해 자세히 알아봅니다.
* [Azure Data Lake Store 개요](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)에서 ADLS(Azure Data Lake Store) 시작에 대해 자세히 알아봅니다.
* [Azure Storage 탐색기에서 Azure Cosmos DB를 사용하는 방법](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) 비디오를 시청합니다.
