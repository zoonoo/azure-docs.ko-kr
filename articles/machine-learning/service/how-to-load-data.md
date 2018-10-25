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
ms.date: 09/24/2018
ms.openlocfilehash: 91db32b7056a0cf211e6293a891d58e0239ca499
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237588"
---
# <a name="load-and-read-data-with-azure-machine-learning"></a>Azure Machine Learning을 사용한 데이터 로드 및 읽기

[Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py)를 사용하여 다양한 형식의 입력 데이터를 로드합니다. 

데이터를 로드하려면 데이터 파일 형식 및 해당 매개 변수를 지정합니다.

## <a name="use-text-line-data"></a>텍스트 줄 데이터 사용 
데이터를 로드하는 가장 간단한 방법 중 하나는 텍스트 줄로 읽는 것입니다.

샘플 코드는 다음과 같습니다.
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

데이터가 수집된 후 전체 데이터 집합에 대한 pandas 데이터 프레임을 검색할 수 있습니다.

샘플 코드는 다음과 같습니다.
```python
df = dataflow.to_pandas_dataframe()
df
```
샘플 출력:
||꺾은선형|
|----|-----|
|0|날짜 \|\| 최소 온도 \|\| 최대 온도|
|1|2015-07-1\|\| 4.1\|\| 10.0|
|2|2015-07-2\|\| 0.8\|\| 10.8|
|3|2015-07-3\|\| 7.0\|\| 10.5|
|4|2015-07-4\|\| 5.5\|\| 9.3|

## <a name="use-csv-data"></a>CSV 데이터 사용
구분 기호로 분리된 파일을 읽는 경우 기본 런타임이 구문 분석 매개 변수(예: 구분 기호, 인코딩, 헤더 사용 여부 등)를 제공하는 대신 유추하도록 할 수 있습니다. 이 예제의 경우 해당 위치만 지정하여 파일을 읽으려고 시도합니다. 

샘플 코드는 다음과 같습니다.
```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

샘플 출력:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |
|3|ALABAMA|1|101710|Hale County|10171002156| |
|4|ALABAMA|1|101710|Hale County|10171000588|2|

지정할 수 있는 매개 변수 중 하나는 읽는 파일에서 건너뛸 줄의 수입니다. 다음 코드를 사용하여 중복된 줄을 필터링합니다.
```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

샘플 출력:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |
|2|ALABAMA|1|101710|Hale County|10171002156| 43|
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589|23 |

다음으로, 열의 데이터 형식을 볼 수 있습니다.
샘플 코드는 다음과 같습니다.
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

그러나 열이 모두 문자열로 되돌아옵니다. 기본적으로 Azure Machine Learning Data Prep SDK가 사용자의 데이터 형식을 변경하지 않기 때문입니다. 읽고 있는 데이터 원본은 텍스트 파일이므로 SDK는 모든 값을 문자열로 읽습니다. 예를 들어 숫자 열을 숫자로 구문 분석하려고 합니다. 이렇게 하려면 inference_arguments 매개 변수를 current_culture로 설정할 수 있습니다.

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

열 몇 개가 숫자로 올바르게 감지되었으며, 해당 형식은 float64로 설정되어 있습니다. 수집이 완료되면 전체 데이터 집합에 대한 pandas 데이터 프레임을 검색할 수 있습니다.
샘플 코드는 다음과 같습니다.
```python
df = dataflow.to_pandas_dataframe()
df
```

샘플 출력:
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|49.0|
|1|ALABAMA|Hale County|1.017100e+10|40.0|
|2|ALABAMA|Hale County|1.017100e+10|43.0|
|3|ALABAMA|Hale County|1.017100e+10|2.0|
|4|ALABAMA|Hale County|1.017100e+10|23.0|

