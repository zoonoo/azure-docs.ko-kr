---
title: '자습서: Custom Vision Service REST API 사용'
titlesuffix: Azure Cognitive Services
description: REST API를 사용하여 Custom Vision 모델 생성, 교육, 테스트, 내보내기를 수행합니다.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 2f28af14c0579a7a6b514d66d2b66a540adb8fac
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226522"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>자습서: Custom Vision REST API 사용

Custom Vision REST API를 사용하여 모델 생성, 교육, 테스트, 내보내기를 수행하는 방법을 알아봅니다.

이 문서에 수록된 정보는 REST 클라이언트에서 REST API를 사용하여 Custom Vision Service를 교육하는 방법을 보여줍니다. 예제는 bash 환경의 `curl` 유틸리티와 Windows PowerShell의 `Invoke-WebRequest`로 API를 사용하는 방법을 보여줍니다.

> [!div class="checklist"]
> * 키 가져오기
> * 프로젝트 만들기
> * 태그 만들기
> * 이미지 추가
> * 모델 교육 및 테스트
> * 모델 내보내기

## <a name="prerequisites"></a>필수 조건

* REST(Representational State Transfer)의 기본적인 사항을 숙지해야 합니다. 이 문서에서는 HTTP 동사, JSON 또는 REST에서 일반적으로 사용되는 다른 요소에 대해 자세히 다루지 않습니다.

* [curl](https://curl.haxx.se) 유틸리티 또는 Windows PowerShell 3.0(또는 그 이상)이 설치된 bash(Bourne Again Shell).

* Custom Vision 계정. 자세한 내용은 [분류자 빌드](getting-started-build-a-classifier.md) 문서를 참조하세요.

## <a name="get-keys"></a>키 가져오기

REST API를 사용 중인 경우 키를 사용하여 인증해야 합니다. 관리 또는 교육 작업을 수행할 때는 __교육 키__를 사용합니다. 모델을 사용하여 예측을 수행할 때는 __예측 키__를 사용합니다.

요청을 수행할 때 키가 요청 헤더로 전송됩니다.

계정의 키를 가져오려면 [Custom Vision 웹 페이지](https://customvision.ai)로 가서 오른쪽 위에 있는 __기어 아이콘__을 선택합니다. __계정__ 섹션에서 __학습 키__ 및 __예측 키__ 필드의 값을 복사합니다.

![키 UI의 이미지](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> 키는 모든 요청을 인증하는 데 사용되므로 이 문서의 예제에서는 환경 변수에 키 값이 포함되어 있다고 가정합니다. 이 문서의 다른 코드 조각을 사용하기 전에 다음 명령을 사용하여 환경 변수에 키를 저장하세요.
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>새 프로젝트 만들기

다음 예제에서는 Custom Vision Service 인스턴스에 `myproject`라는 새 프로젝트를 만듭니다. 이 서비스는 기본적으로 `General` 도메인으로 설정됩니다.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

요청에 대한 응답은 다음 JSON 문서와 유사합니다.

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> 응답의 `id` 항목은 새 프로젝트의 ID입니다. 이는 이 문서의 뒷부분에 나오는 다른 예제에서 사용됩니다.

이 요청에 대한 자세한 내용은 [CreateProject](https://go.microsoft.com/fwlink/?linkid=865446)를 참조하세요.

### <a name="specific-domains"></a>특정 도메인

특정 도메인에 프로젝트를 만들려면 __도메인 ID__를 선택적 매개 변수로 사용하면 됩니다. 다음 예제에서는 사용 가능한 도메인 목록을 검색하는 방법을 보여 줍니다.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

요청에 대한 응답은 다음 JSON 문서와 유사합니다.

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

이 요청에 대한 자세한 내용은 [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d)를 참조하세요.

다음 예제에서는 __Landmarks__ 도메인을 사용하는 새 프로젝트를 만드는 방법을 설명합니다.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>태그 만들기

이미지에 태그를 지정하려면 태그 ID를 사용해야 합니다. 다음 예제에서는 `cat`라는 새 태그를 만들고 태그 ID를 가져오는 방법을 보여줍니다. `{projectId}`를 프로젝트의 ID로 바꿉니다. `name=` 매개 변수를 사용하여 태그의 이름을 지정합니다.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

요청에 대한 응답은 JSON 문서와 유사합니다. 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

`id` 값은 이미지에 태그를 지정할 때 사용되므로 저장하세요.

이 요청에 대한 자세한 내용은 [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d)를 참조하세요.

## <a name="add-images"></a>이미지 추가

다음 예제에서는 URL에서 파일을 추가하는 방법을 보여줍니다. `{projectId}`를 프로젝트의 ID로 바꿉니다. `{tagId}`를 이미지의 태그 ID로 바꿉니다.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

요청에 대한 응답은 다음 JSON 문서와 유사합니다.

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

이 요청에 대한 자세한 내용은 [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287)를 참조하세요.

## <a name="train-the-model"></a>모델 학습

다음 예제에서는 모델을 교육하는 방법을 보여 줍니다. `{projectId}`를 프로젝트의 ID로 바꿉니다.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

요청에 대한 응답은 다음 JSON 문서와 유사합니다.

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

`id` 값은 모델을 테스트하고 내보낼 때 사용되므로 저장하세요.

자세한 내용은 [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294)를 참조하세요.

## <a name="test-the-model"></a>모델 테스트

다음 예제에서는 모델 테스트를 수행하는 방법을 보여 줍니다. `{projectId}`를 프로젝트의 ID로 바꿉니다. `{iterationId}`를 모델 교육 시 반환된 ID로 바꿉니다. `https://linktotestimage`를 테스트 이미지의 경로로 대체합니다.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

요청에 대한 응답은 다음 JSON 문서와 유사합니다.

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

자세한 내용은 [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d)을 참조하세요.

## <a name="export-the-model"></a>모델 내보내기

모델 내보내기는 2단계 프로세스입니다. 먼저 모델 형식을 지정한 후 내보낸 모델의 URL을 요청해야 합니다.

### <a name="request-a-model-export"></a>모델 내보내기 요청

다음 예제에서는 `coreml` 모델을 내보내는 방법을 보여 줍니다. `{projectId}`를 프로젝트의 ID로 바꿉니다. `{iterationId}`를 모델 교육 시 반환된 ID로 바꿉니다.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

요청에 대한 응답은 다음 JSON 문서와 유사합니다.

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

자세한 내용은 [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b)을 참조하세요.

### <a name="download-the-exported-model"></a>내보낸 모델 다운로드

다음 예제에서는 내보낸 모델의 URL을 검색하는 방법을 보여 줍니다. `{projectId}`를 프로젝트의 ID로 바꿉니다. `{iterationId}`를 모델 교육 시 반환된 ID로 바꿉니다.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

요청에 대한 응답은 다음 JSON 문서와 유사합니다.

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

자세한 내용은 [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a)를 참조하세요.
