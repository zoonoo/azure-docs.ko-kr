---
title: Azure SQL Database Edge Preview에서 ONNX를 사용 하는 기계 학습 및 AI | Microsoft Docs
description: Azure SQL Database Edge 미리 보기의 Machine learning은 ONNX (오픈 신경망 교환) 형식의 모델을 지원 합니다. ONNX는 다양 한 기계 학습 프레임 워크 및 도구 간에 모델을 교환 하는 데 사용할 수 있는 개방형 형식입니다.
keywords: sql database edge 배포
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: c4c87f7f6f8735c9a50c61b0e083c77b915e0d98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514018"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>SQL Database Edge 미리 보기에서 ONNX를 사용 하는 기계 학습 및 AI

Azure SQL Database Edge 미리 보기의 Machine learning은 [ONNX (오픈 신경망 교환)](https://onnx.ai/) 형식의 모델을 지원 합니다. ONNX는 다양 한 [기계 학습 프레임 워크 및 도구](https://onnx.ai/supported-tools)간에 모델을 교환 하는 데 사용할 수 있는 개방형 형식입니다.

## <a name="supported-tool-kits"></a>지원 되는 도구 키트

ONNXMLTools를 사용 하면 다양 한 기계 학습 도구 키트에서 ONNX 모델로 모델을 변환할 수 있습니다. 현재 숫자 데이터 형식 및 단일 열 입력에 대해 지원 되는 도구 키트는 다음과 같습니다.

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (실험적)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>ONNX 모델 가져오기

다음과 같은 여러 가지 방법으로 모델을 ONNX 형식으로 가져올 수 있습니다.

- [Onnx 모델 동물원](https://github.com/onnx/models): 다양 한 유형의 작업에 대해 미리 학습 된 여러 onnx 모델을 포함 합니다. Windows Machine Learning에서 지 원하는 버전을 다운로드 하 여 사용할 수 있습니다.

- [기계 학습 학습 프레임 워크에서 네이티브 내보내기](https://onnx.ai/supported-tools): 여러 학습 프레임 워크는 기본 내보내기 기능을 onnx로 지원 하므로,이를 통해 학습 된 모델을 특정 버전의 onnx 형식으로 저장할 수 있습니다. 예: 체 이너, Caffee2 및 PyTorch. 또한 [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning-service/) 및 [Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) 와 같은 서비스는 기본 onnx 내보내기를 제공 합니다.

  - Custom Vision를 사용 하 여 클라우드에서 ONNX 모델을 학습 하 고 내보내는 방법을 알아보려면 [자습서: WINDOWS ML에서 Custom Vision에서 ONNX 모델 사용 (미리 보기)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml)을 참조 하세요.

- [WinMLTools를 사용 하 여 기존 모델 변환](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools):이 Python 패키지를 사용 하면 여러 학습 프레임 워크 형식에서 onnx로 모델을 변환할 수 있습니다. 응용 프로그램이 대상으로 하는 Windows 빌드에 따라 모델을 변환 하려는 ONNX의 버전을 지정할 수 있습니다. Python에 익숙하지 않은 경우 [Windows MACHINE LEARNING UI 기반 대시보드](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) 를 사용 하 여 모델을 변환할 수 있습니다.

> [!IMPORTANT]
> 모든 ONNX 버전이 Azure SQL database Edge에서 지원 되는 것은 아닙니다. 현재 ONNX 모델을 통한 숫자 데이터 유형만 예측 하는 것은 지원 되지 않습니다.

ONNX 모델을 만든 후에는 Azure SQL Database Edge에 모델을 배포할 수 있습니다. 그런 다음 [예측 t-sql 함수에서 기본 점수 매기기를](/sql/advanced-analytics/sql-native-scoring/)사용할 수 있습니다.

## <a name="limitations"></a>제한 사항

현재이 지원은 **숫자 데이터 형식의**모델로 제한 됩니다.

- [int 및 bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real 및 float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)입니다.
  
CAST 및 CONVERT [캐스트 및 convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)를 사용 하 여 다른 숫자 유형을 지원 되는 유형으로 변환할 수 있습니다.

모델에 대 한 각 입력이 테이블의 단일 열에 해당 하는지 모델 입력을 구성 해야 합니다. 예를 들어 pandas 데이터 프레임를 사용 하 여 모델을 학습 하는 경우 각 입력은 모델에 대 한 별도의 열 이어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal를 통해 SQL Database에 지 배포](deploy-portal.md)
- [Azure SQL Database Edge 미리 보기에 ONNX 모델 배포](deploy-onnx.md)