---
title: '빠른 시작: Python용 Form Recognizer 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Python용 Form Recognizer 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.author: pafarley
ms.openlocfilehash: e5debf66b91ebd73bb4a4972a907ef7a283f0044
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965914"
---
[참조 설명서](https://docs.microsoft.com/python/api/overview/azure/formrecognizer) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [패키지(PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 학습 데이터 세트가 포함된 Azure Storage Blob. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 세트 빌드](../../build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다.
* [Python 2.7 또는 3.5 이상](https://www.python.org/)

## <a name="setting-up"></a>설치

### <a name="create-a-form-recognizer-azure-resource"></a>Form Recognizer Azure 리소스 만들기

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>환경 변수 만들기

[!INCLUDE [environment-variables](../environment-variables.md)]


### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 Python 애플리케이션을 새로 만듭니다. 그런 다음, 다음 라이브러리를 가져옵니다.

```python
import os
import azure.ai.formrecognizer
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import ResourceNotFoundError
```

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 해당 변수에 액세스하려면 편집기, IDE 또는 셸을 닫고 다시 열어야 합니다.

```python
endpoint = os.environ["FORM_RECOGNIZER_ENDPOINT"]
key = os.environ["FORM_RECOGNIZER_KEY"]
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install azure-ai-formrecognizer
```

<!-- 
tbd object model
-->

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 Form Recognizer 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [양식 콘텐츠 인식](#recognize-form-content)
* [영수증 확인](#recognize-receipts)
* [사용자 지정 모델 학습](#train-a-custom-model)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 관리](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>클라이언트 인증

여기서는 위에서 정의한 구독 변수를 사용하여 클라이언트 개체 두 개를 인증합니다. **AzureKeyCredential** 개체를 사용하면 필요한 경우 새 클라이언트 개체를 만들지 않고 API 키를 업데이트할 수 있습니다.

```python
form_recognizer_client = FormRecognizerClient(endpoint=endpoint, credential=AzureKeyCredential(key))

form_training_client = FormTrainingClient(endpoint, AzureKeyCredential(key))
```

## <a name="define-variables"></a>변수 정의

> [!NOTE]
> 이 가이드의 코드 조각은 URL을 통해 액세스되는 원격 양식을 사용합니다. 로컬 양식 문서를 대신 처리하려는 경우 [참조 설명서](https://docs.microsoft.com/python/api/overview/azure/formrecognizer) 및 [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)의 관련 메서드를 참조하세요.

또한 학습 및 테스트 데이터에 대한 참조를 URL에 추가해야 합니다.
* 사용자 지정 모델 학습 데이터에 대한 SAS URL를 검색하려면 Microsoft Azure Storage Explorer를 열고, 마우스 오른쪽 단추로 컨테이너를 클릭하고, **공유 액세스 서명 가져오기**를 선택합니다. **읽기** 권한과 **목록 사용** 권한이 선택되어 있는지 확인하고 **만들기**를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.
* 테스트할 양식의 URL을 가져오려면 위의 단계를 따라 Blob Storage에 있는 개별 문서의 SAS URL을 가져오면 됩니다. 또는 다른 위치에 있는 문서의 URL을 가져옵니다.
* 위의 방법을 사용하여 영수증 이미지의 URL을 가져오거나 제공된 샘플 이미지 URL을 사용할 수 있습니다.

```python
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"
formUrl = "<SAS-URL-of-a-form-in-blob-storage>"
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"
```

## <a name="recognize-form-content"></a>양식 콘텐츠 인식

Form Recognizer를 사용하면 모델을 학습시킬 필요 없이 문서의 표, 줄 및 단어를 인식할 수 있습니다.

지정된 URL에서 파일의 콘텐츠를 인식하려면 **begin_recognize_content** 메서드를 사용합니다.

```Python
poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
contents = poller.result()
```

그러면 제출된 문서의 각 페이지당 하나씩 **FormPage** 개체 컬렉션이 반환됩니다. 다음 코드는 이러한 개체에서 반복되고 추출된 키/값 쌍 및 테이블 데이터를 출력합니다.

```python
for idx, content in enumerate(contents):
    print("----Recognizing content from page #{}----".format(idx))
    print("Has width: {} and height: {}, measured with unit: {}".format(
        content.width,
        content.height,
        content.unit
    ))
    for table_idx, table in enumerate(content.tables):
        print("Table # {} has {} rows and {} columns".format(table_idx, table.row_count, table.column_count))
        for cell in table.cells:
            print("...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                cell.row_index,
                cell.column_index,
                cell.text,
                format_bounding_box(cell.bounding_box)
            ))
    for line_idx, line in enumerate(content.lines):
        print("Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
            line_idx,
            len(line.words),
            line.text,
            format_bounding_box(line.bounding_box)
        ))
    print("----------------------------------------")
```

위의 코드는 `format_bounding_box` 도우미 함수를 사용하여 경계 상자의 좌표를 단순화합니다. 별도로 정의합니다.

```python
def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])
```

## <a name="recognize-receipts"></a>영수증 확인

이 섹션에서는 사전 학습된 영수증 모델을 사용하여 미국 영수증의 공통 필드를 인식 및 추출하는 방법을 보여 줍니다. URL을 통해 영수증을 확인하려면 **begin_recognize_receipts_from_url** 메서드를 사용합니다. 

```python
poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
receipts = poller.result()
```

그러면 제출된 문서의 각 페이지당 하나씩 **RecognizedReceipt** 개체 컬렉션이 반환됩니다. 다음 코드 블록은 기본 영수증 정보를 콘솔에 출력합니다.

```python
for idx, receipt in enumerate(receipts):
    print("--------Recognizing receipt #{}--------".format(idx))
    receipt_type = receipt.fields.get("ReceiptType")
    if receipt_type:
        print("Receipt Type: {} has confidence: {}".format(receipt_type.value, receipt_type.confidence))
    merchant_name = receipt.fields.get("MerchantName")
    if merchant_name:
        print("Merchant Name: {} has confidence: {}".format(merchant_name.value, merchant_name.confidence))
    transaction_date = receipt.fields.get("TransactionDate")
    if transaction_date:
        print("Transaction Date: {} has confidence: {}".format(transaction_date.value, transaction_date.confidence))
```

코드의 다음 블록은 영수증에서 검색된 개별 항목에서 반복되고 콘솔에 세부 정보를 출력합니다.


```python
    print("Receipt items:")
    for idx, item in enumerate(receipt.fields.get("Items").value):
        print("...Item #{}".format(idx))
        item_name = item.value.get("Name")
        if item_name:
            print("......Item Name: {} has confidence: {}".format(item_name.value, item_name.confidence))
        item_quantity = item.value.get("Quantity")
        if item_quantity:
            print("......Item Quantity: {} has confidence: {}".format(item_quantity.value, item_quantity.confidence))
        item_price = item.value.get("Price")
        if item_price:
            print("......Individual Item Price: {} has confidence: {}".format(item_price.value, item_price.confidence))
        item_total_price = item.value.get("TotalPrice")
        if item_total_price:
            print("......Total Item Price: {} has confidence: {}".format(item_total_price.value, item_total_price.confidence))
```

마지막으로, 마지막 코드 블록은 주요 영수증 세부 정보의 나머지 부분을 출력합니다.

```python
    subtotal = receipt.fields.get("Subtotal")
    if subtotal:
        print("Subtotal: {} has confidence: {}".format(subtotal.value, subtotal.confidence))
    tax = receipt.fields.get("Tax")
    if tax:
        print("Tax: {} has confidence: {}".format(tax.value, tax.confidence))
    tip = receipt.fields.get("Tip")
    if tip:
        print("Tip: {} has confidence: {}".format(tip.value, tip.confidence))
    total = receipt.fields.get("Total")
    if total:
        print("Total: {} has confidence: {}".format(total.value, total.confidence))
    print("--------------------------------------")
```


## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

이 섹션에서는 사용자 고유의 데이터로 모델을 학습시키는 방법을 보여 줍니다. 학습된 모델은 원본 양식 문서의 키/값 관계가 포함된 구조적 데이터를 출력할 수 있습니다. 모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

> [!NOTE]
> [Form Recognizer 샘플 레이블 지정 도구](../../quickstarts/label-tool.md)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습시킬 수도 있습니다.

### <a name="train-a-model-without-labels"></a>레이블 없이 모델 학습

학습 문서에 수동으로 레이블을 지정하지 않고 사용자 지정 양식에서 발견된 모든 필드와 값을 인식하도록 사용자 지정 모델을 학습시킬 수 있습니다.

다음 코드는 **begin_training** 함수와 학습 클라이언트를 사용하여 지정된 문서 세트에 대한 모델을 학습합니다.

```python
poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=False)
model = poller.result()
```

반환되는 **CustomFormSubmodel** 개체에는 모델에서 인식할 수 있는 양식 유형과 각 양식 유형에서 추출할 수 있는 필드에 대한 정보가 포함됩니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.requested_on))
print("Last modified: {}".format(model.completed_on))

print("Recognized fields:")
# Looping through the submodels, which contains the fields they were trained on
for submodel in model.submodels:
    print("...The submodel has form type '{}'".format(submodel.form_type))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have label '{}'".format(
            name, field.label
        ))
```

### <a name="train-a-model-with-labels"></a>레이블을 사용하여 모델 학습

학습 문서에 레이블을 수동으로 지정하여 사용자 지정 모델을 학습시킬 수도 있습니다. 일부 시나리오에서는 레이블을 사용하여 학습시키면 성능이 향상됩니다. 

> [!IMPORTANT]
> 레이블을 사용하여 학습시키려면 학습 문서와 별도로 Blob Storage 컨테이너에 특별한 레이블 정보 파일( *\<filename\>.pdf.labels.json*)이 있어야 합니다. [Form Recognizer 샘플 레이블 지정 도구](../../quickstarts/label-tool.md)는 이러한 레이블 파일을 만드는 데 도움이 되는 UI를 제공합니다. 레이블 파일이 있으면 `true`로 설정된 *use_training_labels* 매개 변수를 통해 **begin_training** 함수를 호출할 수 있습니다.

```python
poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=True)
model = poller.result()
```

반환된 **CustomFormSubmodel**은 각 필드의 예상 정확도와 함께 모델이 추출할 수 있는 필드를 표시합니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.created_on))
print("Last modified: {}".format(model.last_modified))

print("Recognized fields:")
# looping through the submodels, which contains the fields they were trained on
# The labels are based on the ones you gave the training document.
for submodel in model.submodels:
    print("...The submodel with form type {} has accuracy '{}'".format(submodel.form_type, submodel.accuracy))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have name '{}' with an accuracy of {}".format(
            name, field.name, field.accuracy
        ))
```

## <a name="analyze-forms-with-a-custom-model"></a>사용자 지정 모델을 사용하여 양식 분석

이 섹션에서는 사용자 고유의 양식으로 학습시킨 모델을 사용하여 사용자 지정 양식 유형에서 키/값 정보 및 기타 콘텐츠를 추출하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 시나리오를 구현하려면 해당 ID를 아래 메서드에 전달할 수 있도록 모델을 이미 학습시킨 상태여야 합니다. [모델 학습](#train-a-model-without-labels) 섹션을 참조하세요.

**begin_recognize_custom_forms_from_url** 메서드를 사용합니다. 그러면 제출된 문서의 각 페이지당 하나씩 **RecognizedForm** 개체 컬렉션이 반환됩니다.

```python
# Make sure your form's type is included in the list of form types the custom model can recognize
poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model.model_id, form_url=formUrl)
forms = poller.result()
```

다음 코드에서는 분석 결과를 콘솔에 출력합니다. 인식된 각 필드와 해당 값을 신뢰도 점수와 함께 출력합니다.

```python
for idx, form in enumerate(forms):
    print("--------Recognizing Form #{}--------".format(idx))
    print("Form {} has type {}".format(idx, form.form_type))
    for name, field in form.fields.items():
        # each field is of type FormField
        # The value of the field can also be a FormField, or a list of FormFields
        # In our sample, it is just a FormField.
        print("...Field '{}' has value '{}' with a confidence score of {}".format(
            name, field.value, field.confidence
        ))
        # label data is populated if you are using a model trained with unlabeled data, since the service needs to make predictions for
        # labels if not explicitly given to it.
        if field.label_data:
            print("...Field '{}' has label '{}' with a confidence score of {}".format(
                name,
                field.label_data.text,
                field.confidence
            ))
    print("-----------------------------------")
```

## <a name="manage-your-custom-models"></a>사용자 지정 모델 관리

이 섹션에서는 계정에 저장된 사용자 지정 모델을 관리하는 방법을 보여 줍니다. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer 리소스 계정의 모델 수 확인

다음 코드 블록은 Form Recognizer 계정에 저장된 모델 수를 확인하고 이를 계정 제한과 비교합니다.

```python
# First, we see how many custom models we have, and what our limit is
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>리소스 계정에 현재 저장된 모델 나열

다음 코드 블록은 계정의 현재 모델을 나열하고 해당 세부 정보를 콘솔에 출력합니다. 첫 번째 모델에 대한 참조도 저장합니다.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_custom_models()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="get-a-specific-model-using-the-models-id"></a>모델 ID를 사용하여 특정 모델 가져오기

다음 코드 블록에서는 이전 섹션에서 저장한 모델 ID를 사용하여 모델에 대한 세부 정보를 검색합니다.

```python
# Now we'll get the first custom model in the paged list
custom_model = form_training_client.get_custom_model(model_id=first_model.model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Created on: {}".format(custom_model.requested_on))
print("Last modified: {}".format(custom_model.completed_on))
```

### <a name="delete-a-model-from-the-resource-account"></a>리소스 계정에서 모델 삭제

해당 ID를 참조하여 계정에서 모델을 삭제할 수도 있습니다. 이 코드는 이전 섹션에서 사용된 모델을 삭제합니다.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

# Confirm deletion:
try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
}
```

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `python` 명령을 사용하여 애플리케이션을 실행합니다.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>문제 해결

### <a name="general"></a>일반

Form Recognizer 클라이언트 라이브러리에서 [Azure Core](https://aka.ms/azsdk-python-azure-core)에 정의된 예외가 발생합니다.

## <a name="logging"></a>로깅

이 라이브러리는 로깅에 [표준 로깅 라이브러리](https://docs.python.org/3/library/logging.html)를 사용합니다. HTTP 세션(URL, 헤더 등)에 대한 기본 정보는 정보 수준에서 기록됩니다.

`logging_enable` 키워드 인수를 통해 클라이언트에서 요청/응답 본문 및 미작성 헤더를 포함한 상세 디버그 수준 로깅을 사용할 수 있습니다.

```python
import sys
import logging
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")

# This client will log detailed information about its HTTP sessions, at DEBUG level
form_recognizer_client = FormRecognizerClient(endpoint, credential, logging_enable=True)
```

마찬가지로 `logging_enable`은 클라이언트에 대해 상세 로깅을 사용하지 않는 경우에도 한 작업에만 사용하게 설정할 수 있습니다.

```python
poller = form_recognizer_client.begin_recognize_receipts(receipt, logging_enable=True)
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer Python 클라이언트 라이브러리를 사용하여 다양한 방식으로 모델을 학습시키고 양식을 분석했습니다. 다음으로, 더 나은 학습 데이터 세트를 만들고 더 정확한 모델을 생성하기 위한 팁에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [학습 데이터 세트 빌드](../../build-training-data-set.md)

* [Form Recognizer란?](../../overview.md)
* [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)에서 이 가이드의 샘플 코드 등을 확인할 수 있습니다.
