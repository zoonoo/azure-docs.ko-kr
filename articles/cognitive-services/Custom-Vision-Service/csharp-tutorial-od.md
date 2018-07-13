---
title: C#로 개체 검색 프로젝트 빌드 - Custom Vision Service - Azure Cognitive Services | Microsoft Docs
description: Microsoft Cognitive Services의 Custom Vision API를 사용하는 기본 Windows 앱을 탐색합니다. 기본 끝점을 사용하여 프로젝트를 만들고, 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 예측을 수행합니다.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/07/2018
ms.author: areddish
ms.openlocfilehash: e3def864267a590c86a2dd6663561d8488081ad6
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301083"
---
# <a name="use-custom-vision-api-to-build-an-object-detection-project-in-c35"></a>Custom Vision API를 사용하여 C&#35;에서 개체 검색 프로젝트 빌드 
Computer Vision API를 사용하여 개체 검색 프로젝트를 만드는 기본 Windows 응용 프로그램을 사용하는 방법을 알아봅니다. 프로젝트를 만든 후에는 태그가 지정된 지역을 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 기본 예측 끝점 URL를 획득하고, 해당 끝점을 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. 이 오픈 소스 예제를 Custom Vision API를 사용하여 사용자 고유의 Windows용 앱을 빌드하기 위한 템플릿으로 사용합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="get-the-custom-vision-sdk-and-samples"></a>Custom Vision SDK 및 샘플 다운로드
이 예제를 빌드하려면 다음 Custom Vision SDK NuGet 패키지가 필요합니다.

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[C# 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision)과 함께 이미지를 다운로드할 수 있습니다.

## <a name="get-the-training-and-prediction-keys"></a>학습 및 예측 키 가져오기

이 예제에서 사용된 키를 가져오려면 [Custom Vision 웹 페이지](https://customvision.ai)로 가서 오른쪽 위에 있는 __톱니 바퀴 아이콘__을 선택합니다. __계정__ 섹션에서 __학습 키__ 및 __예측 키__ 필드의 값을 복사합니다.

![키 UI의 이미지](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="step-1-create-a-console-application"></a>1단계: 콘솔 응용 프로그램 만들기

이 단계에서는 콘솔 응용 프로그램을 만들고 예제에 필요한 학습 키 및 이미지를 준비합니다.

1. Visual Studio 2015, Community Edition을 시작합니다. 
2. 새 콘솔 응용 프로그램을 만듭니다.
3. 두 개의 Nuget 패키지에 대한 참조를 추가합니다.
    * Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training
    * Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction

4. **Program.cs**의 내용을 다음 코드로 바꿉니다.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction;
using Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training;
using Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training.Models;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading;

namespace SampleObjectDetection
{
    class Program
    {
        static void Main(string[] args)
        {
            // Add your training key from the settings page of the portal
            string trainingKey = "<your key here>";

            // Create the Api, passing in the training key
            TrainingApi trainingApi = new TrainingApi() { ApiKey = trainingKey };
        }        
    }
}
```

## <a name="step-2-create-a-custom-vision-service-project"></a>2단계: Custom Vision Service 프로젝트 만들기

새 Custom Vision Service 프로젝트를 만들려면 다음 코드를 **main ()** 메서드 끝에 추가합니다.

```csharp
    // Find the object detection domain
    var domains = trainingApi.GetDomains();
    var objDetectionDomain = domains.FirstOrDefault(d => d.Type == "ObjectDetection");

    // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project", null, objDetectionDomain.Id);
```

## <a name="step-3-add-tags-to-your-project"></a>3단계: 프로젝트에 태그 추가

프로젝트에 태그를 추가하려면 **CreateProject()** 호출 뒤에 다음 코드를 삽입합니다.

```csharp
    // Make two tags in the new project
    var forkTag = trainingApi.CreateTag(project.Id, "fork");
    var scissorsTag = trainingApi.CreateTag(project.Id, "scissors");
```

## <a name="step-4-upload-images-to-the-project"></a>4단계: 프로젝트에 이미지 업로드

개체 검색 프로젝트의 경우, 정규화된 좌표와 태그를 사용하여 개체의 지역을 식별해야 합니다. 이미지 및 태그가 지정된 지역을 추가하려면 **Main()** 메서드 끝에 다음 코드를 삽입합니다.

```csharp
    Dictionary<string, double[]> fileToRegionMap = new Dictionary<string, double[]>()
    {
        // The bounding box is specified in normalized coordinates.
        //  Normalized Left = Left / Width (in Pixels)
        //  Normalized Top = Top / Height (in Pixels)
        //  Normalized Bounding Box Width = (Right - Left) / Width (in Pixels)
        //  Normalized Bounding Box Height = (Bottom - Top) / Height (in Pixels)
        // FileName, Left, Top, Width, Height of the bounding box.
        {"scissors_1", new double[] { 0.4007353, 0.194068655, 0.259803921, 0.6617647 } },
        {"scissors_2", new double[] { 0.426470578, 0.185898721, 0.172794119, 0.5539216 } },
        {"scissors_3", new double[] { 0.289215684, 0.259428144, 0.403186262, 0.421568632 } },
        {"scissors_4", new double[] { 0.343137264, 0.105833367, 0.332107842, 0.8055556 } },
        {"scissors_5", new double[] { 0.3125, 0.09766343, 0.435049027, 0.71405226 } },
        {"scissors_6", new double[] { 0.379901975, 0.24308826, 0.32107842, 0.5718954 } },
        {"scissors_7", new double[] { 0.341911763, 0.20714055, 0.3137255, 0.6356209 } },
        {"scissors_8", new double[] { 0.231617644, 0.08459154, 0.504901946, 0.8480392 } },
        {"scissors_9", new double[] { 0.170343131, 0.332957536, 0.767156839, 0.403594762 } },
        {"scissors_10", new double[] { 0.204656869, 0.120539248, 0.5245098, 0.743464053 } },
        {"scissors_11", new double[] { 0.05514706, 0.159754932, 0.799019635, 0.730392158 } },
        {"scissors_12", new double[] { 0.265931368, 0.169558853, 0.5061275, 0.606209159 } },
        {"scissors_13", new double[] { 0.241421565, 0.184264734, 0.448529422, 0.6830065 } },
        {"scissors_14", new double[] { 0.05759804, 0.05027781, 0.75, 0.882352948 } },
        {"scissors_15", new double[] { 0.191176474, 0.169558853, 0.6936275, 0.6748366 } },
        {"scissors_16", new double[] { 0.1004902, 0.279036, 0.6911765, 0.477124184 } },
        {"scissors_17", new double[] { 0.2720588, 0.131977156, 0.4987745, 0.6911765 } },
        {"scissors_18", new double[] { 0.180147052, 0.112369314, 0.6262255, 0.6666667 } },
        {"scissors_19", new double[] { 0.333333343, 0.0274019931, 0.443627447, 0.852941155 } },
        {"scissors_20", new double[] { 0.158088237, 0.04047389, 0.6691176, 0.843137264 } },
        {"fork_1", new double[] { 0.145833328, 0.3509314, 0.5894608, 0.238562092 } },
        {"fork_2", new double[] { 0.294117659, 0.216944471, 0.534313738, 0.5980392 } },
        {"fork_3", new double[] { 0.09191177, 0.0682516545, 0.757352948, 0.6143791 } },
        {"fork_4", new double[] { 0.254901975, 0.185898721, 0.5232843, 0.594771266 } },
        {"fork_5", new double[] { 0.2365196, 0.128709182, 0.5845588, 0.71405226 } },
        {"fork_6", new double[] { 0.115196079, 0.133611143, 0.676470637, 0.6993464 } },
        {"fork_7", new double[] { 0.164215669, 0.31008172, 0.767156839, 0.410130739 } },
        {"fork_8", new double[] { 0.118872553, 0.318251669, 0.817401946, 0.225490168 } },
        {"fork_9", new double[] { 0.18259804, 0.2136765, 0.6335784, 0.643790841 } },
        {"fork_10", new double[] { 0.05269608, 0.282303959, 0.8088235, 0.452614367 } },
        {"fork_11", new double[] { 0.05759804, 0.0894935, 0.9007353, 0.3251634 } },
        {"fork_12", new double[] { 0.3345588, 0.07315363, 0.375, 0.9150327 } },
        {"fork_13", new double[] { 0.269607842, 0.194068655, 0.4093137, 0.6732026 } },
        {"fork_14", new double[] { 0.143382356, 0.218578458, 0.7977941, 0.295751631 } },
        {"fork_15", new double[] { 0.19240196, 0.0633497, 0.5710784, 0.8398692 } },
        {"fork_16", new double[] { 0.140931368, 0.480016381, 0.6838235, 0.240196079 } },
        {"fork_17", new double[] { 0.305147052, 0.2512582, 0.4791667, 0.5408496 } },
        {"fork_18", new double[] { 0.234068632, 0.445702642, 0.6127451, 0.344771236 } },
        {"fork_19", new double[] { 0.219362751, 0.141781077, 0.5919118, 0.6683006 } },
        {"fork_20", new double[] { 0.180147052, 0.239820287, 0.6887255, 0.235294119 } }
    };

    // Add all images for fork
    var imagePath = Path.Combine("Images", "fork");
    var imageFileEntries = new List<ImageFileCreateEntry>();
    foreach (var fileName in Directory.EnumerateFiles(imagePath))
    {
        var region = fileToRegionMap[Path.GetFileNameWithoutExtension(fileName)];
        imageFileEntries.Add(new ImageFileCreateEntry(fileName, File.ReadAllBytes(fileName), null, new List<Region>(new Region[] { new Region(forkTag.Id, region[0], region[1], region[2], region[3]) })));
    }
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFileEntries));

    // Add all images for scissors
    imagePath = Path.Combine("Images", "scissors");
    imageFileEntries = new List<ImageFileCreateEntry>();
    foreach (var fileName in Directory.EnumerateFiles(imagePath))
    {
        var region = fileToRegionMap[Path.GetFileNameWithoutExtension(fileName)];
        imageFileEntries.Add(new ImageFileCreateEntry(fileName, File.ReadAllBytes(fileName), null, new List<Region>(new Region[] { new Region(scissorsTag.Id, region[0], region[1], region[2], region[3]) })));
    }
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFileEntries));
```

## <a name="step-5-train-the-project"></a>5단계: 프로젝트 학습

프로젝트에 태그 및 이미지를 추가했으므로 다음과 같이 학습할 수 있습니다. 

1. **Main()** 의 끝에 다음 코드를 삽입합니다. 이렇게 하면 프로젝트에 첫 번째 반복이 생성됩니다.
2. 이 반복을 기본 반복으로 표시합니다.

```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status != "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get its updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }

    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
