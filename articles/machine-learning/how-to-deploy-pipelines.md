---
title: ML 파이프라인 게시
titleSuffix: Azure Machine Learning
description: 기계 학습 파이프라인과 Python 용 Azure Machine Learning SDK를 사용 하 여 기계 학습 워크플로를 실행 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: fd5c0905b4a5d050eaf35456353122fd26f8cbf7
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445271"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Machine learning 파이프라인 게시 및 추적



이 문서에서는 동료 또는 고객과 machine learning 파이프라인을 공유 하는 방법을 보여 줍니다.

Machine learning 파이프라인은 기계 학습 작업에 다시 사용할 수 있는 워크플로입니다. 파이프라인의 장점 중 하나는 공동 작업입니다. 또한 새 버전에서 작업 하는 동안 고객이 현재 모델을 사용할 수 있도록 파이프라인의 버전을 지정할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* 모든 파이프라인 리소스를 저장할 [Azure Machine Learning 작업 영역](how-to-manage-workspace.md) 을 만듭니다.

* Azure Machine Learning SDK를 설치 하거나 SDK가 이미 설치 된 [Azure Machine Learning 계산 인스턴스](concept-compute-instance.md) 를 사용 하도록 [개발 환경을 구성](how-to-configure-environment.md) 합니다.

* 다음 [자습서: 일괄 처리 점수 매기기를 위한 Azure Machine Learning 파이프라인 빌드](tutorial-pipeline-batch-scoring-classification.md)와 같은 기계 학습 파이프라인을 만들고 실행 합니다. 기타 옵션은 [AZURE MACHINE LEARNING SDK를 사용 하 여 machine learning 파이프라인 만들기 및 실행](how-to-create-your-first-pipeline.md) 을 참조 하세요.

## <a name="publish-a-pipeline"></a>파이프라인 게시

파이프라인을 실행 한 후에는 다른 입력으로 실행 되도록 파이프라인을 게시할 수 있습니다. 매개 변수를 허용 하기 위해 이미 게시 된 파이프라인의 REST 끝점의 경우에는 `PipelineParameter` 다른 인수에 대해 개체를 사용 하도록 파이프라인을 구성 해야 합니다.

1. 파이프라인 매개 변수를 만들려면 기본값으로 [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?preserve-view=true&view=azure-ml-py) 개체를 사용합니다.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. 다음과 같이 이 `PipelineParameter` 개체를 파이프라인의 단계에 대한 매개 변수로 추가합니다.

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. 이 파이프라인을 게시하면 호출 시 매개 변수를 허용합니다.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>게시된 파이프라인 실행

게시된 모든 파이프라인에는 REST 엔드포인트가 있습니다. 파이프라인 끝점을 사용 하 여 Python이 아닌 클라이언트를 비롯 한 모든 외부 시스템에서 파이프라인 실행을 트리거할 수 있습니다. 이 엔드포인트는 일괄 처리 채점 및 다시 학습 시나리오에서 “관리되는 반복 가능성”을 지원합니다.

