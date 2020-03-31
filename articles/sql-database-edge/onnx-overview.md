---
title: Azure SQL 데이터베이스 에지 미리 보기에서 ONNX를 사용하여 기계 학습 및 AI | 마이크로 소프트 문서
description: Azure SQL Database Edge 미리 보기의 기계 학습은 개방형 신경망 교환(ONNX) 형식의 모델을 지원합니다. ONNX는 다양한 기계 학습 프레임워크와 도구 간에 모델을 교환하는 데 사용할 수 있는 개방형 형식입니다.
keywords: SQL 데이터베이스 에지 배포
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366273"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>SQL 데이터베이스 에지 프리뷰에서 ONNX를 사용하여 기계 학습 및 AI

Azure SQL Database Edge 미리 보기의 기계 학습은 [개방형 신경망 교환(ONNX)](https://onnx.ai/) 형식의 모델을 지원합니다. ONNX는 다양한 기계 학습 프레임워크와 도구 간의 모델을 교환하는 데 사용할 수 있는 개방형 [형식입니다.](https://onnx.ai/supported-tools)

## <a name="overview"></a>개요

Azure SQL Database Edge에서 기계 학습 모델을 유추하려면 먼저 모델을 얻어야 합니다. 미리 학습된 모델 또는 선택한 프레임워크로 학습된 사용자 지정 모델일 수 있습니다. Azure SQL Database Edge는 ONNX 형식을 지원하며 모델을 이 형식으로 변환해야 합니다. 모델 정확도에 영향을 미치지 않아야 하며 ONNX 모델이 있으면 Azure SQL Database Edge에 모델을 배포하고 [PREDICT T-SQL 함수를 사용하여 네이티브 점수를](/sql/advanced-analytics/sql-native-scoring/)사용할 수 있습니다.

## <a name="get-onnx-models"></a>ONNX 모델 가져오기

ONNX 형식으로 모델을 얻으려면 다음을 수행합니다.

- **모델 구축 서비스**: Azure 기계 학습 및 [Azure 사용자 지정 비전 서비스에서](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) [자동화된 기계 학습 기능과](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) 같은 서비스는 ONNX 형식으로 학습된 모델을 직접 내보냅니다.

- [**기존 모델 변환 및/또는 내보내기**](https://github.com/onnx/tutorials#converting-to-onnx-format): 여러 교육 프레임워크(예: [PyTorch,](https://pytorch.org/docs/stable/onnx.html)Chainer 및 Caffe2)는 기본 내보내기 기능을 ONNX로 지원하므로 학습된 모델을 특정 버전의 ONNX 형식으로 저장할 수 있습니다. 네이티브 내보내기를 지원하지 않는 프레임워크의 경우 서로 다른 기계 학습 프레임워크에서 학습된 모델을 ONNX 형식으로 변환할 수 있는 독립실행형 ONNX 변환기 설치 가능한 패키지가 있습니다.

     **지원되는 프레임워크**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [텐서 플로우](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [시킷 학습](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    지원되는 프레임워크 및 예제의 전체 목록은 [ONNX 형식으로 변환을](https://github.com/onnx/tutorials#converting-to-onnx-format)참조하십시오.

## <a name="limitations"></a>제한 사항

현재 모든 ONNX 모델이 Azure SQL Database Edge에서 지원되는 것은 아닙니다. 지원은 숫자 데이터 **형식이**있는 모델로 제한됩니다.

- [int 와 bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [실제 및 플로트](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
다른 숫자 형식은 [CAST 및 CONVERT를](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)사용하여 지원되는 유형으로 변환할 수 있습니다.

모델에 대한 각 입력이 테이블의 단일 열에 해당하도록 모델 입력을 구조화해야 합니다. 예를 들어 팬더 데이터 프레임을 사용하여 모델을 학습하는 경우 각 입력은 모델에 별도의 열이어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 포털을 통해 SQL 데이터베이스 에지 배포](deploy-portal.md)
- [Azure SQL 데이터베이스 에지 미리 보기에 ONNX 모델 배포](deploy-onnx.md)
