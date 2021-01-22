---
title: '자습서: Azure Data Factory - Form Recognizer를 사용하여 대량으로 양식 데이터 추출'
titleSuffix: Azure Cognitive Services
description: 대규모 문서 백로그를 디지털화하기 위해 Form Recognizer 모델의 학습 및 실행을 트리거하도록 Azure Data Factory 활동을 설정합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 1780aebc113fa68a9a89cfce9fd67c9b5911fc58
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606708"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>자습서: Azure Data Factory를 사용하여 데이터 대량 복사

이 자습서에서는 Azure 서비스를 사용하여 대량의 양식을 디지털 미디어에 수집하는 방법을 살펴보겠습니다. 이 자습서에서는 문서의 Azure Data Lake에서 Azure SQL 데이터베이스로 데이터 수집을 자동화하는 방법을 보여줍니다. 몇 번의 클릭으로 신속하게 모델을 학습하고 새 문서를 처리할 수 있습니다.

## <a name="business-need"></a>비즈니스 요구

대부분의 조직은 이제 다양한 형식(pdf, 이미지, 비디오)으로 된 데이터가 얼마나 가치 있는지 알고 있습니다. 이들 조직은 자산을 디지털화하는 모범 사례와 가장 비용 효율적인 방법을 찾고 있습니다.

또한 고객은 종종 자신들의 많은 클라이언트와 고객이 제공하는 다양한 유형의 양식을 가지고 있습니다. [빠른 시작](./quickstarts/client-library.md)과는 달리 이 자습서에서는 메타데이터 기반 접근 방식을 사용하여 새롭고 다양한 유형의 양식으로 모델을 자동으로 학습하는 방법을 보여줍니다. 지정된 양식 유형에 대한 기존 모델이 없는 경우 시스템에서 모델을 만들고 모델 ID를 제공합니다. 

양식에서 데이터를 추출하여 기존 운영 체제 및 데이터 웨어하우스와 결합함으로써 기업은 인사이트를 얻고 고객 및 비즈니스 사용자에게 가치를 제공할 수 있습니다.

Azure Form Recognizer를 사용하면 조직에서 데이터를 활용하고 프로세스(청구서 결제, 세금 처리 등)를 자동화하고 비용과 시간을 절약하고 데이터 정확성이 높아집니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Azure Data Lake를 설정하여 양식 저장
> * Azure 데이터베이스를 사용하여 매개 변수화 테이블 만들기
> * Azure Key Vault를 사용하여 중요한 자격 증명 저장
> * Databricks Notebook에서 Form Recognizer 모델 학습
> * Databricks Notebook을 사용하여 양식 데이터 추출
> * Azure Data Factory로 양식 학습 및 추출 자동화

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer 리소스 만들기"  target="_blank">Form Recognizer 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 선택합니다.
    * 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 동일한 형식의 5개 이상 양식으로 구성된 세트. 이 워크플로는 대규모 문서 집합을 지원하는 것이 가장 좋습니다. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [학습 데이터 세트 빌드](./build-training-data-set.md)를 참조하세요. 이 자습서에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2128080)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다.

## <a name="project-architecture"></a>프로젝트 아키텍처 

이 프로젝트는 Azure Data Lake 스토리지 계정의 문서에서 데이터를 학습, 분석 및 추출하는 Python Notebook을 트리거하는 Azure Data Factory 파이프라인 세트를 구성합니다.

Form Recognizer REST AP에는 입력으로 일부 매개 변수가 필요합니다. 보안상의 이유로 이러한 매개 변수 중 일부는 Azure Key Vault에 저장되고 스토리지 Blob 폴더 이름과 같이 덜 중요한 다른 매개 변수는 Azure SQL 데이터베이스의 매개 변수화 테이블에 저장됩니다.

분석할 양식 유형의 경우 데이터 엔지니어 또는 데이터 과학자가 매개 변수 테이블의 행을 채웁니다. 그런 다음, Azure Data Factory를 사용하여 검색된 양식 유형 목록을 반복하고 관련 매개 변수를 Databricks Notebook에 전달하여 Form Recognizer 모델을 학습하거나 다시 학습합니다. 여기에서 Azure 함수를 사용할 수도 있습니다.

그런 다음, Azure Databricks Notebook은 학습된 모델을 사용하여 양식 데이터를 추출하고 해당 날짜를 Azure SQL 데이터베이스로 내보냅니다.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="프로젝트 아키텍처":::


