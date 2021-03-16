---
title: 'ML Studio (클래식): Azure Machine Learning으로 마이그레이션-데이터 집합 다시 빌드'
description: Azure Machine Learning designer에서 Studio (클래식) 데이터 집합 다시 빌드
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 4c04dd5a2b41b3db54b20c9e514767453951cc35
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565045"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Studio (클래식) 데이터 집합을 Azure Machine Learning로 마이그레이션

이 문서에서는 Studio (클래식) 데이터 집합을 Azure Machine Learning로 마이그레이션하는 방법에 대해 알아봅니다. Studio (클래식)에서 마이그레이션하는 방법에 대 한 자세한 내용은 [마이그레이션 개요 문서](migrate-overview.md)를 참조 하세요.

데이터 집합을 Azure Machine Learning으로 마이그레이션하기 위한 세 가지 옵션이 있습니다. 각 섹션을 읽고 시나리오에 가장 적합 한 옵션을 결정 합니다.


|데이터는 어디에 있나요? | 마이그레이션 옵션  |
|---------|---------|
|Studio (클래식)     |  옵션 1: [Studio (클래식)에서 데이터 집합을 다운로드 하 고 Azure Machine Learning에 업로드](#download-the-dataset-from-studio-classic)합니다.      |
|클라우드 스토리지     | 옵션 2: [클라우드 원본에서 데이터 집합을 등록](#import-data-from-cloud-sources)합니다. <br><br>  옵션 3: [데이터 가져오기 모듈을 사용 하 여 클라우드 원본에서 데이터를 가져옵니다](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning는 데이터 집합을 만들고 관리 하기 위한 [코드 중심 워크플로](../how-to-create-register-datasets.md) 도 지원 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역을 만듭니다](../how-to-manage-workspace.md#create-a-workspace).
- 마이그레이션할 Studio (클래식) 데이터 집합입니다.


## <a name="download-the-dataset-from-studio-classic"></a>Studio에서 데이터 집합 다운로드 (클래식)

Studio (클래식) 데이터 집합을 Azure Machine Learning로 마이그레이션하는 가장 간단한 방법은 데이터 집합을 다운로드 하 여 Azure Machine Learning에 등록 하는 것입니다. 이렇게 하면 데이터 집합의 새 복사본이 생성 되 고 Azure Machine Learning 데이터 저장소에 업로드 됩니다.

다음 Studio (클래식) 데이터 집합 형식을 직접 다운로드할 수 있습니다.

* 일반 텍스트(.txt)
* 헤더가 있거나(.csv) 없는(.nh.csv) 쉼표로 구분된 값(CSV)
* 헤더가 있거나(.tsv) 없는(.nh.tsv) 탭으로 구분된 값(TSV)
* Excel 파일
* Zip 파일(.zip)

데이터 집합을 직접 다운로드 하려면 다음을 수행 합니다.
1. 스튜디오 (클래식) 작업 영역으로 이동 [https://studio.azureml.net](https://studio.azureml.net) 합니다 ().
1. 왼쪽 탐색 모음에서 **데이터 집합** 탭을 선택 합니다.
1. 다운로드 하려는 데이터 집합을 선택 합니다.
1. 아래쪽 작업 모음에서 **다운로드** 를 선택 합니다.

    ![Studio에서 데이터 집합을 다운로드 하는 방법을 보여 주는 스크린샷 (클래식)](./media/migrate-register-dataset/download-dataset.png)

다음 데이터 형식에 대해 **CSV로 변환** 모듈을 사용 하 여 데이터 집합을 다운로드 해야 합니다.

* SVMLight data (. SVMLight) 
* 특성 관계 파일 형식 (ARFF) 데이터 (.arff) 
* R 개체 또는 작업 영역 파일(. RData)
* 데이터 집합 형식 (. data). 데이터 집합 형식은 모듈 출력에 대 한 스튜디오 (클래식) 내부 데이터 형식입니다.

데이터 집합을 CSV로 변환 하 고 결과를 다운로드 하려면 다음을 수행 합니다.

1. 스튜디오 (클래식) 작업 영역으로 이동 [https://studio.azureml.net](https://studio.azureml.net) 합니다 ().
1. 새로운 실험 만들기
1. 다운로드 하려는 데이터 집합을 캔버스로 끌어다 놓습니다.
1. **CSV로 변환** 모듈을 추가 합니다.
1. CSV 입력 포트로 **변환** 을 데이터 집합의 출력 포트에 연결 합니다.
1. 실험을 실행합니다.
1. **CSV로 변환** 모듈을 마우스 오른쪽 단추로 클릭 합니다.
1. **결과 데이터 집합**  >  **다운로드** 를 선택 합니다.

    ![CSV로 변환 파이프라인을 설정 하는 방법을 보여 주는 스크린샷](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Azure Machine Learning에 데이터 집합 업로드

데이터 파일을 다운로드 한 후 Azure Machine Learning에서 데이터 집합을 등록할 수 있습니다.

1. Azure Machine Learning studio ([ml.azure.com](https://ml.azure.com))로 이동 합니다.
1. 왼쪽 탐색 창에서 **데이터 집합** 탭을 선택 합니다.
1. 로컬 파일에서 **데이터 집합 만들기**  >  **를** 선택 합니다.
    ![로컬 파일을 만들기 위한 데이터 집합 탭 및 단추를 보여 주는 스크린샷](./media/migrate-register-dataset/register-dataset.png)
1. 이름 및 설명을 입력합니다.
1. **데이터 집합 형식** 에 대해 **테이블 형식** 을 선택 합니다.

    > [!NOTE]
    > ZIP 파일을 데이터 집합으로 업로드할 수도 있습니다. ZIP 파일을 업로드 하려면 **데이터 집합 형식** 에 대해 **파일** 을 선택 합니다.

1. 데이터 **저장소 및 파일 선택의** 경우 데이터 집합 파일을 업로드할 데이터 저장소를 선택 합니다.

    기본적으로 Azure Machine Learning는 기본 작업 영역 blobstore에 데이터 집합을 저장 합니다. Datastores에 대 한 자세한 내용은 [저장소 서비스에 연결](../how-to-access-data.md)을 참조 하세요.

1. 데이터 집합에 대 한 데이터 구문 분석 설정 및 스키마를 설정 합니다. 그런 다음 설정을 확인 합니다.

## <a name="import-data-from-cloud-sources"></a>클라우드 원본에서 데이터 가져오기

데이터가 이미 클라우드 저장소 서비스에 있는 경우 데이터를 기본 위치에 유지 하려는 경우 다음 옵션 중 하나를 사용할 수 있습니다.

|수집 방법|Description|
|---| --- |
|Azure Machine Learning 데이터 집합 등록|로컬 및 온라인 데이터 원본 (Blob, ADLS Gen1, ADLS Gen2, 파일 공유, SQL DB)에서 데이터를 수집 합니다. <br><br>런타임에 지연 계산 되는 데이터 소스에 대 한 참조를 만듭니다. 이 데이터 집합에 반복적으로 액세스 하 고 데이터 버전 관리 및 모니터링과 같은 고급 데이터 기능을 사용 하려는 경우이 옵션을 사용 합니다.
|데이터 가져오기 모듈|온라인 데이터 원본 (Blob, ADLS Gen1, ADLS Gen2, 파일 공유, SQL DB)에서 데이터를 수집 합니다. <br><br> 데이터 집합은 현재 디자이너 파이프라인 실행 으로만 가져오게 됩니다.


>[!Note]
> Studio (클래식) 사용자는 다음과 같은 클라우드 원본이 Azure Machine Learning에서 기본적으로 지원 되지 않는 점에 유의 해야 합니다.
> - 하이브 쿼리
> - Azure 테이블
> - Azure Cosmos DB
> - 온-프레미스 SQL Database
>
> 사용자가 Azure Data Factory를 사용 하 여 지원 되는 저장소 서비스로 데이터를 마이그레이션하는 것이 좋습니다.  

### <a name="register-an-azure-machine-learning-dataset"></a>Azure Machine Learning 데이터 집합 등록

다음 단계를 사용 하 여 클라우드 서비스에서 Azure Machine Learning 데이터 집합을 등록 합니다. 

1. 클라우드 저장소 서비스를 Azure Machine Learning 작업 영역에 연결 하는 [데이터 저장소를 만듭니다](../how-to-connect-data-ui.md#create-datastores). 

1. [데이터 집합을 등록](../how-to-connect-data-ui.md#create-datasets)합니다. Studio (클래식) 데이터 집합을 마이그레이션하는 경우 **테이블 형식** 데이터 집합 설정을 선택 합니다.

Azure Machine Learning에서 데이터 집합을 등록 한 후 디자이너에서 사용할 수 있습니다.
 
1. 새 디자이너 파이프라인 초안을 만듭니다.
1. 왼쪽의 모듈 팔레트에서 **데이터 집합** 섹션을 확장 합니다.
1. 등록 된 데이터 집합을 캔버스로 끌어다 놓습니다. 

### <a name="use-the-import-data-module"></a>데이터 가져오기 모듈 사용

다음 단계를 사용 하 여 데이터를 디자이너 파이프라인으로 직접 가져올 수 있습니다.

1. 클라우드 저장소 서비스를 Azure Machine Learning 작업 영역에 연결 하는 [데이터 저장소를 만듭니다](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores). 

데이터 저장소를 만든 후 디자이너의 [**데이터 가져오기**](../algorithm-module-reference/import-data.md) 모듈을 사용 하 여 데이터를 수집할 수 있습니다.

1. 새 디자이너 파이프라인 초안을 만듭니다.
1. 왼쪽의 모듈 팔레트에서 **데이터 가져오기** 모듈을 찾아 캔버스로 끌어 옵니다.
1. **데이터 가져오기** 모듈을 선택 하 고 오른쪽 패널의 설정을 사용 하 여 데이터 원본을 구성 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Studio (클래식) 데이터 집합을 Azure Machine Learning로 마이그레이션하는 방법에 대해 알아보았습니다. 다음 단계는 [Studio (클래식) 학습 파이프라인을 다시 빌드하](migrate-rebuild-experiment.md)는 것입니다.


Studio (클래식) 마이그레이션 시리즈의 다른 문서를 참조 하세요.

1. [마이그레이션 개요](migrate-overview.md).
1. **데이터 집합을 마이그레이션합니다**.
1. [Studio (클래식) 교육 파이프라인을 다시 빌드합니다](migrate-rebuild-experiment.md).
1. [Studio (클래식) 웹 서비스를 다시 빌드합니다](migrate-rebuild-web-service.md).
1. [Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. [R 스크립트 실행을 마이그레이션합니다](migrate-execute-r-script.md).
