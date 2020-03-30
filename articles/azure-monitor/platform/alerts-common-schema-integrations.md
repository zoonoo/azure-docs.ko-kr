---
title: 일반적인 경고 스키마를 논리 앱과 통합하는 방법
description: 일반적인 경고 스키마를 활용하여 모든 경고를 처리하는 논리 앱을 만드는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668233"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>일반적인 경고 스키마를 논리 앱과 통합하는 방법

이 문서에서는 일반적인 경고 스키마를 활용하여 모든 경고를 처리하는 논리 앱을 만드는 방법을 보여 줍니다.

## <a name="overview"></a>개요

[일반적인 경고 스키마는](https://aka.ms/commonAlertSchemaDocs) 모든 다른 경고 유형에 걸쳐 표준화되고 확장 가능한 JSON 스키마를 제공합니다. 일반적인 경고 스키마는 웹후크, 런북 및 논리 앱을 통해 프로그래밍 방식으로 활용할 때 가장 유용합니다. 이 문서에서는 모든 경고를 처리하기 위해 단일 논리 앱을 작성할 수 있는 방법을 보여 줍니다. 동일한 원칙을 다른 프로그래밍 방식 의 메서드에 적용할 수 있습니다. 이 문서에서 설명하는 논리 앱은 ['필수' 필드에](alerts-common-schema-definitions.md#essentials)대해 잘 정의된 변수를 만들고 [경고 형식](alerts-common-schema-definitions.md#alert-context) 특정 논리를 처리하는 방법에 대해서도 설명합니다.


## <a name="prerequisites"></a>사전 요구 사항 

이 문서에서는 독자가 잘 알고 있다고 가정합니다. 
* 경고 규칙[설정(메트릭,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) [로그,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) [활동 로그)](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)
* 작업 [그룹](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 설정
* 작업 그룹 내에서 [공통 경고 스키마](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) 사용

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>일반적인 경고 스키마를 활용하는 논리 앱 만들기

1. 논리 [앱을 만들려면 설명된 단계에](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app)따라. 

1.  트리거 선택: **HTTP 요청을 받은 경우**.

    ![논리 앱 트리거](media/action-groups-logic-app/logic-app-triggers.png "논리 앱 트리거")

1.  **편집**을 선택하여 HTTP 요청 트리거를 변경합니다.

    ![HTTP 요청 트리거](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP 요청 트리거")


1.  다음 스키마를 복사하여 붙여넣습니다.

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

1. **+** **새 단계를** 선택한 다음 **작업 추가를**선택합니다.

    ![작업 추가](media/action-groups-logic-app/add-action.png "작업 추가")

1. 이 단계에서는 특정 비즈니스 요구 사항에 따라 다양한 커넥터(Microsoft 팀, 슬랙, 세일즈포스 등)를 추가할 수 있습니다. '필수 필드'를 즉시 사용할 수 있습니다. 

    ![필수 필드](media/alerts-common-schema-integrations/logic-app-essential-fields.png "필수 필드")
    
    또는 'Expression' 옵션을 사용하여 경고 유형에 따라 조건부 논리를 작성할 수 있습니다.

    ![논리 앱 표현식](media/alerts-common-schema-integrations/logic-app-expressions.png "논리 앱 표현식")
    
     ['monitoringService' 필드를](alerts-common-schema-definitions.md#alert-context) 사용하면 조건부 논리를 만들 수 있는 경고 유형을 고유하게 식별할 수 있습니다.

    
    예를 들어 아래 코드 조각은 경고가 응용 프로그램 인사이트 기반 로그 경고인지, 검색 결과를 인쇄하는지 확인합니다. 그렇지 않으면 'NA'를 인쇄합니다.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     [논리 앱 표현식 작성에](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)대해 자세히 알아봅니다.

    


## <a name="next-steps"></a>다음 단계

* [작업 그룹에 대해 자세히 알아보세요.](../../azure-monitor/platform/action-groups.md)
* [일반적인 경고 스키마에 대해 자세히 알아봅니다.](https://aka.ms/commonAlertSchemaDocs)

