---
title: R 스크립트를 실행 합니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 R 스크립트 실행 모듈을 사용 하 여 R 코드를 실행 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518054"
---
# <a name="execute-r-script"></a>R 스크립트 실행

이 문서에서는 사용 하는 방법을 설명 합니다 **R 스크립트 실행** 시각적 인터페이스 실험에서 R 코드를 실행 하는 모듈입니다.

R을 사용 하 여 현재 지원 되지 않습니다 기존 모듈에서 같은 작업을 수행할 수 있습니다. 
- 사용자 지정 데이터 변환을 만들합니다
- 사용자 고유의 메트릭을 사용 하 여 예측 평가
- 시각적 인터페이스에서 독립형 모듈로 구현 되지 않았습니다 하는 알고리즘을 사용 하 여 모델 빌드

## <a name="r-version-support"></a>R 버전 지원

R CRAN (Comprehensive R Archive Network) 배포를 사용 하는 Azure Machine Learning 서비스 시각적 인터페이스 현재 사용 되는 버전 3.5.1 CRAN입니다.

## <a name="supported-r-packages"></a>지원 되는 R 패키지

R 환경은 100 개가 넘는 패키지와 함께 사전 설치 합니다. 전체 목록을 보려면 [미리 설치 된 R 패키지](#pre-installed-r-packages)합니다.

에 다음 코드를 추가할 수도 있습니다 **R 스크립트 실행** 모듈 및 설치 된 패키지를 참조 하세요.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>R 패키지 설치
추가 R 패키지를 설치 하려면 사용 된 `install.packages()` 메서드. CRAN 리포지토리를 지정 해야 합니다. 각각에 대해 설치 된 패키지 **R 스크립트 실행** 모듈 및 다른 간에 공유 되지 않습니다 **R 스크립트 실행** 모듈입니다.

이 샘플에는 Zoo를 설치 하는 방법을 보여 줍니다.
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>R 스크립트 실행을 구성 하는 방법

합니다 **R 스크립트 실행** 모듈 시작 점으로 사용할 수 있는 샘플 코드를 포함 합니다. 구성 하는 **R 스크립트 실행** 모듈을 실행 하는 코드와 입력 집합을 제공 합니다.

![R 모듈](media/module/execute-r-script.png)

시각적 인터페이스에 저장 된 데이터 집합은이 모듈을 사용 하 여 로드 하는 경우에 R 데이터 프레임으로 자동 변환 됩니다.

1.  추가 된 **R 스크립트 실행** 모듈을 실험 합니다.

    > [!NOTE]
    > 에 전달 된 모든 데이터를 **R 스크립트 실행** 모듈의 R로 변환 됩니다 `data.frame` 형식입니다.

1. 스크립트에 필요한 입력을 연결 합니다. 입력 선택 사항 이므로 데이터와 추가 R 코드를 포함할 수 있습니다.

    * **Dataset1**: 첫 번째 입력 참조 `dataframe1`합니다. 입력된 데이터 집합 CSV, TSV, ARFF로 포맷 해야 하거나 Azure Machine Learning 데이터 집합을 연결할 수 있습니다.

    * **Dataset2**: 두 번째 입력 참조 `dataframe2`합니다. 이 데이터 집합 또한으로 포맷 되어야 합니다는 CSV, TSV, ARFF 파일 또는 Azure Machine Learning 데이터 집합을 합니다.

    * **스크립트 번들**: 세 번째 입력은 ZIP 파일을 허용합니다. 압축된 된 파일에는 여러 파일 및 여러 파일 유형을 포함할 수 있습니다.

1. 에 **R 스크립트** 텍스트 상자에 입력 하거나 유효한 R 스크립트를 붙여 넣습니다.

    시작 하는 데는 **R 스크립트** 텍스트 상자는 편집 하거나 바꿀 수 있는 샘플 코드로 미리 채워져 있습니다.

    * 스크립트 라는 함수를 포함 해야 합니다 `azureml_main`에이 모듈에 대 한 진입점입니다.

    * 진입점 함수는 최대 2 개의 입력된 인수를 포함할 수 있습니다: `Param<dataframe1>` 및 `Param<dataframe2>`
 
    > [!NOTE]
    >  기존 R 코드에서 시각적 인터페이스 실험을 실행 하려면 약간 변경 해야 합니다. 예를 들어, CSV 형식으로 제공 하는 입력된 데이터를 명시적으로 변환할 데이터 집합에 코드에서 사용할 수 있습니다. R 언어에서 사용 되는 데이터 및 열 형식 차이점도 몇 가지 시각적 인터페이스에 사용 되는 데이터 및 열 형식에서입니다.

1.  **임의 초기값**: R 환경 내에서 임의 초기값으로 사용할 값을 입력 합니다. 이 매개 변수는 호출 `set.seed(value)` R 코드에서.  

1. 실험을 실행합니다.  

## <a name="results"></a>결과

합니다 **R 스크립트 실행** 모듈에는 여러 출력을 반환할 수 있지만 R 데이터 프레임으로 제공 되어야 합니다. 데이터 프레임은 다른 모듈과 호환성에 대 한 시각적 인터페이스 데이터 집합에 자동으로 변환 됩니다.

표준 메시지 및 R의 오류는 모듈의 로그에 반환 됩니다.

## <a name="sample-scripts"></a>샘플 스크립트

사용자 지정 R 스크립트를 사용 하 여 실험을 확장할 수 있는 여러 가지 있습니다.  이 섹션에서는 일반적인 작업에 대 한 샘플 코드를 제공합니다.


### <a name="add-r-script-as-an-input"></a>입력으로 R 스크립트 추가

합니다 **R 스크립트 실행** 모듈은 입력으로 임의 R 스크립트 파일을 지원 합니다. 이렇게 하려면 해당 ZIP 파일의 일부로 작업 영역에 업로드 해야 합니다.

1. 작업 영역에 R 코드를 포함 한 ZIP 파일을 업로드 하려면 클릭 **새로 만들기**, 클릭 **데이터 집합**를 선택한 후 **로컬 파일에서** 고 **Zip 파일**옵션입니다.  

1. 압축된 된 파일에서 사용할 수 있는지 확인 합니다 **저장 된 데이터 집합** 목록입니다.

1.  데이터 집합을 연결 합니다 **스크립트 번들** 포트를 입력 합니다.

1. ZIP 파일에 포함 된 모든 파일은 실험 실행 시간 동안 사용할 수 있습니다. 

    스크립트 번들 파일을 디렉터리 구조에 포함 하는 경우에 구조가 보존 됩니다. 그러나 디렉터리 앞에 코드를 변경 해야 합니다 **합니다. 스크립트 번들 /** 경로에 있습니다.

### <a name="process-data"></a>데이터 처리

다음 샘플 크기를 조정 하 여 입력된 데이터를 정규화 하는 방법을 보여 줍니다.

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>입력으로 ZIP 파일 읽기

이 샘플에 대 한 입력으로 ZIP 파일에서 데이터 집합을 사용 하는 방법을 보여 줍니다 합니다 **R 스크립트 실행** 모듈입니다.

1. CSV 형식의 데이터 파일을 만들고 "mydatafile.csv" 라는 이름을 지정 합니다.
1. ZIP 파일을 만들고 보관 파일을 CSV 파일을 추가 합니다.
1. Azure Machine Learning 작업 영역에 zip 파일을 업로드 합니다. 
1. 결과 데이터 집합을 연결 합니다 **ScriptBundle** 의 입력에 **R 스크립트 실행** 모듈입니다.
1. 다음 코드를 사용 하 여 압축 된 파일에서 CSV 데이터를 읽을 수 있습니다.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>행 복제

이 샘플에는 샘플을 분산 하는 데이터 집합의 양의 레코드를 복제 하는 방법을 보여 줍니다.

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>R 스크립트 실행 모듈 간에 R 개체 전달

인스턴스 간에 R 개체를 전달할 수 있습니다 합니다 **R 스크립트 실행** 내부 직렬화 메커니즘을 사용 하 여 모듈입니다. 이 예제에서는 라는 R 개체를 이동 하 려 한다고 가정 `A` 간 **R 스크립트 실행** 모듈입니다.

1. 첫 번째 추가 **R 스크립트 실행** 형식에 다음 코드를 확인 하 고 실험에서 모듈을 **R 스크립트** serialize 된 개체를 만들려면 텍스트 상자 `A` 데이터 테이블을 출력의 모듈에 열으로:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Serialization 함수는 R에서 데이터를 출력 하기 때문에 정수 형식으로 명시적 변환이 이루어집니다 `Raw` 시각적 인터페이스에서 지원 되지 않는 형식입니다.

1. 두 번째 인스턴스를 추가 합니다 **R 스크립트 실행** 모듈 이전 모듈의 출력 포트에 연결 합니다.

1. 다음 코드를 입력 합니다 **R 스크립트** 개체를 추출 하려면 텍스트 상자 `A` 입력된 데이터 테이블에서. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>사전 설치 된 R 패키지

현재 목록을 미리 설치 된 R 패키지를 사용할 수 있습니다.

|              |            | 
|--------------|------------| 
| 패키지      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| 자료         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| Broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| 캐럿        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| 색상 공간   | 1.4-1      | 
| 컴파일러     | 3.5.1      | 
| 크레용       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| 평가     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| 외래      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| 제네릭     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| 그래픽     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| (grid)         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| 반복기    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| 레이블 지정     | 0.3        | 
| 격자      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| 행렬       | 1.2-17     | 
| 메서드      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| 병렬     | 3.5.1      | 
| 기본 요소       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| 진행률     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| 레시피      | 0.1.5      | 
| 어림 없어      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| 눈금       | 1.0.0      | 
| 다운 그레이드할      | 0.4-1      | 
| 공간      | 7.3-11     | 
| 스플라인      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| 생존     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| 도구        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| 유틸리티        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| 수염      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| 동물원          | 1.8-6      | 

## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 