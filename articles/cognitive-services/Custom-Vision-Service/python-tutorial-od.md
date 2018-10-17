---
title: '자습서: 개체 검색 프로젝트 만들기 - Custom Vision API, Python'
titlesuffix: Azure Cognitive Services
description: 기본 엔드포인트를 사용하여 프로젝트를 만들고, 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 예측을 수행합니다.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: areddish
ms.openlocfilehash: f49f5ab32d834b32de54be2d96c3671ad46f79f3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363703"
---
# <a name="tutorial-build-an-object-detection-project-with-python"></a>자습서: Python으로 개체 검색 프로젝트 빌드

Computer Vision API를 사용하여 개체 검색 프로젝트를 만드는 기본 Python 스크립트를 알아봅니다. 프로젝트를 만든 후에는 태그가 지정된 지역을 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 기본 예측 엔드포인트 URL를 획득하고, 해당 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. 이 오픈 소스 예제를 Custom Vision API를 사용하여 사용자 고유의 앱을 빌드하기 위한 템플릿으로 사용합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 사용하려면 다음을 수행해야 합니다.

- Python 2.7+ 또는 Python 3.5+ 중 하나를 설치합니다.
- pip를 설치합니다.

### <a name="platform-requirements"></a>플랫폼 요구 사항
이 예제는 Python용으로 개발되었습니다.

### <a name="get-the-custom-vision-sdk"></a>Custom Vision SDK 다운로드

이 예제를 빌드하려면 Custom Vision API용 Python SDK를 설치해야 합니다.

```
pip install azure-cognitiveservices-vision-customvision
```

[Python 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)과 함께 이미지를 다운로드할 수 있습니다.

## <a name="step-1-get-the-training-and-prediction-keys"></a>1단계: 학습 및 예측 키 가져오기

