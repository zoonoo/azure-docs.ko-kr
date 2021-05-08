---
title: Azure CLI 스크립트 샘플 - 논리 앱 만들기
description: Azure CLI에서 Logic Apps 확장을 통해 논리 앱을 만드는 샘플 스크립트입니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: 8086ce0e5964d4e37a5ffc3082d5f2856058e4e5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875488"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLI 스크립트 샘플 - 논리 앱 만들기

이 스크립트는 [Azure CLI Logic Apps 확장](/cli/azure/logic)(`az logic`)을 통해 샘플 논리 앱을 만듭니다. Azure CLI를 통해 논리 앱을 만들고 관리하는 방법에 대한 자세한 가이드는 [Azure CLI의 Logic Apps 빠른 시작](quickstart-logic-apps-azure-cli.md)을 참조하세요.

> [!WARNING]
> Azure CLI Logic Apps 확장은 현재 *실험 단계* 이며 *고객 지원에서 다루지 않습니다*. 특히 프로덕션 환경에서 이 확장을 사용하도록 선택하는 경우 이 CLI 확장을 주의해서 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 로컬 컴퓨터에 설치된 [Azure CLI](/cli/azure/install-azure-cli)
* 컴퓨터에 설치된 [Logic Apps Azure CLI 확장](/cli/azure/azure-cli-extensions-list). 이 확장을 설치하려면 `az extension add --name logic` 명령을 사용합니다.
* 논리 앱의 [워크플로 정의](quickstart-logic-apps-azure-cli.md#workflow-definition). 이 JSON 파일은 [워크플로 정의 언어 스키마](logic-apps-workflow-definition-language.md)를 따라야 합니다.
* 논리 앱과 동일한 리소스 그룹에서 지원되는 [Logic Apps 커넥터](../connectors/apis-list.md)를 통해 메일 계정에 API 연결. 이 예제에서는 [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) 커넥터를 사용하지만 [Outlook.com](../connectors/connectors-create-api-outlook.md)과 같은 다른 커넥터를 사용할 수도 있습니다.

### <a name="prerequisite-check"></a>필수 구성 요소 확인

시작하기 전에 현재 환경의 유효성을 검사합니다.

* Azure Portal에 로그인하고 `az login`을 실행하여 구독이 활성 상태인지 확인합니다.

* 터미널 또는 명령 창에서 `az --version`을 실행하여 Azure CLI 버전을 확인합니다. 최신 버전은 [최신 릴리스 정보](/cli/azure/release-notes-azure-cli)를 참조하세요.

  * 최신 버전이 없는 경우 [사용 중인 운영 체제 또는 플랫폼의 설치 가이드](/cli/azure/install-azure-cli)에 따라 설치를 업데이트합니다.

### <a name="sample-workflow-explanation"></a>샘플 워크플로 설명

이 예제 워크플로 정의 파일은 [Azure Portal의 Logic Apps 빠른 시작](quickstart-create-first-logic-app-workflow.md)과 동일한 기본 논리 앱을 만듭니다. 

이 샘플 워크플로는 다음과 같습니다. 

1. 논리 앱의 `$schema` 스키마를 지정합니다.

1. 트리거 목록 `triggers`에서 논리 앱의 트리거를 정의합니다. 트리거는 3시간마다 되풀이됩니다(`recurrence`). 지정된 RSS 피드(`feedUrl`)의 새 피드 항목이 게시될 때(`When_a_feed_item_is_published`) 작업이 트리거됩니다.

1. 작업 목록 `actions`에서 논리 앱의 작업을 정의합니다. 작업은 작업 입력(`inputs`)의 본문 섹션(`body`)에 지정된 대로 Microsoft 365를 통해 RSS 피드 항목의 세부 정보가 포함된 메일을 보냅니다(`Send_an_email_(V2)`).

## <a name="sample-workflow-definition"></a>샘플 워크플로 정의

샘플 스크립트를 실행하려면 먼저 샘플 [워크플로 정의](#prerequisites)를 만들어야 합니다.

1. 컴퓨터에서 JSON 파일 `testDefinition.json`을 만듭니다. 

1. JSON 파일에 다음 내용을 복사합니다. 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. 자리 표시자 값은 자신의 정보로 업데이트합니다.

    1. 자리 표시자 메일 주소(`"To": "test@example.com"`)를 바꿉니다. Logic Apps 커넥터와 호환되는 메일 주소를 사용해야 합니다. 자세한 내용은 [필수 조건](#prerequisites)을 참조하세요.

    1. Office 365 Outlook 커넥터가 아닌 다른 메일 커넥터를 사용하는 경우 추가 커넥터 정보를 바꿉니다.

    1. 연결 매개 변수(`$connections`) 아래에서 연결 ID(`connectionId` 및 `id`)의 자리 표시자 구독 값(`00000000-0000-0000-0000-000000000000`)을 자신의 구독 값으로 바꿉니다.

1. 변경 내용을 저장합니다.

## <a name="sample-script"></a>샘플 스크립트

> [!NOTE]
> 이 샘플은 `bash` 셸용으로 작성되었습니다. Windows PowerShell, 명령 프롬프트 등과 같은 다른 셸에서 이 샘플을 실행하려면 스크립트를 수정해야 할 수 있습니다.

이 샘플 스크립트를 실행하기 전에 다음 명령을 실행하여 Azure에 연결합니다.

```azurecli-interactive

az login

```

그런 다음 워크플로 정의를 만든 디렉터리로 이동합니다. 예를 들어 워크플로 정의 JSON 파일을 바탕 화면에 만든 경우 다음을 실행합니다.

```azurecli

cd ~/Desktop

```

그런 다음 이 스크립트를 실행하여 논리 앱을 만듭니다. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트 사용을 마쳤으면 다음 명령을 실행하여 논리 앱을 포함하여 리소스 그룹과 모든 중첩 리소스를 제거합니다.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>스크립트 설명

이 샘플 스크립트는 다음 명령을 사용하여 새 리소스 그룹과 논리 앱을 만듭니다.

| 명령 | 메모 |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az_group_create) | 논리 앱의 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) | `--definition` 매개 변수에 정의된 워크플로를 기반으로 논리 앱을 만듭니다. |
| [`az group delete`](/cli/azure/vm/extension) | 리소스 그룹과 모든 중첩 리소스를 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure/)를 참조하세요.

[Microsoft 코드 샘플 브라우저](/samples/browse/?products=azure-logic-apps)에서 더 많은 Logic Apps CLI 스크립트 샘플을 찾을 수 있습니다.
