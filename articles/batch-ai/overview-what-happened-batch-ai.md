---
title: Azure Batch AI에 대한 새로운 소식 | Microsoft Docs
description: Azure Batch AI 및 Azure Machine Learning Service 컴퓨팅 옵션의 최신 소식을 알아봅니다.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194505"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI에 대한 새로운 소식

**Azure Batch AI 서비스가 3월에 사용 중지됩니다.** Batch AI의 대규모 학습 및 채점 기능을 이제 [Azure Machine Learning Service](../machine-learning/service/overview-what-is-azure-ml.md)에서 사용할 수 있습니다(2018년 12월 4일 일반 제공됨).

Azure Machine Learning Service에는 다양한 다른 기계 학습 기능과 함께 기계 학습 모델을 학습시키고, 배포하며, 채점하기 위한 클라우드 기반 관리형 컴퓨팅 대상이 포함되어 있습니다. 이 컴퓨팅 대상을 [Azure Machine Learning 컴퓨팅](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)이라고 합니다. [지금 마이그레이션하고 사용을 시작](#migrate)해 보세요. [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md), 명령줄 인터페이스 및 [Azure Portal](../machine-learning/service/quickstart-get-started.md)을 통해 Azure Machine Learning Service와 상호 작용할 수 있습니다.

## <a name="support-timeline"></a>지원 타임라인

현재, 기존 Azure Batch AI 구독을 이전과 같이 계속 사용할 수 있습니다. 그러나 **새 구독**이 가능하지 않으며 새로운 투자도 하고 있지 않습니다.

2019년 3월 &nbsp;31일&nbsp;부터 기존 Batch AI 구독이 더 이상 작동하지 않습니다.

## <a name="compare-to-azure-machine-learning"></a>Azure Machine Learning과 비교
클라우드에서 제공하는 광범위한 모든 규모에 맞게 기계 학습 모델을 학습, 배포, 자동화 및 관리하는 데 사용할 수 있는 클라우드 서비스입니다. [이 개요](../machine-learning/service/overview-what-is-azure-ml.md)에서 Azure Machine Learning Service에 대한 개략적인 이해를 얻습니다.
 

일반적인 모델 개발 수명 주기에는 데이터 준비, 학습 및 실험, 배포 단계가 포함됩니다. 이 엔드투엔드 주기는 Machine Learning 파이프라인을 사용하여 오케스트레이션할 수 있습니다.

![흐름 다이어그램](./media/overview-what-happened-batch-ai/lifecycle.png)


[서비스 작동 방식과 주요 개념에 대해 자세히 알아보세요](../machine-learning/service/concept-azure-machine-learning-architecture.md). 모델 학습 워크플로의 개념 대부분은 Batch AI의 기존 개념과 비슷합니다. 

특히 이러한 개념에 대해 고려해야 하는 방법에 대한 매핑은 다음과 같습니다.
 
|Batch AI 서비스|  Azure Machine Learning 서비스|
|:--:|:---:|
|작업 영역|작업 영역|
|프로비전|   `AmlCompute` 형식의 컴퓨팅|
|파일 서버|데이터 저장소|
|실험|실험|
|교육|실행(중첩 실행 허용)|
 
작업을 더 자세히 시각화하는 데 도움이 되는 동일한 표의 또 다른 보기는 다음과 같습니다.
 
### <a name="batch-ai-hierarchy"></a>Batch AI 계층
![흐름 다이어그램](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Azure Machine Learning 서비스 계층 구조
![흐름 다이어그램](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>플랫폼 기능
Azure Machine Learning Service는 Azure 리소스를 관리하지 않고도 AI 개발에 사용할 수 있는 엔드투엔드 학습 -> 배포 스택을 포함하여 다양한 새 기능 세트를 제공합니다. 다음 표에서는 두 서비스 간의 학습에 지원되는 기능을 비교합니다.

|기능|Batch AI 서비스|Azure Machine Learning 서비스|
|-------|:-------:|:-------:|
|VM 크기 선택 |CPU/GPU    |CPU/GPU. 추론을 위해 FPGA도 지원|
|AI 준비 클러스터(드라이버, Docker 등)|  예 |예|
|노드 준비| 예|    아니요|
|OS 제품군 선택   |부분    |아니요|
|전용 VM 및 낮은 우선 순위 VM  |예    |예|
|자동 크기 조정   |예    |예(기본값)|
|자동 크기 조정 대기 시간  |아니요 |예|
|SSH    |예|   예|
|클러스터 수준 탑재 |예(파일 공유, Blob, NFS, 사용자 지정)   |예(데이터 저장소 탑재 또는 다운로드)|
|분산 학습|  예 |예|
|작업 실행 모드|    VM 또는 컨테이너|    컨테이너|
|사용자 지정 컨테이너 이미지|    예 |예|
|모든 도구 키트    |예    |예(Python 스크립트 실행)|
|작업 준비|    예 |아직 준비되지 않음|
|작업 수준 탑재 |예(파일 공유, Blob, NFS, 사용자 지정)   |예(파일 공유 및 Blob)|
|작업 모니터링     |작업 가져오기를 통해|    실행 기록(다양한 정보, 더 많은 메트릭을 푸시하는 사용자 지정 런타임)을 통해|
|작업 로그 및 파일/모델 검색 |   ListFiles 및 Storage API를 통해  |아티팩트 서비스를 통해|
 |Tensorboard 지원   |아니요|    예|
|VM 제품군 수준 할당량 |예    |예(이전 용량 이월)|
 
앞의 표 외에도, Azure Machine Learning Service에는 일반적으로 Batch AI에서 지원되지 않았던 기능이 있습니다.

|기능|Batch AI 서비스|Azure Machine Learning 서비스|
|-------|:-------:|:-------:|
|환경 준비    |아니요 |예(Conda 준비 및 ACR에 업로드)|
|하이퍼 매개 변수 튜닝  |아니요|    예|
|모델 관리   |아니요 |예|
|운영화/배포| 아니요  |ACI 및 AKS를 통해|
|데이터 준비   |아니요 |예|
|컴퓨팅 대상    |Azure VM  |로컬, Batch AI(AmlCompute로), DataBricks, HDInsight|
|자동화된 Machine Learning |아니요|    예|
|파이프라인  |아니요 |예|
|일괄 처리 점수 매기기  |예    |예|
|포털/CLI 지원|    예 |예|


## <a name="programming-interfaces"></a>프로그래밍 인터페이스

다음 표에는 각 서비스에 사용할 수 있는 다양한 프로그래밍 인터페이스가 나와 있습니다.
    
|기능|Batch AI 서비스|Azure Machine Learning 서비스|
|-------|:-------:|:-------:|
|SDK)    |Java, C#, Python, Nodejs   |Python(일반적인 프레임워크에 대한 구성 기반 및 추정기 실행 모두)|
|CLI    |예    |아직 준비되지 않음|
|Azure portal   |예    |예(작업 제출 제외)|
|REST API   |예    |예, 하지만 마이크로 서비스에 걸쳐 분산되어 있음|


Preview Batch AI에서 GA(일반 공급)되는 Azure Machine Learning Service로 업그레이드하면 추정기 및 데이터 저장소와 같이 사용하기 쉬운 개념을 통해 더 나은 환경을 제공합니다. 또한 GA 수준 Azure 서비스 SLA 및 고객 지원도 보장합니다.

Azure Machine Learning Service도 대부분의 대규모 AI 워크로드에 유용한 자동화된 기계 학습, 하이퍼 매개 변수 튜닝 및 ML 파이프라인과 같은 새로운 기능을 도입했습니다. 별도의 서비스로 전환하지 않고도 학습된 모델을 배포할 수 있으므로 데이터 준비(Data Prep SDK 사용)에서 운영화 및 모델 모니터링까지 데이터 과학 루프를 완료하는 데 도움이 됩니다.

<a name="migrate"></a>
## <a name="migrate"></a>마이그레이션

[Azure Machine Learning 서비스로 마이그레이션](how-to-migrate.md) 문서에서 마이그레이션하는 방법과 사용하는 코드가 Azure Machine Learning 서비스의 어떤 코드에 해당하는지 알아봅니다.

## <a name="get-support"></a>지원 받기

Batch AI는 3월 31일에 사용 중지될 예정이며, 지원을 통해 예외를 제기하여 허용 목록에 추가되지 않는 한 서비스에 대한 등록에서 새 구독을 이미 차단하고 있습니다.  질문이 있거나 Azure Machine Learning Service로 마이그레이션할 때 피드백이 있는 경우 [Azure Batch AI 학습 미리 보기](mailto:AzureBatchAITrainingPreview@service.microsoft.com)에 문의하세요.

Azure Machine Learning Service는 일반적으로 공급됩니다. 즉 커밋된 SLA와 다양한 지원 플랜 중에서 선택할 수 있습니다.

Batch AI 서비스 또는 Azure Machine Learning Service를 통해 Azure 인프라를 사용하는 경우에 대한 가격은 두 경우 모두에서 기본 컴퓨팅에 대한 가격만 청구하므로 다르지 않습니다. 자세한 내용은 [가격 계산기](https://azure.microsoft.com/pricing/details/machine-learning-service/)를 참조하세요.

[Azure Portal](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all)에서 두 서비스 간의 지역별 가용성을 확인해 보세요.


## <a name="next-steps"></a>다음 단계

+ [마이그레이션하는 방법](how-to-migrate.md)과 사용하는 코드가 Azure Machine Learning 서비스의 어떤 코드에 해당하는지 알아봅니다.

+ [Azure Machine Learning Service 개요](../machine-learning/service/overview-what-is-azure-ml.md)를 읽어 봅니다.

+ Azure Machine Learning Service를 사용하여[모델 학습을 위한 컴퓨팅 대상을 구성](../machine-learning/service/how-to-set-up-training-targets.md)합니다.

+ [Azure 로드맵](https://azure.microsoft.com/updates/)을 검토하여 다른 Azure 서비스 업데이트에 대해 알아봅니다.
