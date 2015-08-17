<properties 
   pageTitle="네트워크 리소스 공급자"
   description="네트워크 리소스 공급자"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# 네트워크 리소스 공급자
현대 비즈니스의 성공에 있어서 가장 필요한 것은 대규모 네트워크 인식 응용 프로그램을 신속하고 유연하고 안전하고 반복 가능한 방법으로 작성하여 관리할 수 있는 기능입니다. ARM(Azure 리소스 관리자)을 사용하면 그런 응용 프로그램을 리소스 그룹에서 단일 리소스 컬렉션으로 만들 수 있습니다. 이러한 리소스는 ARM 아래의 다양한 리소스 공급자를 통해 관리됩니다.

ARM(Azure 리소스 관리자)을 사용하면 그런 응용 프로그램 및 연결된 네트워크 리소스 컬렉션을 리소스 그룹에서 단일 리소스 컬렉션으로 만들 수 있습니다. 응용 프로그램과 네트워크 리소스는 ARM 리소스 그룹에서 단일 단위로 실행됩니다.

다음과 같은 관리 인터페이스를 사용하여 네트워킹 리소스를 관리할 수 있습니다.

- REST 기반 API
- PowerShell
- .NET SDK
- Node.JS SDK
- Java SDK
- Azure CLI
- Azure 포털
- ARM 템플릿 언어

네트워크 리소스 공급자를 도입하면 다음과 같은 이점이 있습니다.

- **메타데이터** - 태그를 사용하여 리소스에 정보를 추가할 수 있습니다. 이러한 태그를 사용하여 리소스 그룹 및 구독 간의 리소스 사용률을 추적할 수 있습니다.
- **네트워크 제어 향상** - 네트워크 리소스가 느슨하게 결합되고 네트워크 리소스를 세부적으로 제어할 수 있습니다. 즉, 네트워킹 리소스를 보다 유연하게 관리할 수 있습니다.
- **더 빠른 구성** - 네트워크 리소스가 느슨하게 결합되므로 네트워크 리소스를 병렬로 만들어서 조정할 수 있습니다. 따라서 구성 시간이 크게 단축됩니다.
- **역할 기반 액세스 제어** - RBAC는 기본 역할에 특정 보안 범위를 제공하고, 안전한 관리를 위해 사용자 지정 역할을 만들 수 있도록 허용합니다. 
- **쉬운 관리 및 배포** - 전체 응용 프로그램 스택을 리소스 그룹에서 단일 리소스 컬렉션으로 만들 수 있으므로 응용 프로그램을 쉽게 배포하여 관리할 수 있습니다. 단순히 템플릿 JSON 페이로드를 제공하여 배포할 수 있으므로 배포 시간이 단축됩니다.
- **빠른 사용자 지정** - 선언적 스타일 템플릿을 사용하여 배포를 반복 가능하고 빠르게 사용자 지정할 수 있습니다. 
- **반복 가능한 사용자 지정** - 선언적 스타일 템플릿을 사용하여 배포를 반복 가능하고 빠르게 사용자 지정할 수 있습니다.

## 네트워크 리소스 
이제 네트워크 리소스를 단일 컴퓨팅 리소스(가상 컴퓨터)를 통해 모두 함께 관리하지 않고 독립적으로 관리할 수 있습니다. 이렇게 하면 리소스 그룹에서 복잡한 대규모 인프라를 더 유연하고 신속하게 구성할 수 있습니다.
 
아래 다이어그램은 네트워크 리소스 모델 및 연결을 간략하게 보여 줍니다. 최상위 리소스는 파란색 윤곽선으로 색이 지정됩니다. 최상위 리소스 이외에 회색 윤곽선으로 자식 리소스를 표시할 수 있습니다. 각 리소스를 개별적으로 관리할 수 있습니다.

![네트워크 리소스 모델](./media/resource-groups-networking/Figure1.png)

다중 계층 응용 프로그램을 포함하는 샘플 배포의 개념 보기는 아래와 같습니다. 모든 네트워크 리소스는 파란색 윤곽선으로 색이 지정됩니다.

![네트워크 리소스 모델](./media/resource-groups-networking/Figure2.png)

## REST API 
앞서 설명한 것처럼 REST API,.NET SDK, Node.JS SDK, Java SDK, PowerShell, CLI, Azure 포털, 템플릿을 비롯하여 다양한 인터페이스를 통해 네트워크 리소스를 관리할 수 있습니다.

