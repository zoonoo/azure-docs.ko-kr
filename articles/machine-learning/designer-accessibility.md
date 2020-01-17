---
title: 디자이너에서 내게 필요한 옵션 기능 사용 (미리 보기)
titleSuffix: Azure Machine Learning
description: 디자이너에서 사용할 수 있는 바로 가기 키 및 화면 판독기 접근성 기능에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 9c899ae16d16fc76f0ea8d24169451556cf24025
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76158014"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>키보드를 사용 하 여 Azure Machine Learning designer 사용 (미리 보기)

키보드 및 화면 판독기를 사용 하 여 Azure Machine Learning 디자이너를 사용 하는 방법을 알아봅니다. Azure Portal의 모든 위치에서 작동 하는 바로 가기 키 목록은 [Azure Portal의 바로 가기 키](../azure-portal/azure-portal-keyboard-shortcuts.md) 를 참조 하세요.

이 워크플로는 [내레이터](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) 및 [JAWS](https://www.freedomscientific.com/products/software/jaws/)에서 테스트 되었지만 다른 표준 화면 판독기와 함께 작동 합니다.

## <a name="navigate-the-pipeline-graph"></a>파이프라인 그래프 탐색

파이프라인 그래프가 목록 목록으로 구성 됩니다. 최상위 모듈 목록에는 파이프라인의 모든 모듈이 포함 되어 있습니다. 모듈 목록의 각 항목에는 모든 연결을 설명 하는 연결 목록이 포함 되어 있습니다. 

1. 모듈 목록에서 화살표 키를 사용 하 여 모듈을 전환 합니다.
1. Tab 키를 사용 하 여 대상 모듈의 연결 목록을 엽니다.
1. 화살표 키를 사용 하 여 모듈에 대 한 연결 포트 간을 전환 합니다.
1. "G"를 사용 하 여 대상 모듈로 이동 합니다.

## <a name="edit-the-pipeline-graph"></a>파이프라인 그래프 편집

### <a name="add-a-module-to-the-graph"></a>그래프에 모듈 추가

1. Ctrl + F6을 사용 하 여 캔버스에서 모듈 트리로 포커스를 전환 합니다.
1. 표준 treeview 컨트롤을 사용 하 여 모듈 트리에서 원하는 모듈을 찾습니다.

### <a name="edit-a-module"></a>모듈 편집

모듈을 다른 모듈에 연결 하려면 다음을 수행 합니다.

1. 모듈 목록에서 모듈을 대상으로 지정 하는 경우 Ctrl + Shift + H를 사용 하 여 연결 도우미를 엽니다.
1. 모듈에 대 한 연결 포트를 편집 합니다.

모듈 속성을 조정 하려면:

1. 모듈을 대상으로 하는 경우 Ctrl + Shift + E를 사용 하 여 모듈 속성을 엽니다.
1. 모듈 속성을 편집 합니다.

## <a name="navigation-shortcuts"></a>탐색 바로 가기

| 키 입력 | Description |
|-|-|
| Ctrl + F6 | Canvas와 module 트리 사이에서 포커스 전환 |
| Ctrl + F1   | 모듈 트리의 노드에 포커스를 둘 때 정보 카드 열기 |
| Ctrl + Shift + H | 포커스가 노드에 있을 때 연결 도우미 열기 |
| Ctrl + Shift + E | 노드에 포커스가 있을 때 모듈 속성 열기 |
| Ctrl + G | 파이프라인 실행이 실패 한 경우 첫 번째 실패 한 노드로 포커스 이동 |

## <a name="action-shortcuts"></a>작업 바로 가기

액세스 키와 함께 다음 바로 가기를 사용 합니다. 액세스 키에 대 한 자세한 내용은 https://en.wikipedia.org/wiki/Access_key 를 참조 하세요.

| 키 입력 | 실행력 |
|-|-|
| 액세스 키 + R | 실행 |
| 액세스 키 + P | 게시 |
| 액세스 키 + C | 복제 |
| 액세스 키 + D | 배포 |
| 액세스 키 + I | 유추 파이프라인 만들기/업데이트 |
| 액세스 키 + B | 일괄 처리 유추 파이프라인 만들기/업데이트 |
| 액세스 키 + K | "유추 파이프라인 만들기" 드롭다운 열기 |
| 액세스 키 + U | "업데이트 유추 파이프라인" 드롭다운 열기 |
| 액세스 키 + M | 자세히 열기 (...) 드롭다운 |

## <a name="next-steps"></a>다음 단계

- [고대비 켜기 및 테마 변경](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Microsoft의 내게 필요한 옵션 관련 도구](https://www.microsoft.com/accessibility)
