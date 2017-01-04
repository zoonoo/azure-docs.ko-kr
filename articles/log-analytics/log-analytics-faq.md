---
title: Log Analytics FAQ | Microsoft Docs
description: "Log Analytics 서비스에 대해 자주 묻는 질문에 대한 답변입니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e24e6f9cc383be77dc631a0dd67db099906dccc0


---
# <a name="log-analytics-faq"></a>Log Analytics FAQ
Microsoft FAQ는 Microsoft Operations Management Suite(OMS)의 Log Analytics에 대해 자주 묻는 질문의 목록입니다. Log Analytics에 대한 추가 질문이 있으면 [토론 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) 으로 이동하여 질문을 게시하세요. 커뮤니티의 구성원이 답변을 얻는 데 도움을 줄 것입니다. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="general"></a>일반
**Q. AD 및 SQL 평가 솔루션에서 수행하는 검사는 무엇입니까?**

A. 다음 쿼리는 현재 수행하는 모든 검사에 대한 설명을 보여 줍니다.

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

추가 검토를 위해 결과를 Excel로 내보낼 수 있습니다.

**Q: SCOM Administration에서 *OMS*와 다른 내용이 표시되는 이유는 무엇인가요?**

A: 사용 중인 SCOM의 업데이트 롤업에 따라 *System Center Advisor*, *Operational Insights* 또는 *Log Analytics*에 대한 노드가 표시될 수 있습니다.

*OMS* 에 대한 텍스트 문자열 업데이트가 수동으로 가져와야 하는 관리 팩에 포함됩니다. 최신 SCOM 업데이트 롤업 KB 문서의 지침을 따르고 OMS 콘솔을 새로 고치면 *OMS* 노드에 최신 업데이트가 표시됩니다.

**Q: OMS의 *온-프레미스* 버전이 있나요?**

A: 아니요. Log Analytics에서 대량의 데이터를 처리하고 저장합니다. 클라우드 서비스로 Log Analytics는 필요한 경우 강화하여 환경에 대한 성능 영향을 방지할 수 있습니다.

또한 클라우드 서비스로 제공할 경우 Log Analytics 인프라를 유지 및 실행하지 않아도 되며 빈번한 기능 업데이트 및 수정을 수신할 수 있습니다.

## <a name="configuration"></a>구성
**Q. WAD(Azure 진단)에서 읽어오는 데 사용되는 테이블/Blob 컨테이너의 이름을 변경할 수 있나요?**  

A.    아니요, 현재는 불가능하지만 향후 릴리스에서 가능할 예정입니다.

**Q. OMS 서비스에서 사용하는 IP 주소는 어떻게 되나요? 내 방화벽에서 OMS 서비스에 대한 트래픽만 허용하도록 하려면 어떻게 하나요?**  

