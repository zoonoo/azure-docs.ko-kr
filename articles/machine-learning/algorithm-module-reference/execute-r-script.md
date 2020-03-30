---
title: 'R 스크립트 실행: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 R 스크립트 실행 모듈을 사용하여 R 코드를 실행하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: f038293b48956ac89314e426df3f5dc491954df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064215"
---
# <a name="execute-r-script"></a>R 스크립트 실행

이 문서에서는 **R 스크립트 실행** 모듈을 사용하여 Azure 기계 학습 디자이너(미리 보기) 파이프라인에서 R 코드를 실행하는 방법을 설명합니다.

R을 사용하면 다음과 같은 기존 모듈에서 현재 지원되지 않는 작업을 수행할 수 있습니다. 
- 사용자 지정 데이터 변환 만들기
- 자체 측정항목을 사용하여 예측 평가
- 디자이너에서 독립 실행형 모듈로 구현되지 않은 알고리즘을 사용하여 모델을 빌드합니다.

## <a name="r-version-support"></a>R 버전 지원

Azure 기계 학습 디자이너는 R의 CRAN(포괄적인 R 아카이브 네트워크) 분포를 사용합니다. 현재 사용되는 버전은 CRAN 3.5.1입니다.

## <a name="supported-r-packages"></a>지원되는 R 패키지

