<properties
	pageTitle="Resource Manager 템플릿을 사용하여 진단 설정 자동 활성화 | Microsoft Azure"
	description="Resource Manager 템플릿을 사용하여 이벤트 허브로 진단 로그 스트림을 활성화하거나 로그를 저장소 계정에 저장하는 진단 설정을 만드는 방법을 알아봅니다."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="johnkem"/>

# Resource Manager 템플릿을 사용하여 리소스 생성 시 진단 설정 자동 활성화
이 문서에서는 [Azure Resource Manager 템플릿](../resource-group-authoring-templates.md)을 사용하여 리소스 생성 시 리소스에서 진단 설정을 구성하는 방법을 보여 줍니다. 그러면 이벤트 허브로 진단 로그 및 메트릭의 스트리밍을 자동으로 시작하고 리소스 생성 시 저장소 계정에 보관할 수 있습니다.

Resource Manager 템플릿을 사용하여 진단 로그를 활성화하는 방법은 리소스 형식에 따라 다릅니다.

- **비-계산** 리소스(예를 들어, 네트워크 보안 그룹, 논리 앱, 자동화)는 [이 문서에 설명된 진단 설정](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)을 사용합니다.
- **계산** 리소스(WAD/LAD 기반)는 [이 문서에 설명된 WAD/LAD 구성 파일](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)을 사용합니다..

이 문서에서는 두 방법 중 하나를 사용하여 진단을 구성하는 방법을 설명합니다.

기본적인 단계는 다음과 같습니다.

1. 리소스를 만들고 진단을 활성화하는 방법을 설명하는 JSON 파일로 템플릿을 만듭니다.
2. [배포 방법을 사용하여 템플릿을 배포합니다](../resource-group-template-deploy.md).

다음은 비-계산 및 계산 리소스에 대해 생성해야 하는 템플릿 JSON 파일의 예입니다.

## 비-계산 리소스 템플릿
비-계산 리소스 템플릿의 경우 두 가지 작업을 수행해야 합니다.

1. 저장소 계정 이름 및 서비스 버스 규칙 ID에 대한 매개 변수 Blob에 매개 변수를 추가합니다(저장소 계정에서 진단 로그 보관 활성화 및/또는 이벤트 허브로 로그 스트리밍).

    ```
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    }
    ```
2. 진단 로그를 활성화할 리소스의 리소스 배열에서 `[resource namespace]/providers/diagnosticSettings` 형식으로 리소스를 추가합니다.

    ```
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

진단 설정에 대한 속성 Blob는 [이 문서에 설명된 형식](https://msdn.microsoft.com/library/azure/dn931931.aspx)을 따릅니다.

다음은 네트워크 보안 그룹을 만들고 이벤트 허브로 스트리밍 및 저장소 계정에 저장을 설정하는 전체 예제입니다.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nsgName": {
            "type": "string",
			"metadata": {
				"description": "Name of the NSG that will be created."
			}
        },
		"storageAccountName": {
			"type": "string",
			"metadata": {
				"description":"Name of the Storage Account in which Diagnostic Logs should be saved."
			}
		},
		"serviceBusRuleId": {
			"type": "string",
			"metadata": {
				"description":"Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
			}
		}
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "apiVersion": "2016-03-30",
            "location": "westus",
            "properties": {
                "securityRules": []
            },
            "resources": [
				{
					"type": "providers/diagnosticSettings",
					"name": "Microsoft.Insights/service",
					"dependsOn": [
						"[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
					],
					"apiVersion": "2015-07-01",
					"properties": {
						"storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
                        "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
						"logs": [
							{
								"category": "NetworkSecurityGroupEvent",
								"enabled": true,
								"retentionPolicy": {
									"days": 0,
									"enabled": false
								}
							},
                            {
								"category": "NetworkSecurityGroupRuleCounter",
								"enabled": true,
								"retentionPolicy": {
									"days": 0,
									"enabled": false
								}
							}
						]
					}
				}
			],
            "dependsOn": []
        }
    ]
}
```

## 계산 리소스 템플릿
계산 리소스(예: 가상 컴퓨터 또는 서비스 패브릭 클러스터)에서 진단을 활성화하려면 다음이 필요합니다.

1. Azure 진단 확장을 VM 리소스 정의에 추가합니다.
2. 매개 변수로 저장소 계정 및/또는 이벤트 허브를 지정합니다.
3. 모든 XML 문자를 올바르게 이스케이프하여 WADCfg XML 파일의 내용을 XMLCfg 속성에 추가합니다.

> [AZURE.WARNING] 이 마지막 단계는 이해하기 어려울 수 있습니다. 진단 구성 스키마를 올바르게 이스케이프 및 형식 지정된 변수로 분할하는 예는 [이 문서를 참조](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables)하세요.

샘플을 포함한 전체 과정은 [이 문서](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)에 설명되어 있습니다.


## 다음 단계
- [Azure 진단 로그에 대해 자세히 알아보기](./monitoring-overview-of-diagnostic-logs.md)
- [이벤트 허브로 Azure 진단 로그 스트림](./monitoring-stream-diagnostic-logs-to-event-hubs.md)

<!---HONumber=AcomDC_0817_2016-->