A. Log Analytics 서비스는 Azure의 맨 위에 기본 제공되며 끝점은 [Microsoft Azure 데이터 센터 IP 범위](http://www.microsoft.com/download/details.aspx?id=41653)에 있는 IP를 수신합니다.

서비스 배포가 수행되면서 OMS 서비스의 실제 IP 주소가 변경됩니다. 방화벽을 통과하도록 허용할 DNS 이름이 [Log Analytics에서 프록시 및 방화벽 설정 구성](log-analytics-proxy-firewall.md)에 설명되어 있습니다.

**Q. Express 경로를 사용하여 Azure에 연결합니다. Log Analytics 트래픽이 내 ExpressRoute 연결을 사용할까요?**  

A. 여러 유형의 Express 경로 트래픽이 [Express 경로 설명서](../expressroute/expressroute-faqs.md#supported-services)에 나와 있습니다.

Log Analytics에 대한 트래픽은 공용 피어링 Express 경로 회로를 사용합니다.

**Q. 기존 Log Analytics 작업 영역을 다른 Log Analytics 작업 영역/Azure 구독으로 이동할 간단하고 쉬운 방법이 있나요?**   Azure 구독에서 테스트 및 시험해본 여러 고객의 OMS 작업 영역이 있으며 이 작업 영역을 자신의 Azure/OMS 구독으로 이동하기 위해 프로덕션으로 이동 중입니다.  

A. `Move-AzureRmResource` cmdlet을 사용하면 Log Analytics 작업 영역을 이동할 수 있으며 한 Azure 구독에서 다른 구독으로 자동화 계정도 이동할 수 있습니다. 자세한 내용은 [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx)를 참조하세요.

이러한 변경은 Azure 포털에서 이루어집니다.

데이터를 한 Log Analytics 작업 영역에서 다른 작업 영역으로 이동하거나 Log Analytics 데이터가 저장된 지역을 변경할 수 없습니다.

**Q: OMS를 SCOM에 추가하려면 어떻게 해야 하나요?**

A: 최신 업데이트 롤업으로 업데이트 및 관리 팩 가져오기를 통해 SCOM을 Log Analytics에 연결할 수 있습니다.

Log Analytics에 대한 SCOM 연결은 SCOM 2012 SP1 이상에서만 사용할 수 있습니다.

**Q: 에이전트가 Log Analytics와 통신할 수 있는지 어떻게 확인하나요?**

A: 에이전트가 OMS와 통신할 수 있는지 확인하려면 제어판, 보안 및 설정, **Microsoft Monitoring Agent**로 이동합니다.

**Azure Log Analytics(OMS)** 탭 아래에서 녹색 확인 표시를 찾습니다. 녹색 확인 표시 아이콘은 에이전트가 OMS 서비스와 통신할 수 있음을 확인합니다.

노란색 경고 아이콘은 에이전트가 OMS와 통신하는 데 문제가 있음을 의미합니다. 한 가지 일반적인 이유는 Microsoft Monitoring Agent 서비스가 중지되었고 다시 시작해야 하기 때문입니다.

**Q: 에이전트가 Log Analytics와의 통신을 중지하도록 하려면 어떻게 하나요?**

A: SCOM에서는 OMS 관리되는 목록에서 컴퓨터를 제거합니다. 그러면 에이전트에서 SCOM을 통한 모든 통신이 중지됩니다. OMS에 직접 연결된 에이전트인 경우 제어판, 보안 및 설정, **Microsoft Monitoring Agent**를 통해 OMS와 통신을 중지할 수 있습니다.
**Azure Log Analytics(OMS)**아래에서 나열된 모든 작업 영역을 제거합니다.

## <a name="agent-data"></a>에이전트 데이터
**Q. 에이전트를 통해 Log Analytics로 얼마나 많은 데이터를 보낼 수 있나요? 고객 한 명당 최대 데이터 용량이 있나요?**  
A. 무료 요금제에서는 작업 영역당 일일 용량을 500MB로 설정합니다. 표준 및 프리미엄 요금제에는 업로드되는 데이터 양에 제한이 없습니다. 클라우드 서비스로 OMS의 Log Analytics는 고객의 데이터 볼륨(일일 테라바이트 단위까지)을 처리하도록 자동 강화되도록 설계되었습니다.

Log Analytics 에이전트는 작은 공간이 있고 몇 가지 기본 데이터 압축을 수행하도록 설계되었습니다. 고객 중 한 명이 에이전트에 대해 수행한 테스트와 그에 대한 의견을 다룬 블로그를 실제로 작성했습니다. 데이터 볼륨은 고객이 사용하도록 설정한 솔루션에 따라 달라집니다. OMS 개요 페이지의 **사용량** 타일 아래에서 데이터 볼륨에 대한 자세한 정보를 확인하고 솔루션별로 정리된 내용을 볼 수 있습니다.

자세한 내용은 OMS 에이전트의 적은 사용 공간에 대한 [고객 블로그](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) 를 참조할 수 있습니다.

**Q. 데이터를 Log Analytics로 전송할 때 Microsoft Management Agent(MMA)에 사용된 네트워크 대역폭은 얼마나 되나요?**

A. 대역폭은 전송된 데이터 양에 대한 기능입니다. 네트워크를 통해 데이터가 전송되는 동안 데이터가 압축됩니다.

**Q. 에이전트당 얼마나 많은 데이터가 전송되나요?**

A. 다음에 따라 크게 달라집니다.

* 사용하도록 설정한 솔루션
* 수집되는 로그 및 성능 카운터 수
* 로그에 있는 데이터 볼륨

무료 가격 책정 계층은 여러 서버에 등록하고 일반적인 데이터 볼륨을 측정하는 좋은 방법입니다. 전체 사용 현황은 **사용량** 페이지에 표시됩니다.
WireData 에이전트를 실행할 수 있는 컴퓨터의 경우 다음 쿼리를 사용하여 전송 중인 데이터의 양을 확인할 수 있습니다.

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>다음 단계
* [Log Analytics 시작](log-analytics-get-started.md) 에서 Log Analytics에 대한 정보와 Log Analytics를 몇 분 만에 시작 및 실행하는 방법에 대해 알아보세요.



<!--HONumber=Nov16_HO3-->


