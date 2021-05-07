---
title: 사용자 지정 기술 스케일링 및 관리
titleSuffix: Azure Cognitive Search
description: 최대 처리량을 위해 사용자 지정 기술을 효율적으로 스케일 아웃하기 위한 도구와 기술을 알아봅니다. 사용자 지정 기술은 Azure Cognitive Search에서 AI - 보강 인덱싱 파이프라인에 추가할 수 있는 사용자 지정 AI 모델 또는 논리를 호출합니다.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 4fdc222fa20aef6639bf6d5d485f7dcf6b6ca535
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641133"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>사용자 지정 기술을 효율적으로 스케일 아웃

사용자 지정 기술은 특정 인터페이스를 구현하는 웹 API입니다. 사용자 지정 기술은 공개적으로 주소 지정 가능한 모든 리소스에서 구현할 수 있습니다. 사용자 지정 기술에 가장 일반적인 구현은 다음과 같습니다.
* 사용자 지정 논리 기술을 위한 Azure Functions
* 컨테이너화된 간단한 AI 기술을 위한 Azure Webapps
* 더 복잡하거나 더 대규모인 기술을 위한 Azure Kubernetes Service.

## <a name="prerequisites"></a>필수 구성 요소

+ 사용자 지정 기술이 구현해야 하는 입출력 인터페이스에 대한 소개는 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md)를 검토합니다.

+ 환경을 설정합니다. Visual Studio Code 및 Python 확장을 사용하여 서버리스 Azure 함수를 설정하려면 [관련 자습서 전체](/azure/azure-functions/create-first-function-vs-code-python)를 참조할 수 있습니다.

## <a name="skillset-configuration"></a>기술 세트 구성

인덱싱 프로세스의 처리량을 최대화하기 위해 사용자 지정 기술을 구성하려면 기술, 인덱서 구성 및 기술이 각 문서와 관련되는 방식을 이해해야 합니다. 예를 들어 문서당 기술이 호출된 횟수와 호출당 예상 기간이 있습니다.

### <a name="skill-settings"></a>기술 설정

[사용자 지정 기술](cognitive-search-custom-skill-web-api.md)에서 다음 매개 변수를 설정합니다.

1. 단일 기술 호출에서 기술에 전송되는 레코드 수를 구성하려면 사용자 지정 기술의 `batchSize`를 설정합니다.

2. `degreeOfParallelism`을 설정하여 인덱서가 기술에 대해 수행할 동시 요청 수를 보정합니다.

3. 기술이 유효한 응답으로 대응하기에 충분한 값으로 `timeout`을 설정합니다.

4. `indexer` 정의에서 [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters)를 데이터 원본에서 읽고 동시에 보강해야 하는 문서 수로 설정합니다.

### <a name="considerations"></a>고려 사항

인덱서 성능을 최적화하기 위해 해당 변수를 설정하려면 동시 소규모 요청이 많을 때와 대규모 요청이 적을 때 중 언제 기술이 더 잘 수행되는지 확인해야 합니다. 고려해야 할 몇 가지 질문은 다음과 같습니다.

* 기술 호출 카디널리티란? 기술이 문서(예: 문서 분류 기술)마다 한 번 실행되나요 아니면 기술이 문서(단락 분류 기술)당 여러 번 실행될 수 있나요?

* 기술 일괄 처리 크기에 따라 기술 요청을 작성하기 위해 데이터 원본에서 평균 몇 개의 문서를 읽나요? 인덱서 일괄 처리 크기보다 작아야 합니다. 일괄 처리 크기가 1보다 크면 기술은 여러 원본 문서에서 레코드를 받을 수 있습니다. 예를 들어 인덱서 일괄 처리 수가 5이고 기술 일괄 처리 수가 50이며 각 문서가 5개의 레코드만 생성하는 경우 인덱서는 여러 인덱서 일괄 처리에서 사용자 지정 기술 요청을 채워야 합니다.

* 인덱서 일괄 처리가 생성할 수 있는 평균 요청 수는 병렬 처리 수준에 맞는 최적의 설정을 제공해야 합니다. 기술을 호스팅하는 인프라에서 해당 수준의 동시성을 지원할 수 없으면 병렬 처리 수준을 낮추는 것이 좋습니다. 몇 가지 문서로 구성을 테스트하여 매개 변수 선택의 유효성을 검사하는 것이 모범 사례입니다.

