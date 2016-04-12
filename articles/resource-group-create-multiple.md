<properties
   pageTitle="리소스의 여러 인스턴스를 배포 | Microsoft Azure"
   description="Azure 리소스 관리자 템플릿에서 복사 작업 및 배열을 사용하여 여러 번 반복하는 방법을 설명합니다."
   services="azure-resource-manager"
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
   ms.date="03/09/2016"
   ms.author="tomfitz"/>

# Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기

이 항목에서는 Azure 리소스 관리자 템플릿을 반복하여 리소스의 여러 인스턴스를 만드는 방법을 보여 줍니다.

## copy, copyIndex 및 length

여러 번 만들려는 리소스 내에서 반복 횟수를 지정하는 **copy** 개체를 정의할 수 있습니다. copy는 다음 형식을 갖습니다.

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

아래의 concat 함수에서 볼 수 있듯이 **copyIndex()** 함수로 현재 반복 값에 액세스할 수 있습니다.

    [concat('examplecopy-', copyIndex())]

값의 배열에서 여러 리소스를 만들 때 **lenth** 함수를 사용하여 개수를 지정할 수 있습니다. length 함수에 대한 매개 변수로 배열을 제공합니다.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## 이름에 인덱스 값 사용

복사 작업을 사용하여 증분 인덱스를 기준으로 고유하게 명명되는 리소스의 여러 인스턴스를 만들 수 있습니다. 예를 들어 배포되는 각 리소스 이름의 끝에 고유 번호를 추가할 수 있습니다. 다음 이름의 웹 사이트 3개를 배포하려면

- examplecopy-0
- examplecopy-1
- examplecopy-2.

다음 템플릿을 사용합니다.

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## 인덱스 값 오프셋

인덱스 값이 0에서 2로 이동하는 이전 예제에서 알 수 있습니다. 인덱스 값을 오프셋하여 값을 **copyIndex(1)**과 같은 **copyIndex()** 함수를 전달할 수 있습니다. 수행할 반복 수는 복사 요소에서 지정되지만 copyIndex의 값이 지정된 값 만큼 오프셋됩니다. 따라서 이전 예제와 같은 서식 파일을 사용하지만 **copyIndex(1)**를 지정하여 다음과 같이 명명된 3개의 웹 사이트를 배포합니다.

- examplecopy-1
- examplecopy-2
- examplecopy-3

## 배열 사용
   
복사 작업은 배열의 각 요소를 반복할 수 있으므로 배열을 사용할 때 특히 유용합니다. 다음 이름의 웹 사이트 3개를 배포하려면

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

다음 템플릿을 사용합니다.

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

물론, 복사본 개수를 배열 길이가 아닌 다른 값으로 설정합니다. 예를 들어 많은 값이 포함된 배열을 만든 다음 배포할 배열 요소 수를 지정하는 매개 변수 값을 전달할 수 있습니다. 이 경우 첫 번째 예제와 같이 복사본 개수를 설정합니다.

## 루프의 리소스에 따라 달라짐

**dependsOn** 요소를 사용하여 어떤 리소스를 다른 리소스 다음에 배포하도록 지정할 수 있습니다. 루프의 리소스 컬렉션에 따라 달라지는 리소스를 배포해야 하는 경우 **dependsOn** 요소에 copy 루프의 이름을 제공하는 방식으로 사용할 수 있습니다. 다음 예제에서는 가상 컴퓨터를 배포하기 전에 저장소 계정 3개를 배포하는 방법을 보여줍니다. 전체 가상 컴퓨터 정의는 표시되지 않습니다. 참고로 copy 요소의 **name**은 **storagecopy**로 설정되고 가상 컴퓨터에 대한 **dependsOn** 요소는 **storagecopy**로 설정되었습니다.

    {
	    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "contentVersion": "1.0.0.0",
	    "parameters": {},
	    "resources": [
	        {
		        "apiVersion": "2015-06-15",
		        "type": "Microsoft.Storage/storageAccounts",
		        "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
		        "location": "[resourceGroup().location]",
		        "properties": {
                    "accountType": "Standard_LRS"
            	 },
		        "copy": { 
         	        "name": "storagecopy", 
         	        "count": 3 
      		    }
	        },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
	    ],
	    "outputs": {}
    }

## 중첩된 리소스에 대해 반복합니다.