## <a name="use-excel-data"></a>Excel 데이터 사용
Azure Machine Learning Data Prep SDK에는 Excel 파일을 로드하는 `read_excel` 함수가 포함됩니다. 샘플 코드는 다음과 같습니다.
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```

샘플 출력:
||열1|열2|열3|열4|열5|열6|열7|열8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Iron, IVB|60000000.0|있음|1920.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |-19.58333|17.91667|
|1|Cape York|Iron, IIIAB|58200000.0|있음|1818.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |76.13333|-64.93333|
|2|Campo del Cielo|Iron, IAB-MG|50000000.0|있음|1576.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |-27.46667|-60.58333|
|3|Canyon Diablo|Iron, IAB-MG|30000000.0|있음|1891.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |35.05000|-111.03333|
|4|Armanty|Iron, IIIE|28000000.0|있음|1898.0|http://www.lpi.usra.edu/meteor/metbull.php?cod.. |47.00000|88.00000|

Excel 파일의 첫 번째 시트를 로드했습니다. 로드하려는 시트의 이름을 명시적으로 지정하여 동일한 결과를 얻을 수 있습니다. 대신 두 번째 시트를 로드하려는 경우 해당 이름을 인수로 제공할 수 있습니다. 예: 
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

샘플 출력:
||열1|열2|열3|열4|열5|열6|열7|열8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|1|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|2|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|3|RANK|제목|스튜디오|전 세계|국내 / %|열1|해외 / %|열2|연도 ^|
|4|1|아바타|Fox|2788|760.5|0.273|2027.5|0.727|2009^|5|

보시다시피 두 번째 시트의 테이블에는 머리글 및 빈 행이 3개 있습니다. 예를 들어 다음과 같이 함수의 인수를 적절하게 수정해야 합니다.
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

샘플 출력:
||RANK|제목|스튜디오|전 세계|국내 / %|열1|해외 / %|열2|연도 ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|아바타|Fox|2788|760.5|0.273|2027.5|0.727|2009^|
|1|2|타이타닉|Par.|2186.8|658.7|0.301|1528.1|0.699|1997^|
|2|3|Marvel의 어벤저스|BV|1518.6|623.4|0.41|895.2|0.59|2012|
|3|4|해리포터와 죽음의 성물 2부|WB|1341.5|381|0.284|960.5|0.716|2011|
|4|5|겨울왕국|BV|1274.2|400.7|0.314|873.5|0.686|2013|

## <a name="use-fixed-width-data-files"></a>고정 너비 데이터 파일 사용
고정 너비 파일의 경우 오프셋의 목록을 지정할 수 있습니다. 다음과 같이 첫 번째 열은 항상 오프셋 0에서 시작한다고 가정합니다.
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

샘플 출력:
||010000|99999|BOGUS NORWAY|아니요|NO_1|ENRS|열7|열8|열9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|아니요|아니요|ENSO||||
|1|010010|99999|JAN MAYEN|아니요|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|아니요|아니요|||||
|3|010014|99999|SOERSTOKKEN|아니요|아니요|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|아니요|아니요|ENBL|+61383|+005867|+03270|


파일에 헤더가 없는 경우 첫 번째 행을 데이터로 처리할 수 있습니다. `PromoteHeadersMode.NONE`을 헤더 키워드 인수에 전달하여 헤더 검색을 방지하고 올바른 데이터를 가져올 수 있습니다. 예: 
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```

샘플 출력:

||열1|열2|열3|열4|열5|열6|열7|열8|열9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|아니요|NO_1|ENRS|열7|열8|열9|
|1|010003|99999|BOGUS NORWAY|아니요|아니요|ENSO||||
|2|010010|99999|JAN MAYEN|아니요|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|아니요|아니요|||||
|4|010014|99999|SOERSTOKKEN|아니요|아니요|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|아니요|아니요|ENBL|+61383|+005867|+03270|

