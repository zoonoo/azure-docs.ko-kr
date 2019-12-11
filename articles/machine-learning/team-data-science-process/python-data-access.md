---
title: Python 클라이언트 라이브러리를 사용 하 여 데이터 집합 액세스-팀 데이터 과학 프로세스
description: Python 클라이언트 라이브러리를 설치 하 고 사용 하 여 로컬 Python 환경에서 Azure Machine Learning 데이터를 안전 하 게 액세스 하 고 관리 합니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7b86d643540e46f9a4fc86c83fc77d739bfba418
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978495"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Hozzáférés az adathalmazokhoz Python segítségével, az Azure Machine Learning Python ügyfélkönyvtárat használva
Microsoft Azure Machine Learning Python 클라이언트 라이브러리의 미리 보기를 사용 하면 로컬 Python 환경에서 Azure Machine Learning 데이터 집합에 안전 하 게 액세스할 수 있으며 작업 영역에서 데이터 집합을 만들고 관리할 수 있습니다.

이 항목에서는 다음 방법에 대 한 지침을 제공 합니다.

* Machine Learning Python 클라이언트 라이브러리를 설치 합니다.
* 로컬 Python 환경에서 Azure Machine Learning 데이터 집합에 액세스 하기 위한 권한 부여를 가져오는 방법에 대 한 지침을 포함 하 여 데이터 집합 액세스 및 업로드
* 실험에서 중간 데이터 집합에 액세스
* Python 클라이언트 라이브러리를 사용 하 여 데이터 집합을 열거 하 고, 메타 데이터에 액세스 하 고, 데이터 집합의 내용을 읽고, 새 데이터 집합을 만들고, 기존 데이터 집합 업데이트

## <a name="prerequisites"></a>Előfeltételek
Python 클라이언트 라이브러리는 다음 환경에서 테스트 되었습니다.

* Windows, Mac 및 Linux
* Python 2.7, 3.3 및 3.4

다음 패키지에 대 한 종속성이 있습니다.

* 요청과
* python-dateutil
* pandas

