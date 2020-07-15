---
title: '빠른 시작: .NET용 Form Recognizer 클라이언트 라이브러리'
description: 이 빠른 시작에서는 .NET용 Form Recognizer 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: 2b46e115b6360b161a1b2ad9b176f3afbfaf27d0
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277846"
---
[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 학습 데이터 세트가 포함된 Azure Storage Blob. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 세트 빌드](../../build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다.
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>설치

### <a name="create-a-form-recognizer-azure-resource"></a>Form Recognizer Azure 리소스 만들기

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>환경 변수 만들기

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `formrecognizer-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 _Program.cs_라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다. 

```console
dotnet new console -n formrecognizer-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 그러면 다음을 사용하여 애플리케이션을 빌드합니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 *Program.cs* 파일을 엽니다. 다음 `using` 지시문을 추가합니다.

```csharp
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.IO;
using System.Threading.Tasks;
```

그런 다음, 다음 코드를 애플리케이션의 **Main** 메서드에 추가합니다. 이 비동기 작업은 나중에 정의합니다. 

```csharp
static void Main(string[] args)
{
    var t1 = RunFormRecognizerClient();

    Task.WaitAll(t1);
}
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Form Recognizer 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.


<!-- Objet model TBD -->



## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 .NET용 Form Recognizer 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [양식 콘텐츠 인식](#recognize-form-content)
* [영수증 확인](#recognize-receipts)
* [사용자 지정 모델 학습](#train-a-custom-model)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 관리](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>클라이언트 인증

`Main`에서 참조되는 작업을 `Main` 메서드 아래에 정의합니다. 여기서는 위에서 정의한 구독 변수를 사용하여 클라이언트 개체 두 개를 인증합니다. **AzureKeyCredential** 개체를 사용하면 필요한 경우 새 클라이언트 개체를 만들지 않고 API 키를 업데이트할 수 있습니다.

```csharp
static async Task RunFormRecognizerClient()
{ 
    string endpoint = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_ENDPOINT");
    string apiKey = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_KEY");
    var credential = new AzureKeyCredential(apiKey);
    
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    var recognizerClient = new FormRecognizerClient(new Uri(endpoint), credential);
```

### <a name="call-client-specific-methods"></a>클라이언트 관련 메서드 호출

다음 코드 블록은 클라이언트 개체를 사용하여 Form Recognizer SDK의 각 주요 작업에 대한 메서드를 호출합니다. 이러한 메서드는 나중에 정의합니다.

또한 학습 및 테스트 데이터에 대한 참조를 URL에 추가해야 합니다. 
* 사용자 지정 모델 학습 데이터에 대한 SAS URL를 검색하려면 Microsoft Azure Storage Explorer를 열고, 마우스 오른쪽 단추로 컨테이너를 클릭하고, **공유 액세스 서명 가져오기**를 선택합니다. **읽기** 권한과 **목록 사용** 권한이 선택되어 있는지 확인하고 **만들기**를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.
* 테스트할 양식의 URL을 가져오려면 위의 단계를 따라 Blob Storage에 있는 개별 문서의 SAS URL을 가져오면 됩니다. 또는 다른 위치에 있는 문서의 URL을 가져옵니다.
* 위의 방법을 사용하여 영수증 이미지의 URL을 가져오거나 제공된 샘플 이미지 URL을 사용할 수 있습니다.

> [!NOTE]
> 이 가이드의 코드 조각은 URL을 통해 액세스되는 원격 양식을 사용합니다. 로컬 양식 문서를 대신 처리하려는 경우 [참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre)의 관련 메서드를 참조하세요.

```csharp
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    Console.WriteLine("Get form content...");
    await GetContent(recognizerClient, formUrl);

    Console.WriteLine("Analyze receipt...");
    await AnalyzeReceipt(recognizerClient, receiptUrl);

    Console.WriteLine("Train Model with training data...");
    Guid modelId = await TrainModel(trainingClient, trainingDataUrl);

    Console.WriteLine("Analyze PDF form...");
    await AnalyzePdfForm(recognizerClient, modelId, formUrl);

    Console.WriteLine("Manage models...");
    await ManageModels(trainingClient, trainingDataUrl) ;
}
```

## <a name="recognize-form-content"></a>양식 콘텐츠 인식

Form Recognizer를 사용하면 모델을 학습시킬 필요 없이 문서의 표, 줄 및 단어를 인식할 수 있습니다.

지정된 URI에서 파일의 콘텐츠를 인식하려면 **StartRecognizeContentFromUri** 메서드를 사용합니다.

```csharp
private static async Task<Guid> GetContent(
    FormRecognizerClient recognizerClient, string invoiceUri)
{
    Response<FormPageCollection> formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
```

그러면 제출된 문서의 각 페이지당 하나씩 **FormPage** 개체 컬렉션이 반환됩니다. 다음 코드는 이러한 개체에서 반복되고 추출된 키/값 쌍 및 테이블 데이터를 출력합니다.

```csharp
    foreach (FormPage page in formPages.Value)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count}" + 
            $" lines.");
    
        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count}" + 
                $" word{(line.Words.Count > 1 ? "s" : "")}," +
                $" and text: '{line.Text}'.");
        }
    
        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and" +
                $" {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex})" +
                    $" contains text: '{cell.Text}'.");
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>영수증 확인

