---
title: '빠른 시작: 비정상 탐지기 라이브러리 및 Python을 사용 하 여 데이터 문제를 감지 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: 일괄 처리 또는 스트리밍 데이터에서 데이터 계열에서 변칙을 비정상 탐지기 API를 사용 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 1d89ed8f40547142d41af9c587fc8fc000fa4dd9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503709"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>빠른 시작: Python에 대 한 비정상 탐지기 클라이언트 라이브러리

.NET에 대해 이상 감지기 클라이언트 라이브러리를 사용 하 여 시작 하세요. 기본 작업에 대 한 예제 코드를 사용해 패키지를 설치 하려면 다음이 단계를 따릅니다. 비정상 탐지기 서비스를 사용 하면 시계열 데이터에서 자동으로 산업, 시나리오 또는 데이터 볼륨에 관계 없이 가장 적합 한 모델을 사용 하 여 비정상적인 상태를 찾을 수 있습니다.

## <a name="key-concepts"></a>주요 개념

Python에 대해 이상 감지기 클라이언트 라이브러리를 사용 합니다.

* 일괄 처리 요청으로 시간 시계열 데이터 집합 전체에서 변칙을 검색 합니다.
* 에 시계열의 최신 데이터 요소의 비정상 상태를 검색 합니다.

[라이브러리 참조 설명서](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [패키지 (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [샘플](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>필수 조건

* Azure 구독- [무료로 만들](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* [Pandas 데이터 분석 라이브러리](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>설치

### <a name="create-an-anomaly-detector-resource"></a>비정상 탐지기 리소스 만들기

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>클라이언트 라이브러리를 설치 합니다.

Python을 설치한 후 사용 하 여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>개체 모델

비정상 탐지기 클라이언트는 [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) 넌 트 키를 사용 하 여 Azure에 인증 하는 개체입니다. 클라이언트는 변칙 검색에는 두 가지 방법을 제공합니다. 사용 하 여 전체 데이터 집합 [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-), 및 최신 데이터를 사용 하 여를 지점 [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)합니다. 

일련의 시계열 데이터 보내집니다 [지점](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) 에 [요청](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) 개체입니다. 합니다 `Request` 데이터를 설명 하는 속성을 포함 하는 개체 ([세분성](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) 예를 들어), 및 변칙 검색에 대 한 매개 변수입니다. 

비정상 탐지기 응답은는 [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) 또는 [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) 사용 방법에 따라 개체입니다. 

## <a name="getting-started"></a>시작

원하는 편집기 또는 IDE에서 새 Python 응용 프로그램을 만듭니다. 그런 다음 파일에 다음 가져오기 선언은 추가 합니다. 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> 이 빠른 시작에서는 한 가정 [환경 변수를 만들](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) 이상 감지기 키 라는 `ANOMALY_DETECTOR_KEY`합니다.

환경 변수로 키, 시간 시계열 데이터 파일 및 구독의 azure 위치를 경로 대 한 변수를 만듭니다. 예: `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>코드 예제 

이러한 코드 조각은.NET에 대해 이상 감지기 클라이언트 라이브러리를 사용 하 여 다음을 수행 하는 방법을 보여 줍니다.

* [클라이언트를 인증 합니다.](#authenticate-the-client)
* [시계열 데이터 집합을 파일에서 로드](#load-time-series-data-from-a-file)
* [전체 데이터 집합에서 변칙을 검색 합니다.](#detect-anomalies-in-the-entire-data-set) 
* [최신 데이터 요소의 비정상 상태 검색](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>클라이언트를 인증 합니다.

끝점에 azure 위치 변수를 추가 하 고 키를 사용 하 여 클라이언트를 인증 합니다.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>파일에서 시계열 데이터를 로드 합니다.

이 빠른 시작에서 예제 데이터 다운로드 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. 브라우저에서 마우스 오른쪽 단추로 클릭 **Raw**
2. 클릭 **로 저장 링크**
3. .Csv 파일로 응용 프로그램 디렉터리에 파일을 저장 합니다.

이 시계열 데이터를.csv 파일로 서식이 지정 되어 있으며 비정상 탐지기 API에 전송 됩니다.

있는 Pandas 라이브러리의 데이터 파일을 로드 `read_csv()` 메서드와 데이터 계열 저장 하는 빈 목록 변수 확인 합니다. 파일을 통해 반복 하 고 추가 데이터를 [지점](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) 개체입니다. 이 개체는 타임 스탬프 및.csv 데이터 파일의 행에서 숫자 값을 포함 됩니다. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

만들기는 [요청](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) 시계열을 사용 하 여 개체 및 [세분성](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (또는 주기) 해당 데이터 요소입니다. 예: `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>전체 데이터 집합에서 변칙을 검색 합니다. 

클라이언트를 사용 하 여 전체 시간 시계열 데이터를 통해 문제를 감지 하는 API 호출 [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) 메서드. 반환 된 저장할 [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) 개체입니다. 응답의 반복 `is_anomaly` 목록에서 선택한 인덱스의 인쇄 `true` 값입니다. 이러한 값은 발견 된 경우 비정상적인 데이터 요소의 인덱스에 해당 합니다.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 비정상 상태 검색

최신 데이터 지점과 클라이언트를 사용 하 여 비정상 인지 확인 하려면 비정상 탐지기 API 호출 [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) 메서드와 반환된 된 저장소 [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) 개체입니다. 응답의 `is_anomaly` 은 이때의 비정상 상태를 지정 하는 부울 값입니다.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>애플리케이션 실행

사용 하 여 명령줄 또는 ide에서 응용 프로그램을 실행 합니다 `python` 명령 및 파일 이름입니다.
 
## <a name="clean-up-resources"></a>리소스 정리

정리 하 고 Cognitive Services 구독을 제거 하려는 경우에 리소스 또는 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹과 다른 리소스를 삭제도 리소스 그룹을 삭제 합니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

또한 리소스 그룹 및 해당 관련된 리소스를 제거 하려면 다음 클라우드 셸 명령을 실행할 수 있습니다. 완료 하려면 몇 분 정도 걸릴 수 있습니다. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Databricks를 사용 하 여 스트리밍 변칙 검색](../tutorials/anomaly-detection-streaming-databricks.md)

* 새로운 기능을 [비정상 탐지기 API?](../overview.md)
* [모범 사례](../concepts/anomaly-detection-best-practices.md) 비정상 탐지기 API를 사용 하는 경우.
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)에서 확인할 수 있습니다.