## <a name="set-up-azure-data-lake"></a>Azure Data Lake 설정

양식 백로그는 온-프레미스 환경 또는 (s)FTP 서버에 있을 수 있습니다. 이 자습서는 Azure Data Lake Gen 2 스토리지 계정의 양식을 사용합니다. Azure Data Factory, Azure Storage Explorer 또는 AzCopy를 사용하여 파일을 전송할 수 있습니다. 학습 데이터 세트와 채점 데이터 세트는 서로 다른 컨테이너에 있을 수 있지만, 모든 양식 유형에 대한 학습 데이터 세트는 동일한 컨테이너에 있어야 합니다(다른 폴더에 있을 수 있음).

새 Data Lake를 만들려면 [Azure Data Lake Storage Gen2에서 사용할 스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/blobs/create-data-lake-storage-account)의 지침을 따르세요.

## <a name="create-a-parameterization-table"></a>매개 변수화 테이블 만들기

다음으로, Azure SQL Database에서 테이블을 만들겠습니다. 이 테이블에는 Form Recognizer REST API에 필요한 중요하지 않은 데이터가 포함됩니다. 데이터 세트에 새로운 유형의 양식이 있을 때마다 이 테이블에 새 레코드를 삽입하고 나중에 구현할 학습 및 채점 파이프라인을 트리거하겠습니다.

다음 필드가 테이블에서 사용됩니다.

* **form_description**: 이 필드는 학습의 일부로 필요하지 않습니다. 모델을 학습하는 양식 유형에 대한 설명을 제공합니다(예: "클라이언트 A 양식", "호텔 B 양식").
* **training_container_name**: 이 필드는 학습 데이터 세트를 저장한 스토리지 계정 컨테이너 이름입니다. **scoring_container_name** 과 동일한 컨테이너가 될 수 있습니다.
* **training_blob_root_folder**: 모델 학습을 위해 파일을 저장할 스토리지 계정 내의 폴더입니다.
* **scoring_container_name**: 이 필드는 키 값 쌍을 추출할 파일을 저장한 스토리지 계정 컨테이너 이름입니다. **training_container_name** 과 동일한 컨테이너가 될 수 있습니다.
* **scoring_input_blob_folder**: 데이터를 추출할 파일을 저장할 스토리지 계정의 폴더입니다.
* **model_id**: 다시 학습하려는 모델의 ID입니다. 첫 번째 실행의 경우, 값을 -1로 설정해야 학습 Notebook이 학습할 새 사용자 지정 모델을 만들 수 있습니다. 학습 Notebook은 새로 만든 모델 ID를 Azure Data Factory 인스턴스에 반환하고, 저장 프로시저 작업을 사용하여 Azure SQL 데이터베이스에서 이 값을 업데이트하겠습니다.

  새 양식 유형을 수집하려면 모델을 학습하기 전에 모델 ID를 -1로 수동으로 다시 설정해야 합니다.

* **file_type**: 지원되는 형식은 `application/pdf`, `image/jpeg`, `image/png` 및 `image/tif`입니다.

  다른 파일 유형의 양식이 있는 경우 새 양식 유형을 학습할 때 이 값과 **model_id** 를 변경해야 합니다.
* **form_batch_group_id**: 시간이 지남에 따라 동일한 모델에 대해 학습한 여러 양식 유형이 있을 수 있습니다. **form_batch_group_id** 를 사용하면 특정 모델을 사용하여 학습한 모든 양식 유형을 지정할 수 있습니다.

### <a name="create-the-table"></a>테이블 만들기

[Azure SQL Database를 만든 다음,](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase), [쿼리 편집기](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal)에서 다음 SQL 스크립트를 실행하여 필요한 테이블을 만듭니다.

