---
title: Azure에서 저장소 서비스의 데이터에 연결
titleSuffix: Azure Machine Learning
description: Azure Machine Learning studio를 사용 하 여 Azure에서 저장소 서비스의 데이터에 안전 하 게 연결 하는 데이터 저장소 및 데이터 집합을 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to, data4ml
ms.openlocfilehash: e97546e678b3b7bf7932600ea53d09557493685c
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359870"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Azure Machine Learning studio를 사용 하 여 데이터에 연결

이 문서에서는 [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md)를 사용 하 여 데이터에 액세스 하는 방법을 알아봅니다. [Azure Machine Learning 데이터 저장소](how-to-access-data.md)를 사용 하 여 Azure의 저장소 서비스에서 데이터에 연결 하 고, [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md)을 사용 하 여 ML 워크플로의 태스크에 대 한 해당 데이터를 패키지할 수 있습니다.

다음 표에서는 데이터 저장소 및 데이터 집합의 이점을 정의 하 고 요약 합니다. 

|Object|Description| 이점|   
|---|---|---|
|데이터 저장소| 작업 영역과 연결 된 [Key Vault](https://azure.microsoft.com/services/key-vault/) 에서 구독 ID 및 토큰 권한 부여와 같은 연결 정보를 저장 하 여 Azure의 저장소 서비스에 안전 하 게 연결 합니다. | 사용자 정보를 안전 하 게 저장 하기 때문에 <br><br> <li> &nbsp; &nbsp; 인증 &nbsp; 자격 증명이 &nbsp; 나 &nbsp; 원본 &nbsp; 데이터 원본을 위험에 노출 하지 마십시오. <li> 더 이상 스크립트에서 하드 코드를 코딩할 필요가 없습니다.
|데이터 세트| 데이터 세트를 만들면 데이터 원본 위치에 대한 참조와 해당 메타데이터의 복사본을 만듭니다. 데이터 집합을 사용 하 여 다음을 수행할 수 있습니다. <br><br><li> 모델 학습 중 데이터에 액세스 합니다.<li> 데이터를 공유 하 고 다른 사용자와 공동 작업 합니다.<li> 데이터 탐색을 위해 pandas와 같은 오픈 소스 라이브러리를 활용 합니다. | 데이터 집합은 지연 평가 되 고 데이터는 기존 위치에 남아 있기 때문에 <br><br><li>저장소에 데이터의 단일 복사본을 유지 합니다.<li> 추가 저장소 비용이 발생 하지 않음 <li> 실수로 원래 데이터 원본을 변경 하는 것은 위험 하지 않습니다.<li>ML 워크플로 성능 속도를 향상 시킵니다. 

데이터 저장소 및 데이터 집합이 Azure Machine Learning의 전체 데이터 액세스 워크플로에 적합 한 위치를 이해 하려면 [안전 하 게 데이터 액세스](concept-data.md#data-workflow) 문서를 참조 하세요.

Code first를 사용 하려면 [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py) 를 사용 하는 다음 문서를 참조 하세요.
* [데이터 저장소를 사용 하 여 Azure storage 서비스에 연결](how-to-access-data.md)합니다. 
* [Azure Machine Learning 데이터 집합을 만듭니다](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- [Azure Machine Learning studio](https://ml.azure.com/)에 액세스 합니다.

- Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md).

    -  작업 영역을 만들 때 Azure blob 컨테이너 및 Azure 파일 공유는 작업 영역에 데이터 저장소로 자동으로 등록 됩니다. 각각 이름이 `workspaceblobstore` 및 `workspacefilestore`로 지정됩니다. Blob storage가 요구에 충분 한 경우 `workspaceblobstore` 가 기본 데이터 저장소로 설정 되 고 이미 사용 하도록 구성 되어 있습니다. 그렇지 않으면 [지원 되는 저장소 형식을](how-to-access-data.md#matrix)사용 하 여 Azure에 저장소 계정이 필요 합니다.
    

## <a name="create-datastores"></a>데이터 저장소 만들기

[이 Azure 스토리지 솔루션](how-to-access-data.md#matrix)에서 데이터 저장소를 만들 수 있습니다. **지원 되지 않는 저장소 솔루션의** 경우, ML 실험 중에 데이터 송신 비용을 절약 하려면 지원 되는 Azure storage 솔루션으로 [데이터를 이동](how-to-access-data.md#move) 해야 합니다. [데이터 저장소에 대해 자세히 알아보세요](how-to-access-data.md). 

Azure Machine Learning studio를 사용 하 여 몇 단계로 새 데이터 저장소를 만듭니다.

> [!IMPORTANT]
> 데이터 저장소 계정이 가상 네트워크에 있는 경우 스튜디오에서 데이터에 액세스할 수 있도록 하려면 추가 구성 단계가 필요 합니다. 적절 한 구성 단계가 적용 되도록 하려면 [네트워크 격리 & 개인 정보](how-to-enable-studio-virtual-network.md) 를 참조 하세요.

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.
1. 왼쪽 창의 **관리** 에서 **데이터 저장소** 를 선택합니다.
1. **+ 새 데이터 저장소** 를 선택합니다.
1. 양식을 작성 하 여 새 데이터 저장소를 만들고 등록 합니다. 이 양식은 사용자가 선택하는 Azure 스토리지 유형 및 인증 형식에 따라 지능적으로 업데이트됩니다. 이 양식을 채우는 데 필요한 인증 자격 증명을 찾을 수 있는 위치를 이해 하려면 [저장소 액세스 및 사용 권한 섹션](#access-validation) 을 참조 하세요.

다음 예제에서는 **Azure blob 데이터 저장소** 를 만들 때 양식이 표시 되는 모양을 보여 줍니다.

![새 데이터 저장소에 대한 양식](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>데이터 세트 만들기

데이터 저장소를 만든 후 데이터와 상호 작용 하는 데이터 집합을 만듭니다. 데이터 집합은 학습 같은 기계 학습 작업에 대 한 데이터를 지연 계산 된 소비재 개체로 패키지 합니다. [데이터 세트](how-to-create-register-datasets.md)에 대해 자세히 알아보세요.

데이터 집합에는 FileDataset 및 TabularDataset의 두 가지 유형이 있습니다. 
[Filedatasets](how-to-create-register-datasets.md#filedataset) 은 단일 또는 여러 파일 또는 공용 url에 대 한 참조를 만듭니다. 반면 [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) 는 데이터를 테이블 형식으로 표시 합니다. 

다음 단계와 애니메이션은 [Azure Machine Learning studio](https://ml.azure.com)에서 데이터 집합을 만드는 방법을 보여 줍니다.

> [!Note]
> Azure Machine Learning studio를 통해 만든 데이터 집합은 작업 영역에 자동으로 등록 됩니다.

![UI를 사용 하 여 데이터 집합 만들기](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Studio에서 데이터 집합을 만들려면 다음을 수행 합니다.
1. [Azure Machine Learning studio](https://ml.azure.com/)에 로그인 합니다.
1. 왼쪽 창의 **자산** 섹션에서 **데이터 집합** 을 선택 합니다.
1. 데이터 집합 **만들기** 를 선택 하 여 데이터 집합의 원본을 선택 합니다. 이 원본은 로컬 파일, 데이터 저장소, 공용 Url 또는 [Azure Open 데이터 집합이](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)될 수 있습니다.
1. 데이터 집합 형식으로 **테이블** 형식 또는 **파일** 을 선택 합니다.
1. **다음** 을 선택 하 여 **데이터 저장소 및 파일 선택** 양식을 엽니다. 이 폼에서 데이터 집합을 만든 후 데이터 집합에 사용할 데이터 파일을 선택할 뿐만 아니라 데이터 집합을 보관할 위치를 선택할 수 있습니다.
    1. 데이터가 가상 네트워크에 있는 경우 유효성 검사 건너뛰기를 사용 하도록 설정 합니다. [가상 네트워크 격리 및 개인 정보](how-to-enable-studio-virtual-network.md)에 대해 자세히 알아보세요.
    1. 테이블 형식 데이터 집합의 경우 ' 시계열 ' 특성을 지정 하 여 데이터 집합에 대 한 시간 관련 작업을 활성화할 수 있습니다. [데이터 집합에 시계열 특성을 추가](how-to-monitor-datasets.md#studio-dataset)하는 방법에 대해 알아봅니다.
1. **다음** 을 선택 하 여 **설정 및 미리 보기** 및 **스키마** 폼을 채웁니다. 이러한 형식은 파일 형식에 따라 지능적으로 채워지며 이러한 폼에서 만들기 전에 데이터 집합을 추가로 구성할 수 있습니다. 
1. **다음** 을 선택 하 여 **확인 세부 정보** 양식을 검토 합니다. 선택 항목을 확인 하 고 데이터 집합에 대 한 선택적 데이터 프로필을 만듭니다. [데이터 프로파일링](#profile)에 대한 자세한 정보
1. **만들기** 를 선택 하 여 데이터 집합 만들기를 완료 합니다.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>데이터 프로필 및 미리 보기

데이터 집합을 만든 후 다음 단계에 따라 스튜디오에서 프로필 및 미리 보기를 볼 수 있는지 확인 합니다. 

1. [Azure Machine Learning studio](https://ml.azure.com/) 에 로그인 합니다.
1. 왼쪽 창의 **자산** 섹션에서 **데이터 집합** 을 선택 합니다.
1. 보려는 데이터 집합의 이름을 선택 합니다. 
1. **탐색** 탭을 선택합니다. 
1. **미리 보기** 또는 **프로필** 탭을 선택 합니다. 

![데이터 집합 프로필 및 미리 보기 보기](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

데이터 세트 전체에 대한 다양한 요약 통계를 가져와서 데이터 세트가 ML 준비 상태인지 여부를 확인할 수 있습니다. 숫자가 아닌 열의 경우 최솟값, 최댓값 및 오류 수와 같은 기본 통계만 포함됩니다. 숫자 열의 경우 통계적 모멘트 및 예상 분위수를 검토할 수도 있습니다. 

특히 데이터 집합의 데이터 프로필에는 다음 Azure Machine Learning이 포함 됩니다.

>[!NOTE]
> 관련이 없는 형식의 기능은 빈 항목으로 표시되어 있습니다.

|통계|Description
|------|------
|기능| 요약되는 열의 이름입니다.
|프로필| 유추된 형식을 기반으로 하는 인라인 시각화입니다. 예를 들어 문자열, 술래 및 날짜에는 값 개수가 있고 소수점(숫자)에는 근사 히스토그램이 있습니다. 이를 통해 데이터의 분포를 빠르게 파악할 수 있습니다.
|형식 분포| 열 내 유형의 인라인 값 개수입니다. Null은 고유 형식이므로 이 시각화는 홀수 또는 누락된 값을 탐지하는 데 유용합니다.
|Type|열의 유추된 형식입니다. 가능한 값은 문자열, 부울, 날짜 및 10진수입니다.
|최소값| 열의 최솟값입니다. 형식에 고유 순서 (예: 부울)가 없는 기능에 대 한 빈 항목이 표시 됩니다.
|최대값| 열의 최댓값입니다. 
|개수| 열에서 누락되거나 누락되지 않은 항목의 총 수입니다.
|누락되지 않은 수| 열에서 누락되지 않은 항목의 수입니다. 빈 문자열과 오류는 값으로 처리되므로 "누락되지 않은 수"에 영향을 주지 않습니다.
|분위수| 데이터 분포를 파악하기 위한 각 분위수의 근사 값입니다.
|평균| 열의 산술 평균 또는 평균입니다.
|표준 편차| 이 열의 데이터에 대한 분산 또는 편차를 측정한 값입니다.
|Variance| 이 열의 데이터가 평균 값에서 분산된 정도를 측정한 값입니다. 
|왜곡도| 이 열의 데이터가 정규 분포에서 치우쳐 있는 정도를 측정한 값입니다.
|첨도| 이 열의 데이터가 정규 분포와 비교하여 뾰족하게 밀집된 정도를 측정한 값입니다.

## <a name="storage-access-and-permissions"></a>저장소 액세스 및 사용 권한

Azure storage 서비스에 안전 하 게 연결 하려면 해당 하는 데이터 저장소에 액세스할 수 있는 권한이 Azure Machine Learning 필요 합니다. 이 액세스는 데이터 저장소를 등록 하는 데 사용 되는 인증 자격 증명에 따라 다릅니다.

### <a name="virtual-network"></a>가상 네트워크

데이터 저장소 계정이 **가상 네트워크** 에 있는 경우 Azure Machine Learning 데이터에 대 한 액세스 권한이 있는지 확인 하는 추가 구성 단계가 필요 합니다. 데이터 저장소를 만들고 등록할 때 적절 한 구성 단계가 적용 되도록 하려면 [네트워크 격리 & 개인 정보](how-to-enable-studio-virtual-network.md) 를 참조 하세요.  

### <a name="access-validation"></a>액세스 유효성 검사

**초기 데이터 저장소 만들기 및 등록 프로세스의 일부로** , Azure Machine Learning는 기본 저장소 서비스가 존재 하 고 사용자가 제공한 주체 (사용자 이름, 서비스 주체 또는 SAS 토큰)가 지정 된 저장소에 액세스할 수 있는지 자동으로 검사 합니다.

**데이터 저장소를 만든 후** 에는 데이터 저장소 개체가 검색 **될 때마다 기본** 저장소 컨테이너에 액세스 해야 하는 메서드에 대해서만이 유효성 검사가 수행 됩니다. 예를 들어 데이터 저장소에서 파일을 다운로드하려는 경우에는 유효성 검사가 수행되지만, 기본 데이터 저장소만 변경하려는 경우에는 유효성 검사가 수행되지 않습니다.

기본 저장소 서비스에 대 한 액세스를 인증 하려면 만들려는 데이터 저장소 유형에 따라 계정 키, SAS (공유 액세스 서명) 토큰 또는 서비스 주체를 제공 하면 됩니다. [저장소 유형 행렬](how-to-access-data.md#matrix) 에는 각 데이터 저장소 유형에 해당 하는 지원 되는 인증 유형이 나열 됩니다.

[Azure Portal](https://portal.azure.com)에 대 한 계정 키, SAS 토큰 및 서비스 주체 정보를 찾을 수 있습니다.

* 계정 키 또는 SAS 토큰을 인증에 사용할 계획이면 왼쪽 창에서 **스토리지 계정** 을 선택하고, 등록하려는 스토리지 계정을 선택합니다.
  * **개요** 페이지에서는 계정 이름, 컨테이너, 파일 공유 이름 등의 정보를 제공합니다.
      1. 계정 키의 경우 **설정** 창에서 **액세스 키** 로 이동합니다.
      1. SAS 토큰의 경우 **설정** 창에서 **공유 액세스 서명** 으로 이동합니다.

* 인증을 위해 [서비스 주체](../active-directory/develop/howto-create-service-principal-portal.md) 를 사용 하려는 경우 **앱 등록** 으로 이동 하 여 사용 하려는 앱을 선택 합니다.
    * 해당 **개요** 페이지에는 테 넌 트 id 및 클라이언트 ID와 같은 필수 정보가 포함 됩니다.

> [!IMPORTANT]
> * Azure Storage 계정 (계정 키 또는 SAS 토큰)에 대 한 액세스 키를 변경 해야 하는 경우 새 자격 증명과 해당 작업 영역에 연결 된 데이터 저장소를 동기화 해야 합니다. [업데이트 된 자격 증명을 동기화](how-to-change-storage-access-key.md)하는 방법을 알아봅니다. <br> <br>
> * 같은 이름으로 데이터 저장소의 등록을 취소 하 고 다시 등록 하는 경우 실패 하 고 작업 영역에 대 한 Azure Key Vault에서 일시 삭제를 사용 하도록 설정 하지 않았을 수 있습니다. 기본적으로 일시 삭제는 작업 영역에서 만든 주요 자격 증명 모음 인스턴스에 대해 사용 하도록 설정 되지만 기존 키 자격 증명 모음을 사용 하거나 10 월 2020 일 이전에 만든 작업 영역을 사용 하는 경우에는 사용 하지 못할 수 있습니다. 일시 삭제를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [기존 키 자격 증명 모음에 대 한 일시 삭제]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault)사용을 참조 하세요.

### <a name="permissions"></a>사용 권한

Azure blob 컨테이너 및 Azure Data Lake Gen 2 저장소의 경우 인증 자격 증명에 **저장소 Blob 데이터 판독기** 액세스 권한이 있는지 확인 합니다. [저장소 Blob 데이터 판독기](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)에 대해 자세히 알아보세요. 계정 SAS 토큰의 기본값은 사용 권한 없음입니다. 
* 데이터 **읽기 액세스** 의 경우 인증 자격 증명에는 최소한의 목록 및 컨테이너와 개체에 대 한 읽기 권한이 있어야 합니다. 

* 데이터 **쓰기 액세스** 의 경우 쓰기 및 추가 권한도 필요 합니다.

## <a name="train-with-datasets"></a>데이터 세트로 학습

기계 학습 실험에서 ML 모델 학습을 위해 데이터 집합을 사용 합니다. [데이터 집합을 사용 하 여 학습 하는 방법에 대 한 자세한 정보](how-to-train-with-datasets.md)

## <a name="next-steps"></a>다음 단계

* [TabularDatasets 및 자동화 된 기계 학습으로 학습](tutorial-first-experiment-automated-ml.md)하는 단계별 예제입니다.

* [모델을 학습](how-to-set-up-training-targets.md)합니다.

* 더 많은 데이터 집합 학습 예제는 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)을 참조 하세요.
