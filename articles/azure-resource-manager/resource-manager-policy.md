---
title: "Azure 리소스 관리자 정책 | Microsoft Docs"
description: "Azure 리소스 관리자 정책을 사용하여 구독, 리소스 그룹 또는 개별 리소스 등 서로 다른 범위에서 위반을 방지하는 방법을 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: ravbhatnagar
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2016
ms.author: gauravbh;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 223a890fd18405b2d1331e526403da89354a68f2
ms.openlocfilehash: 467e9f4f7372c619f41bb64445784485de18a863


---
# <a name="use-policy-to-manage-resources-and-control-access"></a>정책을 사용하여 리소스 및 컨트롤 액세스 관리
이제 Azure 리소스 관리자를 사용하여 사용자 지정 정책을 통해 액세스를 제어할 수 있습니다. 정책을 사용하면 조직의 사용자가 조직의 리소스를 관리하는 데 필요한 규칙을 위반하지 않도록 방지할 수 있습니다. 

구체적으로 거부되는 작업 또는 리소스를 설명하는 정책 정의를 만듭니다. 구독, 리소스 그룹 또는 개별 리소스와 같이 원하는 범위에서 해당 정책 정의를 할당합니다. 정책은 모든 자식 리소스에 의해 상속됩니다. 이에 따라 리소스 그룹에 정책을 적용하면 해당 리소스 그룹의 모든 리소스에 해당 정책을 적용할 수 있습니다.

이 문서에서는 정책을 만드는 데 사용할 수 있는 정책 정의 언어의 기본 구조를 설명합니다. 그런 다음 이러한 정책을 서로 다른 범위에 적용하는 방법을 설명합니다.

## <a name="how-is-it-different-from-rbac"></a>RBAC(역할 기반 액세스 제어)와 어떻게 다르나요?
정책과 역할 기반 액세스 제어 사이에는 몇 가지 주요 차이가 있지만, 가장 먼저 알아야 할 사항은 정책과 RBAC가 함께 작동한다는 점입니다. 사용자는 RBAC를 통해 인증되어야 정책을 사용할 수 있습니다. RBAC와는 달리 정책은 기본적으로 허용 및 명시적 거부 시스템입니다. 

RBAC는 **사용자**가 서로 다른 범위에서 수행할 수 있는 작업에 중점을 둡니다. 예를 들어 특정 사용자는 원하는 범위에서 리소스 그룹에 대한 참가자 역할에 추가됩니다. 따라서 사용자는 해당 리소스 그룹을 변경할 수 있습니다. 

정책은 다양한 범위의 **리소스** 작업에 중점을 둡니다. 예를 들어 정책을 통해 프로비전할 수 있는 리소스 종류를 제어하거나 리소스를 프로비전할 수 있는 위치를 제한할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오
일반적인 시나리오 중 하나는 비용 청구 용도로 부서별 태그를 요구하는 것입니다. 조직에서는 적절한 비용 센터가 관련된 경우에만 작업을 허용하고, 그렇지 않으면 요청을 거부할 수 있습니다. 이 정책은 수행된 작업에 대해 적절한 비용 센터에 청구하는 데 도움이 됩니다.

또 다른 일반적인 시나리오는 조직이 리소스가 만들어지는 위치를 제어하고자 하는 경우입니다. 또는 특정 유형의 시나리오만 프로비전할 수 있도록 하여 리소스 액세스를 제어하고자 할 수 있습니다.

마찬가지로, 조직은 서비스 카탈로그를 제어하거나 리소스에 대해 원하는 명명 규칙을 적용할 수 있습니다.

정책을 사용하면 이러한 시나리오를 쉽게 달성할 수 있습니다.

## <a name="policy-definition-structure"></a>정책 정의 구조
정책 정의는 JSON을 사용하여 생성됩니다. 이 정의는 작업을 정의하는 하나 이상의 조건/논리 연산자(conditions/logical operator) 및 조건이 충족될 때 발생하는 결과를 알려 주는 효과(effect)로 구성됩니다. 스키마는 [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json)에 게시되어 있습니다. 

다음 예제는 리소스가 배포되는 위치를 제한하기 위해 사용할 수 있는 정책을 보여줍니다.

