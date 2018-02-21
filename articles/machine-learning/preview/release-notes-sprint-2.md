---
title: "2017년 12월, 스프린트 2에 대한 Azure ML Workbench 릴리스 정보"
description: "이 문서에서는 Azure ML의 스프린트 2 릴리스에 대한 업데이트를 자세히 설명합니다."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: 630e6e22bb41c777a043a7e6580239e254db9f1f
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2018
---
# <a name="sprint-2---december-2017"></a>스프린트 2 - 2017년 12월 

#### <a name="version-number-01171115263"></a>버전 번호: 0.1.1711.15263

>다음은 [버전 번호를 찾는 방법](known-issues-and-troubleshooting-guide.md)입니다.

세 번째 Azure Machine Learning Workbench 업데이트를 시작합니다. 이 업데이트는 Workbench 앱, CLI(명령줄 인터페이스) 및 백 엔드 서비스의 향상된 기능을 포함합니다. 웃는 얼굴과 찡그린 얼굴을 보내 주시어 대단히 감사합니다. 다음 업데이트 중 많은 부분이 여러분이 보내 주신 피드백에 따른 직접적인 결과입니다. 

## <a name="notable-new-features"></a>주목할 만한 새로운 기능
- [데이터 원본으로서 SQL Server 및 Azure SQL DB 지원](data-prep-appendix2-supported-data-sources.md#types) 
- [MMLSpark를 사용하여 GPU 지원이 포함된 Spark에 대한 딥 러닝](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [배포 시 모든 AML 컨테이너는 Azure IoT Edge 장치와 호환됨(추가 단계 필요 없음)](http://aka.ms/aml-iot-edge-blog)
- Azure Portal에서 사용 가능한 등록된 모델 목록 및 세부 정보 보기
- 사용자 이름/암호 기반 액세스 이외에 SSH 키 기반 인증을 사용하여 계산 대상 액세스 
- 데이터 준비 환경의 새 패턴 빈도 검사기 

## <a name="detailed-updates"></a>자세한 업데이트
다음은 이 스프린트의 Azure Machine Learning 구성 요소 영역 각각에 대한 자세한 업데이트 목록입니다.

### <a name="installer"></a>설치 관리자
- 설치 관리자는 사용자가 다시 설치 필요 없이 버그 수정 및 새 기능을 지원할 수 있도록 자체 업데이트할 수 있습니다.

### <a name="workbench-authentication"></a>Workbench 인증
- 인증 시스템에 대한 여러 수정. 여전히 로그인 문제가 발생하는 경우 저희에게 알려주세요.
- 프록시 관리자 설정을 쉽게 찾을 수 있도록 UI가 변경되었습니다.

### <a name="workbench"></a>워크벤치
- 이제 읽기 전용 파일 보기에는 연한 파랑색 배경이 표시됩니다.
- 더 쉽게 검색할 수 있도록 편집 버튼이 오른쪽으로 이동되었습니다.
- 이제 "dsource", "dprep" 및 "ipynb" 파일 형식을 원시 텍스트 형식으로 렌더링할 수 있습니다.
- 이제 Workbench에는 사용자가 외부 IDE를 사용하여 스크립트를 편집하고 Workbench만 사용해도 풍부한 편집 환경(예: 노트북, 데이터 원본, 데이터 준비 패키지)에서 파일 형식을 편집할 수 있도록 안내하는 새로운 편집 환경이 있습니다.
- 사용자가 액세스할 수 있는 작업 영역 및 프로젝트의 목록이 이제 훨씬 빠르게 로드됩니다.

### <a name="data-preparation"></a>데이터 준비 
- 열의 문자열 패턴을 볼 수 있는 패턴 빈도 검사기. 이러한 패턴을 사용하여 데이터를 필터링할 수도 있습니다. 값 개수 검사기와 비슷한 보기가 표시됩니다. 패턴 빈도에는 고유 데이터의 개수가 아니라 데이터의 고유 패턴 개수가 표시된다는 점이 다릅니다. 또한 특정 패턴에 맞는 모든 행을 필터링할 수도 있습니다.

![제품 번호에 대한 패턴 빈도 검사기 이미지](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- '예제별 열 파생' 변환에서 극단적인 사례를 검토하도록 권장하면서 성능 개선

- [데이터 원본으로서 SQL Server 및 Azure SQL DB 지원](data-prep-appendix2-supported-data-sources.md#types) 

![새 SQL Server 데이터 원본 만들기 이미지](media/release-notes-sprint-2/sql-server-data-source.png)

- 행 및 열 개수의 "개요" 보기를 사용하도록 설정

![행 및 열 개수의 개요 보기 이미지](media/release-notes-sprint-2/row-col-count.png)

- 모든 계산 컨텍스트에서 데이터 준비 사용
- SQL Server 데이터베이스를 사용하는 데이터 원본을 모든 계산 컨텍스트에서 사용 가능
- 데이터 준비 그리드 열을 데이터 형식별로 필터링 가능
- 여러 열을 날짜로 변환할 때의 문제 해결
- 사용자가 고급 모드에서 출력 열 이름을 변경한 경우 예제별 열 파생에서 출력 열을 원본으로 선택할 수 있는 문제 해결

### <a name="job-execution"></a>작업 실행
이제 다음 단계에 따라 SSH 키 기반 인증을 사용하여 remotedocker 또는 클러스터 유형 계산 대상을 만들고 액세스할 수 있습니다.
- CLI에서 다음 명령을 사용하여 계산 대상을 연결합니다.

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] 이 명령의 -k(또는 --use-azureml-ssh-key) 옵션은 SSH 키를 사용하여 사용하도록 지정합니다.

- Azure ML Workbench가 공개 키를 생성하여 콘솔에 출력합니다. 동일한 사용자 이름을 사용하여 계산 대상에 로그인하고 이 공개 키가 포함된 ~/.ssh/authorized_keys 파일을 추가합니다.

- 이 계산 대상을 준비하고 실행에 사용할 수 있으며 Azure ML Workbench는 인증을 위해 이 키를 사용합니다.  

계산 대상 만들기에 대한 자세한 내용은 [Azure Machine Learning 실험 서비스 구성](experimentation-service-configuration.md)을 참조하세요.

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017)에 대한 추가 지원입니다. 

### <a name="command-line-interface-cli"></a>CLI(Command Line Interface)
- 추가된 `az ml datasource create` 명령을 사용하면 명령줄에서 데이터 원본 파일을 만들 수 있습니다.

### <a name="model-management-and-operationalization"></a>모델 관리 및 운영화
- [운영화 시 모든 AML 컨테이너는 Azure IoT Edge 장치와 호환됨(추가 단계 필요 없음)](http://aka.ms/aml-iot-edge-blog) 
- o16n CLI의 오류 메시지 개선 사항
- 모델 관리 포털 UX에서 버그 수정  
- 세부 정보 페이지에서 모델 관리 특성에 대한 일관된 대/소문자 구분
- 실시간 점수 매기기 호출 시간 제한을 60초로 설정
- Azure Portal에서 사용 가능한 등록된 모델 목록 및 세부 정보 보기

![포털의 모델 세부 정보](media/release-notes-sprint-2/model-list.jpg)

![포털의 모델 개요](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- [GPU 지원](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)이 포함된 Spark에 대한 딥 러닝
- 손쉬운 리소스 배포를 위한 리소스 관리자 템플릿 지원
- SparklyR 에코시스템에 대한 지원
- [AZTK 통합](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>샘플 프로젝트
- 새 Azure ML SDK 버전으로 [Iris](https://github.com/Azure/MachineLearningSamples-Iris) 및 [MMLSpark](https://github.com/Azure/mmlspark) 샘플 업데이트

## <a name="breaking-changes"></a>주요 변경 내용
- `az ml computetarget attach`에서 하위 명령으로 `--type` 스위치 승격 

    - `az ml computetarget attach --type remotedocker`는 이제 `az ml computetarget attach remotedocker`입니다.
    - `az ml computetarget attach --type cluster`는 이제 `az ml computetarget attach cluster`입니다.
