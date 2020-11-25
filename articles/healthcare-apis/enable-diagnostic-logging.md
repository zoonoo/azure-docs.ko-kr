---
title: Azure API에서 FHIR에 대 한 진단 로깅 사용
description: 이 문서에서는 Azure API for FHIR에서 진단 로깅을 사용 하도록 설정 하는 방법을 설명®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/01/2019
ms.openlocfilehash: 54119585d4f1377b60b85fbad01fe90f097a304f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95905177"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Azure API에서 FHIR에 대 한 진단 로깅 사용

이 문서에서는 FHIR 용 Azure API에서 진단 로깅을 사용 하도록 설정 하 고 이러한 로그에 대 한 몇 가지 샘플 쿼리를 검토할 수 있는 방법에 대해 설명 합니다. 진단 로그에 대 한 액세스는 HIPAA와 같은 규정 요구 사항을 준수 해야 하는 의료 서비스에 필수적입니다. 진단 로그를 사용 하도록 설정 하는 Azure API for FHIR의 기능은 Azure Portal의 [**진단 설정**](../azure-monitor/platform/diagnostic-settings.md) 입니다. 

## <a name="enable-audit-logs"></a>감사 로그 사용
1. Azure API for FHIR에서 진단 로깅을 사용 하도록 설정 하려면 Azure Portal에서 Azure API for FHIR 서비스를 선택 합니다. 
2. **진단 설정**  
 ![ 진단 설정으로 이동 합니다.](media/diagnostic-logging/diagnostic-settings-screen.png) 

3. **+ 진단 설정 추가** 를 선택 합니다.

4. 설정의 이름 입력

5. 진단 로그에 액세스 하는 데 사용할 방법을 선택 합니다.

    1. 감사 또는 수동 검사를 위해 **저장소 계정에 보관** 합니다. 사용 하려는 저장소 계정이 이미 만들어져 있어야 합니다.
    2. 타사 서비스 또는 사용자 지정 분석 솔루션에의 한 수집을 위해 **이벤트 허브로 스트리밍합니다** . 이 단계를 구성 하려면 먼저 이벤트 허브 네임 스페이스 및 이벤트 허브 정책을 만들어야 합니다.
    3. Azure Monitor에서 Log Analytics 작업 영역 **으로 스트리밍합니다** . 이 옵션을 선택 하려면 먼저 로그 분석 작업 영역을 만들어야 합니다.

6. **AuditLogs** 및 캡처할 메트릭을 선택 합니다. FHIR 용 Azure IoT 커넥터를 사용 하는 경우 메트릭에 대 한 **오류, 트래픽 및 대기 시간** 을 선택 해야 합니다. 

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

7. **저장** 을 선택합니다.


> [!Note] 
> Log Analytics에 첫 번째 로그가 표시 되는 데 최대 15 분이 걸릴 수 있습니다.  
 
진단 로그를 사용 하는 방법에 대 한 자세한 내용은 [Azure 리소스 로그 설명서](../azure-monitor/platform/platform-logs-overview.md) 를 참조 하세요.

## <a name="audit-log-details"></a>감사 로그 정보
이번에는 Azure API for FHIR 서비스에서 감사 로그에 다음 필드를 반환 합니다. 

|필드 이름  |Type  |메모  |
|---------|---------|---------|
|CallerIdentity|동적|Id 정보를 포함 하는 일반 속성 모음
|CallerIdentityIssuer|String|발급자 
|CallerIdentityObjectId|String|Object_Id 
|CallerIPAddress|String|호출자의 IP 주소 
|CorrelationId|String| 상관관계 ID
|FhirResourceType|String|작업이 실행 된 리소스 형식입니다.
|LogCategory|String|로그 범주 (현재 ' AuditLogs ' LogCategory를 반환 합니다.)
|위치|String|요청을 처리 한 서버의 위치 (예: 미국 중 남부)
|OperationDuration|Int|이 요청을 완료 하는 데 걸린 시간 (초)
|OperationName|String| 작업 유형 (예: 업데이트, 검색 유형)을 설명 합니다.
|RequestUri|String|요청 URI 
|ResultType|String|사용 가능한 값이 현재 **시작**, **성공** 또는 **실패입니다.**
|StatusCode|Int|HTTP 상태 코드입니다. (예: 200) 
|TimeGenerated|DateTime|이벤트의 날짜 및 시간|
|속성|String| FhirResourceType의 속성을 설명 합니다.
|SourceSystem|String| 원본 시스템 (이 경우 항상 Azure)
|TenantId|String|테넌트 ID
|Type|String|로그 유형 (이 경우 항상 MicrosoftHealthcareApisAuditLog)
|_ResourceId|String|리소스에 대 한 세부 정보

## <a name="sample-queries"></a>샘플 쿼리

로그 데이터를 탐색 하는 데 사용할 수 있는 몇 가지 기본 Application Insights 쿼리는 다음과 같습니다.

다음 쿼리를 실행 하 여 **100** 최신 로그를 확인 합니다.

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

다음 쿼리를 실행 하 여 **리소스 유형별로** 작업을 그룹화 합니다.

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

이 쿼리를 실행 하 여 실패 한 모든 **결과** 가져오기

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>결론 
진단 로그에 대 한 액세스 권한은 서비스를 모니터링 하 고 규정 준수 보고서를 제공 하는 데 필수적입니다. FHIR 용 Azure API를 통해 진단 로그를 통해 이러한 작업을 수행할 수 있습니다. 
 
FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 FHIR 용 Azure API에 대 한 감사 로그를 사용 하도록 설정 하는 방법을 알아보았습니다. 다음으로, FHIR 용 Azure API에서 구성할 수 있는 다른 추가 설정에 대해 알아봅니다.
 
>[!div class="nextstepaction"]
>[추가 설정](azure-api-for-fhir-additional-settings.md)