이 예제에서 사용된 키를 가져오려면 [Custom Vision 사이트](https://customvision.ai)로 가서 오른쪽 위에 있는 __톱니 바퀴 아이콘__을 선택합니다. __계정__ 섹션에서 __학습 키__ 및 __예측 키__ 필드의 값을 복사합니다.

![키 UI의 이미지](./media/python-tutorial/training-prediction-keys.png)

이 예제에서는 [이 위치](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images)의 이미지를 사용합니다.

## <a name="step-2-create-a-custom-vision-service-project"></a>2단계: Custom Vision Service 프로젝트 만들기

새 Custom Vision Service 프로젝트를 만들려면 sample.py 스크립트 파일을 만들고 다음 내용을 추가합니다. 개체 검색 및 이미지 분류 프로젝트를 만드는 작업 간의 차이점은 create_project 호출에 지정되는 도메인입니다.

```Python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry, Region

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Find the object detection domain
obj_detection_domain = next(domain for domain in trainer.get_domains() if domain.type == "ObjectDetection")

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Detection Project", domain_id=obj_detection_domain.id)
```

## <a name="step-3-add-tags-to-your-project"></a>3단계: 프로젝트에 태그 추가

프로젝트에 태그를 추가하려면 다음 코드를 삽입하여 2개의 태그를 만듭니다.

```Python
# Make two tags in the new project
fork_tag = trainer.create_tag(project.id, "fork")
scissors_tag = trainer.create_tag(project.id, "scissors")
```

## <a name="step-4-upload-images-to-the-project"></a>4단계: 프로젝트에 이미지 업로드

개체 검색 프로젝트의 경우 이미지, 지역 및 태그를 업로드해야 합니다. 지역은 정규화된 좌표에 있으며 태그가 지정된 개체의 위치를 지정합니다.

프로젝트에 이미지, 지역 및 태그를 추가하려면 태그를 만든 후 다음 코드를 삽입합니다. 이 자습서에서 지역은 코드의 인라인에 하드 코드됩니다. 지역은 정규화된 좌표에 경계 상자를 지정합니다.

```Python

fork_image_regions = {
    "fork_1": [ 0.145833328, 0.3509314, 0.5894608, 0.238562092 ],
    "fork_2": [ 0.294117659, 0.216944471, 0.534313738, 0.5980392 ],
    "fork_3": [ 0.09191177, 0.0682516545, 0.757352948, 0.6143791 ],
    "fork_4": [ 0.254901975, 0.185898721, 0.5232843, 0.594771266 ],
    "fork_5": [ 0.2365196, 0.128709182, 0.5845588, 0.71405226 ],
    "fork_6": [ 0.115196079, 0.133611143, 0.676470637, 0.6993464 ],
    "fork_7": [ 0.164215669, 0.31008172, 0.767156839, 0.410130739 ],
    "fork_8": [ 0.118872553, 0.318251669, 0.817401946, 0.225490168 ],
    "fork_9": [ 0.18259804, 0.2136765, 0.6335784, 0.643790841 ],
    "fork_10": [ 0.05269608, 0.282303959, 0.8088235, 0.452614367 ],
    "fork_11": [ 0.05759804, 0.0894935, 0.9007353, 0.3251634 ],
    "fork_12": [ 0.3345588, 0.07315363, 0.375, 0.9150327 ],
    "fork_13": [ 0.269607842, 0.194068655, 0.4093137, 0.6732026 ],
    "fork_14": [ 0.143382356, 0.218578458, 0.7977941, 0.295751631 ],
    "fork_15": [ 0.19240196, 0.0633497, 0.5710784, 0.8398692 ],
    "fork_16": [ 0.140931368, 0.480016381, 0.6838235, 0.240196079 ],
    "fork_17": [ 0.305147052, 0.2512582, 0.4791667, 0.5408496 ],
    "fork_18": [ 0.234068632, 0.445702642, 0.6127451, 0.344771236 ],
    "fork_19": [ 0.219362751, 0.141781077, 0.5919118, 0.6683006 ],
    "fork_20": [ 0.180147052, 0.239820287, 0.6887255, 0.235294119 ]
}

scissors_image_regions = {
    "scissors_1": [ 0.4007353, 0.194068655, 0.259803921, 0.6617647 ],
    "scissors_2": [ 0.426470578, 0.185898721, 0.172794119, 0.5539216 ],
    "scissors_3": [ 0.289215684, 0.259428144, 0.403186262, 0.421568632 ],
    "scissors_4": [ 0.343137264, 0.105833367, 0.332107842, 0.8055556 ],
    "scissors_5": [ 0.3125, 0.09766343, 0.435049027, 0.71405226 ],
    "scissors_6": [ 0.379901975, 0.24308826, 0.32107842, 0.5718954 ],
    "scissors_7": [ 0.341911763, 0.20714055, 0.3137255, 0.6356209 ],
    "scissors_8": [ 0.231617644, 0.08459154, 0.504901946, 0.8480392 ],
    "scissors_9": [ 0.170343131, 0.332957536, 0.767156839, 0.403594762 ],
    "scissors_10": [ 0.204656869, 0.120539248, 0.5245098, 0.743464053 ],
    "scissors_11": [ 0.05514706, 0.159754932, 0.799019635, 0.730392158 ],
    "scissors_12": [ 0.265931368, 0.169558853, 0.5061275, 0.606209159 ],
    "scissors_13": [ 0.241421565, 0.184264734, 0.448529422, 0.6830065 ],
    "scissors_14": [ 0.05759804, 0.05027781, 0.75, 0.882352948 ],
    "scissors_15": [ 0.191176474, 0.169558853, 0.6936275, 0.6748366 ],
    "scissors_16": [ 0.1004902, 0.279036, 0.6911765, 0.477124184 ],
    "scissors_17": [ 0.2720588, 0.131977156, 0.4987745, 0.6911765 ],
    "scissors_18": [ 0.180147052, 0.112369314, 0.6262255, 0.6666667 ],
    "scissors_19": [ 0.333333343, 0.0274019931, 0.443627447, 0.852941155 ],
    "scissors_20": [ 0.158088237, 0.04047389, 0.6691176, 0.843137264 ]
}

# Go through the data table above and create the images
print ("Adding images...")
tagged_images_with_regions = []

for file_name in fork_image_regions.keys():
    x,y,w,h = fork_image_regions[file_name]
    regions = [ Region(tag_id=fork_tag.id, left=x,top=y,width=w,height=h) ]

    with open("images/fork/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))

for file_name in scissors_image_regions.keys():
    x,y,w,h = scissors_image_regions[file_name]
    regions = [ Region(tag_id=scissors_tag.id, left=x,top=y,width=w,height=h) ]

    with open("images/scissors/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))


trainer.create_images_from_files(project.id, images=tagged_images_with_regions)
```

## <a name="step-5-train-the-project"></a>5단계: 프로젝트 학습

프로젝트에 태그 및 이미지를 추가했으므로 다음과 같이 학습할 수 있습니다. 

1. 다음 코드를 삽입합니다. 이렇게 하면 프로젝트에 첫 번째 반복이 생성됩니다. 
2. 이 반복을 기본 반복으로 표시합니다.

```Python
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

## <a name="step-6-get-and-use-the-default-prediction-endpoint"></a>6단계: 기본 예측 엔드포인트 가져오기 및 사용

이제 예측을 위해 모델을 사용할 준비가 되었습니다. 

1. 기본 반복과 연결된 엔드포인트를 획득합니다. 
2. 해당 엔드포인트를 사용하여 프로젝트에 테스트 이미지를 보냅니다.

```Python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

# Open the sample image and get back the prediction results.
with open("images/Test/test_od_image.jpg", mode="rb") as test_data:
    results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100), prediction.bounding_box.left, prediction.bounding_box.top, prediction.bounding_box.width, prediction.bounding_box.height)
```

## <a name="step-7-run-the-example"></a>7단계: 예제 실행

솔루션을 실행합니다. 예측 결과가 콘솔에 나타납니다.

```
python sample.py
```
