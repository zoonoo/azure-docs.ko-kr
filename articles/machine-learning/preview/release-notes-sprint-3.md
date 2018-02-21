---
title: "2018년 1월, 스프린트 3에 대한 Azure ML Workbench 릴리스 정보"
description: "이 문서에서는 Azure ML의 스프린트 3 릴리스에 대한 업데이트를 자세히 설명합니다."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/22/2018
ms.openlocfilehash: 266a56d5e247fd4926031e563c8be302599838eb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2018
---
# <a name="sprint-3---january-2018"></a>스프린트 3 - 2018년 1월 

#### <a name="version-number-01171218263"></a>버전 번호: 0.1.1712.18263

>다음은 [버전 번호를 찾는 방법](known-issues-and-troubleshooting-guide.md)입니다.

네 번째 Azure Machine Learning Workbench 업데이트를 시작합니다. 다음은 이번 스프린트의 업데이트 및 개선 사항입니다. 아래 업데이트 중 많은 부분은 사용자가 보내 주신 피드백에 따른 직접적인 결과입니다. 

## <a name="notable-new-features-and-changes"></a>주목할 만한 새로운 기능 및 변경 사항
- 시작 시 인증 스택 강제 로그인 및 계정 선택이 업데이트됨

## <a name="detailed-updates"></a>자세한 업데이트
다음은 이 스프린트의 Azure Machine Learning 구성 요소 영역 각각에 대한 자세한 업데이트 목록입니다.

### <a name="workbench"></a>워크벤치
- 프로그램 추가/제거에서 앱 설치/제거 기능
- 시작 시 인증 스택 강제 로그인 및 계정 선택이 업데이트됨
- Windows의 SSO(Single Sign-On) 환경 개선
- 자격 증명이 다른 여러 테넌트에 속하는 사용자는 Workbench에 로그인 할 수 없음

#### <a name="ui"></a>UI
- 일반적인 개선 사항 및 버그 수정

### <a name="notebooks"></a>노트북
- 일반적인 개선 사항 및 버그 수정

### <a name="data-preparation"></a>데이터 준비 
- 예제별 변환을 수행하는 동안 자동 제안 기능 향상
- 패턴 빈도 검사기에 대한 알고리즘 개선
- 예제별 변환을 수행하는 동안 샘플 데이터 및 피드백을 전송하는 기능 ![장치 열 변환의 피드백 보내기 링크 이미지](media/release-notes-sprint-3/SendFeedbackFromDeriveColumn.png)
- Spark 런타임 개선
- Scala가 Pyspark를 대체
- 시계열 검사기에서 Data Not Applicable(데이터가 적용되지 않음)를 닫을 수 없는 문제 해결 
- HDI의 데이터 준비 실행에 대한 중단 시간 수정

### <a name="model-management-cli-updates"></a>모델 관리 CLI 업데이트 
  - 리소스 프로비저닝을 위해 구독 소유권이 더 이상 필요하지 않습니다. 리소스 그룹에 대한 contributor 액세스만으로 배포 환경을 설정할 수 있습니다.
  - 무료 구독을 위한 로컬 환경 설정을 사용하도록 설정됨 
