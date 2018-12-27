---
title: Azure Monitor의 Azure Active Directory 활동 로그(미리 보기) | Microsoft Docs
description: Azure Monitor의 Azure Active Directory 활동 로그 개요(미리 보기)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0d88aef46761e8c7d8217f04befec88816587c03
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358277"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Azure Monitor의 Azure AD 활동 로그(미리 보기)

이제 Azure Monitor를 사용하여 사용자 고유의 저장소 계정 또는 이벤트 허브에 Azure AD(Azure Active Directory) 활동 로그를 라우팅할 수 있습니다. Azure Monitor의 Azure Active Directory 로그 공개 미리 보기를 사용하면 다음을 수행할 수 있습니다.

* Azure 저장소 계정의 감사 로그를 보관하여 데이터를 장기간 보존합니다.
* Splunk 및 QRadar 같은 인기 있는 SIEM(보안 정보 및 이벤트 관리) 도구를 사용하여 감사 로그를 Azure 이벤트 허브로 스트리밍하여 분석합니다.
* 감사 로그를 이벤트 허브로 스트리밍하여 고유한 사용자 지정 로그 솔루션과 통합합니다.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>지원되는 보고서

이 기능을 사용하여 감사 활동 로그 및 로그인 활동 로그를 Azure Storage 계정, 이벤트 허브 또는 사용자 지정 솔루션으로 라우팅할 수 있습니다. 

* **감사 로그**: [감사 로그 활동 보고서](active-directory-reporting-activity-audit-logs.md)는 테넌트에서 수행된 모든 작업 기록에 대한 액세스를 제공합니다.
* **로그인 로그**: [로그인 활동 보고서](active-directory-reporting-activity-sign-ins.md)를 사용하면 감사 로그에 보고된 작업을 누가 수행했는지 확인할 수 있습니다.

> [!NOTE]
> B2C 관련 감사 및 로그인 활동 로그는 현재 지원되지 않습니다.
>

## <a name="prerequisites"></a>필수 조건

이 기능을 사용하려면 다음이 필요합니다.

