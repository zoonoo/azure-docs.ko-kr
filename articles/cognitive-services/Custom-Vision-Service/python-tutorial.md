---
title: Custom Vision Service Python 자습서 만들기 - Azure Cognitive Services | Microsoft Docs
description: Microsoft Cognitive Services의 Custom Vision API를 사용하는 기본 Python 앱을 탐색합니다. 기본 끝점을 사용하여 프로젝트를 만들고, 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 예측을 수행합니다.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/07/2018
ms.author: areddish
ms.openlocfilehash: 0359935bf266d4f2a5cf845dd0d23183f4f77b72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375278"
---
# <a name="custom-vision-api-python-tutorial"></a>Custom Vision API Python 자습서

Custom Vision Service 및 기본 Python 스크립트를 사용하여 이미지 분류 프로젝트를 만드는 방법을 알아봅니다. 프로젝트를 만든 후에는 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 기본 예측 엔드포인트 URL를 가져오고, 해당 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. 이 오픈 소스 예제를 Custom Vision API를 사용하여 사용자 고유의 앱을 빌드하기 위한 템플릿으로 사용합니다.



## <a name="prerequisites"></a>필수 조건

- Python 2.7+ 또는 Python 3.5+입니다.
- Pip 도구입니다.

## <a name="get-the-training-and-prediction-keys"></a>학습 및 예측 키 가져오기

이 예제에서 사용된 키를 가져오려면 [Custom Vision 웹 페이지](https://customvision.ai)로 가서 오른쪽 위에 있는 __기어 아이콘__을 선택합니다. __계정__ 섹션에서 __학습 키__ 및 __예측 키__ 필드의 값을 복사합니다.

![키 UI의 이미지](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Custom Vision Service SDK 설치

Custom Vision Service SDK를 설치하려면 다음 명령을 사용합니다.

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>예제 이미지 가져오기

이 예제에서는 [https://github.com/Microsoft/Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) 프로젝트의 `Samples/Images` 디렉터리에서 이미지를 사용합니다. 개발 환경에 프로젝트를 복제하거나 다운로드 및 추출합니다.

## <a name="create-a-custom-vision-service-project"></a>Custom Vision Service 프로젝트 만들기

새 Custom Vision Service 프로젝트를 만들려면 `sample.py`라는 새 파일을 만듭니다. 파일 내용으로 다음 코드를 사용합니다.

> [!IMPORTANT]
> 앞서 검색한 학습 키 값에 `training_key`를 설정합니다.
>
> 앞서 검색한 예측 키 값에 `prediction_key`를 설정합니다.

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>프로젝트에 태그 추가

프로젝트에 태그를 추가하려면 `sample.py` 파일의 끝에 다음 코드를 추가합니다.

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>프로젝트에 이미지 업로드

프로젝트에 샘플 이미지를 추가하려면 태그를 만든 후 다음 코드를 삽입합니다. 이 코드는 해당 태그를 사용하여 이미지를 업로드합니다.

> [!IMPORTANT]
>
> 전에 Cognitive-CustomVision-Windows 프로젝트를 다운로드한 위치에 따라 이미지 경로를 변경합니다.

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>프로젝트 학습

분류자를 학습하려면 `sample.py` 파일의 끝에 다음 코드를 추가합니다.

```python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>기본 예측 엔드포인트 가져오기 및 사용

예측 엔드포인트에 이미지를 보내고 예측을 검색하려면 `sample.py` 파일의 끝에 다음 코드를 추가합니다.

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>예제 실행

솔루션을 실행합니다. 예측 결과가 콘솔에 나타납니다.

```
python sample.py
```

응용 프로그램의 출력은 다음 텍스트와 비슷합니다.

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```