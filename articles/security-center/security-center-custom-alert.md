---
title: Azure Security Center의 사용자 지정 경고 규칙 | Microsoft Docs
description: 이 문서를 통해 Azure Security Center에서 사용자 지정 경고 규칙을 만들 수 있습니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 8845cb0a91edefa0350558f35488519ec37d064d
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663579"
---
# <a name="custom-alert-rules-in-azure-security-center-retired"></a>Azure Security Center의 사용자 지정 경고 규칙 (사용 되지 않음)
이 문서는 Azure Security Center에서 사용자 지정 경고 규칙 (미리 보기)을 만드는 데 도움이 됩니다.

> [!NOTE]
> 사용자 지정 경고는 2019 년 6 월 30 일에 Azure Security Center에서 사용이 중지 되었습니다. 

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Azure Security Center에서 사용자 지정 경고 규칙 사용 중지

사용자 지정 경고 환경은 사용 되는 기본 인프라의 사용 중지로 인해 2019 년 6 월 30 일에 사용이 중지 되었습니다. Retriement 이전에 정의 된 모든 사용자 지정 경고는 적용 되지 않으며 사용자 지정 경고 규칙을 기반으로 하는 보안 경고가 생성 되지 않습니다. 아래에서 설명 하는 대체 방법으로 해당 쿼리를 다시 만들기 위해 Security Center에서 사용자 지정 경고 규칙 쿼리를 계속 볼 수 있습니다.

사용자는 다음 중 하나를 수행 하는 것이 좋습니다.
- [Azure 센티널](https://azure.microsoft.com/services/azure-sentinel/) 을 사용 하도록 설정 하 고 기본 제공 [분석](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) 기능을 사용 하 여 경고 규칙을 다시 만듭니다.
- Azure Monitor 로그 경고를 사용 하 여 경고 다시 만들기
                                     
기존 경고를 유지 하 고 Azure 센티널에서 다시 만들려면 [Azure 센티널을 시작](https://portal.azure.com/#create/Microsoft.ASI/preview)하세요. 첫 번째 단계에서는 사용자 지정 경고가 저장 된 작업 영역을 선택 하 고 ' 분석 ' 메뉴 항목을 선택 하 여 사용자 지정 경고 규칙을 설정 합니다. 자세한 내용은 [설명서](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) 를 참조 하세요.

> [!NOTE]
> [Search](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries) 또는 [Union](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) 문 쿼리를 사용 하는 사용자 지정 경고는 Azure 센티널에서 지원 되지 않습니다. 마이그레이션을 수행 하기 전에 이러한 경고를 편집 하십시오.

Azure Monitor 로그 경고를 사용 하 여 경고를 다시 만드는 방법을 알아보려면 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)를 참조 하세요. 일반적인 개요는 [Azure Monitor의 로그 경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)를 참조 하세요.

## <a name="what-are-custom-alert-rules-in-security-center"></a>Security Center에서 사용자 지정 경고 규칙이란?

Security Center에는 위협 또는 의심스러운 활동이 발생할 때 트리거되는 미리 정의된 일련의 [보안 경고](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)가 있습니다. 일부 시나리오에서는 환경의 특정 요구 사항에 맞도록 사용자 지정 알림을 만드는 것이 좋습니다.

Security Center에서 사용자 지정 경고 규칙을 사용하면 환경에서 이미 수집된 데이터를 기반으로 하는 새 보안 경고를 정의할 수 있습니다. 쿼리를 만들 수 있습니다. 또한 이러한 쿼리의 결과는 사용자 지정 규칙에 대한 기준으로 사용될 수 있고 이 조건이 일치하면 규칙이 실행됩니다. 컴퓨터 보안 이벤트, 파트너의 보안 솔루션 로그 또는 API를 사용하여 수집된 데이터를 사용하여 사용자 지정 쿼리를 만들 수 있습니다.

> [!NOTE]
> 사용자 지정 경고는 Security Center의 [조사 기능](security-center-investigation.md)에서 지원되지 않습니다.
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Security Center에서 사용자 지정 경고 규칙을 만들려면 어떻게 할까요?

**Security Center** 대시보드를 열고 다음 단계를 따라 사용자 지정 경고 규칙을 만듭니다.

1.  왼쪽 창의 **감지** 아래에서 **사용자 지정 경고 규칙(미리 보기)** 을 클릭합니다.
2.  **Security Center – 사용자 지정 경고 규칙(미리 보기)** 페이지에서 **새 사용자 지정 경고 규칙**을 클릭합니다.

    ![사용자 지정 경고](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  다음 옵션을 포함한 사용자 지정 경고 규칙 만들기 페이지가 표시됩니다.

    ![만들기](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  **이름** 필드에 이 사용자 지정 규칙의 이름을 입력합니다.
5.  **설명** 필드에 이 규칙의 의도를 반영하는 간단한 설명을 입력합니다.
6.  **심각도** 필드에서 필요에 맞게 심각도 수준(높음, 보통, 낮음)을 선택합니다.
7.  **구독** 필드에서 이 규칙을 적용할 수 있는 구독을 선택합니다.
8.  **작업 영역** 필드 및 **검색 쿼리** 필드에서 이 규칙으로 모니터링하려는 작업 영역 및 결과를 가져오는 데 사용하려는 쿼리를 선택합니다.

    > [!NOTE]
    > 사용자 지정 경고를 저장하기로 선택하는 작업 영역에서 쓰기 권한이 필요합니다.
    >
    >

    쿼리 결과가 경고를 트리거합니다. 유효한 쿼리를 입력하는 경우 녹색 확인 표시가 이 필드의 오른쪽 모서리에 나타납니다.

    ![Query](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. **기간** 필드에서 위의 쿼리가 실행되는 시간 범위를 선택합니다. 이 필드의 아래쪽에서 검색 결과는 선택한 시간 범위에 따라 변경됩니다.

    ![기간](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. **평가** 필드에서 이 규칙을 평가하고 실행해야 하는 빈도를 선택합니다.
12. **결과 수** 필드에서 연산자를 선택합니다(초과 또는 미만).
13. **임계값** 필드에서 이전에 선택한 연산자에 대해 참조로 사용할 수 있는 숫자를 입력합니다.
14. Security Center에서 이 규칙에 다른 경고를 보내기 전에 대기할 시간을 설정하려는 경우 **경고 표시 안 함** 옵션을 사용합니다.
15. **확인**을 클릭하여 마칩니다.

새 경고 규칙 만들기를 마치면 사용자 지정 경고 규칙의 목록에 표시됩니다. 해당 규칙의 조건을 충족하면 새로운 경고가 트리거되고 **보안 경고** 대시보드에서 확인할 수 있습니다.

![알림](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

규칙을 만드는 동안 설정된 매개 변수(검색 쿼리, 임계값 등)를 이 사용자 지정 규칙의 경고에서 사용할 수 있습니다.

## <a name="see-also"></a>참고자료
이 문서에서는 Azure Security Center에서 사용자 지정 경고 규칙을 만드는 방법을 알아보았습니다. Azure Security Center에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