* Azure 구독. Azure 구독이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* Azure Portal에서 Azure AD 로그에 액세스할 수 있는 Azure AD Free, Basic, Premium 1 또는 Premium 2 [라이선스](https://azure.microsoft.com/pricing/details/active-directory/). 

감사 로그 데이터를 라우팅하려는 위치에 따라 다음 둘 중 하나가 필요합니다.

* *ListKeys* 권한이 있는 Azure 저장소 계정. Blob 저장소 계정이 아닌 일반 저장소 계정을 사용하는 것이 좋습니다. 저장소 가격 책정 정보는 [Azure Storage 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=storage)를 참조하세요. 
* 타사 솔루션과 통합할 Azure Event Hubs 네임스페이스.

> [!NOTE]
> Azure AD 활동 로그에 액세스하려면 Azure AD 테넌트의 *글로벌 관리자* 또는 *보안 관리자*여야 합니다.
>

## <a name="cost-considerations"></a>비용 고려 사항

Azure AD 라이선스가 이미 있는 경우 저장소 계정 및 이벤트 허브를 설정하려면 Azure 구독이 필요합니다. Azure 구독은 무료로 제공되지만 보관에 사용하는 저장소 계정과 스트리밍에 사용하는 이벤트 허브를 비롯한 Azure 리소스를 활용하려면 비용을 지불해야 합니다. 데이터의 양 그리고 그에 따라 발생하는 비용은 테넌트 크기에 따라 크게 달라질 수 있습니다. 

### <a name="storage-size-for-activity-logs"></a>활동 로그의 저장소 크기

모든 감사 로그 이벤트는 약 2KB의 데이터 저장소를 사용합니다. 사용자가 100,000명이고 하루에 약 150만 개의 이벤트가 발생하는 테넌트의 경우 하루에 약 3GB의 데이터 저장소가 필요합니다. 약 5분 단위로 쓰기가 발생하므로 매달 약 9000개의 쓰기 작업을 예상할 수 있습니다. 

다음 표에는 미국 서부에서 범용 v2 저장소 계정을 1년 이상 보유할 때 발생할 것으로 예상되는 테넌트 크기에 따른 예상 비용이 정리되어 있습니다. 애플리케이션의 예상 데이터 볼륨에 해당하는 정확한 예상 가격을 계산하려면 [Azure 저장소 가격 계산기](https://azure.microsoft.com/pricing/details/storage/blobs/)를 사용하세요. 

| 로그 범주 | 사용자 수 | 일간 이벤트 수 | 월간 데이터 볼륨(예상치) | 월간 비용(예상치) | 연간 비용(예상치) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| 감사 | 100,000 | 150만&nbsp; | 90GB | $1.93 | $23.12 |
| 감사 | 1,000 | 15,000 | 900MB | $0.02 | $0.24 |
| 로그인 | 1,000 | 34,800 | 4GB | $0.13 | $1.56 |
| 로그인 | 100,000 | 1500만&nbsp; | 1.7TB | $35.41 | $424.92 | 


### <a name="event-hub-messages-for-activity-logs"></a>활동 로그에 대한 이벤트 허브 메시지

이벤트는 약 5분 간격으로 일괄 처리되며 해당 시간대 내에서 모든 이벤트를 포함한 단일 메시지로 전송됩니다. 이벤트 허브에서 한 메시지의 최대 크기는 256KB이며, 해당 시간대의 총 메시지 크기가 해당 볼륨을 초과하면 여러 개의 메시지가 전송됩니다. 

예를 들어 사용자 수가 100,000명을 초과하는 대규모 테넌트에서는 평균적으로 초당 약 18개 이벤트가 발생하며, 이는 5분당 5,400개 이벤트와 동일한 속도입니다. 감사 로그는 이벤트당 약 2KB이므로 데이터 10.8MB와 동일합니다. 따라서 5분 간격으로 메시지 43개가 이벤트 허브로 전송됩니다. 

다음 표에는 미국 서부에 있는 기본 이벤트 허브의 이벤트 데이터 볼륨별 예상 월간 비용이 나와 있습니다. 애플리케이션의 예상 데이터 볼륨에 해당하는 정확한 예상 가격을 계산하려면 [이벤트 허브 가격 계산기](https://azure.microsoft.com/pricing/details/event-hubs/)를 사용하세요.

| 로그 범주 | 사용자 수 | 초당 이벤트 수 | 5분 간격당 이벤트 수 | 간격당 볼륨 | 간격당 메시지 수 | 월간 메시지 수 | 월간 비용(예상치) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| 감사 | 100,000 | 18 | 5,400 | 10.8MB | 43 | 371,520 | $10.83 |
| 감사 | 1,000 | 0.1 | 52 | 104KB | 1 | 8,640 | $10.80 |
| 로그인 | 1,000 | 178 | 53,400 | 106.8MB&nbsp; | 418 | 3,611,520 | $11.06 |  


## <a name="frequently-asked-questions"></a>질문과 대답

이 섹션에서는 Azure Monitor의 Azure AD 로그에 대해 자주 묻는 질문에 대답하고 알려진 문제에 대해 토론합니다.

**Q: 어디서 시작해야 합니까?** 

**A**: 이 문서에서는 이 기능을 배포하려면 무엇이 필요한지 살펴봅니다. 필수 구성 요소를 충족한 후에는 로그를 구성하고 이벤트 허브로 라우팅하는 방법을 안내하는 자습서를 진행합니다.

---

**Q: 어떤 로그가 포함됩니까?**

**A:** 현재 B2C 관련 감사 이벤트가 포함되지 않지만, 이 기능을 라우팅하는 데 로그인 활동 로그와 감사 로그를 모두 사용할 수 있습니다. 현재 지원되는 로그 유형 및 기능 기반 로그를 알아보려면 [감사 로그 스키마](reporting-azure-monitor-diagnostics-audit-log-schema.md) 및 [로그인 로그 스키마](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)를 참조하세요. 

---

**Q: 작업 수행 후 해당 로그가 이벤트 허브에 표시되려면 얼마나 걸립니까?**

**A**: 작업이 수행된 후 2~5분 내에 이벤트 허브에 로그가 나타나야 합니다. Event Hubs에 대한 자세한 내용은 [Azure Event Hubs란?](../event-hubs/event-hubs-about.md)을 참조하세요.

---

**Q: 작업 수행 후 해당 로그가 저장소 계정에 표시되려면 얼마나 걸립니까?**

**A:** Azure 저장소 계정의 경우 대기 시간은 어디서나 작업 수행 후 5~15분입니다.

---

**Q: 데이터를 저장하는 데 비용이 얼마나 들까요?**

**A:** 저장소 비용은 로그의 크기와 사용자가 선택한 보존 기간에 따라 달라집니다. 생성된 로그 볼륨에 따라 달라지는 예상 테넌트 비용 목록을 보려면 [활동 로그에 대한 저장소 크기](https://review.docs.microsoft.com/en-us/azure/active-directory/reporting-azure-monitor-diagnostics-overview?branch=pr-en-us-47660#storage-size-for-activity-logs) 섹션으로 이동하세요.

---

**Q: 이벤트 허브에 데이터를 스트리밍하는 데 비용이 얼마나 들까요?**

**A:** 스트리밍 비용은 분당 수신 메시지 수에 따라 다릅니다. 이 문서에서는 메시지 수를 기반으로 비용을 계산하고 예상 비용을 나열하는 방법을 설명합니다. 

---

**Q: 현재 지원되는 SIEM 도구는 무엇입니까?** 

**A:** Azure Monitor는 현재 Splunk, QRadar 및 Sumo Logic에서 지원됩니다. 그러나 Splunk는 Azure AD 로그에 지원되는 유일한 SIEM 도구입니다. 커넥터의 작동 원리에 대한 자세한 내용은 [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)을 참조하세요.

---

**Q: 외부 SIEM 도구를 사용하지 않고 이벤트 허브에서 데이터에 액세스할 수 있나요?** 

**A**: 예. 사용자 지정 애플리케이션에서 로그에 액세스하려면 [Event Hubs API](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)를 사용합니다. 

---


## <a name="next-steps"></a>다음 단계

* [저장소 계정에 활동 로그 보관](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [활동 로그를 이벤트 허브로 라우팅](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [Azure 진단 로그에 대해 자세히 알아보기](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