Rest API는 HTTP 1.1 프로토콜 사양을 준수합니다. API의 일반 URI 구조는 아래와 같습니다.

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

중괄호로 묶인 매개 변수는 다음 요소를 나타냅니다.

- **subscription-id** - Azure 구독 ID입니다.
- **resource-provider-namespace** - 사용 중인 공급자의 네임스페이스입니다. 네트워크 리소스 공급자에 대한 값은 *Microsoft.Network*입니다.
- **region-name** - Azure 지역 이름입니다.

다음은 REST API를 호출할 때 지원되는 HTTP 메서드입니다.

- **PUT** - 지정된 유형의 리소스를 만들거나, 리소스 속성을 수정하거나, 리소스 간의 연결을 변경하는 데 사용됩니다. 
- **GET** - 프로비전된 리소스에 대한 정보를 검색하는 데 사용됩니다.
- **DELETE** - 기존 리소스를 삭제하는 데 사용됩니다.

요청과 응답이 모두 JSON 페이로드 형식을 준수합니다. 자세한 내용은 [Azure 리소스 관리 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)를 참조하세요.

## ARM 템플릿 언어
API 또는 SDK를 사용하여 명령을 통해 리소스를 관리할 뿐만 아니라, 선언형 프로그래밍 스타일을 사용하여 ARM 템플릿 언어로 네트워크 리소스를 빌드 및 관리할 수 있습니다.

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

- 선언적 스타일로 리소스 그룹에서 복잡한 인프라를 빌드할 수 있습니다. 종속성 관리를 비롯한 리소스 만들기 오케스트레이션은 ARM에 의해 처리됩니다. 
- 간단히 매개 변수를 변경하여 단일 지역에 속하거나 여러 지역에 걸치는 인프라를 반복 가능한 방식으로 만들 수 있습니다. 
- 선언적 스타일을 사용하면 템플릿을 작성하고 인프라를 롤아웃하는 사이의 지연 시간이 단축됩니다. 

샘플 템플릿은 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates)을 참조하세요.

