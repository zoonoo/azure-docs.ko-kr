---
title: "Microsoft 위협 모델링 도구 - Azure | Microsoft Docs"
description: "위협 모델링 도구에서 사용할 수 있는 모든 기능에 대해 알아보기"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 621ff305d7e782f85eeaae6c3fb02031673549c6
ms.contentlocale: ko-kr
ms.lasthandoff: 08/28/2017

---

# <a name="threat-modeling-tool-feature-overview"></a>위협 모델링 도구 기능 개요

위협 모델링 필요에 맞도록 위협 모델링 도구를 사용하도록 선택해 주셔서 감사 드립니다! 아직 선택하지 않았다면 **[위협 모델링 도구 시작](./azure-security-threat-modeling-tool-getting-started.md)**을 방문하여 기본 내용을 알아보세요.

> 자사 도구는 자주 업데이트되므로 최신 기능 및 향상된 기능을 확인하도록 이 가이드를 자주 확인하세요.

"새 모델 만들기" 단추를 클릭하면 아래 이미지와 유사하게 빈 시작 페이지가 열립니다.

![빈 시작 페이지](./media/azure-security-threat-modeling-tool/tmtstart.png)

**[시작](./azure-security-threat-modeling-tool-getting-started.md)** 예제의 자사 팀에서 만든 위협 모델을 사용하여 현재 이 도구에서 사용할 수 있는 모든 기능을 확인해 보겠습니다.

![기본 위협 모델](./media/azure-security-threat-modeling-tool/basictmt.png)

## <a name="navigation"></a>탐색

기본 제공 기능을 알아보기 전에 도구에 있는 주요 구성 요소를 검토해 보겠습니다.

### <a name="menu-items"></a>메뉴 항목

환경은 다른 Microsoft 제품과 비슷해야 합니다. 최상위 메뉴 항목 간을 이동하여 시작해 보겠습니다.

![메뉴 항목](./media/azure-security-threat-modeling-tool/menuitems.png)

| 레이블                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **파일** | <ul><li>파일 열기, 저장 및 닫기</li><li>OneDrive 계정의 로그인/로그아웃</li><li>공유 링크(보기 + 편집)</li><li>파일 정보 보기</li><li>기존 모델에 새 템플릿 적용</li></ul> |
| **편집** | 작업 실행 취소/다시 실행, 복사, 붙여넣기 및 삭제 |
| **보기** | <ul><li>**분석** 및 **디자인** 보기 간 전환</li><li>닫힌 창 열기(예 스텐실, 요소 속성 및 메시지)</li><li>기본 설정으로 레이아웃 다시 설정</li></ul> |
| **다이어그램** | 다이어그램 추가/삭제 및 다이어그램의 "탭"을 통해 이동 |
| **보고서** | 다른 사용자와 공유하는 HTML 보고서 만들기 |
| **도움말** | 도구를 사용하도록 돕는 가이드 |

아이콘은 최상위 메뉴에 대한 바로 가기입니다.

| 아이콘                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **열기** | 새 파일 열기 |
| **저장** | 현재 파일 저장 |
| **디자인** | 모델을 만들 수 있는 디자인 보기로 이동 |
| **분석** | 생성된 위협 및 해당 속성 표시 |
| **다이어그램 추가** | 새 다이어그램 추가(Excel의 새 탭과 유사함) |
| **다이어그램 삭제** | 현재 다이어그램 삭제 |
| **복사/잘라내기/붙여넣기** | 요소 복사/잘라내기/붙여넣기 |
| **실행 취소/다시 실행** | 작업 실행 취소/다시 실행 |
| **확대/축소** | 더 나은 보기를 위해 다이어그램 확대 및 축소 |
| **사용자 의견** | MSDN 포럼 열기 |

### <a name="canvas"></a>캔버스

요소를 끌어서 놓는 공간입니다. 끌어서 놓기는 모델을 작성하는 신속하고 가장 효율적인 방법입니다. 또한 마우스를 오른쪽 단추로 클릭하고 메뉴를 선택할 수도 있습니다. 이는 아래와 같이 사용 중인 요소의 일반 버전을 추가합니다.

#### <a name="dropping-the-stencil-on-the-canvas"></a>캔버스에서 스텐실 삭제

![캔버스 삭제](./media/azure-security-threat-modeling-tool/canvasdrop1.png)

#### <a name="clicking-on-the-stencil"></a>스텐실 클릭

