---
title: 자습서 - Azure Stream Analytics 클라우드 작업을 위한 사용자 지정 .NET 역직렬 변환기
description: 이 자습서에서는 Visual Studio를 사용하여 Azure Stream Analytics 클라우드 작업을 위한 사용자 지정 .NET 역직렬 변환기를 만드는 방법을 보여줍니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: f5fa0a4398c904113dbce5d80844b42b6e775df0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702430"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>자습서: Azure Stream Analytics를 위한 사용자 지정 .NET 역직렬 변환기

Azure Stream Analytics에는 JSON, CSV 및 Avro라는 [세 가지 데이터 형식에 대한 기본 제공 지원](stream-analytics-parsing-json.md)이 있습니다. 사용자 지정 .NET 역직렬 변환기를 사용하면 클라우드 및 에지 작업에 대해 [프로토콜 버퍼](https://developers.google.com/protocol-buffers/), [본드](https://github.com/Microsoft/bond) 및 기타 사용자 정의 형식과 같은 다른 형식에서도 데이터를 읽을 수 있습니다.

이 자습서에서는 Visual Studio를 사용하여 Azure Stream Analytics 클라우드 작업을 위한 사용자 지정 .NET 역직렬 변환기를 만드는 방법을 보여줍니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 프로토콜 버퍼에 대한 사용자 지정 역직렬 변환기를 만듭니다.
> * Visual Studio에서 Azure Stream Analytics 작업을 만듭니다.
> * 사용자 지정 역직렬 변환기를 사용하도록 Stream Analytics 작업을 구성합니다.
> * Stream Analytics 작업을 로컬로 실행하여 사용자 지정 역직렬 변환기를 테스트합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) 또는 [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)를 설치합니다. Enterprise(Ultimate/Premium), Professional 및 Community Edition이 지원됩니다. Express Edition은 지원되지 않습니다.

* [Stream Analytics Tools for Visual Studio를 설치](stream-analytics-tools-for-visual-studio-install.md)하거나 최신 버전으로 업데이트합니다. 다음 Visual Studio 버전이 지원됩니다.
   * Visual Studio 2015
   * Visual Studio 2017

* Visual Studio에서 **클라우드 탐색기**를 열고 Azure 구독에 로그인합니다.

