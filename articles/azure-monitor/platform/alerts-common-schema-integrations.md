---
title: Logic Apps를 사용 하 여 일반적인 경고 스키마를 통합 하는 방법
description: 모든 경고를 처리 하도록 경고 공통 스키마를 활용 하는 논리 앱을 만드는 방법에 알아봅니다.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734885"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Logic Apps를 사용 하 여 일반적인 경고 스키마를 통합 하는 방법

이 문서에서는 일반적인 경고 스키마 모든 경고 처리를 활용 하는 논리 앱을 만드는 방법을 보여 줍니다.

## <a name="overview"></a>개요

합니다 [일반적인 경고 스키마](https://aka.ms/commonAlertSchemaDocs) 표준화 되 고 확장할 수 있는 JSON 스키마를 다른 모든 경고 유형을 제공 합니다. 일반 경고 스키마 웹 후크, runbook 및 logic apps를 통해 프로그래밍 방식으로 – 활용할 때 가장 유용 합니다. 이 문서에서는 모든 경고를 처리 하도록 단일 논리 앱을 작성할 수 있습니다 하는 방법을 보여 줍니다. 프로그래밍 방식으로 다른 메서드에 동일한 원칙을 적용할 수 있습니다. 이 문서에 설명 된 논리 앱에 대 한 잘 정의 된 변수를 만듭니다는 ['필수' 필드](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields), 및 처리 하는 방법을 설명 합니다 [경고 유형](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) 특정 논리입니다.


## <a name="prerequisites"></a>필수 조건 

이 문서에서는 판독기에 잘 알고 있다고 가정 
* 경고 규칙을 설정 ([메트릭을](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)를 [로그](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)하십시오 [활동 로그](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* 설정 [작업 그룹](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* 사용 하도록 설정 합니다 [일반적인 경고 스키마](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) 에서 작업 그룹 내에서

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>일반 경고 스키마를 활용 하 여 논리 앱 만들기

1. 수행 합니다 [논리 앱 만들기에 설명 된 단계를](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app)입니다. 

1.  트리거 선택: **HTTP 요청을 수신하는 경우**.

    ![논리 앱 트리거](media/action-groups-logic-app/logic-app-triggers.png "논리 앱 트리거")

1.  **편집**을 선택하여 HTTP 요청 트리거를 변경합니다.

    ![HTTP 요청 트리거](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP 요청 트리거")


1.  복사한 다음 스키마를 붙여넣습니다.

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

1. 이 단계에서는 다양 한 커넥터 (Microsoft Teams, Slack, Salesforce 등) 특정 비즈니스 요구 사항에 따라 추가할 수 있습니다. '필수 필드'-의-기본을 사용할 수 있습니다. 

    ![필수 필드](media/alerts-common-schema-integrations/logic-app-essential-fields.png "필수 필드")
    
    또는 'Expression' 옵션을 사용 하 여 경고 유형을 기반으로 하는 조건부 논리를 작성할 수 있습니다.

    ![논리 앱 식을](media/alerts-common-schema-integrations/logic-app-expressions.png "논리 앱 식")
    
     ['monitoringService' 필드](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) 기반 경고 유형을 고유 하 게 식별할 수 있도록에서 조건부 논리를 만들 수는 있습니다.

    
    예를 들어는 아래 코드 조각 인지 확인 하는 경우 경고 Application Insights 기반 로그 경고를 검색 결과 인쇄 하는 경우. 그렇지 않은 경우 'NA'를 인쇄합니다.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     에 대해 자세히 알아보세요 [논리 앱 작성할](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)합니다.

    


## <a name="next-steps"></a>다음 단계

* [작업 그룹에 자세히 알아보려면](../../azure-monitor/platform/action-groups.md)합니다.
* [일반 경고 스키마에 자세히 알아보려면](https://aka.ms/commonAlertSchemaDocs)합니다.

