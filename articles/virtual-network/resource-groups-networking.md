---
title: "네트워크 리소스 공급자 개요 | Microsoft Docs"
description: "Azure 리소스 관리자의 새로운 네트워크 리소스 공급자에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 79bf09da-4809-45cb-8d21-705616ef24dc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 2428c707ddeed281fddd1e57bc5574603f0b9b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="network-resource-provider"></a>네트워크 리소스 공급자
현대 비즈니스의 성공에 있어서 가장 필요한 것은 대규모 네트워크 인식 응용 프로그램을 신속하고 유연하고 안전하고 반복 가능한 방법으로 작성하여 관리할 수 있는 기능입니다. Azure Resource Manager를 사용하면 이러한 응용 프로그램을 리소스 그룹에서 단일 리소스 컬렉션으로 만들 수 있습니다. 이러한 리소스는 Resource Manager의 다양한 리소스 공급자를 통해 관리됩니다.

Azure 리소스 관리자는 다양한 리소스 공급자를 사용하여 리소스에 대한 액세스를 제공합니다. 네트워크, 저장소 및 계산의 세 가지 주요 리소스 공급자가 있습니다. 이 문서에서는 다음을 비롯한 네트워크 리소스 공급자의 특징 및 이점에 대해 설명합니다.

* **메타데이터** - 태그를 사용하여 리소스에 정보를 추가할 수 있습니다. 이러한 태그를 사용하여 리소스 그룹 및 구독 간의 리소스 사용률을 추적할 수 있습니다.
* **네트워크 제어 향상** - 네트워크 리소스가 느슨하게 결합되고 네트워크 리소스를 세부적으로 제어할 수 있습니다. 즉, 네트워킹 리소스를 보다 유연하게 관리할 수 있습니다.
* **더 빠른 구성** - 네트워크 리소스가 느슨하게 결합되므로 네트워크 리소스를 병렬로 만들어서 조정할 수 있습니다. 따라서 구성 시간이 크게 단축됩니다.
* **역할 기반 액세스 제어** - RBAC는 기본 역할에 특정 보안 범위를 제공하고, 안전한 관리를 위해 사용자 지정 역할을 만들 수 있도록 허용합니다.
* **쉬운 관리 및 배포** - 전체 응용 프로그램 스택을 리소스 그룹에서 단일 리소스 컬렉션으로 만들 수 있으므로 응용 프로그램을 쉽게 배포하여 관리할 수 있습니다. 단순히 템플릿 JSON 페이로드를 제공하여 배포할 수 있으므로 배포 시간이 단축됩니다.
* **빠른 사용자 지정** - 선언적 스타일 템플릿을 사용하여 배포를 반복 가능하고 빠르게 사용자 지정할 수 있습니다.
* **반복 가능한 사용자 지정** - 선언적 스타일 템플릿을 사용하여 배포를 반복 가능하고 빠르게 사용자 지정할 수 있습니다.
* **관리 인터페이스** - 다음 인터페이스 중 하나를 사용하여 리소스를 관리할 수 있습니다.
  * REST 기반 API
  * PowerShell
  * .NET SDK
  * Node.JS SDK
  * Java SDK
  * Azure CLI
  * Preview 포털
  * Resource Manager 템플릿 언어

## <a name="network-resources"></a>네트워크 리소스
이제 네트워크 리소스를 단일 컴퓨팅 리소스(가상 컴퓨터)를 통해 모두 함께 관리하지 않고 독립적으로 관리할 수 있습니다. 이렇게 하면 리소스 그룹에서 복잡한 대규모 인프라를 더 유연하고 신속하게 구성할 수 있습니다.

다중 계층 응용 프로그램을 포함하는 샘플 배포의 개념 보기는 아래와 같습니다. NIC, 공용 IP 주소 및 VM과 같은 표시되는 각 리소스를 독립적으로 관리할 수 있습니다.

![네트워크 리소스 모델](./media/resource-groups-networking/Figure2.png)

모든 리소스에는 공통 속성 집합 및 개별 속성 집합이 포함되어 있습니다. 공용 속성은 다음과 같습니다.

| 속성 | 설명 | 샘플 값 |
| --- | --- | --- |
| **name** |고유한 리소스 이름입니다. 각 리소스 종류에 고유한 명명 제한이 있습니다. |PIP01, VM01, NIC01 |
| **위치** |리소스가 있는 Azure 지역입니다. |westus, eastus |
| **id** |고유한 URI 기반 ID입니다. |/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP |

아래 섹션에서 리소스의 개별 속성을 확인할 수 있습니다.