* Azure Storage 계정에 컨테이너를 만듭니다.
만드는 컨테이너는 Stream Analytics 작업에 관련된 자산을 저장하는 데 사용됩니다. 기존 컨테이너가 포함된 스토리지 계정이 이미 있는 경우에는 해당 컨테이너를 사용하면 됩니다. 그렇지 않은 경우에는 [새 컨테이너를 만드세요](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>사용자 지정 역직렬 변환기 만들기

1. Visual Studio를 열고 **파일 -> 새로 만들기 -> 프로젝트**를 선택합니다. **Stream Analytics**를 검색하고 **Azure Stream Analytics 사용자 지정 역직렬 변환기 프로젝트(.NET)** 를 선택합니다. 프로젝트 이름을 지정합니다(예: **Protobuf Deserializer**).

   ![Visual Studio dotnet 표준 클래스 라이브러리 프로젝트 만들기](./media/custom-deserializer/create-dotnet-library-project.png)

2. 솔루션 탐색기에서 **Protobuf Deserializer** 프로젝트를 마우스 오른쪽 단추로 클릭하고 메뉴에서 **NuGet 패키지 관리**를 선택합니다. 그런 다음, **Microsoft.Azure.StreamAnalytics** 및 **Google.Protobuf** NuGet 패키지를 설치합니다.

3. [MessageBodyProto 클래스](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) 및 [MessageBodyDeserializer 클래스](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs)를 프로젝트에 추가합니다.

4. **Protobuf Deserializer** 프로젝트를 빌드합니다.

## <a name="add-an-azure-stream-analytics-project"></a>Azure Stream Analytics 프로젝트 추가

1. 솔루션 탐색기에서 **Protobuf Deserializer** 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가 > 새 프로젝트**를 선택합니다. **Azure Stream Analytics > Stream Analytics**에서 **Azure Stream Analytics 애플리케이션**을 선택합니다. 이름을 **ProtobufCloudDeserializer**로 지정하고 **확인**을 선택합니다. 

2. **ProtobufCloudDeserializer** Azure Stream Analytics 프로젝트의 **참조**를 마우스 오른쪽 단추로 클릭합니다. **프로젝트**에서 **Protobuf Deserializer**를 추가합니다. 자동으로 채워집니다.

## <a name="configure-a-stream-analytics-job"></a>Stream Analytics 작업 구성

1. **JobConfig.json**을 두 번 클릭합니다. 다음 설정 외에는 기본 구성을 사용합니다.

   |설정|제안 값|
   |-------|---------------|
   |글로벌 스토리지 설정 리소스|현재 계정에서 데이터 원본 선택|
   |글로벌 스토리지 설정 구독| < 사용자 구독 >|
   |글로벌 스토리지 설정 스토리지 계정| < 사용자 스토리지 계정 >|
   |사용자 지정 코드 스토리지 설정 리소스|현재 계정에서 데이터 원본 선택|
   |사용자 지정 코드 스토리지 설정 스토리지 계정|< 사용자 스토리지 계정 >|
   |사용자 지정 코드 스토리지 설정 컨테이너|< 사용자 스토리지 컨테이너 >|

2. **입력**에서 **Input.json**을 두 번 클릭합니다. 다음 설정 외에는 기본 구성을 사용합니다.

   |설정|제안 값|
   |-------|---------------|
   |원본|Blob Storage|
   |리소스|현재 계정에서 데이터 원본 선택|
   |Subscription|< 사용자 구독 >|
   |스토리지 계정|< 사용자 스토리지 계정 >|
   |컨테이너|< 사용자 스토리지 컨테이너 >|
   |이벤트 직렬화 형식|기타(Protobuf, XML, 소유...)|
   |리소스|ASA 프로젝트 참조 또는 CodeBehind에서 로드|
   |CSharp 어셈블리 이름 입력|ProtobufDeserializer.dll|
   |클래스 이름|MessageBodyProto.MessageBodyDeserializer|
   |이벤트 압축 유형|없음|

3. **Script.asaql** 파일에 다음 쿼리를 추가합니다.

   ```sql
   SELECT * FROM Input
   ```

4. [샘플 protobuf 입력 파일](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)를 다운로드합니다. **입력** 폴더에서 **Input.json**을 마우스 오른쪽 단추로 클릭한 후 **로컬 입력 추가**를 선택합니다. 그런 다음, **local_Input.json**을 두 번 클릭하고 다음 설정을 구성합니다.

   |설정|제안 값|
   |-------|---------------|
   |입력 별칭|입력|
   |원본 유형|데이터 스트림|
   |이벤트 직렬화 형식|기타(Protobuf, XML, 소유...)|
   |CSharp 어셈블리 이름 입력|ProtobufDeserializer.dll|
   |클래스 이름|MessageBodyProto.MessageBodyDeserializer|
   |로컬 입력 파일 경로|< 다운로드한 샘플 protobuf 입력 파일의 파일 경로 >|

## <a name="execute-the-stream-analytics-job"></a>Stream Analytics 작업 실행

1. **Script.asaql**을 열고 **로컬에서 실행**을 선택합니다.

2. **Stream Analytics 로컬 실행 결과**에서 결과를 확인합니다.

Stream Analytics 작업에 대한 사용자 지정 역직렬 변환기를 성공적으로 구현했습니다. 이 자습서에서는 사용자 지정 역직렬 변환기를 로컬로 테스트했습니다. 실제 데이터의 경우 입력 및 출력을 적절히 구성합니다. 그런 다음, Visual Studio에서 Azure에 작업을 제출하여 방금 구현한 사용자 지정 역직렬 변환기를 사용하여 클라우드에서 작업을 실행합니다.

## <a name="debug-your-deserializer"></a>역직렬 변환기 디버그

표준 .NET 코드를 디버그하는 것과 같은 방식으로 .NET 역직렬 변환기를 로컬로 디버그할 수 있습니다. 

1. 함수에 중단점을 추가합니다.

2. **F5** 키를 눌러 디버깅을 시작합니다. 프로그램이 중단점에서 정상적으로 중지됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 스트리밍 작업 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 작업에서 사용된 스트리밍 단위에 대한 청구를 방지합니다. 작업을 나중에 사용하려는 경우 중지하고 나중에 필요할 때 다시 시작할 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음 단계를 사용하여 이 자습서에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택한 다음, 만든 리소스의 이름을 선택합니다.  

2. 리소스 그룹 페이지에서 **삭제**를 선택하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 프로토콜 버퍼 입력 직렬화에 대한 사용자 지정 .NET 역직렬 변환기를 구현하는 방법을 배웠습니다. 사용자 지정 역직렬 변환기를 만드는 방법을 자세히 알아보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Stream Analytics 작업을 위한 다양한 .NET 역직렬 변환기 만들기](custom-deserializer-examples.md)
