---
title: 사용자 지정 기술 크기 조정 및 관리
titleSuffix: Azure Cognitive Search
description: 최대 처리량에 대 한 사용자 지정 기술을 효율적으로 확장 하기 위한 도구 및 기술에 대해 알아봅니다. 사용자 지정 기술은 Azure Cognitive Search에서 AI-보강 인덱싱 파이프라인에 추가할 수 있는 사용자 지정 AI 모델 또는 논리를 호출 합니다.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: ccee8537c2543b67c1bb60711737ec761ebc8cbf
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043073"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>사용자 지정 기술 효율적 확장

사용자 지정 기술은 특정 인터페이스를 구현 하는 웹 Api입니다. 사용자 지정 기술은 공개적으로 주소 지정이 가능한 리소스에 구현할 수 있습니다. 사용자 지정 기술에 대 한 가장 일반적인 구현은 다음과 같습니다.
* 사용자 지정 논리 기술에 대 한 Azure Functions
* Simple 컨테이너 화 된 AI 기술을 위한 Azure Webapps
* 더 복잡 하거나 큰 기술을 위한 Azure Kubernetes 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

+ 사용자 지정 기술에서 구현 해야 하는 입력/출력 인터페이스에 대 한 소개를 보려면 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md) 를 검토 합니다.

+ 환경을 설정 합니다. [이 자습서](/python/tutorial-vs-code-serverless-python-01) 를 시작 하 여 Visual Studio Code 및 Python 확장을 사용 하 여 서버 리스 Azure 함수를 설정할 수 있습니다.

## <a name="skillset-configuration"></a>기술 구성

인덱싱 프로세스의 처리량을 최대화 하기 위해 사용자 지정 기술을 구성 하려면 기술, 인덱서 구성 및 기술이 각 문서와 어떻게 관련 되는지를 이해 해야 합니다. 예를 들어, 문서 당 기술이 호출 되는 횟수와 호출 당 예상 된 기간을 말합니다.

### <a name="skill-settings"></a>기술 설정

[사용자 지정 기술](cognitive-search-custom-skill-web-api.md) 에서 다음 매개 변수를 설정 합니다.

1. 기술에 `batchSize` 대 한 단일 호출로 기술에 전송 되는 레코드 수를 구성 하는 사용자 지정 기술 집합입니다.

2. 를 설정 하 여 `degreeOfParallelism` 인덱서가 기술에 적용 하는 동시 요청 수를 보정 합니다.

3. `timeout`기술이 유효한 응답으로 응답 하는 데 충분 한 값으로 설정 합니다.

4. 정의에서 `indexer` 를 [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) 데이터 원본에서 읽고 동시에 보강 하는 문서 수로 설정 합니다.

### <a name="considerations"></a>고려 사항

인덱서 성능을 최적화 하기 위해 이러한 변수를 설정 하려면 많은 동시 작은 요청이 나 더 적은 수의 요청으로 기술이 더 잘 수행 되는지 확인 해야 합니다. 고려해 야 할 몇 가지 질문은 다음과 같습니다.

* 기술 호출 카디널리티는 무엇 인가요? 문서 분류 기술 처럼 각 문서에 대해 한 번 실행 되거나 문서 마다 여러 번 실행 되는 기술 (단락 분류 기술) 입니까?

* 데이터 원본에서 읽은 문서 수를 평균적으로 기술 일괄 처리 크기에 따라 기술 요청을 작성 하려면 어떻게 해야 하나요? 이상적으로는 인덱서 일괄 처리 크기 보다 작아야 합니다. 일괄 처리 크기가 1 보다 크면 기술은 여러 소스 문서에서 레코드를 받을 수 있습니다. 예를 들어 인덱서 일괄 처리 수가 5이 고 기술 일괄 처리 수가 50이 고 각 문서에서 5 개의 레코드만 생성 하는 경우 인덱서는 여러 인덱서 일괄 처리에 대해 사용자 지정 기술 요청을 채워야 합니다.

* 인덱서 일괄 처리가 생성할 수 있는 평균 요청 수는 병렬 처리 수준에 대 한 최적의 설정을 제공 해야 합니다. 기술을 호스트 하는 인프라에서 해당 수준의 동시성을 지원할 수 없는 경우 병렬 처리 수준으로 전화를 걸어 보세요. 모범 사례로, 몇 가지 문서를 사용 하 여 구성을 테스트 하 여 매개 변수에 대 한 선택 항목의 유효성을 검사 합니다.

