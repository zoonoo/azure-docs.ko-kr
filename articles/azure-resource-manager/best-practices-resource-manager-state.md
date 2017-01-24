---
title: "Resource Manager 템플릿의 상태 처리 | Microsoft Docs"
description: "복잡한 개체를 사용하여 Azure 리소스 관리자 템플릿 및 연결된 템플릿과 상태 데이터를 공유하는 권장 방법을 보여 줍니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fd2f5e2d-d56d-4e01-a57d-34f3eaead4a9
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: cf2f6a82df28047ba7b50dc2cfd6fc2f0dfad794


---
# <a name="sharing-state-in-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿에서 상태 공유
이 항목에서는 템플릿 내에서 상태를 관리 및 공유하는 방법에 대한 모범 사례를 보여 줍니다. 이 항목에 표시된 매개 변수 및 변수는 배포 요구 사항을 편리하게 구성하기 위해 정의할 수 있는 개체 유형의 예입니다. 이러한 예를 통해 사용자 환경에 맞는 속성 값으로 고유한 개체를 구현할 수 있습니다.

이 항목은 더 큰 백서의 일부입니다. 전체 문서를 읽으려면 [세계적인 Resource Manager 템플릿 고려 사항 및 입증 사례](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf)를 다운로드합니다.

## <a name="provide-standard-configuration-settings"></a>표준 구성 설정 제공
일반적인 패턴은 절대적인 유연성과 수많은 변형을 제공하는 템플릿이 아니라 알려진 구성 중에서 선택하도록 제공합니다. 실제로 샌드박스, 작음, 중간 및 대량과 같은 표준 티셔츠 크기를 선택할 수 있습니다. 티셔츠 크기의 다른 예에는 커뮤니티 버전이나 엔터프라이즈 버전 같은 제품이 있습니다. 그 외 경우에는 Map Reduce 또는 No SQL 같은 특정 워크로드에 대한 기술 구성이 있습니다.

복잡한 개체의 경우 "속성 모음"이라고도 하는 데이터 컬렉션을 포함하는 변수를 만들고 해당 데이터를 사용하여 템플릿에서 리소스 선언을 실행할 수 있습니다. 이러한 접근 방식은 고객을 위해 미리 구성된 다양한 크기의 좋은, 알려진 구성을 제공합니다. 알려진 구성이 없는 경우, 템플릿 사용자는 자체적으로 클러스터 크기, 플랫폼 리소스 제약 조건의 요소를 결정하고 그에 따라 파생되는 저장소 계정 및 기타 리소스(클러스터 크기 및 리소스 제약 조언에 따른)에 대한 분할을 식별하기 위해 계산해야 합니다. 고객을 위한 환경을 개선하는 것 외에도, 몇몇 알려진 구성은 지원하기가 쉽고 높은 수준의 밀도를 제공하는데 도움이 됩니다.

다음 예제에서는 데이터 컬렉션을 나타내기 위한 복잡한 개체를 포함하는 변수의 정의 방법을 보여 줍니다. 이 컬렉션은 가상 컴퓨터 크기, 네트워크 설정, 운영 체제 설정 및 가용성 설정에 사용되는 값을 정의합니다.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
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
    }

**tshirtSize** 변수는 매개 변수(**Small**, **Medium**, **Large**)를 통해 제공한 티셔츠 크기를 **tshirtSize** 텍스트에 연결합니다. 이 변수를 사용하여 해당 티셔츠 크기에 대한 연결된 복잡한 개체 변수를 검색합니다.

그런 다음 템플릿의 뒷부분에서 이러한 변수를 참조할 수 있습니다. 명명된 변수 및 해당 속성을 참조하는 기능이 구현되면 템플릿 구문이 단순화되며 컨텍스트를 쉽게 이해할 수 있습니다. 다음 예제에서는 위에 표시된 개체를 통해 값을 설정하여 배포할 리소스를 정의합니다. 예를 들어 디스크 크기 값은 `variables('tshirtSize').diskSize`에서 검색되지만 VM 크기는 `variables('tshirtSize').vmSize` 값을 검색하여 설정합니다. 또한 연결된 템플릿에 대한 URI는 `variables('tshirtSize').vmTemplate`값으로 설정됩니다.

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

