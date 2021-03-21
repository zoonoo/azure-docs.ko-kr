---
title: Apache Spark에 대 한 Python 라이브러리 관리
description: Azure Synapse Analytics에서 Apache Spark에 사용 되는 Python 라이브러리를 추가 하 고 관리 하는 방법을 알아봅니다.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 2d6ac02402414f096a46fec0340c3074d8e1784a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586644"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Apache Spark에 대 한 Python 라이브러리 관리

라이브러리는 프로그램 또는 프로젝트에 포함 시킬 수 있는 재사용 가능한 코드를 제공 합니다. 

여러 가지 이유로 서버를 사용 하지 않는 Apache Spark 풀 환경을 업데이트 해야 할 수 있습니다. 예를 들어 다음을 확인할 수 있습니다.
- 핵심 종속성 중 하나는 새 버전을 출시 했습니다.
- machine learning 모델을 학습 하거나 데이터를 준비 하기 위한 추가 패키지가 필요 합니다.
- 더 나은 패키지를 찾았지만 이전 패키지가 더 이상 필요 하지 않습니다.

응용 프로그램에서 타사 또는 로컬로 빌드된 코드를 사용할 수 있도록 하려면 서버를 사용 하지 않는 Apache Spark 풀 또는 노트북 세션 중 하나에 라이브러리를 설치 하면 됩니다. 이 문서에서는 서버를 사용 하지 않는 Apache Spark 풀에서 Python 라이브러리를 관리 하는 방법을 설명 합니다.

## <a name="default-installation"></a>기본 설치
Azure Synapse Analytics의 Apache Spark에는 일반적인 데이터 엔지니어링, 데이터 준비, 기계 학습 및 데이터 시각화 작업을 위한 전체 라이브러리 집합이 있습니다. 전체 라이브러리 목록은 [Apache Spark 버전 지원](apache-spark-version-support.md)에서 찾을 수 있습니다. 

Spark 인스턴스가 시작 되 면 이러한 라이브러리가 자동으로 포함 됩니다. 추가 Python 및 사용자가 작성 한 패키지는 Spark 풀 및 세션 수준에서 추가할 수 있습니다.

## <a name="pool-libraries"></a>풀 라이브러리
Spark 응용 프로그램에 사용할 Python 라이브러리를 확인 한 후에는 Spark 풀에 설치할 수 있습니다. 풀 수준 라이브러리는 풀에서 실행 되는 모든 노트북 및 작업에 사용할 수 있습니다.

클러스터에 라이브러리를 설치 하는 기본 방법에는 다음 두 가지가 있습니다.
-  작업 영역 패키지로 업로드 된 작업 영역 라이브러리를 설치 합니다.
-  PyPI, Conda-대장간 등의 리포지토리에서 패키지를 설치 하는 *requirements.txt* 또는 Conda 환경을 제공 *합니다.*

> [!IMPORTANT]
> - 설치 하는 패키지가 크거나 설치 하는 데 시간이 오래 걸리는 경우 Spark 인스턴스 시작 시간에 영향을 줍니다.
> - PySpark, Python, Scala/Java, .NET 또는 Spark 버전을 변경 하는 것은 지원 되지 않습니다.
> - PyPI, Conda 또는 기본 Conda 채널과 같은 외부 리포지토리에서 패키지를 설치 하는 작업은 DEP 사용 작업 영역 내에서 지원 되지 않습니다.

### <a name="install-python-packages"></a>Python 패키지 설치
환경 사양 파일을 제공 하 여 PyPI 및 Conda-Forge 같은 리포지토리에서 Python 패키지를 설치할 수 있습니다. 

#### <a name="environment-specification-formats"></a>환경 사양 형식

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
*requirements.txt* 파일 (명령의 출력)을 `pip freeze` 사용 하 여 환경을 업그레이드할 수 있습니다. 풀이 업데이트 되 면이 파일에 나열 된 패키지가 PyPI에서 다운로드 됩니다. 그런 다음 풀을 나중에 다시 사용할 수 있도록 전체 종속성이 캐시 되어 저장 됩니다. 

