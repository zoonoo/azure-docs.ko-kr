---
title: 파트너 센터에서 상업적 marketplace 프로그램 지원 받기
description: 지원 요청을 파일 하는 방법을 비롯 하 여 파트너 센터에서 상업적 marketplace 프로그램의 지원 옵션에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: a1726b29c153bf680d29fe821ac34aa958064335
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879244"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>파트너 센터에서 상업적 marketplace 프로그램 지원

Microsoft는 광범위한 제품 및 서비스에 대한 지원을 제공합니다. 적절 한 적절 한 응답을 보장 하려면 올바른 지원 팀을 찾아야 합니다. 다음은 쿼리를 적절한 팀으로 라우팅하는 데 도움이 되는 시나리오입니다.

- 게시자이 고 고객의 질문이 있는 경우 고객에 게 [Azure Portal](https://portal.azure.com/)지원 링크를 사용 하 여 지원을 요청 하도록 요청 하세요.
- Azure에서 실행 되는 응용 프로그램에 대 한 보안 문제를 검색 한 게시자 인 경우 [보안 이벤트 지원 티켓을 기록 하는 방법](../security/fundamentals/event-support-ticket.md)을 참조 하세요. 판매자는 Azure Marketplace 소프트웨어 및 서비스 제품 관련 보안 인시던트 및 취약성을 비롯하여 의심스러운 보안 이벤트를 최대한 빨리 보고해야 합니다.
- 게시자이 고 앱 이나 서비스와 관련 된 질문이 있는 경우 다음 지원 옵션을 검토 하세요.

## <a name="get-help-or-open-a-support-ticket"></a>도움말 보기 또는 지원 티켓 열기

1. 회사 계정을 사용하여 로그인합니다. 아직 수행 하지 않은 경우 [파트너 센터 계정을 만들어야](partner-center-portal/create-account.md)합니다.

1. 페이지의 오른쪽 위에 있는 메뉴에서 **지원** 아이콘을 선택 합니다. **도움말 및 지원** 창이 페이지의 오른쪽에 나타납니다.

1. 상업적 marketplace에 대 한 도움이 필요 하면 **상업적 marketplace** 를 선택 합니다.

   ![지원 드롭다운 메뉴](./media/support/commercial-marketplace-support-pane.png)

1. **문제 요약** 상자에 문제에 대 한 간단한 설명을 입력 합니다.

1. **문제 유형** 상자에서 다음 중 하나를 수행 합니다.

    - **옵션 1**: Marketplace, Azure 앱, SaaS 제품, 계정 관리, 잠재 고객 관리, 배포 문제, 지급 또는 공동 판매 제품 마이그레이션과 같은 키워드를 입력 합니다. 그런 다음 표시 되는 권장 목록에서 문제 유형을 선택 합니다.

    - **옵션 2**: **범주** 목록에서 **찾아보기 항목** 을 선택 하 고 **상업적 마켓플레이스** 를 선택 합니다. 그런 다음 적절 한 **토픽** 및 하위 **주제** 를 선택 합니다.

1. 선택한 항목을 찾은 후 **솔루션 검토** 를 선택 합니다.

    ![다음 단계](./media/support/next-step.png)

표시 되는 옵션은 다음과 같습니다.

- 다른 항목을 선택 하려면 **다른 문제 선택** 을 클릭 합니다.
- 문제를 해결 하려면 권장 단계와 문서 (사용 가능한 경우)를 검토 합니다.

    ![권장 솔루션](./media/support/recommended-solutions.png)

자체 도움말에서 답변을 찾을 수 없는 경우 **문제 세부 정보 제공** 을 선택 합니다. 모든 필수 필드를 입력 하 여 해결 프로세스의 속도를 높이고, **제출** 을 선택 합니다.

>[!Note]
>파트너 센터에 로그인 하지 않은 경우 티켓을 만들기 전에 로그인 해야 할 수 있습니다.

## <a name="track-your-existing-support-requests"></a>기존 지원 요청 추적

열린 및 닫힌 티켓을 검토 하려면 왼쪽 탐색 메뉴에서 **상업적 마켓플레이스**  >  **지원** 을 선택 합니다.

## <a name="record-issue-details-with-a-har-file"></a>HAR 파일을 사용 하 여 문제 정보 기록

지원 에이전트에서 문제를 해결할 수 있도록 지원 티켓에 HAR (HTTP Archive format) 파일을 연결 하는 것이 좋습니다. HAR 파일은 웹 브라우저에서 네트워크 요청의 로그입니다.

> [!WARNING]
> HAR 파일은 파트너 센터 계정에 대 한 중요 한 데이터를 기록할 수 있습니다.

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge 및 Google Chrome

**Microsoft Edge** 또는 **Google Chrome** 을 사용 하 여 HAR 파일을 생성 하려면:

1. 문제가 발생 하는 웹 페이지로 이동 합니다.
2. 창의 오른쪽 위 모퉁이에서 줄임표 아이콘, **기타 도구**  >  **개발자 도구** 를 차례로 선택 합니다. F12 키를 바로 가기로 사용할 수 있습니다.
3. 개발자 도구 창에서 **네트워크** 탭을 선택 합니다.
4. **네트워크 로그 기록 중지** 를 선택 하 고 기존 로그를 제거 하려면 **선택을 취소** 합니다. 레코드 아이콘은 회색으로 바뀝니다.

    ![Microsoft Edge 또는 Google Chrome에서 기존 로그를 제거 하는 방법](media/support/chromium-stop-clear-session.png)

5. **네트워크 로그** 기록을 선택 하 여 기록을 시작 합니다. 기록을 시작할 때 기록 아이콘은 빨간색으로 바뀝니다.

    ![Microsoft Edge 또는 Google Chrome에서 기록을 시작 하는 방법](media/support/chromium-start-session.png)

6. 문제를 해결 하려는 문제를 재현 합니다.
7. 문제를 재현 한 후 **네트워크 로그 기록 중지** 를 선택 합니다.
8. **HAR 내보내기** 를 선택 하 고 아래쪽 화살표 아이콘으로 표시 한 다음 파일을 저장 합니다.

    ![Microsoft Edge 또는 Google Chrome에서 HAR 파일을 내보내는 방법](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

**Mozilla Firefox** 를 사용 하 여 HAR 파일을 생성 하려면:

1. 문제가 발생 하는 웹 페이지로 이동 합니다.
1. 창의 오른쪽 위 모퉁이에서 줄임표 아이콘, **웹 개발자**  >  **토글 도구** 를 차례로 선택 합니다. F12 키를 바로 가기로 사용할 수 있습니다.
1. **네트워크** 탭을 선택 하 고 **지우기** 를 선택 하 여 기존 로그를 제거 합니다.

    ![Mozilla Firefox에서 기존 로그를 제거 하는 방법](media/support/firefox-clear-session.png)

1. 문제를 해결 하려는 문제를 재현 합니다.
1. 문제를 재현 한 후 **HAR 내보내기/가져오기**  >  **모두 HAR로 저장** 을 선택 합니다.

    ![Mozilla Firefox에서 HAR 파일을 내보내는 방법](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

**Safari** 를 사용 하 여 HAR 파일을 생성 하려면:

1. Safari에서 개발자 도구 사용: **safari**  >  **기본 설정** 을 선택 합니다. **고급** 탭으로 이동한 다음 **메뉴 모음에서 개발 메뉴 표시** 를 선택 합니다.
1. 문제가 발생 하는 웹 페이지로 이동 합니다.
1. **개발** 을 선택한 다음 **웹 검사기 표시** 를 선택합니다.
1. **네트워크** 탭을 선택한 다음 **네트워크 항목 지우기** 를 선택 하 여 기존 로그를 제거 합니다.

    ![Safari에서 기존 로그를 제거 하는 방법](media/support/safari-clear-session.png)

1. 문제를 해결 하려는 문제를 재현 합니다.
1. 문제를 재현 한 후 **내보내기** 를 선택 하 고 파일을 저장 합니다.

    ![Safari에서 HAR 파일을 내보내는 방법](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](partner-center-portal/update-existing-offer.md)