<properties
	pageTitle="Azure 리소스 관리자 템플릿에서 상태를 처리하는 모범 사례"
	description="복잡한 개체를 사용하여 Azure 리소스 관리자 템플릿 및 연결된 템플릿과 상태 데이터를 공유하는 권장 방법을 보여 줍니다."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="mmercuri"/>

# Azure 리소스 관리자 템플릿에서 상태 공유

이 항목에서는 Azure 리소스 관리자 템플릿 내에서 및 연결된 템플릿 간에 상태를 관리 및 공유하는 방법을 설명합니다.

## 복잡한 개체를 사용하여 상태 공유

단일 값 매개 변수 외에, Azure 리소스 관리자 템플릿에서 복잡한 개체를 매개 변수로 사용할 수 있습니다. 복잡한 개체를 사용할 경우 티셔츠 크기(가상 컴퓨터를 설명하기 위한 예임)와 같은 특정 영역, 네트워크 설정, OS(운영 체제) 설정 및 가용성 설정에 대한 데이터 컬렉션을 구현하고 참조할 수 있습니다.

다음 예제에서는 데이터 컬렉션을 나타내기 위한 복잡한 개체를 포함하는 변수의 정의 방법을 보여 줍니다. 이 컬렉션은 가상 컴퓨터 크기, 네트워크 설정, 운영 체제 설정 및 가용성 설정에 사용되는 값을 정의합니다.

    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "slaveCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    },
    "osSettings": {
      "scripts": [
        "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
      ],
      "imageReference": {
				"publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    },
    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    },
    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

그런 다음 템플릿의 뒷부분에서 이러한 변수를 참조할 수 있습니다. 명명된 변수 및 해당 속성을 참조하는 기능이 구현되면 템플릿 구문이 단순화되며 컨텍스트를 쉽게 이해할 수 있습니다. 다음 예제에서는 위에 표시된 개체를 통해 값을 설정하여 배포할 리소스를 정의합니다. 예를 들어 디스크 크기 값은 `variables('tshirtSize').diskSize`에서 검색되지만 VM 크기는 `variables('tshirtSize').vmSize` 값을 검색하여 설정합니다. 또한 연결된 템플릿에 대한 URI는 `variables('tshirtSize').vmTemplate` 값으로 설정됩니다.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
	    "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## 템플릿 및 연결된 템플릿에 상태 전달

다음을 통해 템플릿 및 연결된 해당 템플릿과 상태 정보를 공유할 수 있습니다.

- 배포하는 동안 사용자가 기본 템플릿에 직접 제공하는 매개 변수
- 기본 템플릿이 연결된 해당 템플릿과 공유하는 매개 변수, 정적 변수 및 생성된 변수

### 기본 템플릿에 제공되는 일반 매개 변수

다음 표에는 템플릿 내에서 일반적으로 사용되는 매개 변수가 나와 있습니다.

**기본 템플릿에 전달된 일반적으로 사용되는 매개 변수**

이름 | 값 | 설명
---- | ----- | -----------
location | 제한된 Azure 영역 목록의 문자열 | 리소스가 배포될 위치입니다.
storageAccountNamePrefix | 문자열 | VM의 디스크가 배치될 저장소 계정의 고유 DNS 이름
domainName | 문자열 | 공개적으로 액세스할 수 있는 jumpbox VM의 도메인 이름 형식: **{domainName}.{location}.cloudapp.com** 예: **mydomainname.westus.cloudapp.azure.com**
adminUsername | 문자열 | VM의 사용자 이름
adminPassword | 문자열 | VM에 대한 암호
tshirtSize | 제공된 티셔츠 크기의 제한된 목록에서 가져온 문자열 | 프로비전할 명명된 배율 단위 크기. 예: "Small", "Medium", "Large"
virtualNetworkName | 문자열 | 소비자가 사용하려는 가상 네트워크의 이름.
enableJumpbox | 제한된 목록에서 가져온 문자열(enabled/disabled) | 환경에 대해 jumpbox를 사용하도록 설정할지 여부를 식별하는 매개 변수입니다. 값: "enabled", "disabled"

### 연결된 템플릿으로 전송된 매개 변수

연결된 템플릿에 연결할 경우 정적 변수와 생성된 변수를 혼합해서 사용하게 됩니다.

#### 정적 변수

정적 변수는 템플릿 전체에서 사용되는 URL과 같은 기본 값을 제공하기 위해 또는 동적 변수에 대한 값을 생성하는 데 사용되는 값으로 종종 사용됩니다.

아래에 발췌한 템플릿에서 *templateBaseUrl*은 GitHub에서 템플릿의 루트 위치를 지정합니다. 다음 줄은 *templateBaseUrl* 값을 공유 리소스 템플릿의 알려진 이름에 연결하는 새 변수인 *sharedTemplateUrl*을 작성합니다. 그 아래에 나오는 복잡한 개체 변수는 티셔츠 크기를 저장하는 데 사용됩니다. 여기서 *templateBaseUrl*이 연결되어 *vmTemplate* 속성에 저장된 알려진 구성 템플릿 위치를 지정합니다.

이 방식의 장점은 템플릿을 쉽게 이동하거나 분기하거나 새 템플릿을 기반으로 사용할 수 있다는 것입니다. 템플릿 위치가 변경되면 한 곳, 즉 주 템플릿에서만 정적 변수를 변경하면 됩니다. 그러면 전체 템플릿으로 전달됩니다.

    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "slaveCount": 1,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    }

