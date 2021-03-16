---
title: 'ML Studio (클래식): Azure Machine Learning로 마이그레이션-R 스크립트 실행'
description: Studio (클래식)를 다시 빌드하고 Azure Machine Learning에서 실행할 R 스크립트 모듈을 실행 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565032"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Studio에서 R 스크립트 실행 모듈 마이그레이션 (클래식)

이 문서에서는 Azure Machine Learning에서 Studio (클래식) **R 스크립트 실행** 모듈을 다시 빌드하는 방법에 대해 알아봅니다.

Studio (클래식)에서 마이그레이션하는 방법에 대 한 자세한 내용은 [마이그레이션 개요 문서](migrate-overview.md)를 참조 하세요.

## <a name="execute-r-script"></a>R 스크립트 실행

Azure Machine Learning designer는 이제 Linux에서 실행 됩니다. Studio (클래식)는 Windows에서 실행 됩니다. 플랫폼 변경으로 인해 마이그레이션 중에 **R 스크립트 실행** 을 조정 해야 합니다. 그렇지 않으면 파이프라인이 실패 합니다.

Studio (클래식)에서 **R 스크립트 실행** 모듈을 마이그레이션하려면 `maml.mapInputPort` 및 인터페이스를 표준 함수로 바꾸어야 합니다 `maml.mapOutputPort` .

다음 표에는 R 스크립트 모듈의 변경 내용이 요약 되어 있습니다.

|기능|Studio(클래식)|Azure Machine Learning 디자이너|
|---|---|---|
|스크립트 인터페이스|`maml.mapInputPort` 및 `maml.mapOutputPort`|Function 인터페이스|
|플랫폼|Windows|Linux|
|인터넷 액세스 가능 |예|예|
|메모리|14 GB|계산 SKU에 종속|

### <a name="how-to-update-the-r-script-interface"></a>R 스크립트 인터페이스를 업데이트 하는 방법

다음은 Studio에서 **R 스크립트 실행** 모듈 (클래식)의 내용입니다.
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

다음은 디자이너의 업데이트 된 내용입니다. `maml.mapInputPort`및는 `maml.mapOutputPort` 표준 함수 인터페이스로 대체 되었습니다 `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
자세한 내용은 디자이너 [R 스크립트 실행 모듈 참조](../algorithm-module-reference/execute-r-script.md)를 참조 하세요.

### <a name="install-r-packages-from-the-internet"></a>인터넷에서 R 패키지 설치

Azure Machine Learning designer를 사용 하 여 CRAN에서 직접 패키지를 설치할 수 있습니다.

이는 Studio (클래식) 보다 향상 된 기능입니다. Studio (클래식)는 인터넷에 액세스할 수 없는 샌드박스 환경에서 실행 되므로 패키지를 더 설치 하려면 zip 번들로 스크립트를 업로드 해야 했습니다. 

디자이너의 **R 스크립트 실행** 모듈에 다음 코드를 사용 하 여 cran 패키지를 설치 합니다.
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 R 스크립트 실행 모듈을 Azure Machine Learning로 마이그레이션하는 방법에 대해 알아보았습니다.

Studio (클래식) 마이그레이션 시리즈의 다른 문서를 참조 하세요.

1. [마이그레이션 개요](migrate-overview.md).
1. [데이터 집합을 마이그레이션합니다](migrate-register-dataset.md).
1. [Studio (클래식) 교육 파이프라인을 다시 빌드합니다](migrate-rebuild-experiment.md).
1. [Studio (클래식) 웹 서비스를 다시 빌드합니다](migrate-rebuild-web-service.md).
1. [Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. **R 스크립트 실행 모듈을 마이그레이션합니다**.