* 작은 문서 샘플로 테스트 하 여 문서의 하위 집합을 처리 하는 데 걸리는 전체 시간에 대 한 기술 실행 시간을 평가 합니다. 인덱서가 일괄 처리를 작성 하는 데 더 많은 시간을 소비 하거나 기술에서 응답을 기다리고 있나요? 

* 병렬 처리의 업스트림 영향을 고려 합니다. 사용자 지정 기술에 대 한 입력이 이전 기술의 출력이 면 기술의 모든 기술이 대기 시간을 최소화 하기 위해 효과적으로 확장 되나요?

## <a name="error-handling-in-the-custom-skill"></a>사용자 지정 기술에서 오류 처리

기술이 성공적으로 완료 되 면 사용자 지정 기술이 성공 상태 코드 HTTP 200을 반환 해야 합니다. 일괄 처리에 있는 하나 이상의 레코드에 오류가 발생 하는 경우 다중 상태 코드 207을 반환 하는 것이 좋습니다. 레코드의 오류 또는 경고 목록에는 적절 한 메시지가 포함 되어야 합니다.

일괄 처리에서 오류가 발생 하는 모든 항목은 해당 문서에 오류가 발생 합니다. 문서가 성공 해야 하는 경우 경고를 반환 합니다.

299에 대 한 모든 상태 코드는 오류로 평가 되 고 모든 강화 실패 하 여 실패 한 문서를 생성 합니다. 

### <a name="common-error-messages"></a>일반적인 오류 메시지

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` 더 긴 실행 기간을 허용 하도록 기술에서 timeout 매개 변수를 설정 합니다.

* `Could not execute skill because Web Api skill response is invalid.` 사용자 지정 기술 응답 형식으로 메시지를 반환 하지 않는 기술입니다. 이는 기술에서 catch 되지 않은 예외의 결과일 수 있습니다.

* `Could not execute skill because the Web Api request failed.` 권한 부여 오류 또는 예외로 인해 발생 했을 가능성이 높습니다.

* `Could not execute skill.` 일반적으로 기술 응답의 결과는 문서 계층 구조의 기존 속성에 매핑됩니다.

## <a name="testing-custom-skills"></a>사용자 지정 기술 테스트

유효성을 검사 하려면 REST API 클라이언트를 사용 하 여 사용자 지정 기술을 테스트 하는 것으로 시작 합니다.

* 기술은 요청 및 응답에 대 한 사용자 지정 기술 인터페이스를 구현 합니다.

* 이 기술은 MIME 형식의 유효한 JSON을 반환 합니다. `application/JSON`

* 올바른 HTTP 상태 코드를 반환 합니다.

[디버그 세션](cognitive-search-debug-session.md) 을 만들어 기술에 기술을 추가 하 고 유효한 보강을 생성 하는지 확인 합니다. 디버그 세션에서 기술 성능을 튜닝할 수 있는 것은 아니지만,이 기능을 사용 하면 기술이 유효한 값으로 구성 되었는지 확인 하 고 예상 되는 보강 개체를 반환할 수 있습니다.

## <a name="best-practices"></a>모범 사례

* 기술이 더 큰 페이로드를 수락 하 고 반환할 수 있지만 JSON을 반환할 때 응답을 150 MB 이하로 제한 하는 것이 좋습니다.

* 인덱서 및 기술에서 일괄 처리 크기를 설정 하 여 각 데이터 원본 일괄 처리가 기술에 대 한 전체 페이로드를 생성 하도록 합니다.

* 장기 실행 작업의 경우 문서를 동시에 처리할 때 인덱서가 오류가 발생 하지 않도록 제한 시간을 충분히 큰 값으로 설정 합니다.

* 인덱서 일괄 처리 크기, 기술 일괄 처리 크기 및 기술 수준의 병렬 처리를 최적화 하 여 기술에 필요한 부하 패턴을 생성 하 고, 큰 요청 수가 적거나, 많은 작은 요청을 생성 합니다.

* 데이터 변동의 결과로 특정 요청이 일관 되 게 실패 하는 시나리오를 수행할 수 있으므로 자세한 오류 로그를 사용 하 여 사용자 지정 기술을 모니터링할 수 있습니다.


## <a name="next-steps"></a>다음 단계
축하합니다! 이제 사용자 지정 기술에 대 한 처리량을 최대화 하기 위해 올바른 크기를 조정 했습니다. 

+ [전원 기술: 사용자 지정 기술의 리포지토리입니다.](https://github.com/Azure-Samples/azure-search-power-skills)
+ [AI 보강 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
+ [Azure Machine Learning 기술 추가](./cognitive-search-aml-skill.md)
+ [디버그 세션을 사용 하 여 변경 내용 테스트](./cognitive-search-debug-session.md)