[!INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[!INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[!INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[!INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[!INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[!INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[!INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[!INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[!INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[!INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>관리 인터페이스
다양한 인터페이스를 사용하여 Azure 네트워킹 리소스를 관리할 수 있습니다. 이 문서에서는 이러한 인터페이스 중 REST API 및 템플릿 두 가지를 중점적으로 살펴보겠습니다.

### <a name="rest-api"></a>REST API
앞서 설명한 것처럼 REST API,.NET SDK, Node.JS SDK, Java SDK, PowerShell, CLI, Azure 포털, 템플릿을 비롯하여 다양한 인터페이스를 통해 네트워크 리소스를 관리할 수 있습니다.

Rest API는 HTTP 1.1 프로토콜 사양을 준수합니다. API의 일반 URI 구조는 아래와 같습니다.

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

중괄호로 묶인 매개 변수는 다음 요소를 나타냅니다.

* **subscription-id** - Azure 구독 ID입니다.
* **resource-provider-namespace** - 사용 중인 공급자의 네임스페이스입니다. 네트워크 리소스 공급자에 대한 값은 *Microsoft.Network*입니다.
* **region-name** - Azure 지역 이름입니다.

다음은 REST API를 호출할 때 지원되는 HTTP 메서드입니다.

* **PUT** - 지정된 유형의 리소스를 만들거나, 리소스 속성을 수정하거나, 리소스 간의 연결을 변경하는 데 사용됩니다.
* **GET** - 프로비전된 리소스에 대한 정보를 검색하는 데 사용됩니다.
* **DELETE** - 기존 리소스를 삭제하는 데 사용됩니다.

요청과 응답이 모두 JSON 페이로드 형식을 준수합니다. 자세한 내용은 [Azure 리소스 관리 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)를 참조하세요.

### <a name="resource-manager-template-language"></a>Resource Manager 템플릿 언어
API 또는 SDK를 사용하여 명령을 통해 리소스를 관리할 뿐만 아니라, 선언형 프로그래밍 스타일을 사용하여 Resource Manager 템플릿 언어로 네트워크 리소스를 빌드 및 관리할 수 있습니다.

템플릿의 샘플 표현은 아래와 같습니다.

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

템플릿은 주로 매개 변수를 통해 삽입된 인스턴스 값과 리소스에 대한 JSON 설명입니다. 아래 예를 사용하여 두 개의 서브넷을 포함하는 가상 네트워크를 만들 수 있습니다.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

템플릿을 사용할 때 매개 변수 값을 수동으로 제공하거나 매개 변수 파일을 사용할 수 있습니다. 아래 예에서는 위의 템플릿과 함께 사용할 수 있는 매개 변수 값 집합을 보여 줍니다.

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


템플릿을 사용할 때의 주요 이점은 다음과 같습니다.

* 선언적 스타일로 리소스 그룹에서 복잡한 인프라를 빌드할 수 있습니다. 종속성 관리를 비롯한 리소스 만들기 오케스트레이션은 Resource Manager에 의해 처리됩니다.
* 간단히 매개 변수를 변경하여 단일 지역에 속하거나 여러 지역에 걸치는 인프라를 반복 가능한 방식으로 만들 수 있습니다.
* 선언적 스타일을 사용하면 템플릿을 작성하고 인프라를 롤아웃하는 사이의 지연 시간이 단축됩니다.

샘플 템플릿은 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates)을 참조하세요.

Resource Manager 템플릿 언어에 대한 자세한 내용은 [Azure Resource Manager 템플릿 언어](../azure-resource-manager/resource-group-authoring-templates.md)를 참조하세요.

위의 샘플 템플릿에서는 가상 네트워크와 서브넷 리소스를 사용합니다. 사용 가능한 다른 네트워크 리소스는 아래 목록을 참조하세요.

### <a name="using-a-template"></a>템플릿 사용
PowerShell, AzureCLI를 사용하거나 GitHub에서 배포를 클릭하여 템플릿에서 Azure에 서비스를 배포할 수 있습니다. GitHub의 템플릿에서 서비스를 배포하려면 다음 단계를 실행합니다.

1. GitHub에서 template3 파일을 엽니다. 예를 들어, [두 서브넷을 사용하는 가상 네트워크](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network)를 엽니다.
2. **Azure에 배포**를 클릭한 다음 자격 증명을 사용하여 Azure 포털에 로그인합니다.
3. 템플릿을 확인한 다음 **저장**을 클릭합니다.
4. **매개 변수 편집** 을 클릭하고 *미국 서부*등과 같은 vnet 및 서브넷 위치를 선택합니다.
5. 필요한 경우 **ADDRESSPREFIX** 및 **SUBNETPREFIX** 매개 변수를 변경하고 **확인**을 클릭합니다.
6. **리소스 그룹 선택** 을 클릭하고 vnet과 서브넷에 추가하려는 리소스 그룹을 클릭합니다. **또는 새로 만들기**를 클릭하여 새 리소스 그룹을 만들 수도 있습니다.
7. **만들기**를 클릭합니다. **템플릿 배포 프로비저닝**이라고 표시된 타일을 확인합니다. 배포가 완료되면 아래와 비슷한 화면이 표시됩니다.

![샘플 템플릿 배포](./media/resource-groups-networking/Figure6.png)

## <a name="next-steps"></a>다음 단계
[Azure 리소스 관리자 템플릿 언어](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure 네트워킹- 일반적으로 사용되는 템플릿](https://github.com/Azure/azure-quickstart-templates)

[Azure Resource Manager 및 클래식 배포](../azure-resource-manager/resource-manager-deployment-model.md)

[Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)