다음 코드 조각에서는 요구 사항 파일의 형식을 보여 줍니다. PyPI 패키지 이름은 정확한 버전과 함께 나열 됩니다. 이 파일은 [pip 고정](https://pip.pypa.io/en/stable/reference/pip_freeze/) 참조 설명서에 설명 된 형식을 따릅니다. 

이 예제에서는 특정 버전을 고정 합니다. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML 형식 (미리 보기)
또한 *환경 .yml* 파일을 제공 하 여 풀 환경을 업데이트할 수도 있습니다. 이 파일에 나열 된 패키지는 기본 Conda 채널, Conda-대장간 및 PyPI에서 다운로드 됩니다. 구성 옵션을 사용 하 여 다른 채널을 지정 하거나 기본 채널을 제거할 수 있습니다.

이 예에서는 채널과 Conda/PyPI 종속성을 지정 합니다. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
이 환경에서 환경을 만드는 방법에 대 한 자세한 내용은 yml 파일 [에서 환경 만들기](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually)를 참조 하세요.

#### <a name="update-python-packages"></a>Python 패키지 업데이트
Spark 풀에 설치 하려는 환경 사양 파일이 나 라이브러리 집합을 식별 한 후에는 Azure Synapse Studio 또는 Azure Portal로 이동 하 여 Spark 풀 라이브러리를 업데이트할 수 있습니다. 여기서 환경 사양을 제공 하 고 설치할 작업 영역 라이브러리를 선택할 수 있습니다. 

변경 내용이 저장 되 면 Spark 작업은 설치를 실행 하 고 나중에 다시 사용할 수 있도록 결과 환경을 캐시 합니다. 작업이 완료 되 면 새 Spark 작업 또는 노트북 세션에서 업데이트 된 풀 라이브러리를 사용 합니다. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Azure Synapse Studio 또는 Azure Portal에서 패키지 관리
Spark 풀 라이브러리는 Azure Synapse Studio 또는 Azure Portal에서 관리할 수 있습니다. 

Spark 풀에 라이브러리를 업데이트 하거나 추가 하려면:
1. Azure Portal에서 Azure Synapse Analytics 작업 영역으로 이동 합니다.

    **Azure Portal** 에서 업데이트 하는 경우:

    - **Synapse 리소스** 섹션 아래에서 **Apache Spark 풀** 탭을 선택 하 고 목록에서 Spark 풀을 선택 합니다.
     
    - Spark 풀의 **설정** 섹션에서 **패키지** 를 선택 합니다.
  
    ![환경 구성 파일 업로드 단추를 강조 표시 하는 스크린샷](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python 라이브러리 추가")
   
    **Synapse Studio** 에서 업데이트 하는 경우:
    - 기본 탐색 패널에서 **관리** 를 선택한 다음 **Apache Spark 풀** 을 선택 합니다.

    - 특정 Spark 풀에 대 한 **패키지** 섹션을 선택 합니다.
    ![Studio에서 업로드 환경 구성 옵션을 강조 표시 하는 스크린샷](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Studio에서 Python 라이브러리 추가")
   
2. 페이지의  **패키지** 섹션에서 파일 선택기를 사용 하 여 환경 구성 파일을 업로드 합니다.
3. 추가 **작업 영역 패키지** 를 선택 하 여 풀에 추가할 수도 있습니다. 
4. 변경 내용을 저장 하면 지정 된 라이브러리를 설치 하 고 캐시 하는 시스템 작업이 트리거됩니다. 이 프로세스를 통해 전체 세션 시작 시간을 줄일 수 있습니다. 
5. 작업이 성공적으로 완료 되 면 모든 새 세션에서 업데이트 된 풀 라이브러리를 선택 합니다.

> [!IMPORTANT]
> **새 설정을 강제 적용** 하는 옵션을 선택 하면 선택한 Spark 풀의 모든 현재 세션이 종료 됩니다. 세션이 종료 되 면 풀이 다시 시작 될 때까지 기다려야 합니다. 
>
> 이 설정을 선택 하지 않으면 현재 Spark 세션이 종료 될 때까지 기다리거나 수동으로 중지 해야 합니다. 세션이 종료 되 면 풀을 다시 시작 하도록 해야 합니다.


##### <a name="track-installation-progress-preview"></a>설치 진행률 추적 (미리 보기)
시스템이 예약 된 Spark 작업은 새 라이브러리 집합으로 풀이 업데이트 될 때마다 시작 됩니다. 이 Spark 작업은 라이브러리 설치 상태를 모니터링 하는 데 도움이 됩니다. 라이브러리 충돌이 나 기타 문제로 인해 설치가 실패 하는 경우 Spark 풀이 이전 또는 기본 상태로 되돌려집니다. 

또한 사용자는 설치 로그를 검사 하 여 종속성 충돌을 식별 하거나 풀 업데이트 중에 설치 된 라이브러리를 확인할 수도 있습니다.

이러한 로그를 보려면 다음을 수행 합니다.
1. **모니터** 탭에서 Spark 응용 프로그램 목록으로 이동 합니다. 
2. 풀 업데이트에 해당 하는 시스템 Spark 응용 프로그램 작업을 선택 합니다. 이러한 시스템 작업은 *SystemReservedJob 관리* 제목으로 실행 됩니다.
   ![시스템 예약 라이브러리 작업을 강조 표시 하는 스크린샷](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "시스템 라이브러리 작업 보기")
3. 스위치를 전환 하 여 **드라이버** 및 **stdout** 로그를 봅니다. 
4. 결과 내에서 종속성의 설치와 관련 된 로그가 표시 됩니다.
    ![시스템 예약 라이브러리 작업 결과를 강조 표시 하는 스크린샷](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "시스템 라이브러리 작업 진행률 보기")

## <a name="install-wheel-files"></a>휠 파일 설치
Python 휠 파일은 Python 라이브러리를 패키징하는 일반적인 방법입니다. Azure Synapse Analytics 내에서 사용자는 자신의 휠 파일을 Azure Data Lake Storage 계정의 잘 알려진 위치에 업로드 하거나 Azure Synapse 작업 영역 패키지 인터페이스를 사용 하 여 업로드할 수 있습니다.

### <a name="workspace-packages-preview"></a>작업 영역 패키지 (미리 보기)
작업 영역 패키지는 사용자 지정 또는 개인 휠 파일 일 수 있습니다. 이러한 패키지를 작업 영역에 업로드 하 고 나중에 특정 Spark 풀에 할당할 수 있습니다.

작업 영역 패키지를 추가 하려면:
1.   >  **작업 영역 패키지** 관리 탭으로 이동 합니다.
2. 파일 선택기를 사용 하 여 휠 파일을 업로드 합니다.
3. 파일이 Azure Synapse 작업 영역에 업로드 된 후에는 지정 된 Apache Spark 풀에 이러한 패키지를 추가할 수 있습니다.

![작업 영역 패키지를 강조 표시 하는 스크린샷](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "작업 영역 패키지 보기")

>[!WARNING]
>- Azure Synapse 내에서 Apache Spark 풀은 작업 영역 패키지로 업로드 되거나 잘 알려진 Azure Data Lake Storage 경로 내에 업로드 된 사용자 지정 라이브러리를 활용할 수 있습니다. 그러나 이러한 두 옵션은 동일한 Apache Spark 풀 내에서 동시에 사용할 수 없습니다. 두 방법을 모두 사용 하 여 패키지를 제공 하면 작업 영역 패키지 목록에 지정 된 휠 파일만 설치 됩니다. 
>
>- 지정 된 Apache Spark 풀에 패키지를 설치 하는 데 작업 영역 패키지 (미리 보기)를 사용 하는 경우 동일한 풀에서 저장소 계정 경로를 사용 하 여 패키지를 더 이상 지정할 수 없다는 제한이 있습니다.  

### <a name="storage-account"></a>스토리지 계정
Synapse 작업 영역에 연결 된 Azure Data Lake Storage (Gen2) 계정에 모든 휠 파일을 업로드 하 여 Apache Spark 풀에 사용자 지정 기반 휠 패키지를 설치할 수 있습니다. 

저장소 계정의 기본 컨테이너에 있는 다음 경로에 파일을 업로드 해야 합니다. 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> 아직 존재 하지 않는 경우 위의 구조를 기반으로 파일 경로를 만들어야 하는 경우도 있습니다. 예를 들어 폴더가 ```python``` 아직 없는 경우 폴더 내에 폴더를 추가 해야 할 수 있습니다 ```libraries``` .

> [!IMPORTANT]
> Azure DataLake 저장소 메서드를 사용 하 여 사용자 지정 라이브러리를 설치 하려면 Azure Synapse Analytics 작업 영역에 연결 된 기본 Gen2 저장소 계정에 대 한 **저장소 Blob 데이터 참가자** 또는 **저장소 blob 데이터 소유자** 권한이 있어야 합니다.


## <a name="session-scoped-packages-preview"></a>세션 범위 패키지 (미리 보기)
풀 수준 패키지 외에도 노트북 세션의 시작 부분에 세션 범위 라이브러리를 지정할 수 있습니다.  세션 범위 라이브러리를 사용 하면 노트북 세션 내에서 사용자 지정 Python 환경을 지정 하 고 사용할 수 있습니다. 

세션 범위 라이브러리를 사용 하는 경우 다음 사항을 염두에 두어야 합니다.
   - 세션 범위 라이브러리를 설치할 때 현재 전자 필기장과 지정 된 라이브러리에 대 한 액세스 권한이 있습니다. 
   - 이러한 라이브러리는 동일한 Spark 풀을 사용 하는 다른 세션 또는 작업에 영향을 주지 않습니다. 
   - 이러한 라이브러리는 기본 런타임 및 풀 수준 라이브러리 위에 설치 됩니다. 
   - 노트북 라이브러리는 우선 순위가 가장 높습니다.

세션 범위 패키지를 지정 하려면:
1.  선택한 Spark 풀로 이동 하 여 세션 수준 라이브러리를 사용 하도록 설정 했는지 확인 합니다.  Apache Spark 풀 패키지 **관리** 탭으로 이동 하 여이 설정을 사용 하도록 설정할 수 있습니다  >    >   . ![세션 패키지를 사용 하도록 설정](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "세션 패키지 사용") 합니다.
2.  설정이 적용 된 후에는 노트북을 열고 **세션 패키지 구성** 을 선택할 수 있습니다 >  .
  ![세션 패키지를 지정 합니다.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "세션 구성 업데이트")
3.  여기에서 Conda을 업로드 하 여 세션 내에서 패키지를 설치 하거나 업그레이드할 수 있습니다 *.* 세션을 시작 하면 지정 된 라이브러리가 설치 됩니다. 세션이 종료 되 면 해당 라이브러리는 사용자의 세션과 관련이 있으므로 더 이상 사용할 수 없습니다.

## <a name="verify-installed-libraries"></a>설치 된 라이브러리 확인
올바른 라이브러리의 올바른 버전이 PyPI에서 설치 되었는지 확인 하려면 다음 코드를 실행 합니다.
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
경우에 따라 Conda에서 패키지 버전을 보려면 패키지 버전을 개별적으로 검사 해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 기본 라이브러리 보기: [Apache Spark 버전 지원](apache-spark-version-support.md)
- 라이브러리 설치 오류 문제 해결: [라이브러리 오류 문제 해결](apache-spark-troubleshoot-library-errors.md)
- Azure Data Lake Storage 계정을 사용 하 여 개인 Conda 채널을 만듭니다. [Conda 개인 채널](./spark/../apache-spark-custom-conda-channel.md)