* 더 작은 문서 샘플로 테스트하고 문서의 하위 세트를 처리하는 데 걸린 총 시간에 대한 기술 실행 시간을 평가합니다. 인덱서가 일괄 처리를 빌드하거나 기술의 대응을 기다리는 데 더 많은 시간을 소비하나요? 

* 병렬 처리의 업스트림 영향을 고려합니다. 사용자 지정 기술에 대한 입력이 이전 기술의 출력인 경우 대기 시간을 최소화하기 위해 기술 세트의 모든 기술이 효과적으로 스케일 아웃되나요?

## <a name="error-handling-in-the-custom-skill"></a>사용자 지정 기술의 오류 처리

기술이 성공적으로 완료되면 사용자 지정 기술은 성공 상태 코드 HTTP 200을 반환해야 합니다. 일괄 처리에 있는 하나 이상의 레코드로 인해 오류가 발생하면 다중 상태 코드 207을 반환하는 것이 좋습니다. 레코드의 오류 또는 경고 목록에는 적절한 메시지가 포함되어야 합니다.

일괄 처리의 항목에서 오류가 발생하면 해당 문서에 오류가 발생합니다. 문서가 성공해야 하는 경우 경고를 반환합니다.

299가 넘는 상태 코드는 오류로 평가되고 모든 보강이 실패하므로 문서가 실패합니다. 

### <a name="common-error-messages"></a>일반적인 오류 메시지

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` 더 긴 실행 기간을 허용하려면 기술에 시간 제한 매개 변수를 설정합니다.

* `Could not execute skill because Web Api skill response is invalid.` 사용자 지정 기술 응답 형식으로 메시지를 반환하지 않는 기술을 나타냅니다. 기술에서 예외가 처리되지 않았기 때문일 수 있습니다.

* `Could not execute skill because the Web Api request failed.` 권한 부여 오류 또는 예외 때문에 발생할 가능성이 큽니다.

* `Could not execute skill.` 일반적으로 기술 응답의 결과는 문서 계층의 기존 속성에 매핑됩니다.

## <a name="testing-custom-skills"></a>사용자 지정 기술 테스트

REST API 클라이언트로 사용자 지정 기술을 테스트하여 유효성 검사를 시작합니다.

* 기술은 요청 및 응답에 관한 사용자 지정 기술 인터페이스를 구현함

* 이 기술은 `application/JSON` MIME 형식의 유효한 JSON을 반환함

* 유효한 HTTP 상태 코드를 반환함

[디버그 세션](cognitive-search-debug-session.md)을 만들어 기술 세트에 기술을 추가하고 유효한 보강을 생성하는지 확인합니다. 디버그 세션을 통해 기술의 성능을 조정할 수 없지만, 기술이 유효한 값으로 구성되어 있고 보강된 예상 개체를 반환하는지 확인할 수 있습니다.

## <a name="best-practices"></a>모범 사례

* 기술에서는 더 큰 페이로드를 수락하고 반환할 수 있지만, JSON 반환 시 응답을 150MB 이하로 제한하는 것이 좋습니다.

* 인덱서 및 기술에 일괄 처리 크기를 설정하여 각 데이터 원본 일괄 처리에서 기술에 맞는 전체 페이로드를 생성해야 합니다.

* 장기 실행 작업의 경우 문서를 동시에 처리할 때 인덱서에서 오류가 발생하지 않도록 시간 제한을 충분히 높은 값으로 설정합니다.

* 인덱서 일괄 처리 크기, 기술 일괄 처리 크기 및 기술 병렬 처리 수준을 최적화하여 기술이 기대하는 부하 패턴을 생성하고 대규모 요청을 줄이거나 소규모 요청을 많이 생성합니다.

* 데이터 가변성으로 인해 특정 요청이 일관되게 실패하는 시나리오가 있을 수 있기 때문에 자세한 실패 로그를 사용하여 사용자 지정 기술을 모니터링합니다.


## <a name="next-steps"></a>다음 단계
축하합니다! 이제 인덱서의 처리량을 최대화하기 위해 사용자 지정 기술이 올바르게 스케일링됩니다. 

+ [파워 기술: 사용자 지정 기술의 리포지토리](https://github.com/Azure-Samples/azure-search-power-skills)
+ [AI 보강 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
+ [Azure Machine Learning 기술 추가](./cognitive-search-aml-skill.md)
+ [디버그 세션을 사용하여 변경 내용 테스트](./cognitive-search-debug-session.md)