R 환경은 100개 이상의 패키지와 함께 사전 설치되어 있습니다. 전체 목록은 사전 설치된 [R 패키지](#pre-installed-r-packages)섹션을 참조하십시오.

R **스크립트 실행** 모듈에 다음 코드를 추가하고 설치된 패키지를 볼 수도 있습니다.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>R 패키지 설치
추가 R 패키지를 설치하려면 `install.packages()` 메서드를 사용합니다. CRAN 리포지토리를 지정해야 합니다. 패키지는 각 **실행 R 스크립트** 모듈에 대해 설치되며 다른 실행 R **스크립트** 모듈에서 공유되지 않습니다.

이 샘플에서는 Zoo를 설치하는 방법을 보여 주며,
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
 > [!NOTE]
  > 반복 설치를 피하기 위해 설치하기 전에 패키지가 이미 있는지 확인하십시오. 위의 `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` 샘플 코드와 같습니다. 반복 설치하면 웹 서비스 요청 시간 시간이 변경될 수 있습니다.     

## <a name="upload-files"></a>파일 업로드
**실행 R 스크립트는** Azure 기계 학습 R SDK를 사용하여 파일 업로드를 지원합니다.

다음 예제에서는 **R 스크립트 실행에서**이미지 파일을 업로드 하는 방법을 보여 주며 있습니다.
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

파이프라인 실행이 완료되면 모듈의 오른쪽 패널에서 이미지를 미리 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![업로드된 이미지](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>실행 R 스크립트를 구성하는 방법

**R 스크립트 실행** 모듈에는 시작점으로 사용할 수 있는 샘플 코드가 포함되어 있습니다. **R 스크립트 실행** 모듈을 구성하려면 실행할 입력 및 코드 집합을 제공합니다.

![R 모듈](media/module/execute-r-script.png)

디자이너에 저장된 데이터 집합은 이 모듈을 사용하여 로드될 때 R 데이터 프레임으로 자동으로 변환됩니다.

1.  파이프라인에 **R 스크립트 실행** 모듈을 추가합니다.

  

1. 스크립트에 필요한 입력을 연결합니다. 입력은 선택 사항이며 데이터 및 추가 R 코드를 포함할 수 있습니다.

    * **데이터 집합1**: 첫 `dataframe1`번째 입력을 로 참조합니다. 입력 데이터 집합은 CSV, TSV, ARFF로 서식이 지정되거나 Azure 기계 학습 데이터 집합을 연결할 수 있습니다.

    * **데이터 집합2**: 두 `dataframe2`번째 입력을 로 참조합니다. 또한 이 데이터 집합은 CSV, TSV, ARFF 파일 또는 Azure 기계 학습 데이터 집합으로 서식을 지정해야 합니다.

    * **스크립트 번들**: 세 번째 입력은 ZIP 파일을 허용합니다. 압축된 파일에는 여러 파일과 여러 파일 형식이 포함될 수 있습니다.

1. R **스크립트** 텍스트 상자에서 유효한 R 스크립트를 입력하거나 붙여 넣습니다.

    시작하기 위해 **R 스크립트** 텍스트 상자에 편집하거나 바꿀 수 있는 샘플 코드가 미리 채워집니다.
    
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

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * 스크립트에는 이 모듈의 `azureml_main`진입점인 함수가 포함되어야 합니다.

 * 진입점 함수에는 최대 2개의 입력 `Param<dataframe1>` 인수가 포함될 수 있습니다.`Param<dataframe2>`
 
   > [!NOTE]
    > **실행 R 스크립트** 모듈에 전달된 데이터는 `dataframe1` `dataframe2`Azure 기계 학습 디자이너(디자이너 참조) `dataset1` `dataset2`와 다른 참조 및 참조됩니다. 입력 데이터가 스크립트에서 올바르게 참조되었는지 확인하십시오.  
 
    > [!NOTE]
    >  기존 R 코드는 디자이너 파이프라인에서 실행하기 위해 사소한 변경이 필요할 수 있습니다. 예를 들어 CSV 형식으로 제공하는 입력 데이터는 코드에서 사용하기 전에 데이터 집합으로 명시적으로 변환되어야 합니다. R 언어에 사용되는 데이터 및 열 유형도 디자이너에 사용되는 데이터 및 열 형식과 몇 가지 차이점이 있습니다.

1.  **랜덤 시드**: R 환경 내에서 임의 시드 값으로 사용할 값을 입력합니다. 이 매개 변수는 R 코드에서 `set.seed(value)`를 호출하는 경우와 동일합니다.  

1. 파이프라인을 제출합니다.  

## <a name="results"></a>결과

**실행 R 스크립트** 모듈은 여러 출력을 반환할 수 있지만 R 데이터 프레임으로 제공되어야 합니다. 데이터 프레임은 다른 모듈과의 호환성을 위해 디자이너의 데이터 집합으로 자동으로 변환됩니다.

R의 표준 메시지와 오류는 모듈의 로그에 반환됩니다.

R 스크립트에서 결과를 인쇄해야 하는 경우 모듈의 오른쪽 패널에서 **출력+로그** 탭 아래의 **70_driver_log** 인쇄된 결과를 찾을 수 있습니다.

## <a name="sample-scripts"></a>샘플 스크립트

사용자 지정 R 스크립트를 사용하여 파이프라인을 확장할 수 있는 방법에는 여러 가지가 있습니다.  이 섹션에서는 일반적인 작업에 대한 샘플 코드를 제공합니다.


### <a name="add-r-script-as-an-input"></a>R 스크립트를 입력으로 추가

**R 스크립트 실행** 모듈은 임의의 R 스크립트 파일을 입력으로 지원합니다. 이렇게 하려면 ZIP 파일의 일부로 작업 영역에 업로드해야 합니다.

1. 작업 영역에 R 코드가 포함된 ZIP 파일을 업로드하려면 **Dataset** 자산 페이지로 이동하여 **데이터 집합 만들기를**클릭한 다음 로컬 파일 및 **파일** 데이터 집합 유형 **옵션에서** 선택합니다.  

1. 왼쪽 모듈 트리의 **데이터 집합** 범주 아래의 내 **데이터 집합** 목록에서 압축된 파일을 사용할 수 있는지 확인합니다.

1.  데이터 집합을 스크립트 번들 입력 포트에 **연결합니다.**

1. ZIP 파일에 포함된 모든 파일은 파이프라인 런타임 중에 사용할 수 있습니다. 

    스크립트 번들 파일에 디렉터리 구조가 포함된 경우 구조가 유지됩니다. 그러나 디렉터리 **./Script 번들을** 경로에 미리 준비하려면 코드를 변경해야 합니다.

### <a name="process-data"></a>데이터 처리

다음 샘플에서는 입력 데이터의 크기를 조정하고 정규화하는 방법을 보여 주며 있습니다.

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

### <a name="read-a-zip-file-as-input"></a>ZIP 파일을 입력으로 읽기

이 샘플에서는 ZIP 파일의 데이터 집합을 **실행 R 스크립트** 모듈에 대한 입력으로 사용하는 방법을 보여 주며 있습니다.

1. CSV 형식으로 데이터 파일을 만들고 "mydatafile.csv"라고 지정합니다.
1. ZIP 파일을 만들고 CSV 파일을 아카이브에 추가합니다.
1. 압축된 파일을 Azure 기계 학습 작업 영역에 업로드합니다. 
1. 결과 데이터 집합을 **실행 R 스크립트** 모듈의 **ScriptBundle** 입력에 연결합니다.
1. 다음 코드를 사용하여 압축된 파일에서 CSV 데이터를 읽습니다.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>행 복제

이 샘플에서는 데이터 집합에서 양수 레코드를 복제하여 샘플의 균형을 맞추는 방법을 보여 주며 있습니다.

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>실행 R 스크립트 모듈 간에 R 개체 전달

내부 serialization 메커니즘을 사용하면 **R 스크립트 실행** 모듈 인스턴스 간에 R 개체를 전달할 수 있습니다. 이 예제에서는 두 개의 실행 R `A` **스크립트** 모듈 간에 명명된 R 개체를 이동하려는 것으로 가정합니다.

1. 파이프라인에 첫 번째 **실행 R 스크립트** 모듈을 추가하고 **R 스크립트** 텍스트 상자에 다음 `A` 코드를 입력하여 모듈의 출력 데이터 테이블에서 직렬화된 개체를 열로 만듭니다.  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    직렬화 함수는 디자이너가 지원하지 않는 R `Raw` 형식으로 데이터를 출력하기 때문에 정수 형식으로 명시적 변환이 수행됩니다.

1. **실행 R 스크립트** 모듈의 두 번째 인스턴스를 추가하고 이전 모듈의 출력 포트에 연결합니다.

1. **R 스크립트** 텍스트 상자에 다음 코드를 입력하여 입력 데이터 테이블에서 개체를 `A` 추출합니다. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>사전 설치된 R 패키지

사용할 수 있는 사전 설치된 R 패키지의 현재 목록:

|              |            | 
|--------------|------------| 
| 패키지      | 버전    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| 클립        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| 컴파일러     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| 데이터 세트     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| 평가     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| Fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| 제네릭(generics)     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| graphics     | 3.5.1      | 
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
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| 행렬       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
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
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| 공간      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| 작은 텍스      | 0.13       | 
| 도구        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| 엑스펀 (것)과         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| 동물원          | 1.8-6      | 

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 
