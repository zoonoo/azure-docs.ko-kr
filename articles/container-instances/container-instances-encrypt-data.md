---
title: 배포 데이터 암호화
description: 컨테이너 인스턴스 리소스에 대해 유지되는 데이터의 암호화 및 고객 관리 키로 데이터를 암호화하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080363"
---
# <a name="encrypt-deployment-data"></a>배포 데이터 암호화

클라우드에서 Azure 컨테이너 인스턴스(ACI) 리소스를 실행할 때 ACI 서비스는 컨테이너와 관련된 데이터를 수집하고 유지합니다. ACI는 클라우드에 유지될 때 이 데이터를 자동으로 암호화합니다. 이 암호화는 조직의 보안 및 규정 준수 약정을 충족하는 데 도움이 되는 데이터를 보호합니다. 또한 ACI는 고유한 키로 이 데이터를 암호화할 수 있는 옵션을 제공하므로 ACI 배포와 관련된 데이터를 보다 잘 제어할 수 있습니다.

## <a name="about-aci-data-encryption"></a>ACI 데이터 암호화 정보 

ACI의 데이터는 256비트 AES 암호화를 사용하여 암호화되고 해독됩니다. 모든 ACI 배포에 대해 사용할 수 있으며 이 암호화를 활용하기 위해 배포 또는 컨테이너를 수정할 필요가 없습니다. 여기에는 배포, 환경 변수, 컨테이너에 전달되는 키 및 컨테이너가 중지된 후에도 유지되는 로그에 대한 메타데이터가 포함되므로 계속 볼 수 있습니다. 암호화는 컨테이너 그룹 성능에 영향을 주지 않으며 암호화에 대한 추가 비용이 없습니다.

## <a name="encryption-key-management"></a>암호화 키 관리

컨테이너 데이터의 암호화를 위해 Microsoft에서 관리하는 키를 사용하거나 자체 키로 암호화를 관리할 수 있습니다. 다음 표는 다음 옵션을 비교합니다. 

|    |    마이크로소프트에서 관리되는 키     |     고객 관리형 키     |
|----|----|----|
|    암호화/암호 해독 작업    |    Azure    |    Azure    |
|    키 스토리지    |    마이크로소프트 키 스토어    |    Azure Key Vault    |
|    키 회전 책임    |    Microsoft    |    Customer    |
|    키 액세스    |    마이크로소프트 만    |    마이크로소프트, 고객    |

문서의 나머지 부분에서는 ACI 배포 데이터를 키(고객 관리 키)로 암호화하는 데 필요한 단계를 다룹니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>고객 관리 키로 데이터 암호화

### <a name="create-service-principal-for-aci"></a>ACI에 대한 서비스 주체 만들기