#### 생성된 변수

정적 변수 외에도 다양한 변수가 동적으로 생성됩니다. 이 섹션에서는 생성된 변수의 몇 가지 일반적인 유형에 대해 설명합니다.

##### tshirtSize

주 템플릿을 호출할 때는 일반적으로 *Small*, *Medium* 및 *Large*와 같은 값을 포함하는 고정된 수의 옵션에서 티셔츠 크기를 선택할 수 있습니다.

주 템플릿에서 이 옵션은 *tshirtSize*와 같은 매개 변수로 나타납니다.

    "tshirtSize": {
      "type": "string",
      "defaultValue": "Small",
      "allowedValues": [
        "Small",
        "Medium",
        "Large"
      ],
      "metadata": {
        "Description": "T-shirt size of the MongoDB deployment"
      }
    }

주 템플릿 내에서 변수는 각 크기에 해당합니다. 예를 들어 작은 크기, 중간 크기 및 큰 크기의 세 가지 크기를 사용할 수 있는 경우 변수 섹션에는 *tshirtSizeSmall*, *tshirtSizeMedium* 및 *tshirtSizeLarge*라는 변수가 포함됩니다.

다음 예제에서 볼 수 있듯이 이러한 변수는 특정 티셔츠 크기의 속성을 정의합니다. 각각은 VM 유형, 디스크 크기, 연결할 관련 배율 단위 리소스 템플릿, 인스턴스 수, 저장소 계정 세부 정보 및 jumpbox 상태를 식별합니다.

저장소 계정 이름 접두사는 사용자가 제공한 매개 변수에서 가져오며 연결된 템플릿은 템플릿의 기본 URL과 특정 배율 단위 리소스 템플릿의 파일 이름을 연결한 것입니다.

    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
			"diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    },
    "tshirtSizeMedium": {
      "vmSize": "Standard_A3",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1],
        "jumpbox": 0
      }
    },
    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    }

*tshirtSize* 변수는 변수 섹션에서 더 아래쪽에 나타납니다. 사용자가 제공한 티셔츠 크기의 끝(*Small*, *Medium*, *Large*)이 텍스트 *tshirtSize*와 연결되어 해당 티셔츠 크기에 대해 연결된 복잡한 개체 변수를 검색합니다.

    "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",

이 변수는 연결된 배율 단위 리소스 템플릿에 전달됩니다.

##### networkSettings

용량, 기능 또는 전체 범위의 솔루션 템플릿에서 연결된 템플릿은 일반적으로 네트워크에 존재하는 리소스를 만듭니다. 한 가지 간단한 방법은 복잡한 개체를 사용하여 네트워크 설정을 저장하고 연결된 템플릿에 전달하는 것입니다.