ARM 템플릿 언어에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 언어](https://msdn.microsoft.com/library/azure/dn835138.aspx)를 참조하세요.

위의 샘플 템플릿에서는 가상 네트워크와 서브넷 리소스를 사용합니다. 사용 가능한 다른 네트워크 리소스는 아래 목록을 참조하세요.

## NIC
NIC(네트워크 인터페이스 카드)는 VM(가상 컴퓨터)에 연결 될 수 있는 네트워크 인터페이스를 나타냅니다. VM은 NIC를 하나 이상 포함할 수 있습니다.

![단일 VM의 NIC](./media/resource-groups-networking/Figure3.png)

NIC 리소스의 키 속성은 다음과 같습니다.

- IP 설정

NIC를 다음 네트워크 리소스에 연결할 수도 있습니다.

- NSG(네트워크 보안 그룹) 
- 부하 분산 장치

## 가상 네트워크 및 서브넷
VNET(가상 네트워크)과 서브넷을 사용하여 Azure에서 실행 중인 작업에 대한 보안 경계를 정의할 수 있습니다. VNET은 주소 공간(CIDR 블록이라고도 함)에 따라 구분됩니다.

서브넷은 VNET의 자식 리소스이며, IP 주소 접두사를 사용하여 CIDR 블록 내의 주소 공간 세그먼트를 정의하는 데 도움이 됩니다. 다양한 작업을 실행하는 VM은 서브넷 경계 내에서 작동해야 합니다.

![단일 VM의 NIC](./media/resource-groups-networking/Figure4.png)

VNET 리소스의 키 속성은 다음과 같습니다.

- IP 주소 공간(CIDR 블록) 
- VNET 이름
- 서브넷

VNET을 다음 네트워크 리소스와 연결할 수도 있습니다.

- VPN 게이트웨이

서브넷의 키 속성은 다음과 같습니다.

- IP 주소 접두사
- 서브넷 이름

서브넷을 다음 네트워크 리소스와 연결할 수도 있습니다.

- NSG

## 부하 분산 장치
부하 분산 장치는 응용 프로그램의 크기를 조정하려는 경우에 사용됩니다. 일반적인 배포 시나리오에는 여러 VM 인스턴스에서 실행 중인 응용 프로그램이 포함됩니다. 네트워크 트래픽을 다양한 인스턴스에 분산하는데 도움이 되는 부하 분산 장치가 VM 인스턴스의 앞에 옵니다.

![단일 VM의 NIC](./media/resource-groups-networking/Figure5.png)

부하 분산 장치는 다음과 같은 자식 리소스를 포함합니다.

- **프런트 엔드 IP 구성** - 부하 분산 장치는 하나 이상의 프런트 엔드 IP 주소(VIP(가상 IP)라고 함)를 포함할 수 있습니다. 이러한 IP 주소는 트래픽에 대한 수신으로 사용됩니다. 
- **백 엔드 주소 풀** - 부하가 분산될 VM NIC에 연결된 IP 주소입니다.
- **부하 분산 규칙** - 규칙 속성은 지정된 프런트 엔드 IP와 포트 조합을 백 엔드 IP 주소와 포트 조합 집합에 매핑합니다. 부하 분산 장치 리소스의 단일 정의를 사용하여 각각 VM에 연결된 프런트 엔드 IP와 포트 및 백 엔드 IP와 포트 조합을 반영하는 여러 부하 분산 규칙을 정의할 수 있습니다. 
- **검색** - 검색을 사용하여 VM 인스턴스의 상태를 추적할 수 있습니다. 상태 검색에 실패하면 해당 VM 인스턴스는 자동으로 회전에서 제외됩니다.
- **인바운드 NAT 규칙** - 프런트 엔드 IP를 통해 흐르고 백 엔드 IP에 분산되는 인바운드 트래픽을 정의하는 NAT 규칙입니다.

## 공용 IP
공용 IP 주소 리소스는 예약된 공용 IP 주소 또는 동적 공용 IP 주소를 제공합니다. 공용 IP 주소를 부하 분산 장치 또는 NAT에 할당하거나 VM NIC의 개인 IP 주소와 연결할 수 있습니다.

공용 IP 주소의 키 속성은 다음과 같습니다.

- **IP 할당 방법** - 예약된 IP 또는 동적 IP를 할당합니다. 

## NSG(네트워크 보안 그룹)
NSG 리소스를 사용하면 허용 및 거부 규칙을 구현하여 작업에 대한 보안 경계를 만들 수 있습니다. NIC 수준(VM 인스턴스 수준) 또는 서브넷 수준(VM 그룹)에서 그런 규칙을 적용할 수 있습니다.

NSG 리소스의 키 속성은 다음과 같습니다.

- **보안 규칙** - NSG에 대해 여러 보안 규칙을 정의할 수 있습니다. 각 규칙은 서로 다른 유형의 트래픽을 허용하거나 거부할 수 있습니다.

## 보안 규칙
보안 규칙은 NSG의 자식 리소스입니다.

보안 규칙의 키 속성은 다음과 같습니다.

- **프로토콜** - 이 규칙이 적용되는 네트워크 프로토콜입니다.
- **원본 포트 범위** - 원본 포트 또는 0-65535 범위입니다. 와일드카드를 사용하여 모든 포트를 일치시킬 수 있습니다. 
- **대상 포트 범위** - 대상 포트 또는 0-65535 범위입니다. 와일드카드를 사용하여 모든 포트를 일치시킬 수 있습니다.
- **원본 주소 접두사** - 원본 IP 주소 범위입니다. 
- **대상 주소 접두사** - 대상 IP 주소 범위입니다.
- **액세스** - 트래픽을 *허용* 또는 *거부*합니다.
- **우선 순위** - 100과 4096 사이의 값입니다. 우선 순위 번호는 보안 규칙 컬렉션의 각 규칙에 대해 고유해야 합니다. 우선 순위 번호가 낮을수록 규칙의 우선 순위가 높습니다.
- **방향** - 규칙을 *인바운드* 방향으로 적용할지 *아웃바운드* 방향으로 적용할지를 지정합니다. 

## VPN 게이트웨이 
VPN 게이트웨이 리소스를 사용하여 온-프레미스 데이터 센터와 Azure 간에 보안 연결을 만들 수 있습니다. 다음과 같은 세 가지 방법으로 VPN 게이트웨이 리소스를 구성할 수 있습니다.
 
- **지점 및 사이트 간** - 컴퓨터에서 VPN 클라이언트를 사용하여 VNET에 호스트된 Azure 리소스에 안전하게 액세스할 수 있습니다. 
- **다중 사이트 연결** - 온-프레미스 데이터 센터에서 VNET에 실행 중인 리소스에 안전하게 연결할 수 있습니다. 
- **VNET 간** - 동일한 지역 내의 Azure VNET 간이나 지역 간에 안전하게 연결하여 지리적 복제 기능을 사용하여 작업을 빌드할 수 있습니다.

VPN 게이트웨이의 키 속성은 다음과 같습니다.
 
- **게이트웨이 유형** - 동적으로 라우팅되거나 정적으로 라우팅된 게이트웨이입니다. 
- **VPN 클라이언트 주소 풀 접두사** - 지점 및 사이트 구성으로 연결 중인 클라이언트에 할당할 IP 주소입니다.

## 트래픽 관리자 프로필
트래픽 관리자와 자식 끝점 리소스를 사용하여 Azure 내부와 외부의 끝점에 트래픽을 배포할 수 있습니다. 이러한 트래픽 배포는 정책에 의해 제어됩니다. 또한 트래픽 관리자를 사용하여 끝점 상태를 모니터링할 수 있습니다. 트래픽은 끝점의 상태를 기반으로 적절하게 전환됩니다.

트래픽 관리자 프로필의 키 속성은 다음과 같습니다.

- **트래픽 라우팅 메서드** - 사용 가능한 값은 *성능*, *가중* 및 *우선 순위*입니다.
- **DNS 구성** - 프로필에 대한 FQDN입니다.
- **프로토콜** - 모니터링 중인 프로토콜이며 가능한 값은 *HTTP* 및 *HTTPS*입니다.
- **포트** - 모니터링 중인 포트입니다. 
- **경로** - 모니터링 중인 경로입니다.
- **끝점** - 끝점 리소스에 대한 컨테이너입니다.

## 끝점 
끝점은 트래픽 관리자 프로필의 자식 리소스이며, 트래픽 관리자 프로필 리소스에 구성된 정책을 기반으로 사용자 트래픽이 배포되는 서비스 또는 웹 끝점을 나타냅니다.

끝점의 키 속성은 다음과 같습니다.
 
- **유형** - 끝점의 형식이며, 가능한 값은 *Azure 끝점*, *외부 끝점* 및 *중첩된 끝점*입니다. 
- **대상 리소스 ID** - 서비스 또는 웹 끝점의 공용 IP 주소입니다. Azure 또는 외부 끝점일 수 있습니다.
- **가중치** -트래픽 관리에 사용되는 끝점 가중치입니다. 
- **우선 순위** - 장애 조치(Failover) 동작을 정의하는 데 사용되는 끝점의 우선 순위입니다. 

## 템플릿 사용

PowerShell, AzureCLI를 사용하거나 GitHub에서 배포를 클릭하여 템플릿에서 Azure에 서비스를 배포할 수 있습니다. GitHub의 템플릿에서 서비스를 배포하려면 다음 단계를 실행합니다.

1. GitHub에서 template3 파일을 엽니다. 예를 들어, [두 서브넷을 사용하는 가상 네트워크](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network)를 엽니다.
2. **Azure에 배포**를 클릭한 다음 자격 증명을 사용하여 Azure 포털에 로그인합니다.
3. 템플릿을 확인한 다음 **저장**을 클릭합니다.
4. **매개 변수 편집**을 클릭하고 *미국 서부* 등과 같은 vnet 및 서브넷 위치를 선택합니다.
5. 필요한 경우 **ADDRESSPREFIX** 및 **SUBNETPREFIX** 매개 변수를 변경하고 **확인**을 클릭합니다.
6. **리소스 그룹 선택**을 클릭하고 vnet과 서브넷에 추가하려는 리소스 그룹을 클릭합니다. **또는 새로 만들기**를 클릭하여 새 리소스 그룹을 만들 수도 있습니다.
3. **만들기**를 클릭합니다. **템플릿 배포 프로비저닝**이라고 표시된 타일을 확인합니다. 배포가 완료되면 아래와 비슷한 화면이 표시됩니다.

![샘플 템플릿 배포](./media/resource-groups-networking/Figure6.png)

## 참고 항목

[Azure 네트워킹 API 참조](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[네트워킹을 위한 Azure PowerShell 참조](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure 리소스 관리자 템플릿 언어](https://msdn.microsoft.com/library/azure/dn835138.aspx)

[Azure 네트워킹- 일반적으로 사용되는 템플릿](https://github.com/Azure/azure-quickstart-templates)

[계산 리소스 공급자](../virtual-machines-azurerm-versus-azuresm)

[Azure 리소스 관리자 개요](../resource-group-overview)

[Azure 리소스 관리자의 역할 기반 액세스 제어](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Azure 리소스 관리자에서 태그 사용](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[템플릿 배포](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=August15_HO6-->