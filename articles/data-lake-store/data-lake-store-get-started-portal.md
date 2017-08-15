---
title: "Azure Portal을 사용하여 Data Lake Store 시작 | Microsoft Docs"
description: "Azure Portal을 사용하여 Data Lake Store 계정을 만들고 Data Lake Store에서 기본 작업을 수행합니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/06/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: fa13266993017374ba49709f8e22fbe6b03a28c7
ms.contentlocale: ko-kr
ms.lasthandoff: 08/07/2017

---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Data Lake Store 시작
> [!div class="op_single_selector"]
> * [포털](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Azure Portal을 사용하여 Azure Data Lake Store 계정을 만들고 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등의 기본 작업을 수행하는 방법을 알아봅니다. 자세한 내용은 [Azure Data Lake Store의 개요](data-lake-store-overview.md)를 참조하세요.

다음 두 동영상에는 이 문서에 설명된 것과 같은 정보가 있습니다.

* [Data Lake 저장소 계정 만들기](https://mix.office.com/watch/1k1cycy4l4gen)
* [데이터 탐색기를 사용하여 Data Lake 저장소에서 데이터 관리](https://mix.office.com/watch/icletrxrh6pc)

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음 항목이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="create-an-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정 만들기

1. 새로운 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**를 클릭하고 **데이터 + 저장소**를 클릭한 다음 **Azure Data Lake Store**를 클릭합니다. **Azure Data Lake Store** 블레이드에서 정보를 읽은 다음 블레이드의 왼쪽 아래 모서리에서 **만들기**를 클릭합니다.
3. **새 Data Lake Store** 블레이드에서 아래 스크린샷에 표시된 대로 값을 제공합니다.
   
    ![새 Azure Data Lake Store 계정 만들기](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "새 Azure Data Lake 계정 만들기")
   
   * **이름**. Data Lake Store 계정의 고유한 이름을 입력합니다.
   * **구독**. 새 Data Lake Store 계정을 만들려는 구독을 선택합니다.
   * **리소스 그룹**. 기존 리소스 그룹을 선택하거나 **새로 만들기**를 선택하여 리소스 그룹을 만듭니다. 리소스 그룹은 응용 프로그램에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure의 리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)을 참조하세요.
   * **위치**: 데이터 레이크 저장소 계정을 만들려는 위치를 선택합니다.
   * **암호화 설정**. 세 개의 옵션이 있습니다.
     
     * **암호화를 활성화하지 않습니다**.
     * **Azure Data Lake에서 관리하는 키를 사용합니다**.  Azure Data Lake Store에서 암호화 키를 관리하려는 경우
     * **Azure Key Vault에서 키를 선택합니다**. 기존 Azure Key Vault를 선택하거나 새 Key Vault를 선택할 수 있습니다. Azure Key Vault에서 키를 사용하려면 Azure Data Lake Store 계정이 Azure Key Vault에 액세스할 수 있는 권한을 할당해야 합니다. 자세한 내용은 [Azure Key Vault에 권한 할당](#assign-permissions-to-azure-key-vault)을 참조하세요.
       
        ![Data Lake Store 암호화](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Store 암호화")
       
        **암호화 설정** 블레이드에서 **확인**을 클릭합니다.

        자세한 내용은 [Azure Data Lake Store의 데이터 암호화](./data-lake-store-encryption.md)를 참조하세요.

4. **만들기**를 클릭합니다. 계정을 대시보드에 고정하도록 선택한 경우 대시보드로 다시 돌아가고 Data Lake Store 계정 프로비전의 진행률을 볼 수 있습니다. 데이터 레이크 저장소 계정이 프로비전되면 계정 블레이드가 표시됩니다.

Azure Resource Manager 템플릿을 사용하여 Data Lake Store 계정을 만들 수도 있습니다. 이러한 템플릿은에서 [Azure QuickStart 템플릿](https://azure.microsoft.com/resources/templates/?term=data+lake+store)에서 액세스할 수 있습니다.

- 데이터 암호화 미지원: [데이터 암호화 없이 Azure Data Lake Store를 배포합니다](https://azure.microsoft.com/en-us/resources/templates/101-data-lake-store-no-encryption/).
- Data Lake Store를 사용하여 데이터 암호화 지원: [암호화로 Data Lake Store 계정을 배포합니다(Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
- Azure Key Vault를 사용하여 데이터 암호화 지원: [암호화로 Data Lake Store 계정을 배포합니다(Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).

### <a name="assign-permissions-to-azure-key-vault"></a>Azure Key Vault에 권한 할당
Azure Key Vault에서 키를 사용하여 Data Lake Store 계정에 대한 암호화를 구성한 경우 Data Lake Store 계정과 Azure Key Vault 계정 사이에 액세스를 구성해야 합니다. 이렇게 하려면 다음 단계를 수행합니다.

1. Azure Key Vault에서 키를 사용한 경우 Data Lake Store 계정에 대한 블레이드에서 위쪽에 경고를 표시합니다. 경고를 클릭하여 **Key Vault 사용 권한 구성** 블레이드를 엽니다.
   
    ![Data Lake Store 암호화](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Store 암호화")
2. 블레이드는 액세스를 구성하는 두 가지 옵션을 보여 줍니다.
   
   * 첫 번째 옵션에서 **사용 권한 부여**를 클릭하여 액세스를 구성합니다. 첫 번째 옵션은 Data Lake Store 계정을 만든 사용자가 Azure Key Vault의 관리자인 경우에만 활성화됩니다.
   * 다른 옵션은 블레이드에 표시되는 PowerShell cmdlet을 실행하는 것입니다. Azure Key Vault의 소유자이거나 Azure Key Vault에 대한 사용 권한을 부여할 수 있어야 합니다. cmdlet을 실행한 후에 블레이드로 다시 돌아가서 **사용**을 클릭하여 액세스를 구성합니다.

## <a name="createfolder"></a>Azure 데이터 레이크 저장소 계정에서 폴더 만들기
데이터 레이크 저장소 계정에서 폴더를 만들어 데이터를 관리하고 저장할 수 있습니다.

1. 만든 Data Lake Store 계정을 엽니다. 왼쪽 창에서 **찾아보기**, **Data Lake Store**를 차례로 클릭한 다음 Data Lake Store 블레이드에서 폴더를 만들려는 계정 이름을 클릭합니다. 시작 보드에 계정을 고정한 경우 해당 계정 타일을 클릭합니다.
2. 데이터 레이크 저장소 계정 블레이드에서 **데이터 탐색기**를 클릭합니다.
   
    ![Data Lake Store 계정에 폴더 만들기](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Data Lake Store 계정에 폴더 만들기")
3. Data Lake Store 계정 블레이드에서 **새 폴더**를 클릭하고 새 폴더에 대한 이름을 입력한 다음 **확인**을 클릭합니다.
   
    ![Data Lake Store 계정에 폴더 만들기](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Data Lake Store 계정에 폴더 만들기")
   
    새로 만든 폴더가 **데이터 탐색기** 블레이드에 나열되어 있습니다. 모든 수준까지 중첩된 폴더를 만들 수 있습니다.
   
    ![Data Lake 계정에 폴더 만들기](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Data Lake 계정에 폴더 만들기")

## <a name="uploaddata"></a>Azure 데이터 레이크 저장소 계정에 데이터 업로드
루트 수준에서 Azure 데이터 레이크 저장소 계정에 직접 데이터를 업로드하거나 계정 내에서 만든 폴더에 업로드할 수 있습니다. 아래의 스크린샷에서 단계에 따라 **데이터 탐색기** 블레이드에서 하위 폴더에 파일을 업로드합니다. 이 화면 캡처에서 파일은 이동 경로에 표시된 하위 폴더에 업로드됩니다(빨간색 상자에 표시).

업로드할 일부 샘플 데이터를 찾는 경우 **Azure 데이터 레이크 Git 리포지토리** 의 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)폴더에 있을 수 있습니다.

![데이터 업로드](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "데이터 업로드")

## <a name="properties"></a>저장된 데이터에서 사용할 수 있는 속성 및 작업
새로 추가된 파일을 클릭하여 **속성** 블레이드를 엽니다. 파일과 연결된 속성 및 파일에서 수행할 수 있는 작업은 이 블레이드에서 사용할 수 있습니다. 아래 스크린샷의 빨간색 상자에 강조 표시된 Azure Data Lake Store 계정의 파일에 전체 경로를 복사할 수도 있습니다.

![데이터에 대한 속성](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "데이터에 대한 속성")

* **미리 보기**를 클릭하여 브라우저에서 직접 파일의 미리 보기를 봅니다. 미리 보기의 형식을 지정할 수도 있습니다. **미리 보기**를 클릭하고 **파일 미리 보기** 블레이드에서 **형식**을 클릭하고 **파일 미리 보기 형식** 블레이드에서 표시할 행 개수, 사용할 인코딩, 사용할 구분 기호 등과 같은 옵션을 지정합니다.
  
  ![파일 미리 보기 형식](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "파일 미리 보기 형식")
* **다운로드**를 클릭하여 컴퓨터에 파일을 다운로드합니다.
* **파일 이름 바꾸기**를 클릭하여 파일의 이름을 바꿉니다.
* **파일 삭제**를 클릭하여 파일을 삭제합니다.

## <a name="secure-your-data"></a>데이터 보호
Azure Active Directory 및 액세스 제어(ACL)를 사용하여 Azure 데이터 레이크 저장소 계정에 저장된 데이터를 보호할 수 있습니다. 작업 수행 방법에 대한 지침은 [Azure 데이터 레이크 저장소의 데이터 보안](data-lake-store-secure-data.md)을 참조하세요.

## <a name="delete-azure-data-lake-store-account"></a>Azure 데이터 레이크 저장소 계정 삭제
Azure 데이터 레이크 저장소 계정을 삭제하려면 데이터 레이크 저장소 블레이드에서 **삭제**를 클릭합니다. 작업을 확인하려면 삭제하려는 계정의 이름을 입력하라는 메시지가 표시됩니다. 계정의 이름을 입력한 다음 **삭제**를 클릭합니다.

![Data Lake 계정 삭제](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake 계정 삭제")

## <a name="next-steps"></a>다음 단계
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
* [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store에 대한 진단 로그 액세스](data-lake-store-diagnostic-logs.md)