```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

다음 스크립트를 실행하여 **model_id** 가 학습된 후 자동으로 업데이트하는 절차를 만듭니다.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Azure Key Vault를 사용하여 중요한 자격 증명 저장

보안을 위해 Azure SQL 데이터베이스의 매개 변수화 테이블에는 중요한 특정 정보를 저장하지 않습니다. 중요한 매개 변수를 Azure Key Vault 비밀로 저장하겠습니다.

### <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

[Key Vault 리소스를 만듭니다](https://ms.portal.azure.com/#create/Microsoft.KeyVault). 그런 다음, Key Vault 리소스로 이동하여 **설정** 섹션에서 **비밀** 을 선택하여 매개 변수를 추가합니다.

새 창이 나타나면 **생성/가져오기** 를 선택합니다. 매개 변수 이름과 값을 입력하고 만들기를 클릭합니다. 다음 매개 변수에 대해 이를 수행합니다.

* **CognitiveServiceEndpoint**: Form Recognizer API의 엔드포인트 URL입니다.
* **CognitiveServiceSubscriptionKey**: Form Recognizer 서비스에 대한 액세스 키입니다. 
* **StorageAccountName**: 키-값 쌍을 추출하려는 학습 데이터 세트 및 양식이 저장되는 스토리지 계정입니다. 다른 계정에 있는 경우 각 계정 이름을 별도의 비밀로 입력합니다. 학습 데이터 세트는 모든 양식 유형에 대해 동일한 컨테이너에 있어야 하지만, 다른 폴더에 있을 수 있습니다.
* **StorageAccountSasKey**: 스토리지 계정의 SAS(공유 액세스 서명)입니다. SAS URL을 검색하려면 스토리지 리소스로 이동하여 **Storage Explorer** 탭을 선택합니다. 컨테이너로 이동하고 마우스 오른쪽 단추를 클릭한 후 **공유 액세스 서명 가져오기** 를 선택합니다. 스토리지 계정 자체가 아니라 컨테이너에 대한 SAS를 가져오는 것이 중요합니다. **읽기** 권한과 **목록 사용** 권한이 선택되어 있는지 확인하고 **만들기** 를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Databricks Notebook에서 Form Recognizer 모델 학습

Azure Databricks를 사용하여 Form Recognizer 서비스와 상호 작용하는 Python 코드를 저장하고 실행합니다.

### <a name="create-a-notebook-in-databricks"></a>Databricks에서 Notebook 만들기

Azure Portal에서 [Azure Databricks 리소스를 만듭니다](https://ms.portal.azure.com/#create/Microsoft.Databricks). 리소스를 만든 후 리소스로 이동하여 작업 영역을 시작합니다.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Azure Key Vault에서 지원하는 비밀 범위 만들기

위에서 만든 Azure Key Vault의 비밀을 참조하려면 Databricks에서 비밀 범위를 만들어야 합니다. [Azure Key Vault에서 지원하는 비밀 범위 만들기](https://docs.microsoft.com/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)의 단계를 따릅니다.

### <a name="create-a-databricks-cluster"></a>Databricks 클러스터 만들기

클러스터는 Databricks 계산 리소스의 컬렉션입니다. 클러스터를 만들려면:

1. 사이드바에서 **클러스터** 아이콘을 클릭합니다.
1. **클러스터** 페이지에서 **클러스터 만들기** 를 클릭합니다.
1. **클러스터 만들기** 페이지에서 클러스터 이름을 지정하고 Databricks Runtime 버전 드롭다운에서 **7.2(Scala 2.12, Spark 3.0.0)** 를 선택합니다.
1. **클러스터 만들기** 를 클릭합니다.

### <a name="write-a-settings-notebook"></a>설정 Notebook 작성

이제 Python Notebook을 추가할 준비가 되었습니다. 먼저 **설정** 이라는 Notebook을 만듭니다. 이 Notebook은 매개 변수화 테이블의 값을 스크립트의 변수에 할당합니다. 값은 나중에 Azure Data Factory에서 매개 변수로 전달됩니다. 또한 Key Vault에 있는 비밀의 값을 변수에 할당하겠습니다. 

1. **설정** Notebook을 만들려면 **작업 영역** 단추를 클릭하고, 새로 만들기 탭에서 드롭다운 목록을 클릭한 다음, **만들기** 를 선택하고 **Notebook** 을 선택합니다.
1. 팝업 창에서 Notebook에 제공하려는 이름을 입력하고 **Python** 을 기본 언어로 선택합니다. Databricks 클러스터를 선택하고 **만들기** 를 선택합니다.
1. 첫 번째 노트북 셀에서 Azure Data Factory에서 전달된 매개 변수를 검색합니다.

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. 두 번째 셀에서는 Key Vault에서 비밀을 검색하여 변수에 할당합니다.

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>학습 Notebook 작성

이제 **설정** Notebook을 완료했으므로 모델을 학습하는 Notebook을 만들 수 있습니다. 위에서 언급한 것처럼 Azure Data Lake Gen 2 스토리지 계정의 폴더(**training_blob_root_folder**)에 저장된 파일을 사용합니다. 폴더 이름이 변수로 전달되었습니다. 각 양식 유형 집합은 동일한 폴더에 있으며, 매개 변수 테이블을 반복하면서 모든 양식 유형을 사용하여 모델을 학습하겠습니다. 

1. **TrainFormRecognizer** 라는 새 Notebook을 만듭니다. 
1. 첫 번째 셀에서 설정 Notebook을 실행합니다.

    ```python
    %run "./Settings"
    ```

1. 다음 셀에서 **설정** 파일의 변수를 할당하고 [REST 빠른 시작](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20)의 코드를 적용하여 각 양식 유형에 대해 모델을 동적으로 학습합니다.

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. 학습 프로세스의 마지막 단계는 json 형식으로 학습 결과를 가져오는 것입니다.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-using-a-notebook"></a>Notebook을 사용하여 양식 데이터 추출

### <a name="mount-the-azure-data-lake-storage"></a>Azure Data Lake Storage 탑재

다음 단계는 학습된 모델을 사용하여 다양한 양식을 채점하는 것입니다. Databricks에 Azure Data Lake Storage 계정을 탑재하고 수집 프로세스 중에 탑재를 참조하겠습니다.

학습 단계와 마찬가지로 Azure Data Factory를 사용하여 양식에서 키-값 쌍의 추출하겠습니다. 매개 변수 테이블에 지정된 폴더의 양식을 반복하겠습니다.

1. Databricks에 스토리지 계정을 탑재할 Notebook을 만들어 보겠습니다. 이를 **MountDataLake** 라고 하겠습니다. 
1. 먼저 **설정** Notebook을 호출해야 합니다.

    ```python
    %run "./Settings"
    ```

1. 두 번째 셀에서 Key Vault 비밀에서 검색할 중요한 매개 변수에 대한 변수를 정의하겠습니다.

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. 다음으로, 이전에 탑재된 경우 스토리지 계정을 탑재 해제하겠습니다.

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. 마지막으로 스토리지 계정을 탑재하겠습니다.


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > 이 경우 학습 스토리지 계정만 탑재했습니다.&mdash;학습 파일과 키-값 쌍을 추출하려는 파일은 동일한 스토리지 계정에 있습니다. 채점 스토리지 계정과 학습 스토리지 계정이 다른 경우 여기에 두 스토리지 계정을 모두 탑재해야 합니다. 

### <a name="write-the-scoring-notebook"></a>채점 Notebook 작성

이제 채점 Notebook을 만들 수 있습니다. 학습 Notebook과 마찬가지로 방금 탑재한 Azure Data Lake Storage 계정의 폴더에 저장된 파일을 사용하겠습니다. 폴더 이름이 변수로 전달됩니다. 지정된 폴더의 모든 양식을 반복하고 해당 양식에서 키-값 쌍을 추출하겠습니다. 

1. 새 Notebook을 만들고 이를 **ScoreFormRecognizer** 라고 하겠습니다. 
1. **설정** 및 **MountDataLake** Notebook을 실행합니다.

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. 그런 다음, [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API를 호출하는 다음 코드를 추가합니다.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. 다음 셀에서 키-값 쌍 추출 결과를 가져오겠습니다. 이 셀은 결과를 출력합니다. JSON 형식의 결과를 Azure SQL Database 또는 Cosmos DB로 추가 처리할 것이므로 결과를 .json 파일에 작성하겠습니다. 출력 파일 이름은 "_output.json"과 연결된 채점된 파일의 이름이 됩니다. 파일은 원본 파일과 동일한 폴더에 저장됩니다.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. 새 셀에서 파일 쓰기 프로시저를 수행합니다.

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>Azure Data Factory를 사용하여 학습 및 채점 자동화

이제 ADF(Azure Data Factory) 서비스를 설정하여 학습 및 채점 프로세스를 자동화하는 단계만 남았습니다. 먼저 [데이터 팩터리 만들기](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)의 단계를 따릅니다. ADF 리소스를 생성한 후 3개의 파이프라인을 만들어야 하는 데, 1개는 학습용이고 2개는 채점용입니다(아래 설명 참조).

### <a name="training-pipeline"></a>학습 파이프라인

학습 파이프라인의 첫 번째 작업은 Azure SQL 데이터베이스에서 매개 변수화 테이블의 값을 읽고 반환하는 조회입니다. 모든 학습 데이터 세트는 동일한 스토리지 계정 및 컨테이너(잠재적으로 다른 폴더)에 있으므로 조회 작업 설정에서 기본값인 **첫 번째 행만** 속성을 유지하겠습니다. 모델을 학습시킬 각 양식 유형에 대해 **training_blob_root_folder** 에 있는 모든 파일을 사용하여 모델을 학습하겠습니다.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="데이터 팩터리의 학습 파이프라인":::

저장 프로시저는 **model_id** 및 **form_batch_group_id** 의 두 매개 변수를 사용합니다. Databricks Notebook에서 모델 ID를 반환하는 코드는 `dbutils.notebook.exit(model_id)`이며, 데이터 팩터리에서 저장 프로시저 작업의 코드를 읽는 코드는 `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id`입니다.

### <a name="scoring-pipelines"></a>채점 파이프라인

키-값 쌍을 추출하기 위해 매개 변수화 테이블의 모든 폴더를 검사하고 각 폴더에 대해 해당 폴더에 있는 모든 파일의 키-값 쌍을 추출하겠습니다. 현재 ADF는 중첩된 ForEach 루프를 지원하지 않습니다. 대신 두 개의 파이프라인을 만들겠습니다. 첫 번째 파이프라인이 매개 변수화 테이블에서 조회하고 폴더 목록을 두 번째 파이프라인에 매개 변수로 전달합니다.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="데이터 팩터리의 첫 번째 학습 파이프라인":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="데이터 팩터리의 첫 번째 학습 파이프라인, 세부 정보":::

두 번째 파이프라인은 GetMeta 활동을 사용하여 폴더에 있는 파일 목록을 가져오고 이를 Databricks Notebook에 매개 변수로 전달합니다.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="데이터 팩터리의 두 번째 학습 파이프라인":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="데이터 팩터리의 두 번째 학습 파이프라인, 세부 정보":::

### <a name="specify-a-degree-of-parallelism"></a>병렬 처리 수준 지정

학습 및 채점 파이프라인에서 여러 양식을 동시에 처리하는 병렬 처리 수준을 지정할 수 있습니다.

ADF 파이프라인의 병렬 처리 수준을 설정하려면 다음을 수행합니다.

* Foreach 활동을 선택합니다.
* **순차** 확인란을 선택 취소합니다.
* **일괄 처리 횟수** 텍스트 상자에 병렬 처리 수준을 설정합니다. 채점의 최대 일괄 처리 횟수는 15를 권장합니다.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="ADF의 채점 작업을 위한 병렬 처리 구성":::

## <a name="how-to-use"></a>사용 방법

이제 양식 백로그를 디지털화하고 그 위에 몇 가지 분석을 실행할 수 있는 자동화된 파이프라인이 생겼습니다. 기존 스토리지 폴더에 익숙한 유형의 새 양식을 추가하는 경우, 채점 파이프라인을 다시 실행하면 새 양식에 대한 출력 파일을 포함하여 모든 출력 파일이 업데이트됩니다. 

새로운 유형의 새 양식을 추가하는 경우 학습 데이터 세트도 적절한 컨테이너에 업로드해야 합니다. 그런 다음, 새 문서의 위치와 해당 학습 데이터 세트를 입력하여 매개 변수화 테이블에 새 행을 추가합니다. 이러한 양식에 대해 새 모델을 학습해야 함을 나타내려면 **model_ID** 에 -1 값을 입력합니다. 그런 다음, ADF에서 학습 파이프라인을 실행합니다. 이렇게 하면 테이블에서 읽고 모델을 학습하고 테이블의 모델 ID를 덮어씁니다. 그런 다음, 채점 파이프라인을 호출하여 출력 파일 작성을 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 대규모 파일 백로그를 디지털화하기 위해 Form Recognizer 모델의 학습 및 실행을 트리거하도록 Azure Data Factory 파이프라인을 설정합니다. 다음으로, Form Recognizer API를 탐색하여 다른 작업을 수행할 수 있는지 확인합니다.

* [Form Recognizer REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