이 섹션에서는 사전 학습된 영수증 모델을 사용하여 미국 영수증의 공통 필드를 인식 및 추출하는 방법을 보여 줍니다.

URI를 통해 영수증을 인식하려면 **StartRecognizeReceiptsFromUri** 메서드를 사용합니다. 그러면 제출된 문서의 각 페이지당 하나씩 **RecognizedReceipt** 개체 컬렉션이 반환됩니다. 다음 코드는 지정된 URI에서 영수증을 처리하고 주요 필드와 값을 콘솔에 출력합니다.

```csharp
private static async Task<Guid> AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    RecognizedReceiptCollection receipts = await recognizerClient.StartRecognizeReceiptsFromUri(new Uri(receiptUri))
    .WaitForCompletionAsync();

    foreach (RecognizedReceipt receipt in receipts)
    {
    FormField merchantNameField;
    if (receipt.RecognizedForm.Fields.TryGetValue("MerchantName", out merchantNameField))
    {
        if (merchantNameField.Value.Type == FieldValueType.String)
        {
            string merchantName = merchantNameField.Value.AsString();

            Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
        }
    }

    FormField transactionDateField;
    if (receipt.RecognizedForm.Fields.TryGetValue("TransactionDate", out transactionDateField))
    {
        if (transactionDateField.Value.Type == FieldValueType.Date)
        {
            DateTime transactionDate = transactionDateField.Value.AsDate();

            Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
        }
    }
```

코드의 다음 블록은 영수증에서 검색된 개별 항목에서 반복되고 콘솔에 세부 정보를 출력합니다.

```csharp
    FormField itemsField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Items", out itemsField))
    {
        if (itemsField.Value.Type == FieldValueType.List)
        {
            foreach (FormField itemField in itemsField.Value.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.Value.Type == FieldValueType.Dictionary)
                {
                    IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                    FormField itemNameField;
                    if (itemFields.TryGetValue("Name", out itemNameField))
                    {
                        if (itemNameField.Value.Type == FieldValueType.String)
                        {
                            string itemName = itemNameField.Value.AsString();

                            Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                        }
                    }

                    FormField itemTotalPriceField;
                    if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                    {
                        if (itemTotalPriceField.Value.Type == FieldValueType.Float)
                        {
                            float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                            Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                        }
                    }
                }
            }
        }
    }
```

마지막으로, 마지막 코드 블록은 영수증에 총 값을 출력합니다.

