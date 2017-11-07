---
title: "2017년 10월, 스프린트 0에 대한 Azure ML Workbench 릴리스 정보"
description: "이 문서는 Azure ML 스프린트 0 릴리스의 업데이트에 대해 자세히 설명합니다."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="sprint-0---october-2017"></a>스프린트 0 - 2017년 10월 

**버전 번호: 0.1.1710.04013**

Microsoft Ignite 2017 컨퍼런스에서 최초 공용 미리 보기를 선보인 후에 출시된 Azure Machine Learning Workbench 최초 업데이트입니다. 이 릴리스에서는 주로 안정성이 개선되고 안정화 문제가 수정되었습니다.  해결된 중요한 문제 일부는 다음과 같습니다.

## <a name="new-features"></a>새로운 기능
- macOS High Sierra가 이제 지원됩니다.

## <a name="bug-fixes"></a>버그 수정
### <a name="workbench-experience"></a>Workbench 환경
- Workbench로 파일을 끌어다 놓으면 Workbench가 충돌합니다.
- Workbench용 IDE로 구성된 VS 코드의 터미널 창이 _az ml_ 명령을 인식하지 못합니다.

### <a name="workbench-authentication"></a>Workbench 인증
여러 업데이트를 통해 보고된 다양한 로그인 및 인증 문제를 개선했습니다.
- 특히 인터넷 연결이 안정적이지 않은 경우 인증 창이 계속 팝업됩니다.
- 인증 토큰 만료에 대한 안정성 문제가 개선되었습니다.
- 경우에 따라 인증 창이 두 번 나타납니다.
- 인증 프로세스가 실제로 완료되고 팝업 대화 상자가 이미 닫혀도 Workbench 주 창에는 여전히 "인증 중" 메시지가 표시됩니다.
- 인터넷 연결이 없는 인증 대화 상자가 빈 화면으로 팝업됩니다.

### <a name="data-preparation"></a>데이터 준비 
- 특정 값이 필터링되면 오류 및 누락 값도 필터링됩니다.
- 샘플링 전략을 변경하면 기존의 후속 조인 작업이 제거됩니다.
- 누락 값 대체 변환 시 NaN가 고려되지 않습니다.
- Null 값이 발견되면 데이터 형식 유추가 예외를 throw합니다.

### <a name="job-execution"></a>작업 실행
- 작업 실행이 크기 제한을 초과하기 때문에 프로젝트 폴더를 업로드하지 못한다는 명확한 오류 메시지가 없습니다.
- Python 스크립트가 작업 디렉터리를 변경하는 경우 출력 폴더에 기록된 파일이 추적되지 않습니다. 
- 활성 Azure 구독이 현재 프로젝트가 속한 구독과 다를 경우 작업 제출 시 403 오류가 발생합니다.
- Docker가 없는 경우 사용자가 Docker를 실행 대상으로 사용하려고 할 경우 명확한 오류 메시지가 반환되지 않습니다.
- _실행_ 단추를 클릭하면 .runconfig 파일이 자동으로 저장되지 않습니다.

### <a name="jupyter-notebook"></a>Jupyter 노트북
- 사용자가 특정 로그인 유형을 사용하는 경우 노트북 서버를 시작할 수 없습니다.
- 노트북 서버 오류 메시지는 사용자가 볼 수 있는 로그에 표시되지 않습니다.

### <a name="azure-portal"></a>Azure 포털
- Azure Portal의 어두운 테마를 선택하면 모델 관리 블레이드가 검은색 상자로 표시됩니다.

### <a name="operationalization"></a>운영화
- 매니페스트를 사용하여 웹 서비스를 업데이트하면 임의 이름의 Docker 이미지가 새로 빌드됩니다.
- 웹 서비스 로그를 Kubernetes 클러스터에서 검색할 수 없습니다.
- 사용자가 모델 관리 계정이나 ML Compute 계정 키를 만들려고 할 때 사용 권한 문제가 발생하면 잘못된 오류 메시지가 출력됩니다.
