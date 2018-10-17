---
title: '자습서: 이미지 분류 프로젝트 빌드 - Custom Vision Service, Java'
titlesuffix: Azure Cognitive Services
description: 기본 엔드포인트를 사용하여 프로젝트를 만들고, 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 예측을 수행합니다.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: 9a7f50e0eb33016d6a2d8f28be047b327135c51f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367358"
---
# <a name="tutorial-build-an-image-classification-project-with-java"></a>자습서: Java를 사용하여 이미지 분류 프로젝트 빌드

Java를 사용하는 Custom Vision Service로 이미지 분류 프로젝트를 만드는 방법을 알아봅니다. 프로젝트를 만든 후에는 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습시키고, 프로젝트의 기본 예측 엔드포인트 URL을 가져오고, 해당 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. 이 오픈 소스 예제를 Custom Vision API를 사용하여 사용자 고유의 앱을 빌드하기 위한 템플릿으로 사용합니다.

## <a name="prerequisites"></a>필수 조건

- JDK 7 또는 8가 설치됨.
- Maven이 설치됨.

## <a name="get-the-training-and-prediction-keys"></a>학습 및 예측 키 가져오기

이 예제에서 사용된 키를 가져오려면 [Custom Vision 웹 페이지](https://customvision.ai)로 가서 오른쪽 위에 있는 __기어 아이콘__을 선택합니다. __계정__ 섹션에서 __학습 키__ 및 __예측 키__ 필드의 값을 복사합니다.

![키 UI의 이미지](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Custom Vision Service SDK 설치

Maven 중앙 리포지토리에서 Custom Vision SDK를 설치할 수 있습니다.
* [교육 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [예측 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>코드 이해

이미지를 포함한 전체 프로젝트는 [Java 리포지토리용 Custom Vision Azure 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master)에서 사용할 수 있습니다. 

즐겨 사용하는 Java IDE를 사용하여 `Vision/CustomVision` 프로젝트를 여세요. 

이 응용 프로그램은 앞서 검색한 학습 키를 사용하여 __샘플 Java 프로젝트__라는 새 프로젝트를 만듭니다. 그런 다음, 분류자를 학습하고 테스트하려면 이미지를 업로드합니다. 분류자는 트리가 __솔송나무__인지 __벗나무__인지를 식별합니다.

다음 코드 조각은 이 예제의 기본 기능을 구현합니다.

## <a name="create-a-custom-vision-service-project"></a>Custom Vision Service 프로젝트 만들기

> [!IMPORTANT]
> 앞서 검색한 학습 키 값에 `trainingApiKey`를 설정합니다.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>프로젝트에 태그 추가

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>프로젝트에 이미지 업로드

이 샘플은 최종 패키지에 이미지를 포함하도록 설정되어 있습니다. 이미지는 jar의 리소스 섹션에서 읽고 서비스에 업로드됩니다.

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

이전 코드 조각은 이미지를 리소스 스트림으로 검색하여 서비스에 업로드하는 두 개의 도우미 함수를 사용합니다.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>프로젝트 학습

다음 코드는 프로젝트에서 첫 번째 반복을 작성하여 기본 반복으로 표시합니다. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>기본 예측 엔드포인트 가져오기 및 사용

> [!IMPORTANT]
> 앞서 검색한 예측 키 값에 `predictionApiKey`를 설정합니다.

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>예제 실행

Maven을 사용하여 솔루션을 컴파일하고 실행하려면 다음을 수행하세요.

```
mvn compile exec:java
```

응용 프로그램의 출력은 다음 텍스트와 비슷합니다.

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```