## <a name="pass-state-to-a-template"></a>템플릿에 상태 전달
배포 중에 직접 제공한 매개 변수를 통해 템플릿으로 상태를 공유합니다.

다음 테이블에는 템플릿 내에서 일반적으로 사용되는 매개 변수가 나와 있습니다.

| 이름 | 값 | 설명 |
| --- | --- | --- |
| location |제한된 Azure 영역 목록의 문자열 |리소스가 배포되어 있는 위치 |
| storageAccountNamePrefix |문자열 |VM의 디스크가 배치될 저장소 계정의 고유 DNS 이름 |
| domainName |문자열 |공개적으로 액세스할 수 있는 jumpbox VM의 도메인 이름 형식: **{domainName}.{location}.cloudapp.com** 예: **mydomainname.westus.cloudapp.azure.com** |
| adminUsername |문자열 |VM의 사용자 이름 |
| adminPassword |문자열 |VM에 대한 암호 |
| tshirtSize |제공된 티셔츠 크기의 제한된 목록에서 가져온 문자열 |프로비전할 명명된 배율 단위 크기. 예: "Small", "Medium", "Large" |
| virtualNetworkName |문자열 |소비자가 사용하려는 가상 네트워크의 이름. |
| enableJumpbox |제한된 목록에서 가져온 문자열(enabled/disabled) |환경에 대해 jumpbox를 사용하도록 설정할지 여부를 식별하는 매개 변수입니다. 값: "enabled", "disabled" |

이전 섹션에서 사용된 **tshirtSize** 매개 변수는 다음으로 정의됩니다.

    "parameters": {
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
    }


## <a name="pass-state-to-linked-templates"></a>연결된 템플릿에 상태 전달
연결된 템플릿에 연결할 경우 정적 변수와 생성된 변수를 혼합해서 사용하게 됩니다.

### <a name="static-variables"></a>정적 변수
정적 변수는 템플릿 전체에서 사용되는 URL과 같은 기본값을 제공하는 데 종종 사용됩니다.

아래에 발췌한 다음 템플릿에서 `templateBaseUrl`은 GitHub에서 템플릿의 루트 위치를 지정합니다. 다음 줄은 기본 URL을 공유 리소스 템플릿의 알려진 이름에 연결하는 새 변수 `sharedTemplateUrl`을 작성합니다. 해당 줄 아래에서 복잡한 개체 변수는 티셔츠 크기를 저장하는 데 사용됩니다. 여기서 기본 URL은 알려진 구성 템플릿 위치에 연결되어 `vmTemplate` 속성에 저장됩니다.

이 접근 방식의 이점은 템플릿 위치가 변경된 경우 한 곳에서만 정적 변수를 변경하면 된다는 점입니다. 그러면 연결된 템플릿 전체로 전달됩니다.

    "variables": {
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
    }

### <a name="generated-variables"></a>생성된 변수
정적 변수 외에도 다양한 변수가 동적으로 생성됩니다. 이 섹션에서는 생성된 변수의 몇 가지 일반적인 유형에 대해 설명합니다.

#### <a name="tshirtsize"></a>tshirtSize
위 예제에서 생성된 이 변수는 이미 잘 알고 있을 것입니다.

#### <a name="networksettings"></a>networkSettings
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

#### <a name="availabilitysettings"></a>availabilitySettings
연결된 템플릿에서 만든 리소스는 종종 가용성 집합에 배치됩니다. 다음 예에서는 가용성 집합 이름이 지정되고, 사용할 장애 도메인 및 업데이트 도메인 수도 지정됩니다.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

