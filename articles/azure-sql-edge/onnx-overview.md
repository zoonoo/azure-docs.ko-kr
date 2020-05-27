---
title: Azure SQL Edge(미리 보기)에서 ONNX를 통한 기계 학습 및 AI
description: Azure SQL Edge(미리 보기)의 기계 학습은 ONNX(Open Neural Network Exchange) 형식의 모델을 지원합니다. ONNX는 다양한 기계 학습 프레임워크 및 도구 간에 모델을 교환하는 데 사용할 수 있는 개방형 형식입니다.
keywords: SQL Edge 배포
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 12c1294a804c2063d405c5ec08440865283d51d3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594592"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge-preview"></a>SQL Edge(미리 보기)에서 ONNX를 통한 기계 학습 및 AI

Azure SQL Edge(미리 보기)의 기계 학습은 [ONNX(Open Neural Network Exchange)](https://onnx.ai/) 형식의 모델을 지원합니다. ONNX는 다양한 [기계 학습 프레임워크 및 도구](https://onnx.ai/supported-tools) 간에 모델을 교환하는 데 사용할 수 있는 개방형 형식입니다.

## <a name="overview"></a>개요

Azure SQL Edge에서 기계 학습 모델을 유추하려면 먼저 모델을 가져와야 합니다. 미리 학습된 모델 또는 선택한 프레임워크로 학습된 사용자 지정 모델일 수 있습니다. Azure SQL Edge는 ONNX 형식을 지원하며 모델을 이 형식으로 변환해야 합니다. 모델 정확도에는 영향을 주지 않으며, ONNX 모델을 사용하는 경우 Azure SQL Edge에 모델을 배포하고 [PREDICT T-SQL 함수를 이용하는 기본 점수](/sql/advanced-analytics/sql-native-scoring/)를 사용할 수 있습니다.

## <a name="get-onnx-models"></a>ONNX 모델 가져오기

ONNX 형식으로 모델을 가져오려면 다음을 수행합니다.

- **모델 빌드 서비스**: [Azure Machine Learning의 자동화된 Machine Learning 기능](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) 및 [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)와 같은 서비스는 학습된 모델을 ONNX 형식으로 직접 내보내는 것을 지원합니다.

- [**기존 모델 변환 및/또는 내보내기**](https://github.com/onnx/tutorials#converting-to-onnx-format): 여러 학습 프레임워크(예: [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer 및 Caffe2)는 ONNX 형식으로의 기본 내보내기 기능을 지원하여 학습된 모델을 특정 버전의 ONNX 형식으로 저장할 수 있습니다. 네이티브 내보내기를 지원하지 않는 프레임워크의 경우 다양한 기계 학습 프레임워크에서 학습된 모델을 ONNX 형식으로 변환하는 데 사용할 수 있는 독립 실행형 ONNX 변환기 설치 가능 패키지가 있습니다.

     **지원되는 프레임워크**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    지원되는 프레임워크 및 예제에 대한 전체 목록은 [ONNX 형식으로 변환](https://github.com/onnx/tutorials#converting-to-onnx-format)을 참조하세요.

## <a name="limitations"></a>제한 사항

현재 Azure SQL Edge에서 모든 ONNX 모델이 지원되는 것은 아닙니다. 다음과 같이 **숫자 데이터 형식**인 모델에 대한 지원은 제한됩니다.

- [int 및 bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real 및 float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)
  
다른 숫자 유형은 [CAST 및 CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)를 사용하여 지원되는 유형으로 변환할 수 있습니다.

모델에 대한 각 입력이 테이블의 단일 열에 해당하도록 모델 입력을 구성해야 합니다. 예를 들어 pandas 데이터 프레임를 사용하여 모델을 학습하는 경우 각 입력은 모델에 대한 별도의 열이어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 SQL Edge 배포](deploy-portal.md)
- [Azure SQL Edge(미리 보기)에 ONNX 모델 배포](deploy-onnx.md)
