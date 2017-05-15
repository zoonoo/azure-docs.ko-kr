---
title: "Azure PowerShell을 사용하여 Azure Data Lake Analytics 시작 | Microsoft Docs"
description: "Azure PowerShell을 사용하여 Data Lake Analytics 계정을 만들고, U-SQL을 사용하여 Data Lake Analytics 작업을 만들고, 작업을 제출하는 방법에 대해 알아봅니다. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 시작
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure PowerShell을 사용하여 Azure Data Lake Analytics 계정을 만든 다음 U-SQL 작업을 제출하고 실행하는 방법에 대해 알아봅니다. 데이터 레이크 분석에 대한 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음 정보가 있어야 합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

## <a name="preparing-for-the-tutorial"></a>자습서 준비
Data Lake Analytics 계정을 만들려면 다음을 지정해야 합니다.

* **Azure 리소스 그룹**: Azure 리소스 그룹 내에서 Data Lake Analytics 계정을 만들어야 합니다.
* **Data Lake Analytics 계정 이름**: Data Lake 계정 이름은 소문자와 숫자만을 포함해야 합니다.
* **위치**: 데이터 레이크 분석을 지원하는 Azure 데이터 센터 중 하나입니다.
* **기본 Data Lake Store 계정**: 각 Data Lake Analytics 계정에는 기본 Data Lake Store 계정이 있습니다. 이러한 계정은 동일한 위치에 있어야 합니다.

이 자습서의 PowerShell 코드 조각은 이러한 변수를 사용하여 이 정보를 저장합니다.

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Data Lake 분석 계정 만들기

사용할 리소스 그룹이 아직 없는 경우 만듭니다. 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

모든 Data Lake Analytics 계정에는 로그를 저장하는 데 사용할 기본 Data Lake Store 계정이 필요합니다. 기존 계정을 다시 사용하거나 새 계정을 만들 수 있습니다. 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

리소스 그룹 및 Data Lake Store 계정을 사용할 수 있게 되면 Data Lake Analytics 계정을 만듭니다.

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics 계정에 대한 정보 가져오기

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>U-SQL 작업 제출

다음 U-SQL 스크립트가 포함된 텍스트 파일을 만듭니다.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

스크립트를 제출합니다.

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>U-SQL 작업 모니터링

계정에서 모든 작업을 나열합니다. 출력은 현재 실행 중인 작업 및 최근에 완료된 해당 작업을 포함합니다.

```
Get-AdlJob -Account $adla
```

특정 작업의 상태를 가져옵니다.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

작업이 완료될 때까지 Get-AdlAnalyticsJob을 호출하지 않고 Wait-AdlJob cmdlet을 사용할 수 있습니다.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

작업이 완료되면 폴더의 파일을 나열하여 출력 파일이 있는지 확인합니다.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

파일의 존재 여부를 확인합니다.

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>파일 업로드 및 다운로드

U-SQL 스크립트의 출력을 다운로드합니다.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


사용할 파일을 U-SQL 스크립트에 대한 입력으로 업로드합니다.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>참고 항목
* 다른 도구를 사용하여 같은 자습서를 보려면 페이지 맨 위의 탭 선택기를 클릭합니다.
* U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
* 관리 작업을 보려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.

