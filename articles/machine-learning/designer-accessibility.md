---
title: 디자이너에서 내게 필요한 옵션 기능 사용(미리 보기)
titleSuffix: Azure Machine Learning
description: 디자이너에서 사용할 수 있는 키보드 단축키 및 화면 판독기 내게 필요한 옵션 기능에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366203"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>키보드를 사용하여 Azure 기계 학습 디자이너(미리 보기)

키보드 및 화면 판독기를 사용하여 Azure 기계 학습 디자이너를 사용하는 방법을 알아봅니다. Azure 포털의 모든 곳에서 작동하는 바로 가기 키 목록은 [Azure 포털의 키보드 단축키를](../azure-portal/azure-portal-keyboard-shortcuts.md) 참조하십시오.

이 워크플로는 [내레이터](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) 및 [JAWS에서](https://www.freedomscientific.com/products/software/jaws/)테스트되었지만 다른 표준 화면 판독기에서 작동해야 합니다.

## <a name="navigate-the-pipeline-graph"></a>파이프라인 그래프 탐색

파이프라인 그래프는 중첩된 목록으로 구성됩니다. 외부 목록은 파이프라인 그래프의 모든 모듈을 설명하는 모듈 목록입니다. 내부 목록은 특정 모듈의 모든 연결을 설명하는 연결 목록입니다.  

1. 모듈 목록에서 화살표 키를 사용하여 모듈을 전환합니다.
1. 탭을 사용하여 대상 모듈의 연결 목록을 엽니다.
1. 화살표 키를 사용하여 모듈의 연결 포트 간에 전환할 수 있습니다.
1. "G"를 사용하여 대상 모듈로 이동합니다.

## <a name="edit-the-pipeline-graph"></a>파이프라인 그래프 편집

### <a name="add-a-module-to-the-graph"></a>그래프에 모듈 추가

1. Ctrl+F6를 사용하여 캔버스에서 모듈 트리로 포커스를 전환합니다.
1. 표준 트리뷰 컨트롤을 사용하여 모듈 트리에서 원하는 모듈을 찾습니다.

### <a name="edit-a-module"></a>모듈 편집

모듈을 다른 모듈에 연결하려면 다음을 수행하십시오.

1. 모듈 목록에서 모듈을 대상으로 할 때 Ctrl + Shift + H를 사용하여 연결 도우미를 엽니다.
1. 모듈의 연결 포트를 편집합니다.

모듈 특성을 조정하려면 다음을 수행하십시오.

1. 모듈을 대상으로 모듈을 대상으로 모듈 속성을 열 때 Ctrl + Shift + E를 사용합니다.
1. 모듈 속성을 편집합니다.

## <a name="navigation-shortcuts"></a>탐색 바로 가기

| 키 입력 | 설명 |
|-|-|
| Ctrl + F6 | 캔버스와 모듈 트리 간에 포커스 전환 |
| Ctrl + F1   | 모듈 트리의 노드에 초점을 맞출 때 정보 카드를 엽니다. |
| Ctrl + 시프트 + H | 노드에 포커스가 있을 때 연결 도우미 열기 |
| Ctrl + 시프트 + E | 포커스가 노드에 있을 때 모듈 속성 열기 |
| Ctrl + G | 파이프라인 실행이 실패한 경우 포커스를 첫 번째 실패한 노드로 이동 |

## <a name="action-shortcuts"></a>작업 바로 가기

액세스 키와 함께 다음 바로 가기를 사용합니다. 액세스 키에 대한 자세한 https://en.wikipedia.org/wiki/Access_key내용은 를 참조하십시오.

| 키 입력 | 작업 |
|-|-|
| 액세스 키 + R | 다음을 실행합니다. |
| 액세스 키 + P | 게시 |
| 액세스 키 + C | 복제 |
| 액세스 키 + D | 배포 |
| 액세스 키 + I | 추론 파이프라인 만들기/업데이트 |
| 액세스 키 + B | 일괄 처리 추론 파이프라인 만들기/업데이트 |
| 액세스 키 + K | "추론 파이프라인 만들기" 드롭다운 열기 |
| 액세스 키 + U | "추론 파이프라인 업데이트" 드롭다운 열기 |
| 액세스 키 + M | 더 열기 (...) 드롭다운 |

## <a name="next-steps"></a>다음 단계

- [고대비 켜기 및 테마 변경](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Microsoft의 내게 필요한 옵션 관련 도구](https://www.microsoft.com/accessibility)