첫 번째 단계는 Azure 컨테이너 인스턴스 서비스에 대한 사용 권한을 부여하기 위해 할당된 서비스 주체가 Azure [테넌트가](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 있는지 확인하는 것입니다. 

> [!IMPORTANT]
> 다음 명령을 실행하고 서비스 주체를 성공적으로 만들려면 테넌트에서 서비스 주체를 만들 수 있는 권한이 있는지 확인합니다.
>

다음 CLI 명령은 Azure 환경에서 ACI SP를 설정합니다.

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

이 명령을 실행한 출력에는 "displayName": "Azure 컨테이너 인스턴스 서비스"로 설정된 서비스 주체가 표시됩니다.

서비스 주체를 성공적으로 만들 수 없는 경우:
* 테넌트에서 그렇게 할 수 있는 권한이 있는지 확인합니다.
* 서비스 주체가 테넌트에 ACI에 배포할 수 있는지 확인합니다. 대신 해당 서비스 `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` 주체를 실행하고 사용하여 이 작업을 수행할 수 있습니다.

### <a name="create-a-key-vault-resource"></a>Key Vault 리소스 만들기

[Azure 포털,](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)또는 [PowerShell을](https://docs.microsoft.com/azure/key-vault/quick-create-powershell)사용하여 Azure 키 자격 증명 모음을 만듭니다. 

키 자격 증명 모음의 속성에 대 한 다음 지침을 사용 합니다. 
* 이름: 고유 이름은 필수입니다. 
* 구독: 구독을 선택합니다.
* 리소스 그룹에서 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만들고 리소스 그룹 이름을 입력합니다.
* 위치 풀 다운 메뉴에서 위치를 선택합니다.
* 다른 옵션을 기본값으로 설정하거나 추가 요구 사항에 따라 선택할 수 있습니다.

> [!IMPORTANT]
> 고객 관리 키를 사용하여 ACI 배포 템플릿을 암호화하는 경우 키 자격 증명 모음에 다음 두 속성인 소프트 삭제 및 제거 안 식이라는 두 가지 속성을 설정하는 것이 좋습니다. 이러한 속성은 기본적으로 활성화되어 있지 않지만 새 키 또는 기존 키 자격 증명 모음에서 PowerShell 또는 Azure CLI를 사용하여 사용할 수 있습니다.

### <a name="generate-a-new-key"></a>새 키 생성 

키 자격 증명 모음이 만들어지면 Azure 포털의 리소스로 이동합니다. 리소스 블레이드의 왼쪽 탐색 메뉴에서 설정에서 **키를 클릭합니다.** "키"에 대한 보기에서 "생성/가져오기"를 클릭하여 새 키를 생성합니다. 이 키에 고유한 이름 및 요구 사항에 따라 다른 기본 설정을 사용합니다. 

![새 키 생성](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>액세스 정책 설정

ACI 서비스가 키에 액세스할 수 있도록 허용하는 새 액세스 정책을 만듭니다.

* 키가 생성되면 설정에서 키 볼트 리소스 블레이드에서 액세스 **정책을**클릭합니다.
* 키 자격 증명 모음의 "액세스 정책" 페이지에서 **액세스 정책 추가를**클릭합니다.
* 키 집합 키 사용 권한 **얻기** 및 **래핑 해제를** ![포함하도록 *키 사용* 권한 설정](./media/container-instances-encrypt-data/set-key-permissions.png)
* *보안 주체를 선택:* **Azure 컨테이너 인스턴스 서비스를** 선택합니다.
* 하단에 **추가를** 클릭합니다. 

이제 키 자격 증명 모음의 액세스 정책에 액세스 정책이 표시됩니다.

![새 액세스 정책](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>JSON 배포 템플릿 수정

> [!IMPORTANT]
> 고객 관리 키로 배포 데이터를 암호화하는 것은 현재 출시 중인 최신 API 버전(2019-12-01)에서 사용할 수 있습니다. 배포 템플릿에서 이 API 버전을 지정합니다. 이 문제가 있는 경우 Azure 지원에 문의하십시오.

키 자격 증명 모음 키 및 액세스 정책이 설정되면 ACI 배포 템플릿에 다음 속성을 추가합니다. [자습서: 리소스 관리자 템플릿을 사용하여 다중 컨테이너 그룹을 배포하는 방법에](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)대해 자세히 알아보기 
* 에서 `resources`으로 `apiVersion` `2019-12-01`설정합니다.
* 배포 템플릿의 컨테이너 그룹 속성 섹션 `encryptionProperties`아래에 다음 값을 포함하는 를 추가합니다.
  * `vaultBaseUrl`: 키 볼트의 DNS 이름은 포털의 키 볼트 리소스개요 블레이드에서 찾을 수 있습니다.
  * `keyName`: 이전에 생성된 키의 이름
  * `keyVersion`: 키의 현재 버전입니다. 키 자체를 클릭하면 찾을 수 있습니다(키 자격 증명 모음 리소스의 설정 섹션의 "키" 아래).
* 컨테이너 그룹 속성 아래에 `sku` 값이 `Standard`있는 속성을 추가합니다. 이 `sku` 속성은 API 버전 2019-12-01에 필요합니다.

다음 템플릿 코드 조각은 배포 데이터를 암호화하기 위한 이러한 추가 속성을 보여 주며 다음과 같은 추가 속성을 보여 주며 다음과 같은 경우를 보여 줄 수 있습니다.

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

다음은 자습서의 템플릿에서 수정된 전체 [템플릿입니다.](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group) 

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

데스크톱에서 템플릿 파일을 만들고 편집한 경우 파일을 드래그하여 Cloud Shell 디렉터리에 업로드할 수 있습니다. 

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az group deployment create][az-group-deployment-create] 명령을 사용하여 템플릿을 배포합니다.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다. 배포가 완료되면 ACI 서비스에서 유지되는 데이터와 관련된 모든 데이터는 제공한 키로 암호화됩니다.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
