<properties
   pageTitle="저장소에 대한 리소스 관리자 템플릿 | Microsoft Azure"
   description="템플릿을 통해 저장소 계정을 배포하기 위한 리소스 관리자 스키마를 보여 줍니다."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
   ms.author="tomfitz"/>

# 저장소 계정 템플릿 스키마

저장소 계정을 만듭니다.

## 스키마 형식

저장소 계정을 만들려면 템플릿의 리소스 섹션에 다음 스키마를 추가합니다.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
        	"accountType": string
        }
    }

## 값

다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | 예 | **Microsoft.Storage/storageAccounts** | 만들려는 리소스 형식입니다. |
| apiVersion | enum | 예 | **2015-06-15** <br /> **2015-05-01-미리 보기** | 리소스를 만들 때 사용하는 API 버전입니다. | 
| name | string | 예 | 3자에서 24자까지 중에서 숫자와 소문자만 | 만들려는 저장소 계정의 이름입니다. Azure의 모든 이름은 고유해야 합니다. 아래의 예제에 표시된 대로 명명 규칙에 따라 [uniqueString](resource-group-template-functions.md#uniquestring) 기능 사용을 고려합니다. |
| location | string | 예 | 유효한 영역을 확인하려면 [지원되는 지역](resource-manager-supported-services.md#supported-regions)을 참조하세요. | 저장소 계정을 호스팅하는 영역입니다. |
| properties | object | 예 | (아래 참조) | 만들 저장소 계정 유형을 지정하는 개체입니다.

### properties 개체

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ---- | ---- | -------- | ---------------- | ----------- |
| 계정 유형 | string | 예 | **Standard\_LRS**<br />**Standard\_ZRS**<br />**Standard\_GRS**<br />**Standard\_RAGRS**<br />**Premium\_LRS** | 저장소 계정의 유형입니다. 허용된 값은 표준 로컬 중복, 표준 지역 중복, 표준 지리적 중복, 표준 읽기 액세스 지리적 중복 및 프리미엄 로컬 중복에 해당합니다. 이러한 계정 유형에 대한 정보는 [Azure 저장소 복제](./storage/storage-redundancy.md)를 참조하세요. |

	
## 예

다음 예제에서는 리소스 그룹 ID에 따라 고유 이름을 가진 표준 로컬 중복 저장소 계정을 배포합니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
		         "location": "[resourceGroup().location]",
        	     "properties": 
        	     {
        		      "accountType": "Standard_LRS"
        	     }
	        }
	    ],
	    "outputs": {}
    }

## 빠른 시작 템플릿

저장소 계정을 포함하는 많은 빠른 시작 템플릿이 있습니다. 다음 템플릿은 몇 가지 일반적인 시나리오를 보여 줍니다.

- [표준 저장소 계정 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create)
- [Windows VM의 간단한 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
- [Linux VM의 간단한 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
- [CDN 프로필, 저장소 계정을 원본으로 사용하여 CDN 끝점 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-cdn-with-storage-account)
- [Powershell DSC 확장을 사용하여 9개의 VM을 가진 고가용성 SharePoint 팜 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-server-farm-ha)
- [WAD가 사용하도록 설정된 상태에서 5 노드 보안 서비스 패브릭 클러스터의 간단한 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [4개의 빈 데이터 디스크를 사용하여 Windows 이미지에서 가상 컴퓨터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk)


## 다음 단계

- 저장소에 관한 일반적인 정보는 [Microsoft Azure 저장소 소개](./storage/storage-introduction.md)를 참조하세요.
- 가상 컴퓨터로 새 저장소 계정을 사용하는 예제 템플릿은 [간단한 Linux VM 배포](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) 또는 [간단한 Windows VM 배포](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)를 참조하세요.

<!---HONumber=AcomDC_0107_2016-->