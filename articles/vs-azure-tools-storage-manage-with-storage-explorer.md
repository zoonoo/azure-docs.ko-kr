---
title: "저장소 탐색기(미리 보기) 시작 | Microsoft Docs"
description: "저장소 탐색기(미리 보기)를 사용하여 Azure 저장소 리소스 관리"
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0550f5fecd83ae9dc0acb2770006156425baddf3
ms.openlocfilehash: 0617d2e668fe719d6002254b6d13ca729887c0e3


---
# <a name="getting-started-with-storage-explorer-preview"></a>저장소 탐색기(미리 보기) 시작
## <a name="overview"></a>개요
Microsoft Azure Storage 탐색기(미리 보기)는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 손쉽게 사용할 수 있는 독립 실행형 앱입니다. 이 문서에서는 Azure Storage 계정에 연결하고 관리하는 다양한 방법을 알아봅니다.

![Microsoft Azure Storage 탐색기(미리 보기)][15]

## <a name="prerequisites"></a>선행 조건
* [저장소 탐색기(미리 보기) 다운로드 및 설치](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>저장소 계정 또는 서비스에 연결
저장소 탐색기(미리 보기)는 저장소 계정에 연결하는 다양한 방법을 제공합니다. Azure 구독에 연결된 저장소 계정에 연결, 다른 Azure 구독에서 공유 되는 저장소 계정 및 서비스에 연결, Azure Storage 에뮬레이터를 사용하여 로컬 저장소에 연결 및 관리를 포함합니다. 또한 글로벌 및 국가별 Azure에서 저장소 계정으로 작업할 수 있습니다.

* [Azure 구독에 연결](#connect-to-an-azure-subscription) - Azure 구독에 속한 저장소 리소스를 관리합니다.
* [로컬 개발 저장소로 작업](#work-with-local-development-storage) - Azure Storage 에뮬레이터를 사용하여 로컬 저장소를 관리합니다.
* [외부 저장소에 연결](#attach-or-detach-an-external-storage-account) - 저장소 계정의 계정 이름, 키 및 끝점을 사용하여 다른 Azure 구독에 속하거나 국가별 Azure 클라우드 아래에 있는 저장소 리소스를 관리합니다.
* [SAS를 사용하여 저장소 계정 연결](#attach-storage-account-using-sas) - SAS를 사용하여 다른 Azure 구독에 속하는 저장소 리소스를 관리합니다.
* [SAS를 사용하여 서비스 연결](#attach-service-using-sas) - SAS를 사용하여 다른 Azure 구독에 속하는 특정 저장소 서비스(Blob 컨테이너, 큐 또는 테이블)를 관리합니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결
> [!NOTE]
> Azure 계정이 없는 경우 [무료 평가판을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.
>
>

1. 저장소 탐색기(미리 보기)에서 **Azure 계정 설정**을 선택합니다.

    ![Azure 계정 설정][0]
2. 왼쪽 창에 로그인한 모든 Microsoft 계정이 표시됩니다. 다른 계정에 연결하려면 **계정 추가**를 선택하고 대화 상자를 따라서 하나 이상의 활성 Azure 구독에 연결된 Microsoft 계정을 사용하여 로그인합니다.
> [!NOTE]
>Black Forest Azure, Fairfax Azure 및 Mooncake Azure와 같은 국가별 Azure에 로그인을 통한 연결은 현재 지원되지 않습니다. 국가별 Azure 클라우드 계정에 연결하는 방법은 **외부 저장소 계정 연결 또는 분리** 섹션을 참조하세요.

3. Microsoft 계정으로 성공적으로 로그인하면 왼쪽 창이 해당 계정과 연결된 Azure 구독으로 채워집니다. 작업하려는 Azure 구독을 선택한 후 **적용**을 선택합니다. (나열된 Azure 구독을 모두 선택하거나 하나도 선택하지 않는 **모든 구독** 토글을 선택합니다.)

    ![Azure 구독 선택][3]
4. 왼쪽 창은 선택한 Azure 구독과 연결된 저장소 계정을 표시합니다.

    ![선택한 Azure 구독][4]

## <a name="work-with-local-development-storage"></a>로컬 개발 저장소로 작업
저장소 탐색기(미리 보기)를 사용하면 Azure 저장소 에뮬레이터를 사용하여 로컬 저장소에서 작동할 수 있습니다. Azure에 배포된 저장소 계정 없이 저장소에 대한 코드를 작성하고 테스트할 수 있습니다(저장소 계정은 Azure 저장소 에뮬레이터에서 에뮬레이트되므로).

> [!NOTE]
> Azure 저장소 에뮬레이터는 현재 Windows에 대해서만 지원됩니다.
>
>

1. 저장소 탐색기(미리 보기)의 왼쪽 창에서 **(로컬 및 연결된** > **저장소 계정** > **(개발)** 노드를 확장합니다.

    ![로컬 개발 노드][21]
2. Azure Storage 에뮬레이터를 아직 설치 하지 않은 경우 정보 표시줄을 통해 작업을 수행하라는 메시지가 표시됩니다. 정보 표시줄이 표시되면 **최신 버전 다운로드**를 선택하고 에뮬레이터를 설치합니다.

    ![Azure Storage 에뮬레이터 프롬프트 다운로드][22]
3. 에뮬레이터가 설치되면 로컬 Blob, 큐 및 테이블을 만들고 사용할 수 있습니다. 각 저장소 계정 유형으로 작업하는 방법을 알아보려면 다음 링크 중 하나를 선택합니다.

   * [Azure Blob 저장소 리소스 관리](vs-azure-tools-storage-explorer-blobs.md)
   * Azure 파일 공유 저장소 리소스 관리 - *서비스 예정*
   * Azure 큐 저장소 리소스 관리 - *서비스 예정*
   * Azure 테이블 저장소 리소스 관리 - *서비스 예정*

## <a name="attach-or-detach-an-external-storage-account"></a>외부 저장소 계정 연결 또는 분리
저장소 탐색기(미리 보기)는 저장소 계정을 쉽게 공유할 수 있도록 외부 저장소 계정에 연결하는 기능을 제공합니다. 이 섹션은 외부 저장소 계정에 연결(및 분리)하는 방법을 설명합니다.

### <a name="get-the-storage-account-credentials"></a>저장소 계정 자격 증명 가져오기
외부 저장소 계정을 공유하려면 해당 계정의 소유자는 먼저 계정에 대한 자격 증명(계정 이름 및 키)을 가져온 다음 해당 정보를 해당(외부) 계정에 연결하려는 사용자와 공유해야 합니다. 다음 단계를 수행하여 Azure 포털을 통해 저장소 계정 자격 증명을 가져올 수 있습니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **찾아보기**를 선택합니다.
3. **저장소 계정**을 선택합니다.
4. **저장소 계정** 블레이드에서 원하는 저장소 계정을 선택합니다.
5. 선택한 저장소 계정에 대한 **설정** 블레이드에서 **액세스 키**를 선택합니다.

   ![액세스 키 옵션][5]
6. **액세스 키** 블레이드에서 저장소 계정에 연결할 때 사용할 **저장소 계정 이름** 및 **키 1** 값을 복사합니다.

   ![액세스 키][6]

### <a name="attach-to-an-external-storage-account"></a>외부 저장소 계정에 연결
외부 저장소 계정에 연결하려면 계정의 이름 및 키가 필요합니다. *저장소 계정 자격 증명 가져오기* 섹션은 Azure 포털에서 이러한 값을 가져오는 방법을 설명합니다. 그러나 포털에서 계정 키는 "키 1"이라고 하므로 저장소 탐색기(미리 보기)가 계정 키를 묻는 곳에 "키 1" 값을 입력(또는 붙여 넣기)합니다.

1. 저장소 탐색기(미리 보기)에서 **Azure Storage에 연결**을 선택합니다.

   ![Azure Storage 옵션에 연결][23]
2. **Azure Storage에 연결** 대화 상자에서 계정 키(Azure 포털에서 "키 1" 값)를 지정한 후 **다음**을 선택합니다.
> [!NOTE]
> 국가별 Azure의 저장소 계정에서 Storage Connection 문자열을 입력할 수 있습니다. 예를 들어 Azure Black Forest 저장소 계정에 연결하려면 다음과 유사한 연결 문자열을 입력합니다. DefaultEndpointsProtocol=https;AccountName=cawatest03;AccountKey=<storage_account_key>;EndpointSuffix=core.cloudapi.de; **저장소 계정 자격 증명 가져오기** 섹션에 설명된 대로 Azure Portal에서 같은 방법으로 연결 문자열을 가져올 수 있습니다.

   ![Azure Storage 대화 상자에 연결][24]

3. **외부 저장소 연결** 대화 상자에서 **계정 이름** 상자에 저장소 계정 이름을 입력하고 다른 원하는 설정을 지정하고 완료되면 **다음**을 선택합니다.

   ![외부 저장소 연결 대화 상자][8]
4. **연결 요약** 대화 상자에서 정보를 확인합니다. 변경하려는 경우 **뒤로**를 선택하고 원하는 설정을 다시 입력합니다. 완료되면 **연결**을 선택합니다.
5. 연결되면 외부 저장소 계정이 저장소 계정 이름에 추가된 텍스트 **(외부)**와 함께 표시됩니다.

   ![외부 저장소 계정 연결 결과][9]

### <a name="detach-from-an-external-storage-account"></a>외부 저장소 계정에서 분리
1. 상황에 맞는 메뉴에서 분리하려는 외부 저장소 계정을 마우스 오른쪽 단추로 클릭하고 **분리**를 선택합니다.

   ![저장소 옵션에서 분리][10]
2. 확인 메시지 상자가 나타나면 **예** 를 선택하여 외부 저장소 계정에서 분리를 확인합니다.

## <a name="attach-storage-account-using-sas"></a>SAS를 사용하여 저장소 계정 연결
[SAS(공유 액세스 서명)](storage/storage-dotnet-shared-access-signature-part-1.md) 는 Azure 구독 관리자에게 해당 Azure 구독 자격 증명을 제공할 필요 없이 임시 기반으로 저장소 계정에 대한 액세스를 부여할 수 있도록 합니다.

이를 설명하기 위해 Azure 구독의 관리자인 사용자 A는 사용자 B가 특정 권한으로 제한된 시간에 저장소 계정에 액세스할 수 있도록 한다고 가정해 보겠습니다.

1. 사용자 A는 특정 기간 동안 원하는 사용 권한으로 SAS(저장소 계정에 대한 연결 문자열로 구성)를 생성합니다.
2. 사용자 A는 저장소 계정에 액세스하려는 사용자(이 예에서 사용자 B)와 SAS를 공유합니다.  
3. 사용자 B는 저장소 탐색기(미리 보기)를 사용하여 제공된 SAS를 사용하여 사용자 A에 속한 계정에 연결합니다.

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>공유하려는 계정에 대한 SAS 가져오기
1. 저장소 탐색기(미리 보기)의 상황에 맞는 메뉴에서 공유하려는 저장소 계정을 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기**를 선택합니다.

   ![SAS 상황에 맞는 메뉴 옵션 가져오기][13]
2. **공유 액세스 서명** 대화 상자에서 계정에 대해 원하는 시간 프레임 및 권한을 지정하고 **만들기**를 선택합니다.

    ![SAS 대화 가져오기][14]
3. 두 번째 **공유 액세스 서명** 대화 상자가 SAS를 표시합니다. **연결 문자열** 옆의 **복사**를 선택하여 클립보드에 복사합니다. **닫기** 를 선택하여 대화 상자를 닫습니다.

### <a name="attach-to-the-shared-account-using-the-sas"></a>SAS를 사용하여 공유 계정에 연결
1. 저장소 탐색기(미리 보기)에서 **Azure Storage에 연결**을 선택합니다.

   ![Azure Storage 옵션에 연결][23]
2. **Azure Storage에 연결** 대화 상자에서 연결 문자열을 지정한 후 **다음**을 선택합니다.

   ![Azure Storage 대화 상자에 연결][24]
3. **연결 요약** 대화 상자에서 정보를 확인합니다. 변경하려는 경우 **뒤로**를 선택하고 원하는 설정을 다시 입력합니다. 완료되면 **연결**을 선택합니다.
4. 연결되면 저장소 계정이 제공한 계정 이름에 추가된 텍스트(SAS)와 함께 표시됩니다.

   ![SAS를 사용한 계정 연결 결과][17]

## <a name="attach-service-using-sas"></a>SAS를 사용하여 서비스 연결
[SAS를 사용하여 저장소 계정 연결](#attach-storage-account-using-sas) 섹션에서는 Azure 구독 관리자가 저장소 계정에 대한 SAS를 생성(및 공유)하여 저장소 계정에 대한 임시 액세스를 부여할 수 있는 방법을 보여 줍니다. 마찬가지로, SAS는 저장소 계정 내에서 특정 서비스(Blob 컨테이너, 큐 또는 테이블)에 대해 생성될 수 있습니다.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>공유하려는 서비스에 대한 SAS 생성
이 컨텍스트에서 서비스는 Blob 컨테이너, 큐 또는 테이블일 수 있습니다. 다음 섹션은 나열된 서비스에 대한 SAS를 생성하는 방법을 설명합니다.

* [Blob 컨테이너에 대한 SAS 가져오기](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* 파일 공유에 대한 SAS 가져오기 - *서비스 예정*
* 큐에 대한 SAS 가져오기 - *서비스 예정*
* 테이블에 대한 SAS 가져오기 - *서비스 예정*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>SAS를 사용하여 공유 계정 서비스에 연결
1. 저장소 탐색기(미리 보기)에서 **Azure Storage에 연결**을 선택합니다.

   ![Azure Storage 옵션에 연결][23]
2. **Azure Storage에 연결** 대화 상자에서 SAS URI를 지정한 후 **다음**을 선택합니다.

   ![Azure Storage 대화 상자에 연결][24]
3. **연결 요약** 대화 상자에서 정보를 확인합니다. 변경하려는 경우 **뒤로**를 선택하고 원하는 설정을 다시 입력합니다. 완료되면 **연결**을 선택합니다.
4. 연결되면 새로 연결된 서비스가 **(서비스 SAS)** 노드 아래에 표시됩니다.

   ![SAS를 사용한 공유 서비스 연결 결과][20]

## <a name="search-for-storage-accounts"></a>저장소 계정 검색
저장소 계정 목록이 긴 경우 특정 저장소 계정을 찾는 빠른 방법은 왼쪽 창 맨 위에 있는 검색 상자를 사용하는 것입니다.

검색 상자에 내용을 입력하면 왼쪽 창에 해당 시점에 입력한 검색 값과 일치하는 저장소 계정만 표시됩니다. 다음 스크린샷은 저장소 계정 이름이 "tarcher" 텍스트를 포함하는 모든 저장소 계정에 대해 검색한 위치의 예를 보여 줍니다.

![저장소 계정 검색][11]

검색을 지우려면 검색 상자의 **x** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계
* [저장소 탐색기(미리 보기)를 사용하여 Azure Blob 저장소 리소스 관리](vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png



<!--HONumber=Feb17_HO2-->


