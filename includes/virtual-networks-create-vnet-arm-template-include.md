## ARM 템플릿 다운로드 및 이해

github에서 VNet 및 두 개의 서브넷을 만들기 위한 기존 ARM 템플릿을 다운로드하고 원하는 대로 변경한 후 다시 사용할 수 있습니다. 이 작업을 수행하려면 다음 단계를 따르세요.

1. https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnets로 이동합니다.
2. **azuredeploy.json**을 클릭하고 **RAW**를 클릭합니다.
3. 파일을 컴퓨터의 로컬 폴더에 저장합니다.
4. ARM 템플릿에 익숙한 경우 7단계로 건너뜁니다.
5. 방금 저장한 파일을 열고 줄 5에 있는 **parameters** 아래의 내용을 확인합니다. ARM 템플릿 매개 변수는 배포하는 동안 채울 수 있는 값에 대한 자리 표시자를 제공합니다.

	| 매개 변수 | 설명 |
	|---|---|
	| **위치** | VNet을 만들 Azure 지역 |
	| **vnetName** | 새 VNet의 이름 |
	| **addressPrefix** | CIDR 형식의 VNet에 대한 주소 공간 |
	| **subnet1Name** | 첫 번째 VNet의 이름 |
	| **subnet1Prefix** | 첫 번째 서브넷의 CIDR 블록 |
	| **subnet2Name** | 두 번째 VNet의 이름 |
	| **subnet2Prefix** | 두 번째 서브넷의 CIDR 블록 |

	>[AZURE.IMPORTANT]Github에서 유지 관리되는 ARM 템플릿은 시간이 지나면서 달라질 수 있습니다. 템플릿을 사용하기 전에 확인합니다.
	
6. **resources** 아래의 내용을 확인하고 다음을 검토합니다.

	- **type**. 템플릿에 의해 생성되는 리소스의 유형입니다. 이 경우 VNet를 나타내는 **Microsoft.Network/virtualNetworks**입니다.
	- **name**. 리소스의 이름입니다. **[parameters('vnetName')]**이 사용됩니다. 이것은 해당 이름이 배포 중에 사용자 또는 매개 변수 파일에 의한 입력으로 제공됨을 의미합니다.
	- **properties**. 리소스의 속성 목록입니다. 이 템플릿은 VNet 생성 동안 주소 공간 및 서브넷 속성을 사용합니다.

7. https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnets로 돌아갑니다.
8. **azuredeploy-paremeters.json**을 클릭하고 **RAW**를 클릭합니다.
9. 파일을 컴퓨터의 로컬 폴더에 저장합니다.
10. 방금 저장한 파일을 열고 매개 변수 값을 편집합니다. 아래의 값을 사용하여 이 시나리오에 설명된 VNet을 배포합니다.

		{
		  "location": {
		    "value": "Central US"
		  },
		  "vnetName": {
		      "value": "TestVNet"
		  },
		  "addressPrefix": {
		      "value": "192.168.0.0/16"
		  },
		  "subnet1Name": {
		      "value": "FrontEnd"
		  },
		  "subnet1Prefix": {
		    "value": "192.168.1.0/24"
		  },
		  "subnet2Name": {
		      "value": "BackEnd"
		  },
		  "subnet2Prefix": {
		      "value": "192.168.2.0/24"
		  }
		}

11. 파일을 저장합니다.
  

<!---HONumber=Oct15_HO3-->