---
title: 실험 - Azure Cognitive Services | Microsoft Docs
description: 이 문서는 Azure Custom Decision Service 실험에 대한 가이드입니다.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b0ac0bc049d556423493f0c48dd9a548929bcd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377502"
---
# <a name="experimentation"></a>실험

Custom Decision Service는 [CB(컨텍스트별 산적)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/) 이론에 따라, 반복적으로 컨텍스트를 관찰하고, 작업을 수행하고, 선택한 작업에 대한 보상을 관찰합니다. 예로 콘텐츠 개인 설정이 있습니다. 컨텍스트는 사용자를 설명하고, 작업은 후보 스토리이며, 보상은 사용자가 권장 스토리를 얼마나 좋아하는지를 측정합니다.

Custom Decision Service는 컨텍스트에서 작업에 매핑할 때 정책을 생성합니다. 특정 대상 정책을 사용할 경우 예상되는 보상을 확인할 수 있습니다. 보상을 추정하는 한 가지 방법은 온라인 정책을 사용하여 작업(예: 사용자에게 스토리 권장)이 선택되도록 하는 것입니다. 그러나 이러한 온라인 평가는 다음과 같은 두 가지 이유로 비용이 발생할 수 있습니다.

* 테스트되지 않은, 실험적 정책에 사용자가 노출됩니다.
* 여러 대상 정책을 평가할 수 있게 확장되지 않습니다.

오프-정책 평가는 대체 패러다임입니다. 로깅 정책을 따르는 기존 온라인 시스템의 로그가 있는 경우 오프-정책 평가로 새 대상 정책의 예상되는 보상을 예측할 수 있습니다.

실험은 로그 파일을 사용하여 예상되는 보상이 가장 높은 정책을 찾으려고 합니다. 대상 정책은 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) 인수로 매개 변수화됩니다. 기본 모드에서 스크립트는 다양한 Vowpal Wabbit 인수를 `--base_command`에 추가하여 테스트합니다. 이 스크립트에서는 다음 작업을 수행합니다.

* 입력 파일의 첫 번째 `--auto_lines` 줄에서 네임스페이스를 자동으로 검색합니다.
* 하이퍼 매개 변수(`learning rate`, `L1 regularization` 및 `power_t`)에 대해 첫 번째 스윕을 수행합니다.
* 정책 평가 `--cb_type`(역 성향 점수 `ips`) 또는 이중 로버스트(`dr`)를 테스트합니다. 자세한 내용은 [컨텍스트별 산적 예제](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example)를 참조하세요.
* 한계를 테스트합니다.
* 2차 상호 작용 기능을 테스트합니다.
   * **무차별 대입 단계**: `--q_bruteforce_terms` 쌍 이하를 사용해서 모든 조합을 테스트합니다.
   * **그리디 단계**: `--q_greedy_stop` 반올림 처리에 따른 개선점이 없어질 때까지 최상의 쌍을 추가합니다.
* 하이퍼 매개 변수(`learning rate`, `L1 regularization` 및 `power_t`)에 대해 두 번째 스윕을 수행합니다.

다음 단계를 제어하는 매개 변수에는 일부 Vowpal Wabbit 인수가 포함됩니다.
- 예제 조작 옵션:
  - 공유 네임스페이스
  - 작업 네임스페이스
  - 한계 네임스페이스
  - 2차 기능
- 업데이트 규칙 옵션
  - 학습 속도
  - L1 정규화
  - t 거듭제곱 값

위 인수에 대한 자세한 설명은 [Vowpal Wabbit 명령줄 인수](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
- Vowpal Wabbit: 설치되어 사용자 경로에 있습니다.
  - Windows: [`.msi` installer](https://github.com/eisber/vowpal_wabbit/releases)를 사용합니다.
  - 기타 플랫폼: [소스 코드를 확보](https://github.com/JohnLangford/vowpal_wabbit/releases)합니다.
- Python 3: 설치되어 사용자 경로에 있습니다.
- NumPy: 사용자가 선택한 패키지 관리자를 사용합니다.
- *Microsoft/mwt-ds* 리포지토리: [리포지토리를 복제](https://github.com/Microsoft/mwt-ds)합니다.
- 의사 결정 서비스 JSON 로그 파일: 기본적으로 기본 명령에는 입력 데이터 파일의 의사 결정 서비스 JSON 구문 분석을 가능하게 하는 `--dsjson`이 포함되어 있습니다. [이 형식의 예제를 가져옵니다](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>사용 현황
`mwt-ds/DataScience`로 이동한 후 다음 코드에 설명된 것처럼 관련 인수를 사용해서 `Experimentation.py`를 실행합니다.

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

결과 로그가 *mwt-ds/DataScience/experiments.csv* 파일에 추가됩니다.

### <a name="parameters"></a>매개 변수
| 입력 | 설명 | 기본값 |
| --- | --- | --- |
| `-h`, `--help` | 도움말 메시지를 표시하고 종료됩니다. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | 데이터 파일 경로(`.json` 또는 `.json.gz` 형식 - 각 줄이 하나의 `dsjson`임)입니다. | 필수 |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | 기본 Vowpal Wabbit 명령입니다.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | 사용할 병렬 프로세스 수입니다. | 논리 프로세서 |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | 공유 기능 네임스페이스(예를 들어 `abc`는 네임스페이스 `a`, `b` 및 `c`를 의미함)입니다.  | 데이터 파일에서 자동 검색 |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | 작업 기능 네임스페이스입니다. | 데이터 파일에서 자동 검색 |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | 한계 기능 네임스페이스입니다. | 데이터 파일에서 자동 검색 |  
| `--auto_lines AUTO_LINES` | 자동 검색 기능 네임스페이스를 검색할 데이터 파일 줄 수입니다. | `100` |  
| `--only_hp` | 하이퍼 매개 변수(`learning rate`, `L1 regularization` 및 `power_t`)에 대해서만 스윕을 수행합니다. | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | 학습 속도 범위를 양수 값 `min,max,steps`으로 지정합니다. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | L1 정규화 범위를 양수 값 `min,max,steps`으로 지정합니다. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Power_t 범위를 양수 값 `min,max,step`으로 지정합니다. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | 무차별 대입 단계에서 테스트할 2차 쌍의 수입니다. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | 반올림 처리를 하지만 개선점은 없어집니다. 그러면 2차 그리디 검색 단계가 중단됩니다. | `3` |  

### <a name="examples"></a>예
미리 설정된 기본값을 사용하려면
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

동일하게 Vowpal Wabbit 또한 `.json.gz` 파일을 수집할 수 있습니다.
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

하이퍼 매개 변수에 대해서만 스윕을 수행하려면(`learning rate`, `L1 regularization` 및 `power_t`, 2단계 후 중지)
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