아래에서 네트워크 설정을 전달하는 예제를 볼 수 있습니다.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

##### availabilitySettings

연결된 템플릿에서 만든 리소스는 종종 가용성 집합에 배치됩니다. 다음 예에서는 가용성 집합 이름이 지정되고, 사용할 장애 도메인 및 업데이트 도메인 수도 지정됩니다.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

여러 가용성 집합(예를 들어 마스터 노드용 하나와 데이터 노드용 하나)이 필요한 경우 이름을 접두사로 사용하거나, 여러 가용성 집합을 지정하거나, 앞서 제시된 특정 티셔츠 크기에 해당하는 변수 생성 방법 예를 따라 진행할 수 있습니다.

##### storageSettings

저장소 세부 정보는 연결된 템플릿과 종종 공유됩니다. 아래 예제에서 *storageSettings* 개체는 저장소 계정 및 컨테이너 이름에 대한 세부 정보를 제공합니다.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

##### osSettings

연결된 템플릿을 사용할 경우 알려진 여러 구성 유형의 다양한 노드 형식에 운영 체제 설정을 전달해야 할 수 있습니다. 복잡한 개체는 운영 체제 정보를 쉽게 저장 및 공유하고 운영 체제에서 제공되는 다양한 배포 옵션을 보다 쉽게 지원할 수 있도록 하는 간편한 방법입니다.

다음 예제에는 *osSettings*용 개체가 나와 있습니다.

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

##### machineSettings

생성된 변수 *machineSettings*는 새 VM을 만들기 위한 핵심 변수인 관리자의 사용자 이름과 암호, VM 이름의 접두사 및 운영 체제 이미지 참조 등을 포함하는 복잡한 개체입니다.

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

*osImageReference*는 주 템플릿에 정의된 *osSettings* 변수에서 값을 검색합니다. 즉, VM에 대한 운영 체제를 완전히 또는 템플릿 소비자의 기본 설정에 따라 쉽게 변경할 수 있습니다.

##### vmScripts

*vmScripts* 개체에는 외부 및 내부 참조를 포함하여 VM 인스턴스에서 다운로드 및 실행할 스크립트에 대한 세부 정보가 포함됩니다. 외부 참조는 인프라를 포함합니다. 내부 참조에는 설치된 소프트웨어와 구성이 포함됩니다.

*scriptsToDownload* 속성을 사용하여 VM으로 다운로드할 스크립트 목록을 표시합니다.

아래 예제에 표시된 것처럼 이 개체에는 다양한 유형의 동작에 대한 명령줄 인수의 참조도 포함됩니다. 이러한 동작에는 각 개별 노드에 대한 기본 설치, 모든 노드를 배포한 후에 실행되는 설치 및 지정된 템플릿에서만 사용할 수 있는 추가 스크립트의 실행이 포함됩니다.

이 예제는 MongoDB를 배포하는 데 사용되는 템플릿에서 가져온 것입니다. 여기서 중재자는 고가용성을 제공해야 합니다. 중재자를 설치하기 위해 *vmScripts*에 *arbiterNodeInstallCommand*가 추가되었습니다.

변수 섹션은 적절한 값으로 스크립트를 실행하기 위해 특정 텍스트를 정의하는 변수를 찾는 위치입니다.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## 템플릿에서 상태 반환

템플릿으로 데이터를 전달할 뿐만 아니라 호출 템플릿과 다시 데이터를 공유할 수도 있습니다. 연결된 템플릿의 **출력** 섹션에서는 원본 템플릿에서 사용될 수 있는 키/값 쌍을 제공할 수 있습니다.

다음 예제에서는 연결된 템플릿에서 생성된 개인 IP 주소를 전달하는 방법을 보여 줍니다.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

주 템플릿 내에서 다음 구문을 사용하여 해당 데이터를 사용할 수 있습니다.

    "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]"
    }

## 다음 단계
- [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)
- [Azure 리소스 관리자 템플릿 함수](resource-group-template-functions.md)

<!---HONumber=July15_HO4-->