```csharp
    FormField totalField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Total", out totalField))
    {
        if (totalField.Value.Type == FieldValueType.Float)
        {
            float total = totalField.Value.AsFloat();

            Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
        }
    }
}
```

## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

이 섹션에서는 사용자 고유의 데이터로 모델을 학습시키는 방법을 보여 줍니다. 학습된 모델은 원본 양식 문서의 키/값 관계가 포함된 구조적 데이터를 출력할 수 있습니다. 모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

> [!NOTE]
> [Form Recognizer 샘플 레이블 지정 도구](../../quickstarts/label-tool.md)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습시킬 수도 있습니다.

### <a name="train-a-model-without-labels"></a>레이블 없이 모델 학습

학습 문서에 수동으로 레이블을 지정하지 않고 사용자 지정 양식에서 발견된 모든 필드와 값을 인식하도록 사용자 지정 모델을 학습시킬 수 있습니다.

다음 메서드는 지정된 문서 세트를 모델에 학습시키고 모델의 상태를 콘솔에 출력합니다. 

```csharp
private static async Task<Guid> TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: false).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

반환되는 **CustomFormModel** 개체는 모델에서 인식할 수 있는 양식 유형과 각 양식 유형에서 추출할 수 있는 필드에 대한 정보를 포함합니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

마지막으로, 이 메서드는 모델의 고유 ID를 반환합니다.

```csharp
    return model.ModelId;
}
```

### <a name="train-a-model-with-labels"></a>레이블을 사용하여 모델 학습

학습 문서에 레이블을 수동으로 지정하여 사용자 지정 모델을 학습시킬 수도 있습니다. 일부 시나리오에서는 레이블을 사용하여 학습시키면 성능이 향상됩니다. 레이블을 사용하여 학습시키려면 학습 문서와 별도로 Blob Storage 컨테이너에 특별한 레이블 정보 파일( *\<filename\>.pdf.labels.json*)이 있어야 합니다. [Form Recognizer 샘플 레이블 지정 도구](../../quickstarts/label-tool.md)는 이러한 레이블 파일을 만드는 데 도움이 되는 UI를 제공합니다. 이러한 레이블 파일이 있으면 `true`로 설정된 *uselabels* 매개 변수를 사용하여 **StartTrainingAsync** 메서드를 호출할 수 있습니다.

```csharp
private static async Task<Guid> TrainModelWithLabelsAsync(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
    .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: true).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

반환되는 **CustomFormModel**은 모델에서 추출할 수 있는 필드와 각 필드의 예상 정확도를 나타냅니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Accuracy != null)
            {
                Console.Write($", Accuracy: {field.Accuracy}");
            }
            Console.WriteLine("");
        }
    }
    return model.ModelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>사용자 지정 모델을 사용하여 양식 분석

