---
title: Azure Machine Learning Data Prep SDK를 사용하여 데이터 로드 - Python
description: Azure Machine Learning Data Prep SDK를 사용하여 데이터를 로드하는 방법을 알아봅니다. 다양한 형식의 입력 데이터를 로드하거나, 데이터 파일 형식 및 매개 변수를 지정하거나, SDK 스마트 읽기 기능을 사용하여 파일 형식을 자동으로 검색할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/20/2018
ms.openlocfilehash: 208d6958b56dafbfacc45ecb05a71c14ac024ab4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309876"
---
# <a name="load-and-read-data-with-azure-machine-learning"></a>Azure Machine Learning을 사용한 데이터 로드 및 읽기

이 문서에서는 [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)를 사용하여 데이터를 로드하는 다양한 방법을 알아봅니다. SDK는 다음을 비롯한 여러 데이터 수집 기능을 지원합니다.

* 구문 분석 매개 변수 유추(인코딩, 구분 기호, 헤더)를 사용하여 다양한 파일 형식에서 로드
* 파일 로드 동안 유추를 사용한 형식 변환
* MS SQL Server 및 Azure Data Lake Storage에 대한 연결 지원

## <a name="load-text-line-data"></a>텍스트 줄 데이터 로드 

간단한 텍스트 데이터를 데이터 흐름으로 읽으려면 선택적 매개 변수를 지정하지 말고 `read_lines()`를 사용합니다.

```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```

||꺾은선형|
|----|-----|
|0|날짜 \|\| 최소 온도 \|\| 최대 온도|
|1|2015-07-1 \|\|  -4.1 \|\|  10.0|
|2|2015-07-2 \|\|  -0.8 \|\|  10.8|
|3|2015-07-3 \|\|  -7.0 \|\|  10.5|
|4|2015-07-4 \|\|  -5.5 \|\|  9.3|

데이터를 수집한 후 다음 코드를 실행하여 데이터 흐름 개체를 Pandas 데이터 프레임으로 변환합니다.

```python
pandas_df = dataflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>CSV 데이터 로드

구분 기호로 분리된 파일을 읽는 경우 기본 런타임이 구문 분석 매개 변수(예: 구분 기호, 인코딩, 헤더 사용 여부 등)를 유추할 수 있습니다. 다음 코드를 실행하여 해당 위치만 지정하는 방식으로 CSV 파일을 읽으려고 합니다.

```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |
|3|ALABAMA|1|101710|Hale County|10171002156| |
|4|ALABAMA|1|101710|Hale County|10171000588|2|

로드하는 동안 줄을 제외하려면 `skip_rows` 매개 변수를 정의합니다. 이 매개 변수는 CSV 파일에서 내림차순으로(1부터 시작하는 인덱스 사용) 행 로드를 건너뜁니다.

```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |
|2|ALABAMA|1|101710|Hale County|10171002156| 43|
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589|23 |

다음 코드를 실행하여 열 데이터 형식을 표시합니다.

```python
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```

기본적으로 Azure Machine Learning Data Prep SDK는 사용자의 데이터 형식을 변경하지 않습니다. 읽고 있는 데이터 원본은 텍스트 파일이므로 SDK는 모든 값을 문자열로 읽습니다. 예를 들어, 숫자 열은 번호로 구문 분석해야 합니다. `inference_arguments` 매개 변수를 `InferenceArguments.current_culture()`로 설정하여 파일 읽기 동안 열 형식을 자동으로 유추하고 변환합니다.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```

열 몇 개가 숫자로 올바르게 감지되었으며, 해당 형식은 `float64`로 설정되어 있습니다.

## <a name="use-excel-data"></a>Excel 데이터 사용

SDK에는 Excel 파일을 로드하는 `read_excel()` 함수가 포함되어 있습니다. 기본적으로 이 함수는 통합 문서의 첫 번째 시트를 로드합니다. 로드할 특정 시트를 정의하려면 시트 이름의 문자열 값으로 `sheet_name` 매개 변수를 정의합니다.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

||열1|열2|열3|열4|열5|열6|열7|열8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|1|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|2|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|3|RANK|제목|스튜디오|전 세계|국내 / %|열1|해외 / %|열2|연도 ^|
|4|1|아바타|Fox|2788|760.5|0.273|2027.5|0.727|2009^|5|

