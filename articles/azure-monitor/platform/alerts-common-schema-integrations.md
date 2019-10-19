---
title: Logic Apps와 공통 경고 스키마를 통합 하는 방법
description: 일반적인 경고 스키마를 활용 하 여 모든 경고를 처리 하는 논리 앱을 만드는 방법에 대해 알아봅니다.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: ananthradhakrishnan
ms.author: robb
ms.date: 05/27/2019
ms.openlocfilehash: 50a6067d271ad824f17df1ece36c3dd919c7b55b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555656"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Logic Apps와 공통 경고 스키마를 통합 하는 방법

이 문서에서는 일반적인 경고 스키마를 활용 하 여 모든 경고를 처리 하는 논리 앱을 만드는 방법을 보여 줍니다.

## <a name="overview"></a>개요

[일반적인 경고 스키마](https://aka.ms/commonAlertSchemaDocs) 는 다양 한 경고 유형에 대해 표준화 되 고 확장 가능한 JSON 스키마를 제공 합니다. 일반적인 경고 스키마는 웹 후크, runbook 및 논리 앱을 통해 프로그래밍 방식으로 활용할 때 가장 유용 합니다. 이 문서에서는 모든 경고를 처리 하도록 단일 논리 앱을 작성할 수 있는 방법을 보여 줍니다. 다른 프로그래밍 방법에도 동일한 원칙이 적용 될 수 있습니다. 이 문서에서 설명 하는 논리 앱은 [' 필수 ' 필드](alerts-common-schema-definitions.md#essentials)에 대 한 잘 정의 된 변수를 만들고 [경고 유형별](alerts-common-schema-definitions.md#alert-context) 논리를 처리 하는 방법에 대해서도 설명 합니다.


## <a name="prerequisites"></a>전제 조건 

이 문서에서는 독자가 다음에 대해 잘 알고 있다고 가정 합니다. 
* 경고 규칙 설정 ([메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [로그](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [활동 로그](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* [작업 그룹](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 설정
* 작업 그룹 내에서 [공통 경고 스키마](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) 사용

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>공통 경고 스키마를 활용 하는 논리 앱 만들기

1. 설명 된 [단계에 따라 논리 앱을 만듭니다](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  트리거 선택: **HTTP 요청을 받은 경우**.

    ![논리 앱 트리거](media/action-groups-logic-app/logic-app-triggers.png "논리 앱 트리거")

1.  **편집**을 선택하여 HTTP 요청 트리거를 변경합니다.

    ![HTTP 요청 트리거](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP 요청 트리거")


1.  다음 스키마를 복사 하 여 붙여넣습니다.

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. **+** **새 단계**를 선택한 다음, **작업 추가**를 선택합니다.

    ![작업 추가](media/action-groups-logic-app/add-action.png "작업 추가")

1. 이 단계에서는 특정 비즈니스 요구 사항에 따라 다양 한 커넥터 (Microsoft 팀, 여유 시간, Salesforce 등)를 추가할 수 있습니다. ' 필수 필드 '를 기본으로 사용할 수 있습니다. 

    ![필수 필드](media/alerts-common-schema-integrations/logic-app-essential-fields.png "필수 필드")
    
    또는 ' Expression ' 옵션을 사용 하 여 경고 유형을 기반으로 하는 조건부 논리를 작성할 수 있습니다.

    ![논리 앱 식](media/alerts-common-schema-integrations/logic-app-expressions.png "논리 앱 식")
    
     [' MonitoringService ' 필드](alerts-common-schema-definitions.md#alert-context) 를 사용 하 여 조건부 논리를 만들 수 있는 경고 유형을 고유 하 게 식별할 수 있습니다.

    
    예를 들어 아래 코드 조각은 경고가 Application Insights 기반 로그 경고 인지 확인 하 고, 검색 결과를 인쇄 하는지 확인 합니다. ' NA '를 인쇄 합니다.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     [논리 앱 식 작성](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)에 대해 자세히 알아보세요.

    


## <a name="next-steps"></a>다음 단계

* [작업 그룹에 대해 자세히 알아보세요](../../azure-monitor/platform/action-groups.md).
* [일반적인 경고 스키마에 대해 자세히 알아보세요](https://aka.ms/commonAlertSchemaDocs).

