---
title: "Operations Management Suite 보안 및 감사 솔루션 기준의 웹 기준 평가 | Microsoft Docs"
description: "이 문서에서는 OMS 보안 및 감사 솔루션에 웹 기준 평가를 사용하여 규정 준수 및 보안 목적을 위해 모니터링되는 모든 웹 서버의 기준 평가를 수행하는 방법을 설명합니다."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite 보안 및 감사 솔루션의 웹 기준 평가
이 문서를 통해 OMS 보안 및 감사 웹 기준 평가 기능을 사용하여 모니터링되는 리소스의 보안 상태에 액세스할 수 있습니다.

## <a name="what-is-web-baseline-assessment"></a>웹 기준 평가란?
현재 OMS 보안은 운영 체제에 대한 보안 기준 평가를 제공합니다. 24시간마다 서버의 OS 설정을 검색하고 잠재적으로 취약한 설정에 대한 보기를 제공합니다. 이에 대한 자세한 내용은 [Operations Management Suite 보안 및 감사 솔루션의 기준 평가](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline)를 참조하세요.

웹 기준 평가의 목표는 잠재적으로 취약한 웹 서버 설정을 찾는 것입니다. 웹 기준 구성에 대한 세 가지 기본 소스는 .NET, ASP.NET 및 IIS 구성입니다.  운영 체제 기준 평가와 마찬가지로 OMS 보안은 24시간마다 웹 서버를 검색하고 그 중 보안 상태에 대한 보기를 제공하게 됩니다.  IIS(인터넷 정보 서비스)에서 구성은 고도로 사용자 지정되어 다양한 사이트 및 응용 프로그램 수준을 재정의할 수 있습니다. 스캐너는 기본 루트 수준 외에도 각 응용 프로그램/사이트 수준의 설정을 확인합니다. 잠재적으로 취약한 설정을 찾아서 신속하게 해결하는 데 도움이 되며, 해당 설정에 대한 권장 사항이 제공됩니다.

>[!NOTE] 
>이 [페이지](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0)에서는 OMS 보안에서 사용하는 일반 구성 식별자 및 기준 규칙을 다운로드할 수 있습니다.


## <a name="web-security-baseline-assessment"></a>웹 보안 기준 평가

이 미리 보기의 경우 OMS Search 옵션과 OMS 보안 및 감사 대시보드를 통해 기능을 사용할 수 있습니다. 아래의 단계를 수행하여 적절한 쿼리를 수행합니다.

1. **Microsoft Operations Management Suite** 기본 대시보드에서 **보안 및 감사** 타일을 클릭합니다.
2. **보안 및 감사** 대시보드에서 OS 기준 관점 옆에 있는 웹 기준 관점을 볼 수 있습니다.
   
    ![OMS 보안 및 감사 웹 보안 기준 평가](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. 왼쪽 창에는 기준과 비교한 웹 서버 수, 평가한 모든 서버에서 전달된 규칙의 평균 백분율, 평가된 서버 목록이 표시됩니다.
4. 오른쪽 창에는 실패한 고유한 규칙이 *심각도* 및 *규칙 유형*별로 표시됩니다. 오른쪽 창의 규칙을 클릭하면 해당 규칙의 세부 정보가 표시됩니다. 아래 이미지에 예제가 나와 있습니다. 평가된 규칙은 *규칙 설정* 아래에 나열됩니다. *AzId* 필드는 Microsoft에서 기준 규칙을 추적하는 데 사용하는 각 규칙의 고유 식별자입니다. 그 외에도 사용자가 *예상 결과*(Microsoft의 권장 값) 및 규칙이 보안에 미치는 영향에 대한 기타 세부 정보를 볼 수 있습니다.
    
    ![쿼리](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. 사용자 고유의 쿼리를 만들어서 결과를 검토할 수 있습니다. 

사용할 수 있는 첫 번째 쿼리는 **웹 기준 평가 요약**입니다. **여기에서 검색 시작** 필드에 Type*=SecurityBaselineSummary BaselineType=Web* 쿼리를 입력합니다. 다음은 출력 샘플입니다.

![쿼리 결과](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>이 쿼리에서 각 레코드는 단일 서버에 대한 요약 평가를 나타냅니다.

**로그 검색**에 있는 경우 여러 쿼리를 입력하여 웹 기준 평가에 대한 자세한 정보를 얻을 수 있습니다. 이 미리 보기에서는 이전 쿼리 외에도 다음과 같은 쿼리를 사용할 수 있습니다.

**웹 기준 규칙 평가**: 각 레코드는 단일 서버에서 단일 웹 기준 규칙 평가를 나타냅니다. 실패한 규칙에 대한 모든 데이터, 규칙을 평가한 *SitePath*, *예상 결과* 및 *실제 결과*가 포함됩니다.

쿼리: *Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![쿼리 결과 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**특정 서버에 대한 모든 결과 표시**: 이 쿼리는 특정 서버의 결과를 보는 방법을 보여 줍니다. 쿼리: *Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![쿼리 결과 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

이러한 레코드/쿼리를 사용하여 고유한 대시보드, 보고서 또는 경고를 만들 수 있습니다. 다음은 대시보드에 추가할 수 있는 샘플 UI 컨트롤입니다. [여기](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/)에서 OMS 뷰 디자이너를 사용하여 데이터를 시각화하는 방법을 배울 수 있습니다. 이를 사용자 지정하면 아래 화면은 타일의 모양을 보여 줍니다.

![dashboard](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>참고 항목
이 문서에서는 OMS 보안 및 감사 웹 기준 평가에 대해 알아보았습니다. OMS 보안에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [OMS(Operations Management Suite) 개요](operations-management-suite-overview.md)
* [Operations Management Suite 보안 및 감사 솔루션의 보안 경고 모니터링 및 응답](oms-security-responding-alerts.md)
* [Operations Management Suite 보안 및 감사 솔루션의 리소스 모니터링](oms-security-monitoring-resources.md)

