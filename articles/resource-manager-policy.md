<properties
	pageTitle="Azure 리소스 관리자 정책 | Microsoft Azure"
	description="Azure 리소스 관리자 정책을 사용하여 구독, 리소스 그룹 또는 개별 리소스 등 서로 다른 범위에서 위반을 방지하는 방법을 설명합니다."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="10/06/2015"
	ms.author="gauravbh;tomfitz"/>

# 정책을 사용하여 리소스 및 컨트롤 액세스 관리

이제 Azure 리소스 관리자를 사용하여 사용자 지정 정책을 통해 액세스를 제어할 수 있습니다. 정책은 원하는 범위에서 방지할 수 있는 하나 이상의 위반을 나타냅니다. 이 경우에 범위는 구독, 리소스 그룹 또는 개별 리소스일 수 있습니다.

정책은 시스템을 허용하는 기본값입니다. 정책은 정책 정의를 통해 정의되며 정책 할당을 통해 적용됩니다. 정책 할당을 사용하여 정책이 적용될 수 있는 범위를 제어할 수 있습니다.

이 문서에서는 정책을 만드는 데 사용할 수 있는 정책 정의 언어의 기본 구조를 설명합니다. 그런 다음 이 정책을 서로 다른 범위에서 적용하는 방법을 설명하고 REST API를 통해 이를 달성할 수 있는 방법에 관한 몇 가지 예제를 보여줍니다. PowerShell 지원도 곧 추가됩니다.

## 일반적인 시나리오

일반적인 시나리오 중 하나는 비용 청구 용도로 부서별 태그를 요구하는 것입니다. 조직은 적절한 비용 센터가 관련된 경우에만 작업을 허용하고 그렇지 않으면 요청을 거부하는 것이 좋습니다. 이는 수행된 작업에 적절한 비용 센터를 청구하는 데 도움이 될 수 있습니다.

또 다른 일반적인 시나리오는 조직이 리소스가 만들어지는 위치를 제어하고자 하는 경우입니다. 또는 특정 유형의 시나리오만 프로비전할 수 있도록 하여 리소스 액세스를 제어하고자 할 수 있습니다.

마찬가지로, 조직은 서비스 카탈로그를 제어하거나 리소스에 대해 원하는 명명 규칙을 적용할 수 있습니다.

정책을 사용하면 아래에 설명하는 것처럼 이러한 시나리오를 쉽게 달성할 수 있습니다.

## 정책 정의 구조

정책 정의 JSON는 JSON을 사용하여 생성됩니다. 이는 작업을 정의하는 하나 이상의 조건/논리 연산자 및 조건이 충족될 때 일어나는 일을 알려 주는 효과로 구성됩니다.

기본적으로 정책은 다음을 포함합니다.

**조건/논리 연산자:** 논리 연산자 집합을 통해 조작할 수 있는 조건의 집합을 포함하고 있습니다.

**효과:** 조건이 만족될 때의 효과 - 거부 또는 감사를 설명합니다. 감사 효과는 경고 이벤트 서비스 로그를 내보냅니다. 예를 들어 관리자는 누군가가 큰 VM을 만들 경우 감사를 수행하게 하는 정책을 만든 다음 나중에 로그를 검토할 수 있습니다.

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## 논리 연산자

지원되는 논리 연산자와 구문은 다음과 같습니다.

| 연산자 이름 | 구문 |
| :------------- | :------------- |
| Not | "not" : {&lt;condition&gt;} |
| 및 | "allOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |
| 또는 | "anyOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |


## 조건

지원되는 조건과 구문은 다음과 같습니다.

