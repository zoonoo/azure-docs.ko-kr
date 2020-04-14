---
title: 리눅스 탐험
titleSuffix: Azure Data Science Virtual Machine
description: Linux 데이터 과학 가상 머신을 사용하여 몇 가지 일반적인 데이터 과학 작업을 완료하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 38088503fee016651a8c1c9a1f57ad4bbe102456
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257150"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Azure의 Linux 데이터 과학 가상 머신을 통한 데이터 과학

이 연습에서는 Linux 데이터 과학 가상 머신(DSVM)을 사용하여 몇 가지 일반적인 데이터 과학 작업을 완료하는 방법을 보여 주며 이 연습에서는 다음과 같은 몇 가지 일반적인 데이터 과학 작업을 완료할 수 있습니다. Linux DSVM은 Azure에서 사용할 수 있는 가상 시스템 이미지로 데이터 분석 및 기계 학습에 일반적으로 사용되는 도구 모음과 함께 사전 설치되어 있습니다. 주요 소프트웨어 구성 요소는 [Linux 데이터 과학 가상 컴퓨터 프로비저닝에서](linux-dsvm-intro.md)항목별로 분류됩니다. DSVM 이미지를 사용하면 각 도구를 개별적으로 설치하고 구성할 필요 없이 몇 분 만에 데이터 과학을 쉽게 시작할 수 있습니다. 필요한 경우 DSVM을 쉽게 확장할 수 있으며 사용하지 않을 때 중지할 수 있습니다. DSVM 리소스는 탄력적이고 비용 효율적입니다.