중첩된 리소스에 대해 복사 반복을 사용할 수 없습니다. 일반적으로 다른 리소스 내에 중첩되어 있다고 정의하는 리소스의 여러 인스턴스를 만드는 경우 해당 리소스를 최상위 리소스로 대신 만들어 **형식** 및 **이름** 속성을 통해 부모 리소스와의 관계를 정의해야 합니다.

예를 들어, 데이터 집합을 데이터 팩터리 내에 중첩된 리소스로 정의한다고 가정합니다.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
데이터 집합의 여러 인스턴스를 만들려면 다음과 같이 템플릿을 변경해야 합니다. 정규화된 형식 및 이름이 데이터 팩터리 이름을 포함하는 것을 확인합니다.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## 가상 컴퓨터에 대한 여러 데이터 디스크 만들기

일반적인 시나리오는 가상 컴퓨터에 대해 여러 데이터 디스크를 만드는 것입니다. **dataDisks**는 자체 리소스 형식이 아니라 가상 컴퓨터에 대한 속성이므로 데이터 디스크에 대해 **복사**를 사용할 수 없습니다. **복사**는 리소스에만 작동합니다. 대신 필요한 수만큼의 데이터 디스크를 정의하고 지정된 수의 데이터 디스크가 있는 배열을 출력하는 연결된 템플릿을 만듭니다. 배포 템플릿에서 이 템플릿으로 연결한 후 반환할 데이터 디스크 수를 전달합니다. 배포 템플릿에서 **dataDisks** 속성을 연결된 템플릿의 출력 값으로 설정합니다.

이 패턴의 전체 예제는 [동적 데이터 디스크를 선택하여 VM 만들기](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) 템플릿에 나와 있습니다.

배포 템플릿의 관련 섹션은 아래에 나와 있습니다. 많은 수의 데이터 디스크를 동적으로 만드는 작업과 관련된 섹션을 중점적으로 제공하기 위해 많은 템플릿을 제거했습니다. **numDataDisks** 매개 변수는 만들려는 디스크 수를 제공하는 데 사용합니다. **diskSelection**이라는 연결된 템플릿이 리소스 섹션에 지정됩니다. 이 연결된 템플릿의 출력은 가상 컴퓨터의 **dataDisks** 속성에 할당됩니다.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        ...
        "numDataDisks": {
          "type": "string",
          "allowedValues": [
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
            "10",
            "11",
            "12",
            "13",
            "14",
            "15",
            "16",
            "32"
          ],
          "metadata": {
            "description": "This parameter allows the user to select the number of disks they want"
          }
        }
      },
      "variables": {
        "artifactsLocation": "https://raw.githubusercontent.com/singhkay/azure-quickstart-templates/master/201-vm-dynamic-data-disks-selection/", 
        "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
        "sizeOfDataDisksInGB": 100,
        "diskCaching": "ReadWrite",
        ...
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "diskSelection",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "uri": "[concat(variables('artifactsLocation'), 'disksSelector', '.json')]",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "numDataDisks": {
                "value": "[parameters('numDataDisks')]"
              },
              "diskStorageAccountName": {
                "value": "[variables('storageAccountName')]"
              },
              "diskCaching": {
                "value": "[variables('diskCaching')]"
              },
              "diskSizeGB": {
                "value": "[variables('sizeOfDataDisksInGB')]"
              }
            }
          }
        },
        ...
        {
          "type": "Microsoft.Compute/virtualMachines",
          "properties": {
            "storageProfile": {
              ...
              "dataDisks": "[reference('diskSelection').outputs.dataDiskArray.value]"
            },
            ...
          }
        }
      ]
    }