```

## <a name="step-6-get-and-use-the-default-prediction-endpoint"></a>6단계: 기본 예측 끝점 가져오기 및 사용

이제 예측을 위해 모델을 사용할 준비가 되었습니다. 

1. **Main()** 끝에 다음 코드를 삽입하여 기본 반복과 연결된 끝점을 획득합니다. 
2. 해당 끝점을 사용하여 프로젝트에 테스트 이미지를 보냅니다.

```csharp
    // Now there is a trained endpoint, it can be used to make a prediction

    // Add your prediction key from the settings page of the portal
    // The prediction key is used in place of the training key when making predictions
    string predictionKey = "<your key here>";

    // Create a prediction endpoint, passing in the obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };

    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var imageFile = Path.Combine("Images", "test", "test_image.jpg");
    using (var stream = File.OpenRead(imageFile))
    {
        var result = endpoint.PredictImage(project.Id, File.OpenRead(imageFile));

        // Loop over each prediction and write out the results
        foreach (var c in result.Predictions)
        {
            Console.WriteLine($"\t{c.TagName}: {c.Probability:P1} [ {c.BoundingBox.Left}, {c.BoundingBox.Top}, {c.BoundingBox.Width}, {c.BoundingBox.Height} ]");
        }
    }
```

## <a name="step-7-run-the-example"></a>7단계: 예제 실행

솔루션을 빌드하고 실행합니다. 예측 결과가 콘솔에 나타납니다.
