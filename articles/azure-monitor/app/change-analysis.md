---
title: Azure 모니터 응용 프로그램 변경 분석-Azure Monitor 응용 프로그램을 사용 하 여 라이브 사이트 문제/중단에 영향을 줄 수 있는 변경 분석 검색 | Microsoft Docs
description: Azure Monitor 응용 프로그램 변경 분석을 사용 하 여 Azure App Service에서 응용 프로그램 라이브 사이트 문제 해결
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415832"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Azure 모니터 응용 프로그램 변경 분석 (공개 미리 보기)

라이브 사이트 문제/중단이 발생 하는 경우 근본 원인을 신속 하 게 결정이 중요 합니다. 솔루션을 모니터링 하는 standard를 사용 하는 문제가 발생 하는 구성 요소에도 종종 실패를 신속 하 게 식별 도움이 될 수 있습니다. 하지만이 오류가 발생 하는 이유는 무엇에 대 한 즉시 설명이로 인해 항상 없습니다. 사이트는 손상 된 이제 5 분 이전으로 작동 합니다. 지난 5 분 동안에서 변경 내용? 새 응용 프로그램 변경 분석 응답 하도록 디자인 되어 Azure Monitor 기능 하는 질문입니다. 기능을 구축 하 여 합니다 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview) 응용 프로그램 변경 분석은 Azure App Service 응용 프로그램 변경 내용을 관찰성을 늘리고 줄일 MTTR (평균 시간에 복구)에 대 한 정보를 제공 합니다.

> [!IMPORTANT]
> Azure 모니터 응용 프로그램 변경 분석 현재 공개 미리 보기 중입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-do-i-use-application-change-analysis"></a>응용 프로그램 변경 분석을 사용 하는 방법

Azure 모니터 응용 프로그램 변경 분석은 현재 셀프 서비스 내장 **진단 및 문제 해결** 환경에서 액세스할 수 있는 합니다 **개요** Azure App Service의 섹션 응용 프로그램:

![Azure App Service의 스크린 샷 개요 개요 단추 주위에 빨간색 상자를 사용 하 여 페이지와 진단 및 문제 단추 해결](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>변경 분석 사용

1. 선택 **가용성 및 성능**

    ![가용성 및 성능 문제 해결 옵션의 스크린샷](./media/change-analysis/availability-and-performance.png)

2. 클릭 합니다 **응용 프로그램이 크래시 되** 바둑판식으로 배열 합니다.

   ![응용 프로그램 충돌 타일을 사용 하 여 스크린 샷](./media/change-analysis/application-crashes-tile.png)

3. 사용할 수 있도록 **변경 Analysis** 선택 **지금 사용**합니다.

   ![가용성 및 성능 문제 해결 옵션의 스크린샷](./media/change-analysis/application-crashes.png)

4. 전체 활용 되려면 분석 기능 집합을 변경 **변경 분석**, **코드 변경을 검색할**, 및 **무중단** 에 **에서** 선택한 **저장할**합니다.

    ![Azure App Service 설정 변경 분석 사용자 인터페이스 스크린샷](./media/change-analysis/change-analysis-on.png)

    하는 경우 **변경 분석** 가 사용 하도록 설정 해야 리소스 수준 변경 내용을 검색할 수 있습니다. 하는 경우 **코드 변경 사항 검색** 가 사용 하도록 설정 또한 배포 파일을 참조 하 고 사이트 구성 변경 합니다. 사용 하도록 설정 **무중단** 변경 검색 성능을 최적화 하지만 청구 관점에서 추가 비용이 발생할 수 있습니다.

5.  모든 활성화 되 면 선택 **진단 및 문제 해결** > **가용성과 성능을** > **응용 프로그램이 크래시 되** 하면 변경 분석 환경에 액세스할 수 있습니다. 그래프에는 이러한 변경 내용에 세부 정보와 함께 시간이 지남에 따라 발생 한 변경 유형을 summerize 됩니다.

     ![변경 diff 보기의 스크린샷](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>문제 해결

### <a name="unable-to-fetch-change-analysis-information"></a>변경 분석 정보를 가져올 수 없습니다.

현재 미리 보기 온 보 딩 환경 사용 하 여 임시 문제입니다. 해결 방법은 웹 앱에서 숨겨진된 태그를 설정 하 고 다음 페이지를 새로 고쳐 구성 됩니다.

   ![숨겨진 변경 태그의 스크린샷](./media/change-analysis/hidden-tag.png)

PowerShell을 사용 하 여 숨겨진된 태그를 설정 합니다.

1. Azure Cloud Shell을 엽니다.

    ![Azure Cloud Shell 변화의 스크린 샷](./media/change-analysis/cloud-shell.png)

2. PowerShell 셸 형식을 변경 합니다.

   ![Azure Cloud Shell 변화의 스크린 샷](./media/change-analysis/choose-powershell.png)

3. 다음 명령 실행:

```powershell
$webapp=Get-AzWebApp -Name <name_of_your_webapp>
$tags = $webapp.Tags
$tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
```

> [!NOTE]
> 숨겨진된 태그 추가 되 면 처음 수 있으려면 먼저 변경 내용을 보려면 최대 4 시간을 대기 해야 계속 합니다. 변경 분석 서비스 검색의 성능 영향을 제한 하는 동안 웹 앱을 검색 하는 데 사용 하는 4 시간 freqeuncy 때문입니다.

## <a name="next-steps"></a>다음 단계

- Azure App Services의 모니터링을 향상 [Application Insights 기능을 사용 하 여](azure-web-apps.md) Azure Monitor의 합니다.
- 에 대 한 이해를 향상 합니다 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview) power Azure Monitor 응용 프로그램 분석을 변경할 수 있습니다. 