![요소 속성](./media/azure-security-threat-modeling-tool/canvasdrop2.png)

### <a name="stencils"></a>스텐실

여기에서 선택한 템플릿에 따라 사용 가능한 모든 스텐실을 찾을 수 있습니다. 올바른 요소를 찾을 수 없는 경우 다른 템플릿을 사용하거나 필요에 맞게 수정합니다. 일반적으로 아래와 같은 범주의 조합을 찾을 수 있습니다.

| 스텐실 이름                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **프로세스** | 응용 프로그램, 브라우저 플러그 인, 스레드, 가상 컴퓨터 |
| **외부 인자** | 인증 공급자, 브라우저, 사용자, 웹 응용 프로그램 |
| **데이터 저장소** | 캐시, 저장소, 구성 파일, 데이터베이스, 레지스트리 |
| **데이터 흐름** | 이진, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, 명명된 파이프, RPC/DCOM, SMB, UDP |
| **신뢰 선/경계** | 회사 네트워크, 인터넷, 컴퓨터, 샌드박스, 사용자/커널 모드 |

### <a name="notesmessages"></a>참고 사항/메시지

| 구성 요소                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **메시지** | 요소 간 데이터 흐름 없음과 같은 오류가 발생할 때마다 사용자에게 경고하는 내부 도구 논리 |
| **참고 사항** | 설계 및 검토 프로세스 전체에서 엔지니어링 팀에 의해 파일에 추가된 수동 참고 사항 |

### <a name="element-properties"></a>요소 속성

이는 선택한 요소에 따라 다릅니다. 신뢰 경계 외에도 다른 모든 요소에 일반적인 3가지 선택 항목이 포함되어 있습니다.

| 요소 속성                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **Name** | 프로세스, 저장소, 인자 및 흐름을 쉽게 인식하도록 이름을 지정하는 데 유용 |
| **범위 외** | 선택한 경우 요소는 위협 생성 매트릭스에서 제거됩니다(권장하지 않음). |
| **범위 외에 대한 이유** | 범위 외가 선택된 이유를 사용자에게 알리는 이유 필드 |

속성은 각 요소 범주 아래에서 변경됩니다. 각 요소를 클릭하여 사용할 수 있는 옵션을 검사하거나 템플릿을 열어 자세한 내용을 알아봅니다. 기능에 대해 알아보겠습니다.

## <a name="welcome-screen"></a>시작 화면

시작 화면은 앱을 열 때 보는 첫 번째 화면입니다.

### <a name="open-a-model"></a>모델 열기

"모델 열기" 단추를 가리키면 두 개의 숨겨진 옵션 "이 컴퓨터에서 열기" 및 "OneDrive에서 열기"가 표시됩니다. 첫 번째는 파일 열기 화면을 여는 반면 두 번째는 OneDrive에 대한 로그인 프로세스를 안내하여 성공적인 인증 후 파일 및 폴더를 선택할 수 있도록 합니다.

![모델 열기](./media/azure-security-threat-modeling-tool/openmodel.png)