이 섹션에서는 사용자 고유의 양식으로 학습시킨 모델을 사용하여 사용자 지정 양식 유형에서 키/값 정보 및 기타 콘텐츠를 추출하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 시나리오를 구현하려면 해당 ID를 아래 메서드에 전달할 수 있도록 모델을 이미 학습시킨 상태여야 합니다. [모델 학습](#train-a-model-without-labels) 섹션을 참조하세요.

**StartRecognizeCustomFormsFromUri** 메서드를 사용합니다. 그러면 제출된 문서의 각 페이지당 하나씩 **RecognizedForm** 개체 컬렉션이 반환됩니다.

```csharp
// Analyze PDF form data
private static async Task AnalyzePdfForm(
    FormRecognizerClient formClient, Guid modelId, string pdfFormFile)
{    
    Response<IReadOnlyList<RecognizedForm>> forms = await recognizerClient
        .StartRecognizeCustomFormsFromUri(modelId, new Uri(formUri))
        .WaitForCompletionAsync();
```

다음 코드에서는 분석 결과를 콘솔에 출력합니다. 인식된 각 필드와 해당 값을 신뢰도 점수와 함께 출력합니다.

```csharp
    foreach (RecognizedForm form in forms.Value)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");
    
            if (field.LabelText != null)
            {
                Console.WriteLine($"    Label: '{field.LabelText.Text}");
            }
    
            Console.WriteLine($"    Value: '{field.ValueText.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
    }
}
```

## <a name="manage-your-custom-models"></a>사용자 지정 모델 관리

이 섹션에서는 계정에 저장된 사용자 지정 모델을 관리하는 방법을 보여 줍니다. 다음 코드는 단일 메서드의 모든 모델 관리 작업을 수행하는 예를 보여 줍니다. 우선 아래의 메서드 시그니처를 복사하세요.

```csharp
private static async Task ManageModels(
    FormRecognizerClient trainingClient, string trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer 리소스 계정의 모델 수 확인

다음 코드 블록은 Form Recognizer 계정에 저장된 모델 수를 확인하고 이를 계정 제한과 비교합니다.

```csharp
    // Check number of models in the FormRecognizer account, 
    // and the maximum number of models that can be stored.
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit}" +
        $" models.");
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>리소스 계정에 현재 저장된 모델 나열

다음 코드 블록은 계정의 현재 모델을 나열하고 해당 세부 정보를 콘솔에 출력합니다.

```csharp
    // List the first ten or fewer models currently stored in the account.
    Pageable<CustomFormModelInfo> models = trainingClient.GetModelInfos();
    
    foreach (CustomFormModelInfo modelInfo in models.Take(10))
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Created On: {modelInfo.CreatedOn}");
        Console.WriteLine($"    Last Modified: {modelInfo.LastModified}");
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>모델 ID를 사용하여 특정 모델 가져오기

다음 코드 블록은 [모델 학습](#train-a-model-without-labels) 섹션에서와 마찬가지로 새 모델을 학습시킨 다음, 해당 ID를 사용하여 두 번째 참조를 검색합니다.

```csharp
    // Create a new model to store in the account
    CustomFormModel model = await trainingClient.StartTrainingAsync(
        new Uri(trainingFileUrl)).WaitForCompletionAsync();
    
    // Get the model that was just created
    CustomFormModel modelCopy = trainingClient.GetCustomModel(model.ModelId);
    
    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following" +
        " form types:");
    
    foreach (CustomFormSubModel subModel in modelCopy.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

### <a name="delete-a-model-from-the-resource-account"></a>리소스 계정에서 모델 삭제

해당 ID를 참조하여 계정에서 모델을 삭제할 수도 있습니다.

```csharp
    // Delete the model from the account.
    trainingClient.DeleteModel(model.ModelId);
}
```

## <a name="run-the-application"></a>애플리케이션 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>문제 해결

.NET SDK를 사용하여 Cognitive Services Form Recognizer 클라이언트 라이브러리와 상호 작용하는 경우 서비스에서 반환되는 오류로 인해 `RequestFailedException`이 발생합니다. REST API 요청에서 반환된 것과 동일한 HTTP 상태 코드를 포함합니다.

예를 들어 잘못된 URI를 사용하여 영수증 이미지를 제출하는 경우 "잘못된 요청"을 나타내는 `400` 오류가 반환됩니다.

```csharp Snippet:FormRecognizerBadRequest
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();

}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

작업의 클라이언트 요청 ID와 같은 추가 정보가 로깅됩니다.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer .NET 클라이언트 라이브러리를 사용하여 다양한 방식으로 모델을 학습시키고 양식을 분석했습니다. 다음으로, 더 나은 학습 데이터 세트를 만들고 더 정확한 모델을 생성하기 위한 팁에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [학습 데이터 세트 빌드](../../build-training-data-set.md)

* [Form Recognizer란?](../../overview.md)
* [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)에서 이 가이드의 샘플 코드 등을 확인할 수 있습니다.
