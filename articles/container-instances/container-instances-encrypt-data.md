---
title: 배포 데이터 암호화
description: 컨테이너 인스턴스 리소스에 대해 유지되는 데이터의 암호화와 고객 관리형 키로 해당 데이터를 암호화하는 방법을 알아봅니다.
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 23c81aeab3bf6e9ee7f2d89fbdf8def20dab4aa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790872"
---
# <a name="encrypt-deployment-data"></a>배포 데이터 암호화

클라우드에서 ACI(Azure Container Instances) 리소스를 실행하는 경우 ACI 서비스는 컨테이너와 관련된 데이터를 수집하고 유지합니다. ACI는 해당 데이터를 클라우드에 유지하는 경우 자동으로 암호화합니다. 이 암호화는 데이터를 보호하여 조직의 보안 및 규정 준수 약정을 준수하는 데 도움이 됩니다. 또한 ACI는 고유한 키로 데이터를 암호화하여 ACI 배포와 관련된 데이터를 더 효율적으로 제어할 수 있는 옵션도 제공합니다.

## <a name="about-aci-data-encryption"></a>ACI 데이터 암호화 정보 

ACI의 데이터는 256비트 AES 암호화를 사용하여 암호화되고 암호 해독됩니다. 모든 ACI 배포에서 사용하도록 설정되어 있으므로 배포나 컨테이너를 수정하지 않고도 이 암호화를 활용할 수 있습니다. 해당 데이터는 배포에 대한 메타데이터, 환경 변수, 컨테이너에 전달되는 키, 컨테이너가 중지된 후 유지되어 계속 볼 수 있는 로그 등을 포함합니다. 암호화는 컨테이너 그룹 성능에 영향을 주지 않으며 암호화의 추가 비용은 없습니다.

## <a name="encryption-key-management"></a>암호화 키 관리

Microsoft 관리형 키를 컨테이너 데이터 암호화에 사용할 수도 있고 자체 키를 사용하여 암호화를 관리할 수도 있습니다. 다음 표에서는 해당 옵션을 비교합니다. 

|    |    Microsoft 관리형 키     |     고객 관리형 키     |
|----|----|----|
|    **암호화/암호 해독 작업**    |    Azure    |    Azure    |
|    **키 스토리지**    |    Microsoft 키 저장소    |    Azure Key Vault    |
|    **키 회전 책임**    |    Microsoft    |    고객    |
|    **키 액세스**    |    Microsoft 전용    |    Microsoft, 고객    |

나머지 내용에서는 사용자 키(고객 관리형 키)로 ACI 배포 데이터를 암호화하는 데 필요한 단계를 다룹니다. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>고객 관리형 키를 사용하여 데이터 암호화

### <a name="create-service-principal-for-aci"></a>ACI의 서비스 주체 만들기

첫 번째 단계에서는 [Azure 테넌트](../active-directory/develop/quickstart-create-new-tenant.md)에 Azure Container Instances 서비스에 대한 사용 권한을 부여하기 위한 서비스 주체가 할당되어 있는지 확인합니다. 

> [!IMPORTANT]
> 다음 명령을 실행하여 서비스 주체를 만들려면 테넌트에서 서비스 주체를 만들 수 있는 권한이 있는지 확인하세요.
>

다음 CLI 명령은 Azure 환경에서 ACI SP를 설정합니다.

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

이 명령 실행의 출력에 “displayName”이 “Azure Container Instance 서비스”로 설정된 서비스 주체가 표시되어야 합니다.

서비스 주체를 만들 수 없는 경우 다음을 수행합니다.
* 테넌트에서 이 작업을 수행할 수 있는 권한이 있는지 확인
* ACI에 배포할 서비스 주체가 테넌트에 이미 있는지 확인. 이렇게 하려면 `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9`를 실행하고 대신 해당 서비스 주체를 사용합니다.

### <a name="create-a-key-vault-resource"></a>Key Vault 리소스 만들기

[Azure Portal](../key-vault/general/quick-create-portal.md), [Azure CLI](../key-vault/general/quick-create-cli.md) 또는 [Azure PowerShell](../key-vault/general/quick-create-powershell.md)을 사용하여 Azure Key Vault를 만듭니다.

키 자격 증명 모음의 속성은 다음 지침을 따릅니다. 
* Name: 고유 이름은 필수입니다. 
* 구독: 구독을 선택합니다.
* 리소스 그룹에서 기존 리소스 그룹을 선택하거나, 새로 만들고 리소스 그룹 이름을 입력합니다.
* 위치 풀 다운 메뉴에서 위치를 선택합니다.
* 다른 옵션은 기본값으로 두거나 추가 요구 사항에 따라 선택할 수 있습니다.