## <a name="use-sql-data"></a>SQL 데이터 사용
Azure Machine Learning Data Prep SDK는 SQL 서버에서도 데이터를 로드할 수 있습니다. 현재 Microsoft SQL Server만 지원됩니다.
SQL 서버에서 데이터를 읽으려면 연결 정보를 포함한 데이터 원본 개체를 만듭니다. 예: 
```python
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
보시다시피 `MSSQLDataSource`의 암호 매개 변수는 비밀 개체를 수락합니다. 두 가지 방법으로 비밀 개체를 가져올 수 있습니다.
-   실행 엔진을 사용하여 비밀 및 해당 값을 등록합니다. 
-   ID만 사용하여 비밀을 만듭니다(비밀 값이 실행 환경에 이미 등록되어 있는 경우에만 유용).

데이터 원본 개체를 만든 후 데이터 읽기를 계속 진행할 수 있습니다. 예: 
```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

샘플 출력:
||ProductID|이름|ProductNumber|색|StandardCost|ListPrice|크기|무게|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|검정|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|없음|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|빨강|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|없음|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet, Red|HL-U509-R|빨강|13.0863|34.99|없음|없음|35|33|2005-07-01 00:00:00+00:00|없음|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|
|3|708|Sport-100 Helmet, Black|HL-U509|검정|13.0863|34.99|없음|없음|35|33|2005-07-01 00:00:00+00:00|없음|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000+00:00|
|4|709|Mountain Bike Socks, M|SO-B909-M|흰색|3.3963|9.50|M|없음|27|18|2005-07-01 00:00:00+00:00|2006-06-30 00:00:00+00:00|없음|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000+00:00|

```python
df = dataflow.to_pandas_dataframe()
df.dtypes
```

샘플 출력:
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="use-azure-data-lake-storage"></a>Azure Data Lake Storage 사용
SDK는 두 가지 방법으로 Azure Data Lake Storage에 액세스하는 데 필요한 OAuth 토큰을 획득할 수 있습니다.
-   사용자의 Azure CLI 로그인의 최근 로그인 세션에서 액세스 토큰을 검색
-   SP(서비스 주체) 및 인증서를 비밀로 사용

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>최근 Azure CLI 세션의 액세스 토큰 사용
로컬 머신에서 다음 명령을 실행합니다.

> [!NOTE] 
> 사용자 계정이 둘 이상의 Azure 테넌트의 구성원인 경우 AAD URL 호스트 이름 형식으로 테넌트를 지정해야 합니다.


예: 
```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Azure CLI를 통해 서비스 주체 만들기
Azure CLI를 사용하여 서비스 주체 및 해당 인증서를 만들 수 있습니다. 이 특정 서비스 주체는 Azure Data Lake Storage 계정 ‘dpreptestfiles’로만 축소된 범위를 사용하는 Reader로 구성됩니다.  예: 
```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
이 명령은 인증서 파일에 대한 경로 및 `appId`를 내보냅니다(일반적으로 홈 폴더에서). .crt 파일은 공용 인증서와 개인 키를 PEM 형식으로 모두 포함합니다.

지문 추출:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Azure Data Lake Storage 파일 시스템에 대한 ACL을 구성하려면 사용자의 objectId 또는 이 예제의 경우 서비스 주체를 사용합니다. 예: 
```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Azure Data Lake Storage 파일 시스템에 대한 `Read` 및 `Execute` 액세스를 구성하려면 폴더 및 파일에 대한 ACL을 개별적으로 구성해야 합니다. 이는 기본 HDFS ACL 모델이 상속을 지원하지 않기 때문입니다. 예: 
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
`adal` 패키지를 사용하여(`pip install adal`을 통해) MSFT 테넌트에서 인증 컨텍스트를 만들고 OAuth 액세스 토큰을 획득합니다. ADLS의 경우 토큰 요청의 리소스는 대부분의 다른 Azure 리소스와는 달리 ‘ https://datalake.azure.net’에 있어야 합니다.

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
|4|1010691|10th Steet Community Farmers Market|http://agrimissouri.com/mo-grown/grodetail.php.. |10th Street and Poplar|라마|바턴|