여러 가용성 집합(예를 들어 마스터 노드용 하나와 데이터 노드용 하나)이 필요한 경우 이름을 접두사로 사용하거나, 여러 가용성 집합을 지정하거나, 앞서 제시된 특정 티셔츠 크기에 해당하는 변수 생성 방법 예를 따라 진행할 수 있습니다.

#### <a name="storagesettings"></a>storageSettings
저장소 세부 정보는 연결된 템플릿과 종종 공유됩니다. 아래 예제에서 *storageSettings* 개체는 저장소 계정 및 컨테이너 이름에 대한 세부 정보를 제공합니다.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### <a name="ossettings"></a>osSettings
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

#### <a name="machinesettings"></a>machineSettings
생성된 변수인 *machineSettings*는 VM을 만들기 위한 혼합된 핵심 변수를 포함하는 복잡한 개체입니다. 변수에는 관리자 사용자 이름 및 암호, VM 이름의 접두사 및 운영 체제 이미지 참조가 포함됩니다.

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

#### <a name="vmscripts"></a>vmScripts
*vmScripts* 개체에는 외부 및 내부 참조를 포함하여 VM 인스턴스에서 다운로드 및 실행할 스크립트에 대한 세부 정보가 포함됩니다. 외부 참조는 인프라를 포함합니다.
내부 참조에는 설치된 소프트웨어와 구성이 포함됩니다.

*scriptsToDownload* 속성을 사용하여 VM으로 다운로드할 스크립트 목록을 표시합니다. 이 개체에는 다양한 유형의 동작에 대한 명령줄 인수의 참조도 포함됩니다. 이러한 동작에는 각 개별 노드에 대한 기본 설치, 모든 노드를 배포한 후에 실행되는 설치 및 지정된 템플릿에서만 사용할 수 있는 추가 스크립트의 실행이 포함됩니다.

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


## <a name="return-state-from-a-template"></a>템플릿에서 상태 반환
템플릿으로 데이터를 전달할 뿐만 아니라 호출 템플릿과 다시 데이터를 공유할 수도 있습니다. 연결된 템플릿의 **출력** 섹션에서는 원본 템플릿에서 사용될 수 있는 키/값 쌍을 제공할 수 있습니다.

다음 예제에서는 연결된 템플릿에서 생성된 개인 IP 주소를 전달하는 방법을 보여 줍니다.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

주 템플릿 내에서 다음 구문을 사용하여 해당 데이터를 사용할 수 있습니다.

    "[reference('master-node').outputs.masterip.value]"

주 템플릿의 출력 섹션 또는 리소스 섹션에서 이 식을 사용할 수 있습니다. 변수 섹션은 런타임 상태에 의존하므로 이 식을 사용할 수 없습니다. 주 템플릿에서 이 값을 반환하려면 다음을 사용합니다.

    "outputs": {
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }

연결된 템플릿의 출력 섹션을 사용하여 가상 컴퓨터에 대한 데이터 디스크를 반환하는 예제는 [가상 컴퓨터에 대한 여러 데이터 디스크 만들기](resource-group-create-multiple.md)를 참조하세요.

## <a name="define-authentication-settings-for-virtual-machine"></a>가상 컴퓨터에 대한 인증 설정 정의
위에 표시된 동일한 패턴을 구성 설정에 사용하여 가상 컴퓨터의 인증 설정을 지정할 수 있습니다. 인증 형식을 전달하기 위한 매개 변수를 만듭니다.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

다른 인증 형식에 대한 변수 및 매개 변수 값에 따라 이 배포에 사용되는 형식을 저장할 변수를 추가합니다.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

가상 컴퓨터를 정의할 때 **osProfile** 을 사용자가 만든 변수로 설정합니다.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## <a name="next-steps"></a>다음 단계
* 템플릿의 섹션에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)
* 템플릿 내에서 사용할 수 있는 모든 함수는 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)



<!--HONumber=Dec16_HO2-->