이 연습에서 설명한 데이터 과학 작업은 [팀 데이터 과학 프로세스에](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 설명된 단계를 따릅니다. 팀 데이터 과학 프로세스는 데이터 과학자 팀이 지능형 응용 프로그램을 구축하는 수명 주기 동안 효과적으로 협업할 수 있도록 도와주는 데이터 과학에 대한 체계적인 접근 방식입니다. 또한 데이터 과학 프로세스는 개별 사용자가 수행할 수 있는 데이터 과학을 위한 반복되는 프레임워크를 제공합니다.

이 연습에서는 [스팸베이스](https://archive.ics.uci.edu/ml/datasets/spambase) 데이터 집합을 분석합니다. 스팸베이스는 스팸 또는 햄(스팸아님)으로 표시된 이메일 집합입니다. 스팸베이스는 이메일의 내용에 대한 몇 가지 통계도 포함되어 있습니다. 이 연습의 후반부에서 통계에 대해 이야기합니다.

## <a name="prerequisites"></a>사전 요구 사항

Linux DSVM을 사용하려면 먼저 다음과 같은 필수 구성 조건이 있어야 합니다.

* **Azure 구독**. Azure 구독을 받으려면 [오늘 무료 Azure 계정 만들기를](https://azure.microsoft.com/free/)참조하십시오.
* [**리눅스 데이터 과학 가상 머신**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). 가상 컴퓨터 프로비저닝에 대한 자세한 내용은 [Linux 데이터 과학 가상 컴퓨터 프로비저닝을](linux-dsvm-intro.md)참조하십시오.
* [**X2Go는**](https://wiki.x2go.org/doku.php) 열려있는 XFCE 세션과 컴퓨터에 설치. 자세한 내용은 [X2Go 클라이언트 설치 및 구성을](linux-dsvm-intro.md#x2go)참조하십시오.
* DSVM의 Firefox 웹 브라우저에서 보다 원활한 스크롤 환경을 위해 `gfx.xrender.enabled` `about:config`에서 플래그를 전환합니다. [자세히 알아보기](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). 또한 로 `mousewheel.enable_pixel_scrolling` `False`설정하는 것을 고려하십시오. [자세히 알아보기](https://support.mozilla.org/questions/981140).
* **Azure 기계 학습 계정**. 아직 계정이 없는 경우 [Azure 기계 학습 홈 페이지에서](https://azure.microsoft.com/free/services/machine-learning//)새 계정에 등록합니다.

## <a name="download-the-spambase-dataset"></a>spambase 데이터 세트 다운로드

[스팸베이스](https://archive.ics.uci.edu/ml/datasets/spambase) 데이터 집합은 4,601개의 예제를 포함하는 비교적 작은 데이터 집합입니다. 데이터 집합은 리소스 요구 사항을 적당하게 유지하기 때문에 DSVM의 일부 주요 기능을 보여 주는 편리한 크기입니다.

> [!NOTE]
> 이 연습은 D2 v2 크기 리눅스 DSVM (우분투 18.04 에디션)를 사용 하 여 만든. 이 크기의 DSVM을 사용하여 이 연습에서 설명된 절차를 완료할 수 있습니다.

더 많은 저장 공간이 필요한 경우 추가 디스크를 만들어 DSVM에 연결할 수 있습니다. 디스크는 영구 Azure 저장소를 사용하므로 크기 조정으로 인해 서버가 다시 프로비저닝되거나 종료된 경우에도 해당 데이터가 유지됩니다. 디스크를 추가하고 DSVM에 연결하려면 [Linux VM에 디스크 추가](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)단계를 완료합니다. 디스크를 추가하는 단계는 DSVM에 이미 설치된 Azure CLI를 사용합니다. DSVM 자체에서 단계를 완전히 완료할 수 있습니다. 저장소를 늘리는 또 다른 옵션은 [Azure 파일을](../../storage/files/storage-how-to-use-files-linux.md)사용하는 것입니다.

데이터를 다운로드하려면 터미널 창을 연 다음 이 명령을 실행합니다.

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

다운로드한 파일에 헤더 행이 없습니다. 헤더가있는 다른 파일을 만들어 보겠습니다. 다음 명령을 실행하여 적합한 머리글이 있는 파일을 만듭니다.

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

그런 다음 두 파일을 함께 연결합니다.

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

데이터 집합에는 각 전자 메일에 대한 여러 유형의 통계가 있습니다.

* **단어\_freq\_WORD와** 같은 열은 *WORD와*일치하는 전자 메일의 단어 비율을 나타냅니다. 예를 **들어,\_단어\_freq 확인이** **1인**경우 전자 메일의 모든 단어 중 1%가 *확인되었습니다.*
* **char\_freq\_CHAR와** 같은 열은 *CHAR인*전자 메일의 모든 문자의 백분율을 나타냅니다.
* **capital\_run\_length\_longest**는 대문자 시퀀스의 가장 긴 길이입니다.
* **capital\_run\_length\_average**는 모든 대문자 시퀀스의 평균 길이입니다.
* **capital\_run\_length\_total**는 모든 대문자 시퀀스의 총 길이입니다.
* **spam** 은 메일이 스팸으로 간주되는지 여부를 나타냅니다(1 = 스팸, 0 = 스팸이 아님).

## <a name="explore-the-dataset-by-using-r-open"></a>R 열기를 사용하여 데이터 집합 탐색

R을 사용하여 데이터를 검사하고 몇 가지 기본 기계 학습을 수행해 보겠습니다. DSVM은 [마이크로 소프트 R 오픈](https://mran.revolutionanalytics.com/open/) 사전 설치된 함께 제공됩니다. R의 사전 설치된 버전에서 다중 스레드 수학 라이브러리는 단일 스레드 버전보다 더 나은 성능을 제공합니다. R Open은 CRAN 패키지 리포지토리의 스냅샷을 통해 재현성을 제공합니다.

이 연습에서 사용되는 코드 샘플의 복사본을 얻으려면 git을 사용하여 Azure-Machine-Learning-Data-Science 리포지토리를 복제합니다. Git은 DSVM에 사전 설치되어 있습니다. git 명령줄에서 다음을 실행합니다.

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

터미널 창을 열고 R 대화형 콘솔에서 새 R 세션을 시작합니다. DSVM에 사전 설치된 RStudio를 사용할 수도 있습니다.

데이터를 가져오고 환경을 설정하려면 다음을 수행합니다.

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

각 열에 대한 요약 통계를 보려면

```R
summary(data)
```

데이터를 다른 보기로 보려면

```R
str(data)
```

이 보기는 각 변수의 유형과 데이터 집합의 처음 몇 가지 값을 보여 주며 있습니다.

**spam** 열은 정수로 읽었지만 실제로는 범주 변수(또는 요소)입니다. 해당 형식을 설정하려면

```R
data$spam <- as.factor(data$spam)
```

일부 예비 분석을 수행하려면 DSVM에 사전 설치된 R에 대한 인기 있는 그래프 라이브러리인 [ggplot2](https://ggplot2.tidyverse.org/) 패키지를 사용합니다. 앞에서 보여 드시면 요약 데이터를 기반으로 느낌표 문자의 빈도에 대한 요약 통계가 있습니다. 다음 명령을 실행하여 이러한 주파수를 플로팅해 보겠습니다.

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

0 막대가 플롯을 왜곡하기 때문에 다음을 제거해 보겠습니다.

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

1 이상의 사소한 밀도가 흥미롭게 보입니다. 해당 데이터만 살펴보겠습니다.

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

그런 다음 스팸과 햄으로 나눕습니다.

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

이러한 예제는 유사한 플롯을 만들고 다른 열의 데이터를 탐색하는 데 도움이 됩니다.

## <a name="train-and-test-a-machine-learning-model"></a>기계 학습 모델 교육 및 테스트

데이터 집합의 전자 메일을 스팸 또는 햄을 포함하는 것으로 분류하기 위해 몇 가지 기계 학습 모델을 학습해 보겠습니다. 이 섹션에서는 의사 결정 트리 모델과 임의포리스트 모델을 학습합니다. 그런 다음 예측의 정확도를 테스트합니다.

> [!NOTE]
> 다음 코드에 사용되는 *rpart(재구성* 분할 및 회귀 트리) 패키지가 DSVM에 이미 설치되어 있습니다.

먼저 데이터 집합을 학습 집합 및 테스트 집합으로 분할해 보겠습니다.

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

그런 다음 전자 메일을 분류하는 의사 결정 트리를 만듭니다.

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

결과:

![생성된 의사 결정 트리다이어그램](./media/linux-dsvm-walkthrough/decision-tree.png)

학습 집합에 대해 얼마나 잘 수행하는지를 확인하려면 다음 코드를 사용합니다.

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

테스트 집합에 대해 얼마나 잘 수행하는지를 확인하려면

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

임의 포리스트 모델도 살펴보겠습니다. 임의 포리스트는 수많은 의사 결정 트리를 학습하고 모든 개별 의사 결정 트리에서 분류 모드인 클래스를 출력합니다. 의사 결정 트리 모델이 학습 데이터 집합에 과도하게 적합하도록 하는 경향을 수정하기 때문에 보다 강력한 기계 학습 접근 방식을 제공합니다.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>딥 러닝 자습서 및 연습

프레임워크 기반 샘플 외에도 포괄적인 연습 집합도 제공됩니다. 이러한 연습을 통해 이미지 및 텍스트/언어 이해와 같이 도메인에서 딥 러닝 애플리케이션의 개발을 빠르게 시작할 수 있습니다.

- [서로 다른 프레임워크에서 신경망 실행](https://github.com/ilkarman/DeepLearningFrameworks): 한 프레임워크에서 다른 프레임워크로 코드를 마이그레이션하는 방법을 보여 주는 포괄적인 연습입니다. 또한 프레임워크 전체에서 모델 및 런타임 성능을 비교하는 방법을 보여 줍니다. 

- [이미지 내에서 제품을 감지하는 엔드 투 엔드 솔루션을 구축하는 방법 가이드](https://github.com/Azure/cortana-intelligence-product-detection-from-images): 이미지 감지는 이미지 내에서 개체를 찾고 분류할 수 있는 기술입니다. 이 기술은 많은 실제 비즈니스 영역에서 엄청난 보상을 가져올 수있는 잠재력을 가지고 있습니다. 예를 들어 소매업체는 이 기술을 사용하여 고객이 선택한 제품을 확인할 수 있습니다. 또한 이 정보는 매장의 제품 재고를 관리하는 데 도움이 됩니다. 

- [오디오에 대한 딥 러닝](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): 이 자습서에서는 [도시 사운드 데이터 집합에서](https://urbansounddataset.weebly.com/)오디오 이벤트 감지를 위한 딥 러닝 모델을 학습하는 방법을 보여 주며. 이 자습서에서는 오디오 데이터 사용 방법에 대한 개요를 제공합니다.

- [텍스트 문서 분류](https://github.com/anargyri/lstm_han): 이 연습에서는 계층적 주의 네트워크와 LSTM(장기 기억)이라는 두 가지 신경망 아키텍처를 구축하고 학습하는 방법을 보여 줍니다. 이러한 신경망은 Keras API를 딥 러닝에 사용하여 텍스트 문서를 분류합니다. Keras는 가장 인기 있는 딥 러닝 프레임워크인 Microsoft Cognitive Toolkit, TensorFlow 및 Theano의 프런트 엔드입니다.

## <a name="other-tools"></a>기타 도구

나머지 섹션에서는 Linux DSVM에 설치된 일부 도구를 사용하는 방법을 보여 준다. 이러한 도구에 대해 설명합니다.

* XGBoost
* Python
* 주피터허브
* Rattle
* 포스트그레SQL 및 스퀴렐 SQL
* SQL Server 데이터 웨어하우스

### <a name="xgboost"></a>XGBoost

[XGBoost는](https://xgboost.readthedocs.org/en/latest/) 빠르고 정확한 부스트 트리 구현을 제공합니다.

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

XGBoost는 파이썬 이나 명령줄에서 호출 할 수도 있습니다.

### <a name="python"></a>Python

파이썬 개발을 위해 아나콘다 파이썬 배포판 3.5 및 2.7이 DSVM에 설치됩니다.

> [!NOTE]
> 아나콘다 분포에는 [콘다 분포가](https://conda.pydata.org/docs/index.html)포함됩니다. Conda를 사용하여 다른 버전이나 패키지가 설치된 사용자 정의 파이썬 환경을 만들 수 있습니다.

일부 스팸베이스 데이터 집합을 읽고 Scikit-learn의 지원 벡터 컴퓨터로 이메일을 분류해 보겠습니다.

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

예측하려면

```Python
clf.predict(X.ix[0:20, :])
```

Azure 기계 학습 끝점을 게시하는 방법을 보여 드리기 위해 보다 기본적인 모델을 만들어 보겠습니다. R 모델을 이전에 게시할 때 사용한 세 가지 변수를 사용합니다.

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Azure 기계 학습에 모델을 게시하려면 다음을 수행합니다.

```Python
# Publish the model.
workspace_id = "<workspace-id>"
workspace_token = "<workspace-token>"
from azureml import services
@services.publish(workspace_id, workspace_token)
@services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
@services.returns(int) # 0 or 1
def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
    inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
    return clf.predict(inputArray)

# Get some info about the resulting model.
predictSpam.service.url
predictSpam.service.api_key

# Call the model
predictSpam.service(1, 1, 1)
```

> [!NOTE]
> 이 옵션은 파이썬 2.7에서만 사용할 수 있습니다. 파이썬 3.5에서 아직 지원되지 않습니다. 실행하려면 **/anaconda/bin/python2.7을**사용합니다.

### <a name="jupyterhub"></a>주피터허브

DSVM의 아나콘다 배포판에는 파이썬, R 또는 줄리아 코드 및 분석을 공유하기 위한 크로스 플랫폼 환경인 Jupyter 노트북이 함께 제공됩니다. 주피터 노트북은 JupyterHub를 통해 액세스 할 수 있습니다. DSVM DNS 이름 또는 IP\<주소\>:8000/https:// 에서 로컬 Linux 사용자 이름과 암호를 사용하여 로그인합니다. JupyterHub의 모든 구성 파일은 /etc/jupyterhub에서 찾을 수 있습니다.

> [!NOTE]
> 현재 커널의 Jupyter `pip` 노트북의 파이썬 패키지 관리자(명령을 통해)를 사용하려면 코드 셀에서 이 명령을 사용합니다.
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 현재 커널의 Jupyter 노트북에서 명령을 통해 `conda` Conda 설치 관리자를 사용하려면 코드 셀에서 이 명령을 사용합니다.
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

여러 샘플 노트북이 이미 DSVM에 설치되어 있습니다.

* 샘플 파이썬 노트북 :
  * [인트로토주피터파이썬.이핀브](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [아이리스분류기PymlWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* 샘플 R 노트북:
  * [인트로튜토리얼틴R](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> 줄리아 언어는 Linux DSVM의 명령줄에서도 사용할 수 있습니다.

### <a name="rattle"></a>Rattle

[딸랑이](https://cran.r-project.org/web/packages/rattle/index.html) *(R* *A*nalytical *T*o *o* *L*적립 *E*asily) 데이터 마이닝을위한 그래픽 R 도구입니다. Rattle은 직관적인 인터페이스를 통해 데이터를 쉽게 로드, 탐색 및 변환하고 모델을 빌드 및 평가할 수 있습니다. [딸랑이: R용 데이터 마이닝 GUI는](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) 래틀의 기능을 보여 주는 연습을 제공합니다.

다음 명령을 실행하여 딸랑이를 설치하고 시작합니다.

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> DSVM에 딸랑이를 설치할 필요가 없습니다. 그러나 Rattle이 열리면 추가 패키지를 설치하라는 메시지가 표시될 수 있습니다.

Rattle은 탭 기반 인터페이스를 사용합니다. 대부분의 탭은 데이터 로드 또는 데이터 탐색과 같은 [팀 데이터 과학 프로세스의](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)단계에 해당합니다. 데이터 과학 프로세스는 탭을 통해 왼쪽에서 오른쪽으로 진행됩니다. 마지막 탭에는 Rattle이 실행한 R 명령의 로그가 포함되어 있습니다.

데이터 세트를 로드하고 구성하려면

1. 파일을 로드하려면 **데이터** 탭을 선택합니다.
1. **파일 이름**옆의 선택기를 선택한 다음 **spambaseHeaders.data를 선택합니다.**
1. 파일을 로드하려면 **실행**을 선택합니다. 식별된 데이터 형식을 포함하여 각 열에 대한 요약이 표시됩니다. 입력, 대상 또는 기타 유형의 변수인지 여부; 및 고유 값의 수입니다.
1. Rattle은 **spam** 열을 대상으로 제대로 식별합니다. **스팸** 열을 선택한 다음 **대상 데이터 유형을** **Categoric로**설정합니다.

데이터를 탐색하려면

1. **탐색** 탭을 선택합니다.
1. 변수 형식 및 일부 요약 통계에 대한 정보를 보려면 **요약** > **실행**을 선택합니다.
1. 각 변수에 대한 다른 유형의 통계를 보려면 **설명** 또는 기본 값과 같은 다른 옵션을 **선택합니다.**

**탐색** 탭을 사용하여 통찰력 있는 플롯을 생성할 수도 있습니다. 데이터의 히스토그램을 나타내려면

1. **배포**를 선택합니다.
1. **word_freq_remove** 및 **word_freq_you**위해 **히스토그램을**선택합니다.
1. **실행을 선택합니다.** 단일 그래프 창에서 두 밀도 플롯을 모두 볼 _수_ 있으며, 이 경우 제거하는 _것보다_전자 메일에 훨씬 더 자주 표시되는 단어가 분명합니다.

**상관 관계** 플롯도 흥미롭습니다. 플롯을 작성하려면 다음을 수행합니다.

1. **유형의**경우 **상관 관계를**선택합니다.
1. **실행을 선택합니다.**
1. 권장 최대 변수는 40개라는 경고 메시지가 표시됩니다. 도표를 보려면 **예** 를 선택합니다.

몇 가지 흥미로운 상관 관계가 있습니다: _기술은_ _HP와_ _실험실과_밀접한 상관 관계가 있습니다. 또한 데이터 집합 기증자의 영역 번호가 _650이기_ 때문에 650과 밀접한 상관 관계가 있습니다.

단어 간의 상관 관계에 대한 숫자 값은 **탐색** 창에서 사용할 수 있습니다. 예를 들어, _기술이_ 귀사및 _돈과_부정적으로 상관관계가 있다는 점에 유의하는 것이 _흥미롭습니다._

Rattle은 몇 가지 일반적인 문제를 처리하기 위해 데이터 세트를 변환할 수 있습니다. 예를 들어 피처의 크기를 조정하고, 누락된 값을 분리하고, 이상값을 처리하고, 누락된 데이터가 있는 변수 또는 관측값을 제거할 수 있습니다. Rattle은 관측값과 변수 간의 연관 규칙을 식별할 수도 있습니다. 이 탭은 이 소개 연습에서 다루지 않습니다.

딸랑이는 클러스터 분석을 실행할 수도 있습니다. 출력을 더 쉽게 읽을 수 있도록 일부 기능을 제외하겠습니다. **데이터** 탭에서 다음 10개 항목을 제외한 각 변수 옆에 **무시를** 선택합니다.

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

**클러스터** 탭으로 돌아갑니다. **KMeans를**선택한 다음 클러스터 수를 **4로** **설정합니다.** **실행을 선택합니다.** 결과가 출력 창에 표시됩니다. 한 클러스터는 _조지와_ _hp의_높은 주파수를 가지고 있으며, 아마도 합법적 인 비즈니스 이메일입니다.

기본 의사 결정 트리 기계 학습 모델을 구축하려면 다음을 수행하십시오.

1. **모델** 탭을 선택하고
1. **유형에서** **트리를**선택합니다.
1. **실행** 을 선택하여 출력 창에 텍스트 형식으로 트리를 표시합니다.
1. **그리기** 단추를 선택하여 그래픽 버전을 봅니다. 의사 결정 트리는 rpart를 사용하여 이전에 얻은 트리와 유사합니다.

Rattle의 유용한 기능은 여러 기계 학습 방법을 실행하고 신속하게 평가할 수 있다는 것입니다. 단계는 다음과 같습니다.

1. **유형의**경우 **모두**를 선택합니다.
1. **실행을 선택합니다.**
1. Rattle 실행이 완료되면 **SVM과**같은 **유형** 값을 선택하고 결과를 볼 수 있습니다.
1. **평가** 탭을 사용하여 유효성 검사 집합에서 모델의 성능을 비교할 수도 있습니다. 예를 들어 **오류 매트릭스** 선택에는 유효성 검사 세트의 각 모델에 대한 혼동 행렬, 전체 오류 및 평균 클래스 오류가 표시됩니다. ROC 곡선을 플롯하고, 감도 해석을 실행하고, 다른 유형의 모델 평가를 수행할 수도 있습니다.

모델 작성이 완료되면 **로그** 탭을 선택하여 세션 중에 Rattle이 실행한 R 코드를 봅니다. **내보내기** 단추를 선택하여 저장할 수 있습니다.

> [!NOTE]
> Rattle의 현재 릴리스에는 버그가 포함되어 있습니다. 스크립트를 수정하거나 나중에 단계를 반복하는 데 사용하려면 **#** 로그 텍스트에 *이 로그 내보내기* 앞에 문자를 삽입해야 합니다.

### <a name="postgresql-and-squirrel-sql"></a>포스트그레SQL 및 스퀴렐 SQL

DSVM은 PostgreSQL이 설치된 상태로 제공됩니다. PostgreSQL은 정교한 오픈 소스 관계형 데이터베이스입니다. 이 섹션에서는 스팸베이스 데이터 집합을 PostgreSQL에 로드한 다음 쿼리하는 방법을 보여 주며 이 섹션에서는

데이터를 로드하려면 먼저 로컬 호스트에서 암호 인증을 허용해야 합니다. 명령 프롬프트에서 다음을 실행합니다.

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

구성 파일의 아래쪽 몇 줄은 허용되는 연결을 자세히 설명하는 줄입니다.

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

**IPv4 로컬 연결** 라인을 ID 대신 **ident** **md5를** 사용하도록 변경하여 사용자 이름과 암호를 사용하여 로그인할 수 있습니다.

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

그런 다음 PostgreSQL 서비스를 다시 시작합니다.

```Bash
sudo systemctl restart postgresql
```

psql(PostgreSQL용 대화형 터미널)을 기본 제공 postgres 사용자로 실행하려면 다음 명령을 실행합니다. *psql*

```Bash
sudo -u postgres psql
```

로그인하는 데 사용한 Linux 계정의 사용자 이름을 사용하여 새 사용자 계정을 만듭니다. 암호 만들기:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

psql에 로그인:

```Bash
psql
```

데이터를 새 데이터베이스로 가져옵니다.

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

이제 JDBC 드라이버를 통해 데이터베이스와 상호 작용하는 데 사용할 수 있는 그래픽 도구인 SQuirreL SQL을 사용하여 데이터를 탐색하고 일부 쿼리를 실행해 보겠습니다.

시작하려면 응용 프로그램 메뉴에서 SQuirreL **SQL을 엽니다.** 드라이버를 설정하려면

1. **윈도우** > **보기 드라이버를 선택합니다.**
1. **PostgreSQL을** 마우스 오른쪽 단추로 클릭하고 **드라이버 수정을**선택합니다.
1. **추가 클래스 경로** > 추가 를**선택합니다.**
1. **파일 이름의**경우 **/usr/share/java/jdbcdriver/postgresql-9.4.1208.jre6.jar를**입력합니다.
1. **열기**를 선택합니다.
1. **드라이버 목록**선택 . **클래스 이름의**경우 **org.postgresql.Driver를**선택한 다음 **확인을**선택합니다.

로컬 서버에 연결을 설정하려면

1. **Windows** > **보기 별칭을 선택합니다.**
1. 단추를 **+** 선택하여 새 별칭을 만듭니다. 새 별칭 이름의 경우 **스팸 데이터베이스를**입력합니다. 
1. **드라이버의**경우 **PostgreSQL을**선택합니다.
1. URL을 **jdbc:postgresql://localhost/spam**으로 설정합니다.
1. 사용자 이름 및 암호를 입력합니다.
1. **확인**을 선택합니다.
1. **연결** 창을 열려면 별칭 **스팸 데이터베이스**를 두 번 클릭합니다.
1. **연결**을 선택합니다.

일부 쿼리를 실행하려면

1. **SQL** 탭을 선택합니다.
1. **SQL** 탭 상단의 쿼리 상자에 다음과 같은 `SELECT * from data;`기본 쿼리를 입력합니다.
1. Ctrl+Enter를 눌러 쿼리를 실행합니다. 기본적으로 SQuirreL SQL은 쿼리에서 처음 100개의 행을 반환합니다.

이 데이터를 탐색하기 위해 실행할 수 있는 쿼리가 더 많습니다. 예를 들어 스팸과 햄 간에 *make* 라는 단어의 빈도가 얼마나 차이가 있을까요?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

또는 *3D를*자주 포함하는 전자 메일의 특징은 무엇입니까?

```SQL
SELECT * from data order by word_freq_3d desc;
```

*3D의* 발생이 높은 대부분의 이메일은 분명히 스팸입니다. 이 정보는 전자 메일을 분류하는 예측 모델을 작성하는 데 유용할 수 있습니다.

PostgreSQL 데이터베이스에 저장된 데이터를 사용하여 기계 학습을 수행하려면 [MADlib](https://madlib.incubator.apache.org/)을 사용하는 것이 좋습니다.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse는 관계형 및 비관계형 모두 대량의 데이터를 처리할 수 있는 클라우드 기반의 확장 데이터베이스입니다. 자세한 내용은 [Azure SQL Data Warehouse란?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

데이터 웨어하우스에 연결하고 테이블을 만들려면 명령 프롬프트에서 다음 명령을 실행합니다.

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

sqlcmd 프롬프트에서 다음 명령을 실행합니다.

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

숨은 참조를 사용하여 데이터를 복사합니다.

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> 다운로드한 파일에는 Windows 스타일 선 끝이 포함되어 있습니다. bcp 도구는 유닉스 스타일 선 끝을 기대합니다. -r 플래그를 사용하여 숨은 참조를 알 수 있습니다.

그런 다음 sqlcmd를 사용하여 쿼리합니다.

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

SQuirreL SQL을 사용하여 쿼리할 수도 있습니다. SQL Server JDBC 드라이버를 사용하여 PostgreSQL과 유사한 단계를 따릅니다. JDBC 드라이버는 /usr/share/java/jdbcdriver/sqljdbc42.jar 폴더에 있습니다.

## <a name="next-steps"></a>다음 단계

Azure의 데이터 과학 프로세스를 구성하는 작업을 안내하는 문서에 대한 개요는 [팀 데이터 과학 프로세스](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)를 참조하십시오.

특정 시나리오에 대한 팀 데이터 과학 프로세스의 단계를 보여 주는 종단 간 연습에 대한 설명은 [팀 데이터 과학 프로세스 연습을](../team-data-science-process/walkthroughs.md)참조하십시오. 또한 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 설명합니다.
