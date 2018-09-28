---
title: Azure Machine Learning Package for Computer Vision을 사용하여 개체 감지 모델을 빌드하고 배포합니다.
description: Azure Machine Learning Package for Computer Vision을 사용하여 컴퓨터 비전 개체 감지 모델을 빌드하고, 학습하고, 테스트하고, 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 166adc2b464ede5ba77d049075479e3b37a02a88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954861"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Azure Machine Learning으로 개체 감지 모델 빌드 및 배포

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


이 아티클에서는 **Azure Machine Learning Package for Computer Vision**을 사용하여 [빠른 R-CNN](https://arxiv.org/abs/1506.01497) 개체 감지 모델을 학습하고, 테스트하고, 배포하는 방법을 알아봅니다. 

컴퓨터 비전 도메인의 많은 문제는 개체 감지를 사용하여 해결할 수 있습니다. 이러한 문제에는 이미지에서 여러 개체를 찾는 모델을 빌드하는 작업이 포함됩니다. 

이 패키지를 사용하여 이 모델을 빌드하고 배포하는 경우 다음 단계를 수행합니다.
1.  데이터 집합 만들기
2.  DNN(심층 신경망) 모델 정의
3.  모델 학습
4.  평가 및 시각화
5.  웹 서비스 배포
6.  웹 서비스 부하 테스트

이 예제에서 TensorFlow는 딥 러닝 프레임워크로 사용되며 학습은 [딥 러닝 데이터 과학 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)과 같은 GPU 기반 컴퓨터에서 로컬로 수행되고 배포는 Azure ML Operationalization CLI를 사용합니다.

각 모듈 및 클래스에 대한 자세한 내용은 [패키지 참조 설명서](https://aka.ms/aml-packages/vision)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

1. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

1. 다음 계정 및 응용 프로그램을 설정하고 설치해야 합니다.
   - Azure Machine Learning 실험 계정 
   - Azure Machine Learning 모델 관리 계정
   - Azure Machine Learning Workbench 설치

   이 세 가지를 아직 만들거나 설치하지 않은 경우 [Azure Machine Learning 빠른 시작 및 Workbench 설치](../desktop-workbench/quickstart-installation.md) 문서를 참조하세요. 

1. Azure Machine Learning Package for Computer Vision을 설치해야 합니다. [패키지를 설치하는 방법은 여기](https://aka.ms/aml-packages/vision)를 참조하세요.

## <a name="sample-data-and-notebook"></a>샘플 데이터 및 노트북

### <a name="get-the-jupyter-notebook"></a>Jupyter Notebook 가져오기

여기에 설명된 샘플을 직접 실행하려면 노트북을 다운로드하세요.

> [!div class="nextstepaction"]
> [Jupyter Notebook 가져오기](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>샘플 데이터 로드

이 데모에서 냉장고 내 식품 항목의 데이터 집합은 30개의 이미지 및 8개의 클래스(계란판, 요구르트, 케찹, 버섯, 머스터드, 오렌지, 호박 및 물)로 구성되어 제공됩니다. 각 jpg 이미지에는 비슷한 이름의 주석 xml 파일이 있습니다. 

다음 그림에서는 권장되는 폴더 구조를 보여줍니다. 

![폴더 구조](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>이미지 주석

개체 탐지기를 학습하고 평가하는 데 주석이 지정된 개체 위치가 필요합니다. [LabelImg](https://tzutalin.github.io/labelImg)는 이미지를 주석으로 지정하는 데 사용할 수 있는 오픈 소스 주석 도구입니다. LabelImg는 Pascal-VOC 형식의 이미지별 xml 파일이며 이 패키지에서 읽을 수 있습니다. 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>데이터 집합 만들기

해당하는 경계 상자 주석을 사용하여 이미지 집합으로 구성된 CVTK 데이터 집합을 만듭니다. 이 예제에서는 "../sample_data/foods/training" 폴더에서 제공되는 냉장고 이미지를 사용합니다. JPEG 이미지만 사용할 수 있습니다.


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![png](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>모델 정의

이 예제에서는 빠른 R-CNN 모델을 사용합니다. 이 모델을 정의할 때 다양한 매개 변수가 제공될 수 있습니다. 즉, 학습에 사용되는 매개 변수(다음 섹션 참조)뿐만 아니라 이러한 매개 변수는 CVTK API 문서 또는 [Tensorflow 개체 감지 웹 사이트](https://github.com/tensorflow/models/tree/master/research/object_detection)에서 찾을 수 있습니다. 빠른 R-CNN 모델에 대한 자세한 정보는 [이 링크](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details)에서 찾을 수 있습니다. 이 모델은 빠른 R-CNN에 기반하며 이에 대한 자세한 정보는 [여기](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details)에서 찾을 수 있습니다.


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>모델 학습

ResNet50을 사용하는 COCO 학습 빠른 R-CNN 모델은 학습을 위한 시작점으로 사용됩니다. 

탐지기를 학습하려면 코드에서 학습 단계 수를 350으로 설정하여 학습이 더 신속하게(GPU에서 5분 이하) 실행됩니다. 실제 학습 집합에 있는 이미지 수의 10배 이상으로 설정합니다.

이 예제에서는 빠른 학습을 위해 탐지기 학습 단계 수를 350으로 설정합니다. 그러나 thumb의 규칙은 단계를 학습 집합에 있는 이미지 수의 10배 이상으로 설정하는 것입니다.

학습의 두 가지 주요 매개 변수는 다음과 같습니다.
- 모델을 학습하는 단계 수이며 num_seps 인수에 의해 표시됩니다. 각 단계는 하나의 일괄 처리 크기의 미니배치를 사용하여 모델을 학습합니다.
- 학습률은 initial_learning_rate에서 설정할 수 있습니다.

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

TensorBoard는 학습 진행률을 시각화하는 데 사용할 수 있습니다. TensorBoard 이벤트는 모델 개체의 train_dir 특성에서 지정한 폴더에 있습니다. TensorBoard를 보려면 다음을 수행합니다.
1. 명령줄에 'tensorboard-logdir'로 시작하는 인쇄물을 복사하고 실행합니다. 
2. TensorBoard를 보려면 명령줄에서 웹 브라우저로 반환된 URL을 복사합니다. 

TensorBoard는 다음 스크린샷과 유사해야 합니다. 학습 폴더를 채우는 데 몇 분이 걸립니다. 따라서 TensorBoard가 올바르게 표시되지 않으면 먼저 1-2단계를 시도합니다.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>모델 평가

'평가' 메서드는 모델을 평가하는 데 사용됩니다. 이 함수는 ObjectDetectionDataset 개체를 입력으로 사용해야 합니다. 학습 데이터 집합에 사용되는 것과 동일한 함수를 사용하여 평가 데이터 집합을 만들 수 있습니다. 지원되는 메트릭은 [PASCAL VOC 챌린지](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf)에 정의된 대로 평균 정밀도입니다.  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

깔끔한 형식으로 평가 결과를 인쇄할 수 있습니다.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

마찬가지로 학습 집합에 대한 모델의 정확도를 계산할 수 있습니다. 이렇게 하면 학습을 적합한 솔루션으로 수렴할 수 있습니다. 성공적인 학습 이후 학습 집합의 정확도는 100%에 가깝습니다.

TensorBoard에서 예측된 경계 상자를 사용하는 맵 값 및 이미지를 비롯한 평가 결과를 볼 수도 있습니다. 다음 코드에서 인쇄물을 명령줄 창에 복사하여 TensorBoard 클라이언트를 시작합니다. 여기서 포트 값 8008은 학습 상태 보기에 사용되는 기본값인 6006과 충돌을 피하기 위해 사용됩니다.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>이미지 점수 매기기

학습된 모델의 성능에 만족했다면 모델 개체의 '점수 매기기' 함수는 새 이미지의 점수를 매기는 데 사용할 수 있습니다. '시각화' 함수를 사용하여 반환된 점수를 시각화할 수 있습니다. 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![png](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>모델 저장

다음 코드 예제에 표시된 대로 학습된 모델을 디스크에 저장하고, 메모리에 다시 로드할 수 있습니다.


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>점수를 매기기 위해 저장된 모델 로드

저장된 모델을 사용하려면 '로드' 함수를 사용하여 메모리에 모델을 로드합니다. 한 번만 로드해야 합니다. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

모델을 로드한 후에 이미지 또는 이미지 목록의 점수를 매기는 데 사용할 수 있습니다. 단일 이미지의 경우 'detection_boxes', 'detection_scores' 및 'num_detections'와 같은 키를 사용하여 사전이 반환됩니다. 입력이 이미지 목록인 경우 하나의 이미지에 해당하는 하나의 사전을 사용하여 사전의 목록이 반환됩니다. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

레이블, 점수 및 좌표를 포함하여 점수가 0.5를 초과하는 검색된 개체는 인쇄될 수 있습니다.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

이전과 동일하게 점수를 시각화합니다.


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![png](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

## <a name="operationalization-deploy-and-consume"></a>운영화: 배포 및 소비

운영화는 모델 및 코드를 웹 서비스로 게시하고 이러한 서비스를 소비하여 비즈니스 결과를 산출하는 프로세스입니다. 

모델이 학습된 후에는 [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning)를 사용하여 모델을 웹 서비스로 배포하여 소비할 수 있습니다. 모델은 로컬 시스템이나 ACS(Azure Container Service) 클러스터에 배포 할 수 있습니다. ACS를 사용하면 웹 서비스를 수동으로 확장하거나 자동 확장 기능을 사용할 수 있습니다.

**Azure CLI로 로그인**

유효한 구독이 있는 [Azure](https://azure.microsoft.com/) 계정으로 다음 CLI 명령을 사용하여 로그인합니다.
<br>`az login`

+ 다른 Azure 구독으로 전환하려면 아래 명령을 사용합니다.
<br>`az account set --subscription [your subscription name]`

+ 현재 모델 관리 계정을 보려면 아래 명령을 사용합니다.
  <br>`az ml account modelmanagement show`

**클러스터 배포 환경 만들기 및 설정**

배포 환경은 한 번만 설정하면 됩니다. 아직 환경이 없으면 [해당 지침](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup)을 사용하여 배포 환경을 설정합니다. 

활성 배포 환경을 보려면 다음 CLI 명령을 사용합니다.
<br>`az ml env show`
   
배포 환경을 만들고 설정하는 샘플 Azure CLI 명령

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>웹 서비스 및 배포 관리

다음 API를 사용하여 모델을 웹 서비스로 배포하고 웹 서비스를 관리하며 배포를 관리할 수 있습니다.

|Task|API|
|----|----|
|배포 개체 만들기|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|웹 서비스 배포|`deploy_obj.deploy()`|
|이미지 채점|`deploy_obj.score_image(local_image_path_or_image_url)`|
|웹 서비스 삭제|`deploy_obj.delete()`|
|웹 서비스 없이 Docker 이미지 빌드|`deploy_obj.build_docker_image()`|
|기존 배포 나열|`AMLDeployment.list_deployment()`|
|배포 이름이 있는 서비스가 있으면 삭제|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**API 설명서:** 각 모듈 및 클래스에 대한 자세한 내용은 [패키지 참조 설명서](https://aka.ms/aml-packages/vision)를 참조하세요.

**CLI 참조:** 배포와 관련된 복잡한 작업은 [모델 관리 CLI 참조](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference)를 참조하세요.

**Azure Portal에서 배포 관리**: [Azure Portal](https://ms.portal.azure.com/)에서 배포를 추적 및 관리할 수 있습니다. Azure Portal에서 이름을 사용하여 Machine Learning 모델 관리 계정 페이지를 찾습니다. 그런 다음 모델 관리 계정 페이지 > 모델 관리 > 서비스로 이동합니다.

```python
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>웹 서비스 사용

웹 서비스를 만들면 배포된 웹 서비스를 사용하여 이미지의 점수를 매길 수 있습니다. 여러 옵션이 있습니다.

   - 다음을 사용하여 배포 개체에서 웹 서비스의 점수를 직접 매길 수 있습니다. deploy_obj.score_image(image_path_or_url) 
   - 또는 다음과 함께 서비스 엔드포인트 URL 및 서비스 키(로컬 배포에서 없음)를 사용할 수 있습니다. AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
   - HTTP 요청을 직접 형성하여 웹 서비스 엔드포인트를 채점합니다(고급 사용자의 경우).

### <a name="score-with-existing-deployment-object"></a>기존 배포 개체로 채점
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>서비스 엔드포인트 URL 및 서비스 키로 채점
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>HTTP 요청으로 엔드포인트 직접 채점
HTTP 요청을 직접 Python으로 형성하는 일부 예제 코드는 다음과 같습니다. 다른 프로그래밍 언어로도 이 작업을 수행할 수 있습니다.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>웹 서비스에서 직렬화된 결과 구문 분석
웹 서비스의 결과는 구문 분석할 수 있는 json 문자열에 있습니다.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>다음 단계

아래 문서에서 Azure Machine Learning Package for Computer Vision에 대해 알아봅니다.

+ [패키지 개요](https://aka.ms/aml-packages/vision)를 확인합니다.

+ 이 패키지에 대한 [참조 설명서](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision)를 탐색합니다.

+ [Azure Machine Learning용 다른 Python 패키지](reference-python-package-overview.md)를 자세히 알아봅니다.
