---
title: 자습서 - 리소스 온보딩
description: 사용자 지정 공급자를 통해 리소스를 온보딩하면 기존 Azure 리소스를 조작하고 확장할 수 있습니다.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75648610"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>자습서: Azure 사용자 지정 공급자를 사용하여 리소스 온보딩

이 자습서에서는 Microsoft.CustomProviders/associations 리소스 종류를 사용하여 Azure Resource Manager API를 확장하는 사용자 지정 리소스 공급자를 Azure에 배포합니다. 이 자습서에서는 사용자 지정 공급자 인스턴스가 위치한 리소스 그룹 외부에 있는 기존 리소스를 확장하는 방법을 보여줍니다. 이 자습서에서 사용자 지정 리소스 공급자는 Azure 논리 앱을 기반으로 하지만, 아무 공용 API 엔드포인트를 사용해도 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 사항을 알고 있어야 합니다.

* [Azure 사용자 지정 공급자](overview.md) 기능
* [사용자 지정 공급자를 통한 리소스 온보딩](concepts-resource-onboarding.md)에 대한 기본 정보

## <a name="get-started-with-resource-onboarding"></a>리소스 온보딩 시작

이 자습서에서는 사용자 지정 공급자와 연결이라는 두 가지를 배포해야 합니다. 원한다면 프로세스를 단순화하기 위해 두 가지를 모두 배포하는 단일 템플릿을 사용해도 됩니다.

이 템플릿은 다음 리소스를 사용합니다.

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/workflows
* Microsoft.CustomProviders/associations

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>사용자 지정 공급자 인프라 배포

템플릿의 첫 번째 부분에서는 사용자 지정 공급자 인프라를 배포합니다. 이 인프라는 연결 리소스의 효과를 정의합니다. 사용자 지정 공급자에 대해 잘 모르는 경우 [사용자 지정 공급자 기본 사항](overview.md)을 참조하세요.

사용자 지정 공급자 인프라를 배포하겠습니다. 위의 템플릿을 복사하고, 저장하고, 배포하거나 Azure Portal을 사용하여 자습서에 따라 인프라를 배포합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.

2. **모든 서비스**에서 또는 주 검색 상자를 사용하여 **템플릿**을 검색합니다.

   ![템플릿 검색](media/tutorial-resource-onboarding/templates.png)

3. 다음과 같이 **템플릿** 창에서 **추가**를 선택합니다.

   ![추가 선택](media/tutorial-resource-onboarding/templatesadd.png)

4. **일반** 아래에서 새 템플릿의 **이름** 및 **설명**을 입력합니다.

   ![템플릿 이름 및 설명](media/tutorial-resource-onboarding/templatesdescription.png)

5. 이 문서의 "리소스 온보딩 시작" 섹션에서 JSON 템플릿을 복사하여 Resource Manager 템플릿을 만듭니다.

   ![Resource Manager 템플릿 만들기](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. **추가**를 선택하여 템플릿을 만듭니다. 새 템플릿이 표시되지 않으면 **새로 고침**을 선택합니다.

7. 새로 만든 템플릿을 선택하고 **배포**를 선택합니다.

   ![새 템플릿을 선택하고 [배포]를 선택합니다.](media/tutorial-resource-onboarding/templateselectspecific.png)

8. 필수 필드의 설정을 입력하고 구독 및 리소스 그룹을 선택합니다. **사용자 지정 리소스 공급자 Id** 상자는 비워 놓아도 됩니다.

   | 설정 이름 | 필수 여부 | Description |
   | ------------ | -------- | ----------- |
   | 위치 | 예 | 템플릿에서 리소스의 위치입니다. |
   | 논리 앱 이름 | 예 | 논리 앱의 이름입니다. |
   | 사용자 지정 리소스 공급자 이름 | 예 | 사용자 지정 리소스 공급자 이름입니다. |
   | 사용자 지정 리소스 공급자 Id | 예 | 연결 리소스를 지원하는 기존 사용자 지정 리소스 공급자입니다. 여기서 값을 지정하는 경우 논리 앱 및 사용자 지정 공급자 배포를 건너뛰게 됩니다. |
   | 연결 이름 | 예 | 연결 리소스의 이름입니다. |

   샘플 매개 변수:

   ![템플릿 매개 변수 입력](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. 배포로 이동하여 배포가 끝나기를 기다립니다. 다음 스크린샷과 유사한 내용이 보일 것입니다. 새 연결 리소스가 출력으로 표시됩니다.

   ![배포 정상 완료](media/tutorial-resource-onboarding/customproviderdeployment.png)

   다음은 **숨겨진 형식 표시**가 선택된 리소스 그룹입니다.

   ![사용자 지정 공급자 배포](media/tutorial-resource-onboarding/showhidden.png)

10. 논리 앱 **실행 기록** 탭을 검색하여 연결 만들기 호출을 확인합니다.

    ![논리 앱 실행 기록](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>추가 연결 배포

사용자 지정 공급자 인프라를 설정한 후에는 더 많은 연결을 쉽게 배포할 수 있습니다. 추가 연결을 위한 리소스 그룹은 사용자 지정 공급자 인프라를 배포한 리소스 그룹과 같을 필요가 없습니다. 연결을 만들려면 지정된 사용자 지정 리소스 공급자 ID에 대한 Microsoft.CustomProviders/resourceproviders/write 권한이 있어야 합니다.

1. 이전 배포의 리소스 그룹에서 사용자 지정 공급자 **Microsoft.CustomProviders/resourceProviders** 리소스로 이동합니다. **숨겨진 형식 표시** 확인란을 선택해야 합니다.

   ![리소스로 이동](media/tutorial-resource-onboarding/showhidden.png)

2. 사용자 지정 공급자의 리소스 ID 속성을 복사합니다.

3. **모든 서비스**에서 또는 주 검색 상자를 사용하여 **템플릿**을 검색합니다.

   ![템플릿 검색](media/tutorial-resource-onboarding/templates.png)

4. 다음과 같이 이전에 만든 템플릿을 선택하고 **배포**를 선택합니다.

   ![이전에 만든 템플릿을 선택하고 [배포] 선택](media/tutorial-resource-onboarding/templateselectspecific.png)

5. 필수 필드의 설정을 입력한 다음, 구독 및 다른 리소스 그룹을 선택합니다. **사용자 지정 리소스 공급자 Id** 설정의 경우 이전에 배포한 사용자 지정 공급자에서 복사한 리소스 ID를 입력합니다.

6. 배포로 이동하여 배포가 끝나기를 기다립니다. 이제 다음과 같이 새 연결 리소스만 배포됩니다.

   ![새 연결 리소스](media/tutorial-resource-onboarding/createdassociationresource.png)

원한다면 논리 앱 **실행 기록**으로 돌아가서 논리 앱에 대한 다른 호출이 수행되었는지 확인할 수 있습니다. 만든 각 연결에 대한 추가 기능을 보강하도록 논리 앱을 업데이트할 수 있습니다.

## <a name="getting-help"></a>도움말 보기

Azure 사용자 지정 공급자에 대한 질문이 있는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)에서 문의하세요. 이미 비슷한 질문과 그에 대한 답변이 게시되었을 수도 있으므로, 먼저 확인한 후 게시하세요. 빠른 응답을 얻으려면 `azure-custom-providers` 태그를 추가하세요.