| 조건 이름 | 구문 |
| :------------- | :------------- |
| 같음 | "equals" : "&lt;value&gt;" |
| 유사함 | "like" : "&lt;value&gt;" |
| 포함 | "contains" : "&lt;value&gt;"|
| 내용 | "in" : [ "&lt;value1&gt;","&lt;value2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;keyName&gt;" |


## 필드 및 소스

조건은 필드와 소스를 사용하여 형성됩니다. 다음과 같은 필드와 소스가 지원됩니다.

필드: **name**, **kind**, **type**, **location**, **tags**, **tags.***.

소스: **action**

## 정책 정의 예제

이제 정책을 사용하여 위에 열거한 시나리오를 달성하는 방법을 살펴보겠습니다.

### 차지백: 부서 태그 필요

아래 정책은 "costCenter" 키를 포함하고 있는 태그가 없는 모든 요청을 거부합니다.

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


### 지리적 준수: 리소스 위치 확인

아래 예제에서는 위치가 북유럽 또는 서유럽이 아닌 모든 요청을 거부하는 정책을 보여줍니다.

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

### 서비스 큐레이션: 서비스 카탈로그 선택

아래 예제는 소스 사용을 보여줍니다. 이는 Microsoft.Resources/*, Microsoft.Compute/*, Microsoft.Storage/*, Microsoft.Network/* 유형의 서비스에 대한 작업만 허용된다는 것을 나타냅니다. 그외 모든 것은 거부됩니다.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### 명명 규칙

아래 예제는 "like" 같은 조건에 의해 지원되는 와일드 카드 사용을 보여줍니다. 이 조건은 이름이 앞에 말한 패턴(namePrefix * nameSuffix)과 일치하면 요청을 거부한다는 것을 나타냅니다.

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

## 정책 할당

정책을 구독, 리소스 그룹 및 개별 리소스 등 서로 다른 범위에서 적용할 수 있습니다. 정책은 모든 자식 리소스에 의해 상속됩니다. 따라서 리소스 그룹에 정책을 적용하면 해당 리소스 그룹의 모든 리소스에 정책을 적용할 수 있습니다.

## 정책 만들기

이 섹션에서는 REST API를 사용하여 정책을 만들 수 있는 방법에 대한 세부 정보를 제공합니다.

### REST API를 사용하여 정책 정의 만들기

[정책 정의에 대한 REST API](https://msdn.microsoft.com/library/azure/mt588471.aspx)를 사용하여 정책을 만들 수 있습니다. REST API를 사용하여 정책 정의를 만들고, 삭제하고, 기존 정의에 관한 정보를 가져올 수 있습니다.

새 정책을 만들려면 다음을 실행합니다.

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

요청 본문이 다음과 유사한 경우:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


정책 정의를 위에 나오는 예제 중 하나로 정의할 수 있습니다. api-version에는 *2015-10-01-preview*를 사용합니다. 예제 및 더 자세한 세부 정보는 [정책 정의에 대한 REST API](https://msdn.microsoft.com/library/azure/mt588471.aspx)를 참조하세요.

### PowerShell을 사용하여 정책 정의 만들기

아래와 같이 New-AzureRmPolicyDefinition cmdlet을 사용하여 새 정책 정의를 만들 수 있습니다. 아래 예제는 북유럽과 서유럽에서만 리소스를 허용하기 위한 정책을 만듭니다.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation onlyin certain regions" -Policy '{	"if" : {
    	    			    "not" : {
    	      			    	"field" : "location",
    	      			    		"in" : ["northeurope" , "westeurope"]
    	    			    	}
    	    		          },
    	      		    		"then" : {
    	    			    		"effect" : "deny"
    	      			    		}
    	    		    	}'    		

실행의 출력은 $policy 개체에 저장되어 나중에 정책 할당 중에 사용할 수 있습니다. 정책 매개 변수의 경우 아래 나와 있는 것처럼 정책 인라인을 지정하지 않고 정책이 포함된 .json 파일에 대한 경로가 제공될 수도 있습니다.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## 정책 적용

### REST API를 사용하여 정책 할당

[정책 할당에 대한 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx)를 통해 원하는 범위에 정책 정의를 적용할 수 있습니다. REST API를 사용하여 정책 할당을 만들고, 삭제하고, 기존 할당에 관한 정보를 가져올 수 있습니다.

새 정책 할당을 만들려면 다음을 실행합니다.

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment}는 정책 할당의 이름입니다. api-version에는 *2015-10-01-preview*를 사용합니다.

요청 본문이 다음과 유사한 경우:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

예제 및 더 자세한 세부 정보는 [정책 할당에 대한 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx)를 참조하세요.

### PowerShell을 사용하여 정책 할당

PowerShell을 통해 위에서 만든 정책을 아래 나와 있는 것처럼 New-AzureRmPolicyAssignment cmdlet을 사용하여 원하는 범위에 적용할 수 있습니다.

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
여기서 $policy는 아래 나와 있는 것처럼 New-AzureRmPolicyDefinition cmdlet을 실행한 결과로 반환된 정책 개체입니다. 여기서 범위는 지정하는 리소스 그룹의 이름입니다.

위의 정책 할당을 제거하려는 경우 다음과 같이 수행하면 됩니다.

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition 및 Remove-AzureRmPolicyDefinition cmdlet을 통해 각각 정책 정의를 가져오거나 변경 또는 제거할 수 있습니다.

마찬가지로 Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment 및 Remove-AzureRmPolicyAssignment cmdlet을 통해 각각 정책 할당을 가져오거나 변경 또는 제거할 수 있습니다.

<!---HONumber=Oct15_HO3-->