연결된 템플릿은 반환할 배열을 정의합니다. 아래에 나와 있는 템플릿은 3~32개 사이의 반복되는 디스크 정의를 생략하지만 실제 템플릿에서는 이러한 모든 정의를 포함해야 합니다. 32개보다 많은 데이터 디스크가 필요하면 패턴을 계속할 수 있습니다. 이 템플릿을 통해 배포되는 리소스는 없습니다. 대신 데이터 디스크를 정의하는 요청된 수의 개체가 있는 배열을 반환합니다.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "string",
      "allowedValues": [
        "1",
        "2",
        "3",
        "4",
        "5",
        "6",
        "7",
        "8",
        "9",
        "10",
        "11",
        "12",
        "13",
        "14",
        "15",
        "16",
        "32"
      ],
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    },
    "diskStorageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the storage account where the data disks are stored"
      }
    },
    "diskCaching": {
      "type": "string",
      "allowedValues": [
        "None",
        "ReadOnly",
        "ReadWrite"
      ],
      "metadata": {
        "description": "Caching type for the data disks"
      }
    },
    "diskSizeGB": {
      "type": "int",
      "minValue": 1,
      "maxValue": 1023,
      "metadata": {
        "description": "Size of the data disks"
      }
    }
  },
  "variables": {
    "disksArray": {
      "1": "[variables('dataDisks')['1']]",
      "2": "[concat(variables('dataDisks')['1'], variables('dataDisks')['2'])]",
      "3": "[concat(variables('dataDisks')['1'], variables('dataDisks')['2'], variables('dataDisks')['3'])]",
      "4": "[variables('diskDeltas')['4delta']]",
      "5": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'])]",
      "6": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'], variables('dataDisks')['6'])]",
      "7": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'], variables('dataDisks')['6'], variables('dataDisks')['7'])]",
      "8": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'])]",
      "9": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'])]",
      "10": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'], variables('dataDisks')['10'])]",
      "11": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'], variables('dataDisks')['10'], variables('dataDisks')['11'])]",
      "12": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'])]",
      "13": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'])]",
      "14": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'], variables('dataDisks')['14'])]",
      "15": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'], variables('dataDisks')['14'], variables('dataDisks')['15'])]",
      "16": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('diskDeltas')['16delta'])]",
      "32": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('diskDeltas')['16delta'], variables('diskDeltas')['32delta'])]"
    },
    "dataDisks": {
      "1": [
        {
          "name": "datadisk1",
          "lun": 0,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      "2": [
        {
          "name": "datadisk2",
          "lun": 1,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      "3": [
        {
          "name": "datadisk3",
          "lun": 2,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      ...
      "32": [
        {
          "name": "datadisk32",
          "lun": 31,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk32.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ]
    },
    "_comment2": "The delta arrays below build the difference from 0 to 4, 4 to 8, 8 to 12 disks and so on",
    "diskDeltas": {
      "4delta": [
        "[variables('dataDisks')['1'][0]]",
        "[variables('dataDisks')['2'][0]]",
        "[variables('dataDisks')['3'][0]]",
        "[variables('dataDisks')['4'][0]]"
      ],
      "8delta": [
        "[variables('dataDisks')['5'][0]]",
        "[variables('dataDisks')['6'][0]]",
        "[variables('dataDisks')['7'][0]]",
        "[variables('dataDisks')['8'][0]]"
      ],
      "12delta": [
        "[variables('dataDisks')['9'][0]]",
        "[variables('dataDisks')['10'][0]]",
        "[variables('dataDisks')['11'][0]]",
        "[variables('dataDisks')['12'][0]]"
      ],
      "16delta": [
        "[variables('dataDisks')['13'][0]]",
        "[variables('dataDisks')['14'][0]]",
        "[variables('dataDisks')['15'][0]]",
        "[variables('dataDisks')['16'][0]]"
      ],
      "32delta": [
        "[variables('dataDisks')['17'][0]]",
        "[variables('dataDisks')['18'][0]]",
        "[variables('dataDisks')['19'][0]]",
        "[variables('dataDisks')['20'][0]]",
        "[variables('dataDisks')['21'][0]]",
        "[variables('dataDisks')['22'][0]]",
        "[variables('dataDisks')['23'][0]]",
        "[variables('dataDisks')['24'][0]]",
        "[variables('dataDisks')['25'][0]]",
        "[variables('dataDisks')['26'][0]]",
        "[variables('dataDisks')['27'][0]]",
        "[variables('dataDisks')['28'][0]]",
        "[variables('dataDisks')['29'][0]]",
        "[variables('dataDisks')['30'][0]]",
        "[variables('dataDisks')['31'][0]]",
        "[variables('dataDisks')['32'][0]]"
      ]
    }
  },
  "resources": [],
  "outputs": {
    "dataDiskArray": {
      "type": "array",
      "value": "[variables('disksArray')[parameters('numDataDisks')]]"
    }
  }
}

```

## 다음 단계
- 템플릿 섹션에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)을 참조하세요.
- 템플릿에서 사용할 수 있는 모든 함수는 [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)를 참조하세요.
- 템플릿 배포 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_0316_2016-->