Python, IPython 및 위에 나열 된 세 개의 패키지와 함께 제공 되는 [Anaconda](http://continuum.io/downloads#all) 또는 [Canopy](https://store.enthought.com/downloads/)와 같은 python 배포를 사용 하는 것이 좋습니다. IPython은 반드시 필요한 것은 아니지만 대화형으로 데이터를 조작 하 고 시각화 하는 데 유용한 환경입니다.

### <a name="installation"></a>Azure Machine Learning Python 클라이언트 라이브러리를 설치 하는 방법
이 항목에 설명 된 작업을 완료 하려면 Python 클라이언트 라이브러리 Azure Machine Learning 설치 해야 합니다. [Python 패키지 인덱스](https://pypi.python.org/pypi/azureml)에서 사용할 수 있습니다. Python 환경에 설치 하려면 로컬 Python 환경에서 다음 명령을 실행 합니다.

    pip install azureml

또는 [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)의 원본에서를 다운로드 하 여 설치할 수 있습니다.

    python setup.py install

컴퓨터에 git가 설치 되어 있는 경우 pip를 사용 하 여 git 리포지토리에서 직접 설치할 수 있습니다.

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>코드 조각을 사용 하 여 데이터 집합에 액세스
Python 클라이언트 라이브러리를 사용 하면 실행 된 실험에서 기존 데이터 집합에 프로그래밍 방식으로 액세스할 수 있습니다.

Azure Machine Learning Studio (클래식) 웹 인터페이스에서 로컬 컴퓨터에 데이터 집합을 pandas 데이터 프레임 개체로 다운로드 하 고 deserialize 하는 데 필요한 모든 정보를 포함 하는 코드 조각을 생성할 수 있습니다.

### <a name="security"></a>데이터 액세스를 위한 보안
Python 클라이언트 라이브러리와 함께 사용 하기 위해 Azure Machine Learning Studio (클래식)에서 제공 하는 코드 조각에는 작업 영역 ID와 권한 부여 토큰이 포함 되어 있습니다. 이러한 작업은 작업 영역에 대 한 모든 권한을 제공 하며 암호와 같이 보호 되어야 합니다.

보안상의 이유로, 코드 조각 기능은 해당 역할이 작업 영역에 대 한 **소유자로** 설정 된 사용자만 사용할 수 있습니다. 역할은 **사용자** 페이지의 **설정**아래 Azure Machine Learning Studio (클래식)에 표시 됩니다.

![Biztonság][security]

역할이 **소유자**로 설정 되어 있지 않으면 소유자에 게 다시 초대를 요청 하거나 작업 영역의 소유자에 게 코드 조각을 제공 하도록 요청할 수 있습니다.

권한 부여 토큰을 가져오기 위해 다음 중 하나를 수행할 수 있습니다.

* 소유자에 게 토큰을 요청 합니다. 소유자는 Azure Machine Learning Studio (클래식)에 있는 작업 영역의 설정 페이지에서 권한 부여 토큰에 액세스할 수 있습니다. 왼쪽 창에서 **설정** 을 선택 하 고 **권한 부여 토큰** 을 클릭 하 여 기본 및 보조 토큰을 확인 합니다. 주 또는 보조 권한 부여 토큰을 코드 조각에서 사용할 수 있지만 소유자는 보조 권한 부여 토큰만 공유 하는 것이 좋습니다.

   ![권한 부여 토큰](./media/python-data-access/ml-python-access-settings-tokens.png)

* 소유자 역할에 대 한 승격을 요청 합니다. 이렇게 하려면 작업 영역의 현재 소유자가 먼저 작업 영역에서 사용자를 제거 하 고 소유자로 다시 초대 해야 합니다.

개발자가 작업 영역 ID와 권한 부여 토큰을 얻은 후에는 역할에 관계 없이 코드 조각을 사용 하 여 작업 영역에 액세스할 수 있습니다.

권한 부여 토큰은 **설정**아래의 **권한 부여 토큰** 페이지에서 관리 됩니다. 다시 생성할 수 있지만이 절차는 이전 토큰에 대 한 액세스를 취소 합니다.

### <a name="accessingDatasets"></a>로컬 Python 응용 프로그램의 데이터 집합에 액세스
1. Machine Learning Studio (클래식)의 왼쪽 탐색 모음에서 **데이터 집합** 을 클릭 합니다.
2. 액세스 하려는 데이터 집합을 선택 합니다. **내 데이터** 집합 목록 또는 **샘플** 목록에서 데이터 집합을 선택할 수 있습니다.
3. 아래쪽 도구 모음에서 **데이터 액세스 코드 생성**을 클릭 합니다. 데이터가 Python 클라이언트 라이브러리와 호환 되지 않는 형식이 면이 단추를 사용할 수 없습니다.
   
    ![Adathalmazok][datasets]
4. 표시 되는 창에서 코드 조각을 선택 하 고 클립보드에 복사 합니다.
   
    ![데이터 액세스 코드 생성 단추][dataset-access-code]
5. 로컬 Python 응용 프로그램의 노트북에 코드를 붙여넣습니다.
   
    ![노트북에 코드 붙여넣기][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Machine Learning 실험에서 중간 데이터 집합에 액세스
클래식 버전의 Machine Learning Studio에서 실험을 실행 한 후에는 모듈의 출력 노드에서 중간 데이터 집합에 액세스할 수 있습니다. 중간 데이터 집합은 모델 도구를 실행 하는 경우 중간 단계에 대해 생성 되 고 사용 되는 데이터입니다.

데이터 형식이 Python 클라이언트 라이브러리와 호환 되는 동안에는 중간 데이터 집합에 액세스할 수 있습니다.

지원 되는 형식은 다음과 같습니다 (`azureml.DataTypeIds` 클래스에 대 한 상수).

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

모듈 출력 노드를 가리키면 형식을 확인할 수 있습니다. 도구 설명에 노드 이름과 함께 표시 됩니다.

[Split][split] 모듈과 같은 일부 모듈은 Python 클라이언트 라이브러리에서 지원 하지 않는 `Dataset`라는 형식으로 출력 됩니다.

![데이터 집합 형식][dataset-format]

[CSV로 변환][convert-to-csv]과 같은 변환 모듈을 사용 하 여 출력을 지원 되는 형식으로 가져와야 합니다.

![GenericCSV 형식][csv-format]

다음 단계에서는 실험을 만들고 실행 하며 중간 데이터 집합에 액세스 하는 예제를 보여 줍니다.

1. 새 실험을 만듭니다.
2. **성인 인구 조사 소득 이진 분류 데이터 집합** 모듈을 삽입 합니다.
3. [분할][split] 모듈을 삽입 하 고 해당 입력을 데이터 집합 모듈 출력에 연결 합니다.
4. [CSV로 변환][convert-to-csv] 모듈을 삽입 하 고 해당 입력을 [Split][split] 모듈 출력 중 하나에 연결 합니다.
5. 실험을 저장 하 고 실행 한 후 실행이 완료 될 때까지 기다립니다.
6. [CSV로 변환][convert-to-csv] 모듈에서 출력 노드를 클릭 합니다.
7. 상황에 맞는 메뉴가 표시 되 면 **데이터 액세스 코드 생성**을 선택 합니다.
   
    ![상황에 맞는 메뉴][experiment]
8. 코드 조각을 선택 하 고 표시 되는 창에서 클립보드에 복사 합니다.
   
    ![상황에 맞는 메뉴에서 액세스 코드 생성][intermediate-dataset-access-code]
9. 노트북에 코드를 붙여 넣습니다.
   
    ![전자 필기장에 코드 붙여넣기][ipython-intermediate-dataset]
10. Matplotlib를 사용 하 여 데이터를 시각화할 수 있습니다. Age 열에 대해 히스토그램에 표시 됩니다.
    
    ![히스토그램][ipython-histogram]

## <a name="clientApis"></a>Machine Learning Python 클라이언트 라이브러리를 사용 하 여 데이터 집합 액세스, 읽기, 만들기 및 관리
### <a name="workspace"></a>Munkaterület
작업 영역은 Python 클라이언트 라이브러리에 대 한 진입점입니다. `Workspace` 클래스에 작업 영역 ID와 권한 부여 토큰을 제공 하 여 인스턴스를 만듭니다.

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>데이터 집합 열거
지정 된 작업 영역의 모든 데이터 집합을 열거 하려면:

    for ds in ws.datasets:
        print(ds.name)

사용자가 만든 데이터 집합만 열거 하려면:

    for ds in ws.user_datasets:
        print(ds.name)

예제 데이터 집합만 열거 하려면:

    for ds in ws.example_datasets:
        print(ds.name)

이름 (대/소문자 구분)을 사용 하 여 데이터 집합에 액세스할 수 있습니다.

    ds = ws.datasets['my dataset name']

또는 인덱스를 사용 하 여 액세스할 수 있습니다.

    ds = ws.datasets[0]


### <a name="metadata"></a>Metaadatok
데이터 집합은 콘텐츠 외에도 메타 데이터를 포함 합니다. 중간 데이터 집합은이 규칙에 대 한 예외 이며 메타 데이터는 없습니다.

일부 메타 데이터 값은 사용자가 만든 시간에 할당 합니다.

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

다른 항목은 Azure ML에서 할당 한 값입니다.

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

사용 가능한 메타 데이터에 대 한 자세한 내용은 `SourceDataset` 클래스를 참조 하세요.

### <a name="read-contents"></a>내용 읽기
Machine Learning Studio (클래식)에서 제공 하는 코드 조각은 데이터 집합을 자동으로 다운로드 하 고 pandas 데이터 프레임 개체로 deserialize 합니다. 이 작업은 `to_dataframe` 메서드를 사용 하 여 수행 됩니다.

    frame = ds.to_dataframe()

원시 데이터를 다운로드 하 고 deserialization을 수행 하려는 경우에는이 옵션을 선택 합니다. 현재이 옵션은 Python 클라이언트 라이브러리에서 deserialize 할 수 없는 ' ARFF '와 같은 형식의 유일한 옵션입니다.

내용을 텍스트로 읽으려면:

    text_data = ds.read_as_text()

내용을 이진으로 읽으려면 다음을 수행 하십시오.

    binary_data = ds.read_as_binary()

내용에 대 한 스트림을 열 수도 있습니다.

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>새 데이터 집합 만들기
Python 클라이언트 라이브러리를 사용 하면 Python 프로그램에서 데이터 집합을 업로드할 수 있습니다. 이러한 데이터 집합은 작업 영역에서 사용할 수 있습니다.

Pandas 데이터 프레임에 데이터가 있는 경우 다음 코드를 사용 합니다.

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

데이터가 이미 직렬화 된 경우 다음을 사용할 수 있습니다.

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Python 클라이언트 라이브러리는 pandas 데이터 프레임을 다음 형식으로 serialize 할 수 있습니다. 이러한 상수에 대 한 상수는 `azureml.DataTypeIds` 클래스에 있습니다.

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>기존 데이터 집합 업데이트
기존 데이터 집합과 일치 하는 이름을 가진 새 데이터 집합을 업로드 하려고 하면 충돌 오류가 발생 합니다.

기존 데이터 집합을 업데이트 하려면 먼저 기존 데이터 집합에 대 한 참조를 가져와야 합니다.

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

그런 다음 `update_from_dataframe`를 사용 하 여 Azure에서 데이터 집합의 내용을 직렬화 하 고 바꿉니다.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

데이터를 다른 형식으로 serialize 하려면 선택적 `data_type_id` 매개 변수에 대 한 값을 지정 합니다.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

필요에 따라 `description` 매개 변수의 값을 지정 하 여 새 설명을 설정할 수 있습니다.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

필요에 따라 `name` 매개 변수의 값을 지정 하 여 새 이름을 설정할 수 있습니다. 지금부터 새 이름만 사용 하 여 데이터 집합을 검색 합니다. 다음 코드에서는 데이터, 이름 및 설명을 업데이트 합니다.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

`data_type_id`, `name` 및 `description` 매개 변수는 선택 사항이 며 기본값은 이전 값입니다. `dataframe` 매개 변수는 항상 필요 합니다.

데이터가 이미 직렬화 된 경우 `update_from_dataframe`대신 `update_from_raw_data`를 사용 합니다. `dataframe`대신 `raw_data`를 전달 하는 경우에도 유사한 방식으로 작동 합니다.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