```json
{
  "properties": {
    "parameters": {
      "listOfAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "An array of permitted locations for resources.",
          "strongType": "location",
          "displayName": "List of locations"
        }
      }
    },
    "displayName": "Geo-compliance policy template",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('listOfAllowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

기본적으로 정책에 포함되는 섹션은 다음과 같습니다.

**매개 변수:** 정책이 할당될 때 지정되는 값입니다.

**condition/logical operator:** 논리 연산자 집합을 통해 조작할 수 있는 조건의 집합입니다.

**effect:** 조건이 만족될 때의 결과, 즉 deny 또는 audit입니다. 감사 효과는 경고 이벤트 서비스 로그를 내보냅니다. 예를 들어 관리자는 누군가가 대용량 VM을 만드는 경우 감사를 수행하게 하는 정책을 만든 다음 나중에 로그를 검토할 수 있습니다.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }

## <a name="policy-evaluation"></a>정책 평가
리소스를 만들 때 정책이 평가됩니다. 템플릿 배포의 경우 템플릿에서 각 리소스를 만드는 중에 정책이 평가됩니다. 

> [!NOTE]
> 현재 정책은 태그, 종류 및 위치를 지원하지 않는 리소스 종류(예: Microsoft.Resources/deployments)를 평가하지 않습니다. 이 지원은 나중에 추가됩니다. 이전 버전과의 호환성 문제를 방지하기 위해 정책을 작성할 때 유형을 명시적으로 지정해야 합니다. 예를 들어 형식을 지정하지 않은 태그 정책이 모든 형식에 적용됩니다. 여기서 태그를 지원하지 않는 중첩된 리소스가 있고 배포 리소스 형식을 정책 평가에 추가한 경우에는 템플릿 배포에 실패할 수 있습니다. 
> 
> 

## <a name="parameters"></a>매개 변수
2016-12-01 버전 API에서는 정책 정의에서 매개 변수를 사용할 수 있습니다. 매개 변수의 사용은 정책 정의의 수를 줄여 정책 관리를 간소화하는 데 도움이 됩니다. 정책을 할당할 때 매개 변수에 값을 제공합니다.

정책 정의 만들 때 매개 변수를 선언합니다.

    "parameters": {
      "listOfLocations": {
        "type": "array",
        "metadata": {
          "description": "An array of permitted locations for resources.",
          "displayName": "List Of Locations"
        }
      }
    }

매개 변수의 형식은 문자열 또는 배열이 될 수 있습니다. 메타데이터 속성은 Azure 포털과 같은 도구에 사용되어 사용자 친화적 정보를 표시합니다. 

정책 규칙에서는 템플릿에 수행할 작업과 유사한 매개 변수를 참조할 수 있습니다. 예: 
        
    { 
        "field" : "location",
        "in" : "[parameters(listOfLocations)]"
    }

## <a name="logical-operators"></a>논리 연산자
지원되는 논리 연산자와 구문은 다음과 같습니다.

| 연산자 이름 | 구문 |
|:--- |:--- |
| not |"not" : {&lt;condition  or operator &gt;} |
| and |"allOf" : [ {&lt;condition  or operator &gt;},{&lt;condition  or operator &gt;}] |
| or |"anyOf" : [ {&lt;condition  or operator &gt;},{&lt;condition  or operator &gt;}] |

리소스 관리자를 사용하면 중첩된 연산자를 통해 정책에서 복잡한 논리를 지정할 수 있습니다. 예를 들어 지정된 리소스 종류에 대한 특정 위치에서 리소스 만들기를 거부할 수 있습니다. 이 항목에 중첩된 연산자의 예제가 나와 있습니다.

## <a name="conditions"></a>조건
조건은 **field** 또는 **source**에서 특정 기준을 충족하는지를 평가합니다. 지원되는 조건의 이름과 구문은 다음과 같습니다.

| 조건 이름 | 구문 |
|:--- |:--- |
| equals |"equals" : "&lt;value&gt;" |
| like |"like" : "&lt;value&gt;" |
| contains |"contains" : "&lt;value&gt;" |
| 내용 |"in" : [ "&lt;value1&gt;","&lt;value2&gt;" ] |
| ContainsKey |"containsKey" : "&lt;keyName&gt;" |
| exists |"exists" : "&lt;bool&gt;" |

### <a name="fields"></a>필드
조건은 필드와 소스를 사용하여 구성됩니다. 필드는 리소스의 상태를 설명하는 데 사용되는 리소스 요청 페이로드의 속성을 나타냅니다. 원본은 요청 자체의 특성을 나타냅니다. 

다음과 같은 필드와 소스가 지원됩니다.

필드: **name**, **kind**, **type**, **location**, **tags**, **tags.*** 및 **property alias**. 

### <a name="property-aliases"></a>속성 별칭
속성 별칭은 설정 및 sku와 같은 리소스 종류 특정 속성에 액세스하는 정책 정의에 사용될 수 있는 이름입니다. 속성이 존재하는 모든 API 버전에서 작동합니다. REST API를 사용하여 별칭을 검색할 수 있습니다(Powershell 지원은 향후 추가될 예정).

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01

다음 예제는 별칭의 정의를 보여 줍니다. 여기에서 볼 수 있듯이 별칭은 속성 이름을 변경하는 경우에도 서로 다른 API 버전에 경로를 정의합니다. 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

현재 지원되는 별칭:

| 별칭 이름 | 설명 |
| --- | --- |
| {resourceType}/sku.name |지원되는 리소스 유형: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family |지원되는 리소스 종류: Microsoft.Cache/Redis |
| {resourceType}/sku.capacity |지원되는 리소스 종류: Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |


## <a name="effect"></a>결과
정책은 세 가지 유형의 효과, 즉 **deny**, **audit** 및 **append**를 지원합니다. 

* 거부는 감사 로그에 이벤트를 생성하고 요청을 실패합니다.
* 감사는 감사 로그에 이벤트를 생성하지만 요청을 실패하지는 않습니다.
* 추가는 정의된 필드 집합을 요청에 추가합니다. 

**append**의 경우 아래와 같이 details(세부 정보)를 제공해야 합니다.

    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

값은 문자열 또는 JSON 형식의 개체일 수 있습니다. 


## <a name="policy-definition-examples"></a>정책 정의 예제
이제 앞에서 나온 시나리오를 달성하기 위해 정책을 정의하는 방법을 살펴보겠습니다.

### <a name="chargeback-require-departmental-tags"></a>차지백: 부서 태그 필요
아래 정책은 "costCenter" 키를 포함하고 있는 태그가 없는 요청을 거부합니다.

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

태그가 없는 경우 아래 정책은 미리 정의된 값을 포함한 costCenter 태그를 추가합니다. 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }

아래 정책은 costCenter 태그가 없지만 다른 태그가 있는 경우 미리 정의된 값을 포함한costCenter 태그를 추가합니다. 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]

      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>지리적 준수: 리소스 위치 확인
아래 예제에서는 유럽 북부 또는 유럽 서부 위치가 아닌 요청을 거부하는 정책을 보여 줍니다.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>서비스 큐레이션: 서비스 카탈로그 선택
아래 예제에서는 Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\*, Microsoft.Network/\* 형식의 서비스에 대한 작업만 허용하는 정책을 보여 줍니다. 그 외 다른 작업은 거부됩니다.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>승인된 SKU 사용
아래 예제에서는 SKU를 제한하는 속성 별칭의 사용을 보여 줍니다. 이 예제에서 Standard_LRS 및 Standard_GRS만 저장소 계정에 대해 승인됩니다.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }


### <a name="naming-convention"></a>명명 규칙
아래 예제에서는 "like" 조건으로 지원되는 와일드 카드의 사용을 보여 줍니다. 이 조건은 앞에서 언급된 패턴(namePrefix\*nameSuffix)과 일치하는 이름이면 요청을 거부한다는 것을 나타냅니다.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="tag-requirement-just-for-storage-resources"></a>저장소 리소스 전용 태그 요구 사항
아래 예제에서는 저장소 리소스에만 응용 프로그램 태그를 요구하도록 논리 연산자를 중첩하는 방법을 보여 줍니다.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="create-and-assign-a-policy"></a>정책 만들기 및 할당
정책을 적용하려면 정책 정의를 만든 후 범위에 적용해야 합니다. 

### <a name="rest-api"></a>REST API
[정책 정의에 대한 REST API](https://docs.microsoft.com/rest/api/resources/policydefinitions)를 사용하여 정책을 만들 수 있습니다. REST API를 사용하여 정책 정의를 만들고, 삭제하고, 기존 정의에 관한 정보를 가져올 수 있습니다.

정책을 만들려면 다음과 같이 실행합니다.

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

api-version에는 *2016-04-01* 또는 *2016-12-01*을 사용합니다. 다음 예제와 비슷한 요청 본문을 포함합니다.

    {
      "properties": {
        "parameters": {
          "listOfAllowedLocations": {
            "type": "array",
            "metadata": {
              "description": "An array of permitted locations for resources.",
              "strongType": "location",
              "displayName": "List Of Locations"
            }
          }
        },
        "displayName": "Geo-compliance policy template",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
        "policyRule": {
          "if": {
            "not": {
              "field": "location",
              "in": "[parameters('listOfAllowedLocations')]"
            }
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }

[정책 할당에 대한 REST API](https://docs.microsoft.com/rest/api/resources/policyassignments)를 통해 원하는 범위에 정책 정의를 적용할 수 있습니다. REST API를 사용하여 정책 할당을 만들고, 삭제하고, 기존 할당에 관한 정보를 가져올 수 있습니다.

정책 할당을 만들려면 다음과 같이 실행합니다.

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment}는 정책 할당의 이름입니다. api-version에는 *2016-04-01* 또는 *2016-12-01* (매개 변수에 대해). 

요청 본문은 아래 예제와 비슷합니다.

    {
      "properties":{
        "displayName":"West US only policy assignment on the subscription ",
        "description":"Resources can only be provisioned in West US regions",
        "parameters": {
             "listOfAllowedLocations": ["West US", "West US2"]
         },
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
    }

### <a name="powershell"></a>PowerShell
New-AzureRmPolicyDefinition cmdlet을 사용하면 정책 정의를 만들 수 있습니다. 아래 예제에서는 유럽 북부와 유럽 서부에서만 리소스를 허용하는 정책을 만듭니다.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'            

실행의 출력은 $policy 개체에 저장되어 나중에 정책 할당 시에 사용할 수 있습니다. 정책 매개 변수의 경우 정책 인라인을 지정하지 않는 대신 정책을 포함하고 있는 .json 파일에 대한 경로가 제공될 수도 있습니다.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain     regions" -Policy "path-to-policy-json-on-disk"

New-AzureRmPolicyAssignment cmdlet을 사용하면 정책을 원하는 범위에 적용할 수 있습니다.

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

여기서 $policy는 New-AzureRmPolicyDefinition cmdlet을 실행한 결과로 반환된 정책 개체입니다. 여기서 범위는 지정하는 리소스 그룹의 이름입니다.

정책 할당을 제거하려면 다음을 사용합니다.

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition 및 Remove-AzureRmPolicyDefinition cmdlet을 통해 각각 정책 정의를 가져오거나 변경하거나 제거할 수 있습니다.

마찬가지로 Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment 및 Remove-AzureRmPolicyAssignment cmdlet을 통해 각각 정책 할당을 가져오거나 변경하거나 제거할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI
정책 정의 명령과 함께 Azure CLI를 사용하여 정책 정의를 만들 수 있습니다. 아래 예제에서는 유럽 북부와 유럽 서부에서만 리소스를 허용하는 정책을 만듭니다.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    


정책 인라인을 지정하지 않고 정책이 포함된 .json 파일에 대한 경로를 지정할 수 있습니다.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"

정책 할당 명령을 사용하여 정책을 원하는 범위에 적용할 수 있습니다.

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

여기서 범위는 지정하는 리소스 그룹의 이름입니다. 매개 변수 정책 정의 ID 값을 알 수 없는 경우 Azure CLI를 통해 얻을 수 있습니다. 

    azure policy definition show <policy-name>

정책 할당을 제거하려면 다음을 사용합니다.

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

정책 정의 표시, 설정 및 삭제 명령을 통해 각각 정책 정의를 가져오거나 변경하거나 제거할 수 있습니다.

마찬가지로 정책 할당 표시 및 삭제 명령 각각을 통해 정책 할당을 가져오거나 변경하거나 제거할 수 있습니다.

## <a name="policy-audit-events"></a>정책 감사 이벤트
정책을 적용한 후 정책 관련 이벤트를 보려면 시작합니다. 포털로 이동하거나 PowerShell 또는 Azure CLI를 사용하여 이 데이터를 가져올 수 있습니다. 

### <a name="powershell"></a>PowerShell
거부 효과와 관련된 모든 이벤트를 보기 위해 사용할 수 있는 PowerShell 명령은 다음과 같습니다.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

감사 효과와 관련된 모든 이벤트를 보기 위해 사용할 수 있는 명령은 다음과 같습니다.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="azure-cli"></a>Azure CLI
거부 효과와 관련된 리소스 그룹의 모든 이벤트를 보기 위해 사용할 수 있는 CLI 명령은 다음과 같습니다.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

감사 효과와 관련된 모든 이벤트를 보기 위해 사용할 수 있는 CLI 명령은 다음과 같습니다

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"

## <a name="view-a-policy"></a>정책 보기
PowerShell, Azure CLI 또는 REST API를 사용하여 정책을 봅니다. 배포가 실패한 후 정책을 봐야 하거나 배포가 거부된 규칙을 보고 싶을 수 있습니다. 오류 메시지에는 정책 정의 ID가 포함됩니다.

### <a name="powershell"></a>PowerShell
정책을 가져오려면 다음 cmdlet을 사용합니다.

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json

정책 정의에 대한 JSON이 반환됩니다.

### <a name="azure-cli"></a>Azure CLI
정책을 가져오려면 다음 명령을 사용합니다.

    azure policy definition show {definition-name} --json

### <a name="rest-api"></a>REST API
정책을 가져오려면 [정책 정의 가져오기](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get) 작업을 사용합니다.

## <a name="next-steps"></a>다음 단계
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.




<!--HONumber=Dec16_HO2-->