![컴퓨터 또는 OneDrive에서 열기](./media/azure-security-threat-modeling-tool/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>피드백, 제안 및 문제

이 옵션을 선택하면 SDL 도구에 대한 MSDN 포럼으로 이동합니다. 해결 방법 및 새로운 아이디어를 포함한 도구에 대한 다른 사용자의 의견을 확인하는 좋은 방법입니다.

![사용자 의견](./media/azure-security-threat-modeling-tool/feedback.png)

## <a name="design-view"></a>디자인 보기

새 모델을 열거나 만들 때마다 디자인 보기로 이동합니다.

### <a name="adding-elements"></a>요소 추가

2가지 방법으로 그리드에서 요소를 추가할 수 있습니다.

- **끌어서 놓기** – 원하는 요소를 그리드로 끌어온 다음 요소 속성을 사용하여 추가 정보를 제공합니다.
- **마우스 오른쪽 단추로 클릭** – 마우스 오른쪽 단추로 그리드에서 아무 곳이나 클릭하고 드롭다운 메뉴에서 선택합니다. 해당 요소에 대한 일반 표현이 화면에 표시됩니다.

### <a name="connecting-elements"></a>요소 연결

2가지 방법으로 도구에서 요소를 연결할 수 있습니다.

- **끌어서 놓기** – 원하는 데이터 흐름을 그리드로 끌어오고 양쪽 끝을 적절한 요소에 연결합니다.
- **클릭 + Shift** – 첫 번째 요소(데이터 전송)를 클릭하고, Shift 키를 누른 다음 두 번째 요소(데이터 수신)를 선택합니다. 마우스 오른쪽 단추로 클릭하고 "연결"을 선택합니다. 양방향 데이터 흐름을 사용하는 경우 순서는 중요하지 않습니다.

### <a name="properties"></a>properties

다이어그램에 있는 스텐실에서 수정할 수 있는 모든 속성을 표시합니다. 속성을 보려면 스텐실을 클릭합니다. 정보가 그에 따라 입력됩니다. 아래 예제에서는 "데이터베이스" 스텐실이 다이어그램으로 끌어진 이전 및 이후를 보여 줍니다.

#### <a name="before"></a>이전

![이전](./media/azure-security-threat-modeling-tool/properties1.png)

#### <a name="after"></a>이후

![이후](./media/azure-security-threat-modeling-tool/properties2.png)

### <a name="messages"></a>메시지

위협 모델을 만들고 데이터 흐름을 요소에 연결하는 것을 잊은 경우 메시지 창에서 해당 작업을 알립니다. 이를 무시하거나 지침을 따라 문제를 해결할 수 있습니다. 

![메시지](./media/azure-security-threat-modeling-tool/messages.png)

### <a name="notes"></a>참고 사항

메시지에서 참고 사항으로 탭을 전환하면 모든 아이디어를 캡처하도록 다이어그램에 메모를 추가할 수 있습니다.

## <a name="analysis-view"></a>분석 보기

다이어그램 작성을 완료하면 상단 메뉴 선택 항목으로 이동하고 페인트 팔레트 옆에 있는 돋보기를 선택하여 분석 보기로 전환합니다.

![분석 보기](./media/azure-security-threat-modeling-tool/analysisview.png)

### <a name="generated-threat-selection"></a>생성된 위협 선택

위협을 클릭하면 세 가지 고유한 기능을 활용할 수 있습니다.

| 기능                               | 정보      |
| --------------------------------------- | ------------ |
| **읽기 표시기** | <p>위협은 이제 읽은 상태로 표시됩니다. 이를 통해 이미 지나간 항목의 추적을 쉽게 유지할 수 있습니다.</p><p>![읽기/읽지 않음 표시기](./media/azure-security-threat-modeling-tool/readmode.png)</p> |
| **상호 작용 포커스** | <p>해당 위협에 속한 다이어그램의 상호 작용이 강조 표시됩니다.</p><p>![상호 작용 포커스](./media/azure-security-threat-modeling-tool/interactionfocus.png)</p> |
| **위협 속성** | <p>위협에 대한 추가 정보가 위협 속성 창에 채워집니다.</p><p>![위협 속성](./media/azure-security-threat-modeling-tool/threatproperties.png)</p> |

### <a name="priority-change"></a>우선 순위 변경

생성된 각 위협의 우선 순위 수준을 변경하면 높음, 중간 및 낮음 우선 순위 위협을 식별하기 쉽도록 해당 색도 변경됩니다.

![우선 순위 변경](./media/azure-security-threat-modeling-tool/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>위협 속성 편집 가능한 필드

위의 그림에서와 같이 사용자는 도구에서 생성되는 정보를 변경할 수 있으며 이유와 같은 특정 필드에 정보를 추가할 수도 있습니다. 이러한 필드는 템플릿에서 생성되므로 각 위협에 대한 자세한 정보가 필요한 경우 수정하는 것이 좋습니다.

![위협 속성](./media/azure-security-threat-modeling-tool/threatproperties.png)

## <a name="reports"></a>보고서

우선 순위 변경 및 생성된 각 위협의 상태 업데이트가 완료되면 파일을 저장하고/또는 "보고서", "전체 보고서 만들기"로 이동하여 보고서를 출력할 수 있습니다. 보고서의 이름을 지정하라는 메시지가 나타나며 이름을 지정하면 아래 이미지와 비슷한 것이 표시됩니다.

![보고서](./media/azure-security-threat-modeling-tool/report.png)

## <a name="next-steps"></a>다음 단계

커뮤니티에 템플릿을 제공하려면 **[GitHub](https://github.com/Microsoft/threat-modeling-templates)** 페이지로 이동하세요. 도구를 **[다운로드](https://aka.ms/tmtpreview)**하여 지금 시작합니다.

