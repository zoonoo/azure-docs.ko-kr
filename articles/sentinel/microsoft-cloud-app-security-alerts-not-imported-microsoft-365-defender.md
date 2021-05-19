---
title: Microsoft 365 Defender 통합을 통해 Azure Sentinel로 가져오지 않은 Microsoft Cloud App Security 경고 | Microsoft Docs
description: 이 문서에서는 Microsoft 365 Defender에서 수집되지 않으므로 Azure Sentinel로 직접 수집해야 하는 Microsoft Cloud App Security의 경고를 표시합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 0232cbde5aaddad268aa0e4725ee3587a2461800
ms.sourcegitcommit: 18cd3c1c8cc47258c6a1a04e0e03d6248c52ef24
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107992754"
---
# <a name="microsoft-cloud-app-security-alerts-not-imported-into-azure-sentinel-through-microsoft-365-defender-integration"></a>Microsoft 365 Defender 통합을 통해 Azure Sentinel로 가져오지 않은 Microsoft Cloud App Security 경고

다른 Microsoft Defender 구성 요소(예: Defender for Endpoint, Defender for Identity 및 Defender for Office 365)와 마찬가지로 Microsoft Cloud App Security는 Microsoft 365 Defender에서 수집되는 경고를 생성합니다. Microsoft 365 Defender는 Microsoft 365 Defender 커넥터를 사용하는 경우 [Azure Sentinel](microsoft-365-defender-sentinel-integration.md#microsoft-365-defender-incidents-and-microsoft-incident-creation-rules)에서 수집되고 동기화되는 인시던트를 생성합니다.

다른 세 가지 구성 요소와 달리 **일부 유형** 의 Cloud App Security 경고는 Microsoft 365 Defender에 온보딩되지 않으므로 Azure Sentinel에서 모든 Cloud App Security 경고의 인시던트를 원하는 경우, Sentinel로 직접 수집되는 경고는 계속해서 인시던트를 생성하고 Microsoft 365 Defender로 온보딩되는 경고는 인시던트를 생성하지 않아 중복되지 않도록 Microsoft 인시던트 생성 분석 규칙을 적절하게 조정해야 합니다.

## <a name="cloud-app-security-alerts-not-onboarded-to-microsoft-365-defender"></a>Cloud App Security 경고가 Microsoft 365 Defender로 온보딩되지 않음

다음 경고는 Microsoft 365 Defender에 온보딩되지 않으며, 이러한 경고를 발생하는 Microsoft 인시던트 생성 규칙은 인시던트를 생성하도록 계속 구성되어야 합니다.

| Cloud App Security 경고 표시 이름 | Cloud App Security 경고 이름 |
|-|-|
| **액세스 정책 경고** | `ALERT_CABINET_INLINE_EVENT_MATCH` |
| **검색된 트래픽 로그의 활동 만들기가 일일 제한을 초과함** | `ALERT_DISCOVERY_TRAFFIC_LOG_EXCEEDED_LIMIT` |
| **활동 정책 경고** | `ALERT_CABINET_EVENT_MATCH_AUDIT` |
| **비정상적인 반출 경고** | `ALERT_EXFILTRATION_DISCOVERY_ANOMALY_DETECTION` |
| **손상된 계정** | `ALERT_COMPROMISED_ACCOUNT` |
| **검색된 앱 보안 위반 경고** | `ALERT_MANAGEMENT_DISCOVERY_BREACHED_APP` |
| **비활성 계정** | `ALERT_ZOMBIE_USER` |
| **조사 우선 순위 점수가 증가함** | `ALERT_UEBA_INVESTIGATION_PRIORITY_INCREASE` |
| **악의적인 OAuth 앱 동의** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MALICIOUS_OAUTH_APP_CONSENT` |
| **잘못된 OAuth 앱 이름** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MISLEADING_APP_NAME` |
| **OAuth 앱의 잘못된 게시자 이름** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MISLEADING_PUBLISHER_NAME` |
| **새 앱이 검색됨** | `ALERT_CABINET_DISCOVERY_NEW_SERVICE` |
| **OAuth 앱에서 비보안 리디렉션 URL이 사용됨** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_NON_SECURE_REDIRECT_URL` |
| **OAuth 앱 정책 경고** | `ALERT_CABINET_APP_PERMISSION` |
| **의심스러운 활동 경고** | `ALERT_SUSPICIOUS_ACTIVITY` |
| **의심스러운 클라우드 사용 경고** | `ALERT_DISCOVERY_ANOMALY_DETECTION` |
| **의심스러운 OAuth 앱 이름** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_SUSPICIOUS_APP_NAME` |
| **시스템 경고 앱 커넥터 오류** | `ALERT_MANAGEMENT_DISCONNECTED_API` |
| **시스템 경고 Cloud Discovery 자동 로그 업로드 오류** | `ALERT_MANAGEMENT_LOG_COLLECTOR_LOW_RATE` |
| **시스템 경고 Cloud Discovery 로그 처리 오류** | `ALERT_MANAGEMENT_LOG_COLLECTOR_CONSTANTLY_FAILED_PARSING` |
| **시스템 경고 ICAP 커넥터 오류** | `ALERT_MANAGEMENT_DLP_CONNECTOR_ERROR` |
| **시스템 경고 SIEM 에이전트 오류** | `ALERT_MANAGEMENT_DISCONNECTED_SIEM` |
| **시스템 경고 SIEM 에이전트 알림** | `ALERT_MANAGEMENT_NOTIFICATIONS_SIEM` |
| **클라우드 리소스에 대한 특이 지역** | `MCAS_ALERT_ANUBIS_DETECTION_UNCOMMON_CLOUD_REGION` |
|

## <a name="next-steps"></a>다음 단계

- Azure Sentinel에 [Microsoft 365 Defender 연결](connect-microsoft-365-defender.md)
- [Azure Sentinel](overview.md), [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender) 및 [Cloud App Security](/cloud-app-security/what-is-cloud-app-security)에 대해 자세히 알아봅니다.