> [!IMPORTANT]
> 고객 관리형 키를 사용하여 ACI 배포 템플릿을 암호화할 때는 키 자격 증명 모음에서 일시 삭제와 제거 안 함의 두 가지 속성을 설정하는 것이 좋습니다. 두 속성은 기본적으로 설정되어 있지 않지만 새로운 또는 기존 키 자격 증명 모음에서 PowerShell 또는 Azure CLI를 사용하여 설정할 수 있습니다.

### <a name="generate-a-new-key"></a>새로운 키 생성 

키 자격 증명 모음을 만든 후 Azure Portal에서 리소스로 이동합니다. 리소스 블레이드의 왼쪽 탐색 메뉴에 있는 설정에서 **키** 를 클릭합니다. “키”의 보기에서 “생성/가져오기”를 클릭하여 새 키를 생성합니다. 이 키의 고유한 이름을 사용하고 요구 사항에 따라 기타 기본 설정을 지정합니다. 

![새로운 키 생성](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>액세스 정책 설정

ACI 서비스에서 키에 액세스할 수 있도록 하는 새 액세스 정책을 만듭니다.

* 키가 생성되면 키 자격 증명 모음 리소스 블레이드로 돌아가서 설정에서 **액세스 정책** 을 클릭합니다.
* 키 자격 증명 모음의 “액세스 정책” 페이지에서 **액세스 정책 추가** 를 클릭합니다.
* **가져오기**, **키 래핑 해제** 를 포함하도록 ‘키 권한’을 설정합니다. ![키 권한 설정](./media/container-instances-encrypt-data/set-key-permissions.png)
* ‘보안 주체 선택’에서 **Azure Container Instance 서비스** 를 선택합니다.
* 아래쪽에서 **추가** 를 클릭합니다. 

이제 액세스 정책이 키 자격 증명 모음의 액세스 정책에 표시됩니다.

![새 액세스 정책](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>JSON 배포 템플릿 수정

> [!IMPORTANT]
> 고객 관리형 키로 배포 데이터 암호화는 현재 출시 중인 최신 API 버전(2019-12-01)에서 사용할 수 있습니다. 배포 템플릿에 이 API 버전을 지정하세요. 관련하여 문제가 있는 경우 Azure 지원에 문의하세요.

키 자격 증명 모음 키와 액세스 정책을 설정했으면 ACI 배포 템플릿에 다음 속성을 추가합니다. 템플릿을 사용하여 ACI 리소스를 배포하는 방법에 대한 자세한 내용은 [Tutorial: Deploy a multi-container group using a Resource Manager template](./container-instances-multi-container-group.md)(자습서: 에서 Resource Manager 템플릿을 사용하여 다중 컨테이너 그룹 배포)을 참조하세요. 
* `resources` 아래에서 `apiVersion`를 `2019-12-01`로 설정합니다.
* 배포 템플릿의 컨테이너 그룹 속성 섹션에서 다음 값을 포함하는 `encryptionProperties`를 추가합니다.
  * `vaultBaseUrl`: 키 자격 증명 모음의 DNS 이름. Portal에서 키 자격 증명 모음 리소스의 개요 블레이드에서 확인할 수 있습니다.
  * `keyName`: 이전에 생성된 키의 이름
  * `keyVersion`: 키의 현재 버전. 키 자체를 클릭하여 확인할 수 있습니다(키 자격 증명 모음 리소스의 설정 섹션에 있는 “키”에서).
* 컨테이너 그룹 속성 아래에 `Standard` 값을 사용하여 `sku` 속성을 추가합니다. `sku` 속성은 API 버전 2019-12-01에서 필수입니다.

다음 템플릿 코드 조각에서는 배포 데이터를 암호화하는 이러한 추가 속성을 보여 줍니다.

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

다음은 [Tutorial: Deploy a multi-container group using a Resource Manager template](./container-instances-multi-container-group.md)(자습서: Resource Manager 템플릿을 사용하여 다중 컨테이너 그룹 배포)에서 가져온 전체 템플릿입니다. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>리소스 배포

데스크톱에서 템플릿 파일을 만들고 편집했으면 파일을 끌어서 Cloud Shell 디렉터리로 업로드할 수 있습니다. 

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az deployment group create][az-deployment-group-create] 명령을 사용하여 템플릿을 배포합니다.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다. 배포가 완료되면 ACI 서비스에서 유지하는 배포와 관련된 모든 데이터가 제공한 키로 암호화됩니다.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group/#az_deployment_group_create
