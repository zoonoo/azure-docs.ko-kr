---
title: Azure SQL Database Edge Preview에서 ONNX를 사용 하는 기계 학습 및 AI | Microsoft Docs
description: Azure SQL Database Edge 미리 보기의 Machine learning은 ONNX (오픈 신경망 교환) 형식의 모델을 지원 합니다. ONNX는 다양 한 기계 학습 프레임 워크 및 도구 간에 모델을 교환 하는 데 사용할 수 있는 개방형 형식입니다.
keywords: sql database edge 배포
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366273"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>SQL Database Edge 미리 보기에서 ONNX를 사용 하는 기계 학습 및 AI

Azure SQL Database Edge 미리 보기의 Machine learning은 [ONNX (오픈 신경망 교환)](https://onnx.ai/) 형식의 모델을 지원 합니다. ONNX는 다양 한 [기계 학습 프레임 워크 및 도구](https://onnx.ai/supported-tools)간에 모델을 교환 하는 데 사용할 수 있는 개방형 형식입니다.

## <a name="overview"></a>개요

Azure SQL Database Edge에서 기계 학습 모델을 유추 하려면 먼저 모델을 가져와야 합니다. 미리 학습 된 모델 또는 선택한 프레임 워크로 학습 된 사용자 지정 모델 일 수 있습니다. Azure SQL Database Edge는 ONNX 형식을 지원 하며 모델을이 형식으로 변환 해야 합니다. 모델 정확도에는 영향을 주지 않아야 합니다. ONNX 모델을 사용 하는 경우에는 Azure SQL Database Edge에 모델을 배포 하 고 [PREDICT t-sql 함수에서 네이티브 점수 매기기](/sql/advanced-analytics/sql-native-scoring/)를 사용할 수 있습니다.

## <a name="get-onnx-models"></a>ONNX 모델 가져오기

ONNX 형식으로 모델을 가져오려면 다음을 수행 합니다.

- **모델 빌드 서비스**: Azure Machine Learning 및 [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) [의 자동화 된 Machine Learning 기능과](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) 같은 서비스는 직접 학습 된 모델을 onnx 형식으로 직접 내보내는 기능을 지원 합니다.

- [**기존 모델 변환 및/또는 내보내기**](https://github.com/onnx/tutorials#converting-to-onnx-format): 여러 학습 프레임 워크 (예: [PyTorch](https://pytorch.org/docs/stable/onnx.html), 체 이너 및 CAFFE2)는 onnx로의 기본 내보내기 기능을 지원 하 여, 학습 된 모델을 특정 버전의 onnx 형식으로 저장할 수 있습니다. 네이티브 내보내기를 지원 하지 않는 프레임 워크의 경우 다양 한 기계 학습 프레임 워크에서 학습 된 모델을 ONNX 형식으로 변환 하는 데 사용할 수 있는 독립 실행형 ONNX 변환기 설치 가능 패키지가 있습니다.

     **지원되는 프레임워크**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    지원 되는 프레임 워크 및 예제에 대 한 전체 목록은 [ONNX 형식으로 변환](https://github.com/onnx/tutorials#converting-to-onnx-format)을 참조 하세요.

## <a name="limitations"></a>제한 사항

현재 일부 ONNX 모델은 Azure SQL Database Edge에서 지원 되지 않습니다. 지원에는 **숫자 데이터 형식의**모델만 사용할 수 있습니다.

- [int 및 bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real 및 float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)입니다.
  
[CAST 및 CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)를 사용 하 여 다른 숫자 유형을 지원 되는 유형으로 변환할 수 있습니다.

모델에 대 한 각 입력이 테이블의 단일 열에 해당 하는지 모델 입력을 구성 해야 합니다. 예를 들어 pandas 데이터 프레임를 사용 하 여 모델을 학습 하는 경우 각 입력은 모델에 대 한 별도의 열 이어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal를 통해 SQL Database에 지 배포](deploy-portal.md)
- [Azure SQL Database Edge 미리 보기에 ONNX 모델 배포](deploy-onnx.md)
