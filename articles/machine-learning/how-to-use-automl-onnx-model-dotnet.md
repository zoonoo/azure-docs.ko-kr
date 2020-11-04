---
title: .NET에서 AutoML ONNX 모델을 사용 하 여 예측 수행
description: ML.NET를 사용 하 여 .NET에서 AutoML ONNX 모델을 사용 하 여 예측을 만드는 방법 알아보기
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to
ms.openlocfilehash: cb4e57cfe8b7494b7d5c38869f83190bff76ef2a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305772"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>.NET에서 AutoML ONNX 모델을 사용 하 여 예측 수행

이 문서에서는 ML.NET를 사용 하 여 c # .NET Core 콘솔 응용 프로그램에서 자동화 된 ML (AutoML) Open 신경망 교환 (ONNX) 모델을 사용 하 여 예측을 만드는 방법에 대해 알아봅니다.

[ML.NET](/dotnet/machine-learning/) 는 c # 또는 F #에서 코드 우선 방법을 사용 하 여 사용자 지정 기계 학습 모델을 학습 하 고 사용할 수 있도록 하는 .net 에코 시스템의 오픈 소스 플랫폼 간 기계 학습 프레임 워크로, [모델 작성기](/dotnet/machine-learning/automate-training-with-model-builder) 및 [ML.NET CLI](/dotnet/machine-learning/automate-training-with-cli)와 같은 낮은 코드 도구를 통해 사용자 지정 기계 학습 모델을 학습 하 고 사용할 수 있도록 합니다. 프레임 워크는 확장 가능 하며 TensorFlow 및 ONNX와 같은 기타 인기 있는 기계 학습 프레임 워크를 활용할 수 있습니다.