출력은 두 번째 시트의 데이터에서 머리글 앞의 3개 행이 비어 있음을 보여줍니다. `read_excel()` 함수는 행을 건너뛰고 헤더를 사용하기 위한 선택적 매개 변수를 포함합니다. 다음 코드를 실행하여 처음 세 개의 행을 건너뛰고 네 번째 행을 머리글로 사용합니다.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
```

||RANK|제목|스튜디오|전 세계|국내 / %|열1|해외 / %|열2|연도 ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|아바타|Fox|2788|760.5|0.273|2027.5|0.727|2009^|
|1|2|타이타닉|Par.|2186.8|658.7|0.301|1528.1|0.699|1997^|
|2|3|Marvel의 어벤저스|BV|1518.6|623.4|0.41|895.2|0.59|2012|
|3|4|해리포터와 죽음의 성물 2부|WB|1341.5|381|0.284|960.5|0.716|2011|
|4|5|겨울왕국|BV|1274.2|400.7|0.314|873.5|0.686|2013|

## <a name="load-fixed-width-data-files"></a>고정 너비 데이터 파일 로드

고정 너비 파일을 로드하려면 문자 오프셋 목록을 지정합니다. 첫 번째 열은 항상 0 오프셋에서 시작한다고 가정합니다.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

||010000|99999|BOGUS NORWAY|아니요|NO_1|ENRS|열7|열8|열9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|아니요|아니요|ENSO||||
|1|010010|99999|JAN MAYEN|아니요|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|아니요|아니요|||||
|3|010014|99999|SOERSTOKKEN|아니요|아니요|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|아니요|아니요|ENBL|+61383|+005867|+03270|

머리글을 감지하지 않도록 하고 올바른 데이터를 구문 분석하려면 `header` 매개 변수에 `PromoteHeadersMode.NONE`을 전달합니다.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||열1|열2|열3|열4|열5|열6|열7|열8|열9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|아니요|NO_1|ENRS|열7|열8|열9|
|1|010003|99999|BOGUS NORWAY|아니요|아니요|ENSO||||
|2|010010|99999|JAN MAYEN|아니요|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|아니요|아니요|||||
|4|010014|99999|SOERSTOKKEN|아니요|아니요|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|아니요|아니요|ENBL|+61383|+005867|+03270|

## <a name="load-sql-data"></a>SQL 데이터 로드

SDK는 SQL 원본에서 데이터를 로드할 수도 있습니다. 현재 Microsoft SQL Server만 지원됩니다. SQL 서버에서 데이터를 읽으려면 연결 매개 변수를 포함하는 `MSSQLDataSource` 개체를 만듭니다. 암호 매개 변수 `MSSQLDataSource`는 `Secret` 개체를 수락합니다. 다음 두 가지 방법으로 비밀 개체를 빌드할 수 있습니다.

* 실행 엔진을 사용하여 비밀 및 해당 값을 등록합니다. 
* `dprep.create_secret("[SECRET-ID]")`를 사용하여 `id`만 있는 비밀을 만듭니다(비밀 값이 실행 환경에 이미 등록되어 있는 경우).

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

데이터 원본 개체를 만든 후 쿼리 출력에서 데이터 읽기를 계속 진행할 수 있습니다.

```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

||ProductID|이름|ProductNumber|색|StandardCost|ListPrice|크기|무게|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|검정|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|없음|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|빨강|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|없음|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet, Red|HL-U509-R|빨강|13.0863|34.99|없음|없음|35|33|2005-07-01 00:00:00+00:00|없음|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|
|3|708|Sport-100 Helmet, Black|HL-U509|검정|13.0863|34.99|없음|없음|35|33|2005-07-01 00:00:00+00:00|없음|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000+00:00|
|4|709|Mountain Bike Socks, M|SO-B909-M|흰색|3.3963|9.50|M|없음|27|18|2005-07-01 00:00:00+00:00|2006-06-30 00:00:00+00:00|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000+00:00|

## <a name="use-azure-data-lake-storage"></a>Azure Data Lake Storage 사용

SDK는 두 가지 방법으로 Azure Data Lake Storage에 액세스하는 데 필요한 OAuth 토큰을 획득할 수 있습니다.

* 사용자의 Azure CLI 로그인의 최근 세션에서 액세스 토큰을 검색
* SP(서비스 주체) 및 인증서를 비밀로 사용

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>최근 Azure CLI 세션의 액세스 토큰 사용

로컬 머신에서 다음 명령을 실행합니다.

```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```

> [!NOTE] 
> 사용자 계정이 둘 이상의 Azure 테넌트의 구성원인 경우 AAD URL 호스트 이름 형식으로 테넌트를 지정해야 합니다.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Azure CLI를 통해 서비스 주체 만들기

Azure CLI를 사용하여 서비스 주체 및 해당 인증서를 만듭니다. 이 특정 서비스 주체는 Azure Data Lake Storage 계정 ‘dpreptestfiles’로만 축소된 범위를 사용하는 `reader` 역할로 구성됩니다.

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

이 명령은 인증서 파일에 대한 경로 및 `appId`를 내보냅니다(일반적으로 홈 폴더에서). .crt 파일은 공용 인증서와 개인 키를 PEM 형식으로 모두 포함합니다.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Azure Data Lake Storage 파일 시스템에 대한 ACL을 구성하려면 사용자의 objectId를 사용합니다. 이 예제에서는 서비스 주체가 사용됩니다.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Azure Data Lake Storage 파일 시스템에 대한 `Read` 및 `Execute` 액세스를 구성하려면 폴더 및 파일에 대한 ACL을 개별적으로 구성합니다. 이는 기본 HDFS ACL 모델이 상속을 지원하지 않기 때문입니다. 

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>OAuth 액세스 토큰 획득

`adal` 패키지를 사용하여(`pip install adal`) MSFT 테넌트에서 인증 컨텍스트를 만들고 OAuth 액세스 토큰을 획득합니다. ADLS의 경우 토큰 요청의 리소스는 대부분의 다른 Azure 리소스와는 달리 ‘ https://datalake.azure.net ’에 있어야 합니다.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Website|street|city|국가|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association - Danville|https://sites.google.com/site/caledoniafarmers.. ||댄빌|칼레도니아|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|파르마|카이어호가|
|2|1011878|100 Mile Market|http://www.pfcmarkets.com |507 Harrison St|칼라마주|칼라마주|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|식스 마일|||
|4|1010691|10th Street Community Farmers Market|http://agrimissouri.com/mo-grown/grodetail.php.. |10th Street and Poplar|라마|바턴|