> [!IMPORTANT]
> RBAC (역할 기반 액세스 제어)를 사용 하 여 파이프라인에 대 한 액세스를 관리 하는 경우 [파이프라인 시나리오에 대 한 사용 권한을 설정 합니다 (학습 또는 점수 매기기)](how-to-assign-roles.md#common-scenarios).

이전 파이프라인의 실행을 호출 하려면 Azure Active Directory 인증 헤더 토큰이 필요 합니다. 이러한 토큰을 가져오는 방법은 [Azurecliauthentication 클래스](/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?preserve-view=true&view=azure-ml-py) 참조 및 [Azure Machine Learning 노트북의 인증](https://aka.ms/pl-restep-auth) 에 설명 되어 있습니다.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

`json`POST 요청에 대 한 인수에는 키의 경우 `ParameterAssignments` 파이프라인 매개 변수 및 해당 값이 포함 된 사전이 포함 되어야 합니다. 또한 인수에는 `json` 다음 키가 포함 될 수 있습니다.

| 키 | Description |
| --- | --- | 
| `ExperimentName` | 이 끝점과 연결 된 실험의 이름입니다. |
| `Description` | 끝점을 설명 하는 자유형 텍스트 | 
| `Tags` | 요청에 레이블을 지정 하 고 주석을 추가 하는 데 사용할 수 있는 자유형 키-값 쌍  |
| `DataSetDefinitionValueAssignments` | 사전 학습 없이 데이터 집합을 변경 하는 데 사용 되는 사전 (아래 설명 참조) | 
| `DataPathAssignments` | 사전 학습 없이 datapaths를 변경 하는 데 사용 되는 사전 (아래 설명 참조) | 

### <a name="run-a-published-pipeline-using-c"></a>C를 사용 하 여 게시 된 파이프라인 실행 # 

다음 코드에서는 c #에서 비동기적으로 파이프라인을 호출 하는 방법을 보여 줍니다. 부분 코드 조각은 호출 구조만 보여 주고 Microsoft 샘플의 일부가 아닙니다. 전체 클래스 또는 오류 처리는 표시 되지 않습니다. 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>Java를 사용 하 여 게시 된 파이프라인 실행

다음 코드에서는 인증을 요구 하는 파이프라인에 대 한 호출을 보여 줍니다 ( [Azure Machine Learning 리소스 및 워크플로에 대 한 인증 설정](how-to-setup-authentication.md)참조). 파이프라인을 공개적으로 배포 하는 경우를 생성 하는 호출이 필요 하지 않습니다 `authKey` . 부분 코드 조각은 Java 클래스 및 예외 처리 상용구를 표시 하지 않습니다. 이 코드는 `Optional.flatMap` 빈를 반환할 수 있는 함수를 연결 하는 데를 사용 합니다 `Optional` . 를 사용 하면 `flatMap` 코드를 단축 하 고 명확 하 게 `getRequestBody()` 할 수 있지만 숨깁니다 예외를 허용 합니다.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>다시 학습 없이 데이터 집합 및 datapaths 변경

다른 데이터 집합 및 datapaths에 대해 학습 하 고 유추 하려고 할 수 있습니다. 예를 들어, 더 작은 데이터 집합을 학습 하 고 전체 데이터 집합에 대 한 유추를 수행할 수 있습니다. 요청의 인수에서 키를 사용 하 여 데이터 집합을 전환 `DataSetDefinitionValueAssignments` `json` 합니다. Datapaths를로 전환 `DataPathAssignments` 합니다. 두 방법의 기술은 비슷합니다.

1. 파이프라인 정의 스크립트에서 `PipelineParameter` 데이터 집합에 대 한를 만듭니다. `DatasetConsumptionConfig`에서 또는를 만듭니다 `DataPath` `PipelineParameter` .

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. ML 스크립트에서 다음을 사용 하 여 동적으로 지정 된 데이터 집합에 액세스 합니다 `Run.get_context().input_datasets` .

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    ML 스크립트는 () `DatasetConsumptionConfig` 의 값이 아니라 ()에 대해 지정 된 값에 액세스 합니다 `tabular_dataset` `PipelineParameter` `tabular_ds_param` .

1. 파이프라인 정의 스크립트에서를 매개 변수로로 설정 합니다 `DatasetConsumptionConfig` `PipelineScriptStep` .

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. 추론 REST 호출에서 데이터 집합을 동적으로 전환 하려면 다음을 사용 합니다 `DataSetDefinitionValueAssignments` .
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

노트북 [보여주는 데이터 집합 및 PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) 및 [보여주는 데이터 경로 및 PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) 에는이 기술의 전체 예제가 포함 되어 있습니다.

## <a name="create-a-versioned-pipeline-endpoint"></a>버전 지정 파이프라인 끝점 만들기

게시 된 여러 파이프라인이 있는 파이프라인 끝점을 만들 수 있습니다. 이 기법은 ML 파이프라인을 반복 하 고 업데이트할 때 고정 REST 끝점을 제공 합니다.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>파이프라인 끝점에 작업 제출

파이프라인 끝점의 기본 버전에 작업을 제출할 수 있습니다.

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

특정 버전으로 작업을 제출할 수도 있습니다.

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

REST API를 사용 하 여 동일한 작업을 수행할 수 있습니다.

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>스튜디오에서 게시 된 파이프라인 사용

또한 스튜디오에서 게시 된 파이프라인을 실행할 수 있습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다.

1. [작업 영역을 봅니다](how-to-manage-workspace.md#view).

1. 왼쪽에서 **끝점** 을 선택 합니다.

1. 위쪽에서 **파이프라인 끝점** 을 선택 합니다.
 ![machine learning 게시 된 파이프라인 목록](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. 파이프라인 끝점의 이전 실행 결과를 실행, 사용 또는 검토 하려면 특정 파이프라인을 선택 합니다.

## <a name="disable-a-published-pipeline"></a>게시 된 파이프라인 사용 안 함

게시 된 파이프라인 목록에서 파이프라인을 숨기려면 스튜디오 또는 SDK에서 파이프라인을 사용 하지 않도록 설정 합니다.

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

에서 다시 사용 하도록 설정할 수 있습니다 `p.enable()` . 자세한 내용은 [PublishedPipeline 클래스](/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?preserve-view=true&view=azure-ml-py) 참조를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [GitHub의 Jupyter 노트북](https://aka.ms/aml-pipeline-readme)을 사용하여 기계 학습 파이프라인을 추가로 살펴봅니다.
- [Azureml-파이프라인-코어](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) 패키지 및 azureml- [파이프라인 단계](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py) 패키지에 대 한 SDK 참조 도움말을 참조 하세요.
- 파이프라인 디버깅 및 문제 해결에 대 한 팁은 [방법을](how-to-debug-pipelines.md) 참조 하세요.