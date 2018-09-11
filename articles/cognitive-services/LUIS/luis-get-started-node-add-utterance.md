---
title: '빠른 시작: Node.js를 사용하여 모델을 변경하고 LUIS 앱 학습시키기 - Azure Cognitive Services | Microsoft Docs'
description: Node.js 빠른 시작에서는 Home Automation 앱에 예제 발언을 추가하여 앱을 학습시킵니다. 예제 발언은 의도에 매핑된 대화형 사용자 텍스트입니다. 의도에 대한 예제 발언을 제공하여, 사용자가 제공한 텍스트의 종류가 어떤 의도에 속하는지 LUIS에 알려줍니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: c6ad30340e0800e475af9a9fefa0af7df1769f6c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43750584"
---
# <a name="quickstart-change-model-using-nodejs"></a>빠른 시작: Node.js를 사용하여 모델 변경

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>필수 조건

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* NPM를 사용하는 최신 [**Node.js**](https://nodejs.org/en/download/)
* 이 아티클에 대한 NPM 종속성은 [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra)입니다.  
* [Visual Studio Code](https://code.visualstudio.com/)

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>빠른 시작 코드 만들기 

`add-utterances.js` 파일에 NPM 종속성을 추가합니다.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

파일에 LUIS 상수를 추가합니다. 다음 코드를 복사하고 작성 키, 응용 프로그램 ID 및 버전 ID로 변경합니다.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

사용자 발언을 포함하는 업로드 파일의 이름 및 위치를 추가합니다. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

`addUtterance` 함수에 대한 메서드와 개체를 추가합니다.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

`train` 함수에 대한 메서드와 개체를 추가합니다.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

`sendUtteranceToApi` 함수를 추가하여 HTTP 호출을 주고 받습니다. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

작업을 선택하는 **기본** 코드를 추가합니다.

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>종속성 설치

다음 텍스트로 `package.json` 파일을 만듭니다.

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

명령줄에서 package.json이 있는 디렉터리에 NPM: `npm install`을 사용하여 종속성을 설치합니다.

## <a name="run-code"></a>코드 실행

Node.js를 사용하여 명령줄의 응용 프로그램을 실행합니다.

`npm start`를 호출하면 발언이 추가되고, 학습이 진행되고, 학습 상태를 가져옵니다.

```CMD
> npm start 
```

이 명령줄은 add utterances API를 호출한 결과를 표시합니다. 

[!include[Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>리소스 정리

빠른 시작을 완료하면 이 빠른 시작에서 생성된 모든 파일을 제거하세요. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [프로그래밍 방식으로 LUIS 앱 빌드](luis-tutorial-node-import-utterances-csv.md)