ONNX는 AI 모델에 대 한 오픈 소스 형식입니다. ONNX에서는 프레임워크 간의 상호 운용성을 지원합니다. 즉, PyTorch와 같은 널리 사용 되는 여러 기계 학습 프레임 워크 중 하나에서 모델을 학습 하 고, ONNX 형식으로 변환 하 고, ML.NET와 같은 다른 프레임 워크에서 ONNX 모델을 사용할 수 있습니다. 자세히 알아보려면 [ONNX 웹 사이트](https://onnx.ai/)를 방문하세요.

## <a name="prerequisites"></a>필수 구성 요소

- [.NET Core SDK 3.1 이상](https://dotnet.microsoft.com/download)
- 텍스트 편집기 또는 IDE (예: [Visual Studio](https://visualstudio.microsoft.com/vs/) 또는 [Visual Studio Code](https://code.visualstudio.com/Download))
- ONNX 모델. AutoML ONNX 모델을 학습 하는 방법에 대 한 자세한 내용은 다음 [은행 마케팅 분류 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)을 참조 하세요.
- [Netron](https://github.com/lutzroeder/netron)(선택 사항)

## <a name="create-a-c-console-application"></a>C# 콘솔 애플리케이션 만들기

이 샘플에서는 .NET Core CLI 사용 하 여 응용 프로그램을 빌드합니다. 하지만 Visual Studio를 사용 하 여 동일한 작업을 수행할 수 있습니다. [.NET Core CLI](/dotnet/core/tools/)에 대해 자세히 알아보세요.

1. 터미널을 열고 새 c # .NET Core 콘솔 응용 프로그램을 만듭니다. 이 예제에서 응용 프로그램의 이름은 `AutoMLONNXConsoleApp` 입니다. 응용 프로그램의 내용과 동일한 이름을 사용 하 여 디렉터리를 만듭니다.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. 터미널에서 *AutoMLONNXConsoleApp* 디렉터리로 이동 합니다.

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>소프트웨어 패키지 추가

1. .NET Core CLI를 사용 하 여 Microsoft.ML, **MICROSOFT ml. OnnxRuntime** 및 **Microsoft** **Microsoft.ML** NuGet 패키지를 설치 합니다.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    이러한 패키지에는 .NET 응용 프로그램에서 ONNX 모델을 사용 하는 데 필요한 종속성이 포함 되어 있습니다. ML.NET는 예측을 위해 [Onnx 런타임을](https://github.com/Microsoft/onnxruntime) 사용 하는 API를 제공 합니다.

1. *Program.cs* 파일을 열고 `using` 맨 위에 다음 문을 추가 하 여 적절 한 패키지를 참조 합니다.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>ONNX 모델에 대 한 참조 추가

콘솔 응용 프로그램에서 ONNX 모델에 액세스 하는 방법은 빌드 출력 디렉터리에 추가 하는 것입니다.  MSBuild 공통 항목에 대해 자세히 알아보려면 [msbuild 가이드](/visualstudio/msbuild/common-msbuild-project-items)를 참조 하세요.

응용 프로그램에서 ONNX 모델 파일에 대 한 참조 추가

1. 응용 프로그램의 *AutoMLONNXConsoleApp* 루트 디렉터리에 onnx 모델을 복사 합니다.
1. *AutoMLONNXConsoleApp* 파일을 열고 노드 내에 다음 콘텐츠를 추가 합니다 `Project` .

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    이 경우 ONNX 모델 파일의 이름은 *automl-model. onnx* 입니다.

1. *Program.cs* 파일을 열고 클래스 내부에 다음 줄을 추가 합니다 `Program` .

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>MLContext 초기화

`Main`클래스의 메서드 내 `Program` 에서의 새 인스턴스를 만듭니다 [`MLContext`](xref:Microsoft.ML.MLContext) .

```csharp
MLContext mlContext = new MLContext();
```

[`MLContext`](xref:Microsoft.ML.MLContext)클래스는 모든 ML.NET 작업에 대 한 시작점 이며 초기화는 `mlContext` 모델 수명 주기 동안 공유할 수 있는 새 ML.NET 환경을 만듭니다. Entity Framework에서 개념적으로 DbContext 하는 것과 비슷합니다.

## <a name="define-the-model-data-schema"></a>모델 데이터 스키마 정의

모델에는 특정 형식의 입력 및 출력 데이터가 필요 합니다. ML.NET를 사용 하면 클래스를 통해 데이터 형식을 정의할 수 있습니다. 경우에 따라 형식이 무엇 인지 이미 알고 있을 수 있습니다. 데이터 형식을 모르는 경우 Netron와 같은 도구를 사용 하 여 ONNX 모델을 검사할 수 있습니다.

이 샘플에 사용 된 모델은 NYC TLC Taxi 여행 데이터 집합의 데이터를 사용 합니다. 다음은 데이터의 샘플을 볼 수 있습니다.

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3.75|CRD|15.5|
|VTS|1|1|480|2.72|CRD|10.0|
|VTS|1|1|1,680|7.8|CSH|26.5|

### <a name="inspect-the-onnx-model-optional"></a>ONNX 모델 검사 (선택 사항)

Netron와 같은 도구를 사용 하 여 모델의 입력 및 출력을 검사 합니다.

1. Netron를 엽니다.
1. 상단 메뉴 모음에서 **파일 > 열기** 를 선택 하 고 파일 브라우저를 사용 하 여 모델을 선택 합니다.
1. 모델이 열립니다. 예를 들어 *automl 모델. onnx* 모델의 구조는 다음과 같습니다.

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX 모델":::

1. 그래프의 맨 아래에 있는 마지막 노드 ( `variable_out1` 이 경우)를 선택 하 여 모델의 메타 데이터를 표시 합니다. 세로 막대의 입/출력에는 모델의 예상 입력, 출력 및 데이터 형식이 표시 됩니다. 이 정보를 사용 하 여 모델의 입력 및 출력 스키마를 정의 합니다.

### <a name="define-model-input-schema"></a>모델 입력 스키마 정의

`OnnxInput` *Program.cs* 파일 내에서 다음 속성을 사용 하 여 라는 새 클래스를 만듭니다.

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

각 속성은 데이터 집합의 열에 매핑됩니다. 특성을 사용 하 여 속성에 주석을 추가 합니다.

[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute)특성을 사용 하면 데이터에 대해 작업을 수행할 때 ML.NET에서 열을 참조 하는 방법을 지정할 수 있습니다. 예를 들어 `TripDistance` 속성이 표준 .net 명명 규칙을 따르는 경우에도 모델은로 알려진 열 또는 기능을 인식 `trip_distance` 합니다. 이 명명 불일치를 해결 하기 위해 [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 특성은 `TripDistance` 이름을 기준으로 속성을 열 또는 기능에 매핑합니다 `trip_distance` .
  
숫자 값의 경우 ML.NET는 값 형식에 대해서만 작동 [`Single`](xref:System.Single) 합니다. 그러나 일부 열의 원래 데이터 형식은 정수입니다. [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute)특성은 ONNX와 ML.NET 간에 형식을 매핑합니다.

데이터 특성에 대 한 자세한 내용은 [ML.NET load data guide](/dotnet/machine-learning/how-to-guides/load-data-ml-net)를 참조 하세요.

### <a name="define-model-output-schema"></a>모델 출력 스키마 정의

데이터를 처리 한 후에는 특정 형식의 출력을 생성 합니다. 데이터 출력 스키마를 정의 합니다. `OnnxOutput` *Program.cs* 파일 내에서 다음 속성을 사용 하 여 라는 새 클래스를 만듭니다.

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

와 마찬가지로 `OnnxInput` 특성을 사용 [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 하 여 출력을 `variable_out1` 보다 설명적인 이름으로 매핑합니다 `PredictedFare` .

## <a name="define-a-prediction-pipeline"></a>예측 파이프라인 정의

ML.NET의 파이프라인은 일반적으로 입력 데이터에서 작동 하 여 출력을 생성 하는 일련의 연결 된 변환입니다. 데이터 변환에 대해 자세히 알아보려면 [ML.NET 데이터 변환 가이드](/dotnet/machine-learning/resources/transforms)를 참조 하세요.

1. `GetPredictionPipeline`클래스 내에서 라는 새 메서드를 만듭니다. `Program`

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. 입력 및 출력 열의 이름을 정의 합니다. `GetPredictionPipeline` 메서드 내에 다음 코드를 추가합니다.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. 파이프라인을 정의 합니다. 는 [`IEstimator`](xref:Microsoft.ML.IEstimator%601) 파이프라인의 작업, 입력 및 출력 스키마에 대 한 청사진을 제공 합니다.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    이 경우 [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) 는 입력 및 출력 열의 이름과 ONNX 모델 파일에 대 한 경로를 사용 하는 파이프라인의 유일한 변환입니다.

1. 는 [`IEstimator`](xref:Microsoft.ML.IEstimator%601) 데이터에 적용할 작업 집합만 정의 합니다. 데이터에서 작동 하는 작업을 라고 [`ITransformer`](xref:Microsoft.ML.ITransformer) 합니다. 메서드를 사용 `Fit` 하 여에서 하나를 만듭니다 `onnxPredictionPipeline` .

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    메서드는를 [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A) [`IDataView`](xref:Microsoft.ML.IDataView) 입력으로 예상 하므로에서 작업을 수행할 수 있습니다. 는 [`IDataView`](xref:Microsoft.ML.IDataView) 테이블 형식을 사용 하 여 ML.NET에 데이터를 표시 하는 방법입니다. 이 경우 파이프라인은 예측에만 사용 되므로 빈를 제공 [`IDataView`](xref:Microsoft.ML.IDataView) 하 여 [`ITransformer`](xref:Microsoft.ML.ITransformer) 필요한 입력 및 출력 스키마 정보를 제공할 수 있습니다. [`ITransformer`](xref:Microsoft.ML.ITransformer)그러면 응용 프로그램에서 나중에 사용 하기 위해 적합 한가 반환 됩니다.

    > [!TIP]
    > 이 샘플에서는 파이프라인이 동일한 응용 프로그램 내에서 정의 되 고 사용 됩니다. 그러나 별도의 응용 프로그램을 사용 하 여 파이프라인을 정의 하 고 사용 하 여 예측을 수행 하는 것이 좋습니다. ML.NET에서는 파이프라인을 직렬화 하 고 다른 .NET 최종 사용자 응용 프로그램에서 추가로 사용할 수 있도록 저장할 수 있습니다. ML.NET는 데스크톱 응용 프로그램, 웹 서비스, WebAssembly 응용 프로그램 * 등의 다양 한 배포 대상을 지원 합니다. 파이프라인을 저장 하는 방법에 대 한 자세한 내용은 [학습 된 모델 저장 및 로드 가이드 ML.NET](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net)를 참조 하세요.
    >
    > * Weasembomis는 .NET Core 5 이상 에서만 지원 됩니다.

1. 메서드 내 `Main` 에서 `GetPredictionPipeline` 필수 매개 변수를 사용 하 여 메서드를 호출 합니다.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>모델을 사용하여 예측하기

이제 파이프라인이 있으므로 예측을 만드는 데 사용할 수 있습니다. ML.NET는 라는 단일 데이터 인스턴스에서 예측을 만들기 위한 편리한 API를 제공 [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) 합니다.

1. 메서드 내에서 `Main` [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) 메서드를 사용 하 여를 만듭니다 [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) .

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. 테스트 데이터 입력을 만듭니다.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. 메서드를 `predictionEngine` 사용 하 여 새 데이터를 기반으로 예측을 수행 하려면를 사용 합니다 `testInput` [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) .

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. 예측 결과를 콘솔에 출력 합니다.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. .NET Core CLI를 사용 하 여 응용 프로그램을 실행 합니다.

    ```dotnetcli
    dotnet run
    ```

    결과는 다음 출력과 유사 하 게 표시 됩니다.

    ```text
    Predicted Fare: 15.621523
    ```

ML.NET에서 예측을 만드는 방법에 대 한 자세한 내용은 [모델을 사용 하 여 예측 하기 가이드](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [모델을 ASP.NET Core 웹 API로 배포](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [서버를 사용 하지 않는 .NET Azure 함수로 모델 배포](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)