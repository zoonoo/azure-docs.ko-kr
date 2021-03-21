---
title: 상용 marketplace 분석 데이터에 액세스 하기 위한 샘플 응용 프로그램
description: 샘플 응용 프로그램을 사용 하 여 사용자 고유의 상용 marketplace 분석 응용 프로그램을 빌드합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584080"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>상용 marketplace 분석 데이터에 액세스 하기 위한 샘플 응용 프로그램

샘플 응용 프로그램은 c # 및 JAVA 언어로 생성 되며 [GitHub](https://github.com/partneranalytics)에서 사용할 수 있습니다.

- [C # 샘플 응용 프로그램](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [JAVA 샘플 응용 프로그램](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

샘플 응용 프로그램에서 원하는 언어를 사용 하 여 응용 프로그램을 작성 하도록 선택할 수 있습니다.

샘플 응용 프로그램은 다음과 같은 목표를 달성 합니다.

- Azure Active Directory (Azure AD) 토큰을 생성 합니다.
- 사용 가능한 데이터 집합을 가져옵니다.
- 사용자 정의 쿼리를 만듭니다.
- 사용자 정의 및 시스템 쿼리를 가져옵니다.
- 보고서를 예약 합니다.

샘플 응용 프로그램은 다른 기능을 위해 Api를 호출 하는 방법을 다루지 않습니다. 그러나 다른 Api를 호출 하는 프로세스는 위에 설명 된 것과 동일 하 게 유지 됩니다.

## <a name="how-to-run-the-application"></a>응용 프로그램을 실행 하는 방법

1. 다음 명령을 사용 하 여 로컬 시스템에 리포지토리를 복제 합니다.

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > 자세한 내용은 `ProgrammaticExportSampleAppISV/README.md` GitHub [리포지토리에서](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)파일을 참조 하세요.

1. 앱을 신속 하 게 실행 하려면 **의appsettings.Development.js** 에서 클라이언트 ID 및 클라이언트 암호를 업데이트 합니다.

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="파일의 appsettings.Development.js코드 조각을 보여 줍니다.":::

앱을 실행 하면 로컬 웹 서버가 시작 되 고 페이지가 열립니다 ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ).

[![일정 보고서 페이지를 보여 줍니다.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

이 페이지에서는 로컬 컴퓨터에서 실행 되는 웹 서버에 대 한 API 호출을 수행 합니다. 그러면 실제 프로그래밍 방식 액세스 API가 호출 됩니다.

## <a name="code-snippets"></a>코드 조각

프로그래밍 방식 액세스 API 호출을 수행 하는 c # 코드의 기본 구조는 다음과 같습니다.

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="API 호출의 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [상용 marketplace 분석 데이터에 액세스 하기 위한 Api](analytics-available-apis.md)
