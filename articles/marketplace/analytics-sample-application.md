---
title: 상업용 Marketplace 분석 데이터에 액세스하기 위한 샘플 애플리케이션
description: 샘플 애플리케이션을 사용하여 사용자 고유의 상업용 Marketplace 분석 애플리케이션을 빌드합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584080"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>상업용 Marketplace 분석 데이터에 액세스하기 위한 샘플 애플리케이션

샘플 애플리케이션은 C# 및 JAVA 언어로 만들어지며 [GitHub](https://github.com/partneranalytics)에서 제공됩니다.

- [C# 샘플 애플리케이션](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [JAVA 샘플 애플리케이션](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

샘플 애플리케이션에서 아이디어를 얻어 원하는 언어로 자체 애플리케이션을 빌드할 수 있습니다.

샘플 애플리케이션은 다음과 같은 목표를 달성합니다.

- Azure Active Directory(Azure AD) 토큰을 생성합니다.
- 사용 가능한 데이터 세트를 가져옵니다.
- 사용자 정의 쿼리를 만듭니다.
- 사용자 정의 및 시스템 쿼리를 가져옵니다.
- 보고서를 예약합니다.

샘플 애플리케이션은 다른 기능을 위해 API를 호출하는 방법을 다루지 않습니다. 그러나 다른 API를 호출하는 프로세스는 위에 설명된 것과 동일하게 유지됩니다.

## <a name="how-to-run-the-application"></a>애플리케이션을 실행하는 방법

1. 다음 명령을 사용하여 로컬 시스템에 리포지토리를 복제합니다.

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > 자세한 내용은 GitHub [리포지토리](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)의 `ProgrammaticExportSampleAppISV/README.md` 파일을 참조하세요.

1. 앱을 신속하게 실행하려면 **appsettings.Development.json** 에서 클라이언트 ID와 클라이언트 암호를 업데이트합니다.

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="appsettings.Development.json 파일의 코드 조각을 보여 줍니다.":::

앱을 실행하면 로컬 웹 서버가 시작되고 페이지가 열립니다(`https://localhost:44365/ProgrammaticExportSampleApp/sample`).

[![일정 보고서 페이지를 보여 줍니다.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

이 페이지는 로컬 머신에서 실행되는 웹 서버에 대한 API 호출을 수행합니다. 이에 따라 실제 프로그래밍 방식 액세스 API 호출을 수행합니다.

## <a name="code-snippets"></a>코드 조각

프로그래밍 방식 액세스 API 호출을 수행하는 C# 코드의 기본 구조는 다음과 같습니다.

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="API 호출을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace 분석 데이터에 액세스하기 위한 API](analytics-available-apis.md)
