---
title: 아티팩트 전송
description: Azure storage 계정을 사용 하 여 전송 파이프라인을 만들어 한 컨테이너 레지스트리에서 다른 컨테이너로 이미지 또는 기타 아티팩트의 컬렉션 전송
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: fd2cee972ef173853572b871bc80b92b28c505cd
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932603"
---
# <a name="transfer-artifacts-to-another-registry"></a>다른 레지스트리에 아티팩트 전송

이 문서에서는 한 Azure container registry에서 다른 레지스트리로 이미지 또는 기타 레지스트리 아티팩트의 컬렉션을 전송 하는 방법을 보여 줍니다. 원본 및 대상 레지스트리는 동일 하거나 다른 구독, Active Directory 테 넌 트, Azure 클라우드 또는 물리적으로 분리 된 클라우드에 있을 수 있습니다. 

아티팩트를 전송 하려면 [blob storage](../storage/blobs/storage-blobs-introduction.md)를 사용 하 여 두 레지스트리 간에 아티팩트를 복제 하는 *전송 파이프라인* 을 만듭니다.

* 원본 레지스트리의 아티팩트를 원본 저장소 계정의 blob으로 내보냅니다. 
* Blob이 원본 저장소 계정에서 대상 저장소 계정으로 복사 됩니다.
* 대상 저장소 계정의 blob을 대상 레지스트리에서 아티팩트로 가져옵니다. 대상 저장소에서 아티팩트 blob이 업데이트 될 때마다 트리거되도록 가져오기 파이프라인을 설정할 수 있습니다.

전송은 물리적으로 연결이 끊어진 클라우드의 두 Azure 컨테이너 레지스트리 간에 콘텐츠를 복사 하는 데 이상적 이며 각 클라우드의 저장소 계정으로 조정. Docker 허브 및 기타 클라우드 공급 업체를 포함 하 여 연결 된 클라우드의 컨테이너 레지스트리에서 이미지를 복사 하려는 경우 [이미지 가져오기를](container-registry-import-images.md) 권장 합니다.

이 문서에서는 Azure Resource Manager 템플릿 배포를 사용 하 여 전송 파이프라인을 만들고 실행 합니다. Azure CLI는 저장소 암호와 같은 연결 된 리소스를 프로 비전 하는 데 사용 됩니다. Azure CLI 버전 2.2.0 이상을 권장 합니다. CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 계층](container-registry-skus.md)을 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* **컨테이너 레지스트리** -전송할 아티팩트가 있는 기존 원본 레지스트리 및 대상 레지스트리가 필요 합니다. ACR 전송은 물리적으로 연결이 끊어진 클라우드에서 이동 하기 위한 것입니다. 테스트를 위해 원본 및 대상 레지스트리는 동일 하거나 다른 Azure 구독, Active Directory 테 넌 트 또는 클라우드에 있을 수 있습니다. 

   레지스트리를 만들어야 하는 경우 [빠른 시작: Azure CLI을 사용 하 여 개인 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조 하세요. 
* **저장소 계정** -구독 및 사용자가 선택한 위치에 원본 및 대상 저장소 계정을 만듭니다. 테스트를 위해 원본 및 대상 레지스트리로 동일한 구독 또는 구독을 사용할 수 있습니다. 클라우드 간 시나리오의 경우 일반적으로 각 클라우드에서 별도의 저장소 계정을 만듭니다. 

  필요한 경우 [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) 또는 다른 도구를 사용 하 여 저장소 계정을 만듭니다. 

  각 계정에서 아티팩트 전송을 위한 blob 컨테이너를 만듭니다. 예를 들어 *transfer*라는 컨테이너를 만듭니다. 두 개 이상의 전송 파이프라인이 동일한 저장소 계정을 공유할 수 있지만 다른 저장소 컨테이너 범위를 사용 해야 합니다.
* **키 자격 증명 모음** -키 자격 증명 모음은 원본 및 대상 저장소 계정에 액세스 하는 데 사용 되는 SAS 토큰 암호를 저장 하는 데 필요 원본 및 대상 레지스트리와 동일한 Azure 구독 또는 구독에 원본 및 대상 키 자격 증명 모음을 만듭니다. 데모용으로이 문서에 사용 된 템플릿과 명령은 원본 및 대상 키 자격 증명 모음이 각각 원본 및 대상 레지스트리와 동일한 리소스 그룹에 있는 것으로 가정 합니다. 이러한 공용 리소스 그룹 사용은 필요 하지 않지만이 문서에서 사용 되는 템플릿 및 명령을 단순화 합니다.

   필요한 경우 [Azure CLI](../key-vault/secrets/quick-create-cli.md) 또는 다른 도구를 사용 하 여 키 자격 증명 모음을 만듭니다.

* **환경 변수** -이 문서의 예에는 원본 및 대상 환경에 대해 다음과 같은 환경 변수를 설정 합니다. 모든 예제는 Bash 셸에 대해 서식 지정 됩니다.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>시나리오 개요

레지스트리 간에 이미지를 전송 하기 위해 다음 세 개의 파이프라인 리소스를 만듭니다. 모두 PUT 작업을 사용 하 여 생성 됩니다. 이러한 리소스는 *원본* 및 *대상* 레지스트리 및 저장소 계정에 대해 작동 합니다. 

저장소 인증은 키 자격 증명 모음에서 비밀으로 관리 되는 SAS 토큰을 사용 합니다. 파이프라인은 관리 되는 id를 사용 하 여 자격 증명 모음에서 암호를 읽습니다.

* **[Exportpipeline](#create-exportpipeline-with-resource-manager)** - *원본* 레지스트리와 저장소 계정에 대 한 높은 수준의 정보를 포함 하는 오래 지속 되는 리소스입니다. 이 정보에는 원본 저장소 blob 컨테이너 URI와 원본 SAS 토큰을 관리 하는 주요 자격 증명 모음이 포함 됩니다. 
* **[Importpipeline](#create-importpipeline-with-resource-manager)** - *대상* 레지스트리 및 저장소 계정에 대 한 고급 정보를 포함 하는 오래 지속 되는 리소스입니다. 이 정보에는 대상 SAS 토큰을 관리 하는 대상 저장소 blob 컨테이너 URI 및 키 자격 증명 모음이 포함 됩니다. 가져오기 트리거는 기본적으로 사용 하도록 설정 되므로 아티팩트 blob이 대상 저장소 컨테이너에 있는 경우 파이프라인이 자동으로 실행 됩니다. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** -exportpipeline 또는 importpipeline 리소스를 호출 하는 데 사용 되는 리소스입니다.  
  * PipelineRun 리소스를 만들고 내보낼 아티팩트를 지정 하 여 ExportPipeline을 수동으로 실행 합니다.  
  * 가져오기 트리거를 사용 하는 경우 ImportPipeline 자동으로 실행 됩니다. PipelineRun를 사용 하 여 수동으로 실행할 수도 있습니다. 
  * 현재 각 PipelineRun 최대 **50 개의 아티팩트** 를 전송할 수 있습니다.

### <a name="things-to-know"></a>알아야 할 사항
* ExportPipeline 및 ImportPipeline은 일반적으로 원본 및 대상 클라우드와 연결 된 다른 Active Directory 테 넌 트에 있습니다. 이 시나리오에는 내보내기 및 가져오기 리소스에 대 한 별도의 관리 되는 id 및 키 자격 증명 모음이 필요 합니다. 이러한 리소스는 테스트 목적으로 동일한 클라우드에서 id를 공유 하 여 배치할 수 있습니다.
* 기본적으로 ExportPipeline 및 ImportPipeline 템플릿은 각각 시스템 할당 관리 id를 사용 하 여 키 자격 증명 모음 비밀에 액세스 합니다. ExportPipeline 및 ImportPipeline 템플릿은 사용자가 제공한 id도 지원 합니다. 

## <a name="create-and-store-sas-keys"></a>SAS 키 만들기 및 저장

전송에서는 SAS (공유 액세스 서명) 토큰을 사용 하 여 원본 및 대상 환경의 저장소 계정에 액세스 합니다. 다음 섹션에 설명 된 대로 토큰을 생성 하 고 저장 합니다.  

### <a name="generate-sas-token-for-export"></a>내보내기에 대 한 SAS 토큰 생성

[Az storage container generate sas][az-storage-container-generate-sas] 명령을 실행 하 여 아티팩트 내보내기에 사용 되는 원본 저장소 계정의 컨테이너에 대 한 sas 토큰을 생성 합니다.

*권장 토큰 권한*: 읽기, 쓰기, 나열, 추가. 

다음 예제에서는 명령 출력이 '? ' 문자를 접두사로 하는 EXPORT_SAS 환경 변수에 할당 됩니다. `--expiry`사용자 환경에 대 한 값을 업데이트 합니다.

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>내보내기에 대 한 SAS 토큰 저장

[Az keyvault secret set][az-keyvault-secret-set]를 사용 하 여 원본 Azure key VAULT에 SAS 토큰을 저장 합니다.

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>가져올 SAS 토큰 생성

[Az storage container generate sas][az-storage-container-generate-sas] 명령을 실행 하 여 아티팩트 가져오기에 사용 되는 대상 저장소 계정의 컨테이너에 대 한 sas 토큰을 생성 합니다.

*권장 토큰 권한*: 읽기, 삭제, 목록

다음 예제에서는 명령 출력이 '? ' 문자를 접두사로 하는 IMPORT_SAS 환경 변수에 할당 됩니다. `--expiry`사용자 환경에 대 한 값을 업데이트 합니다.

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>가져오기를 위한 SAS 토큰 저장

[Az keyvault secret set][az-keyvault-secret-set]를 사용 하 여 대상 Azure key VAULT에 SAS 토큰을 저장 합니다.

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>리소스 관리자를 사용 하 여 ExportPipeline 만들기

Azure Resource Manager 템플릿 배포를 사용 하 여 소스 컨테이너 레지스트리에 대 한 ExportPipeline 리소스를 만듭니다.

ExportPipeline 리소스 관리자 [템플릿 파일](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) 을 로컬 폴더에 복사 합니다.

파일에 다음 매개 변수 값을 입력 합니다 `azuredeploy.parameters.json` .

|매개 변수  |값  |
|---------|---------|
|registryName     | 원본 컨테이너 레지스트리 이름      |
|exportPipelineName     |  내보내기 파이프라인에 대해 선택 하는 이름       |
|targetUri     |  원본 환경 (내보내기 파이프라인의 대상)에 있는 저장소 컨테이너의 URI입니다.<br/>예: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  원본 키 자격 증명 모음의 이름  |
|sasTokenSecretName  | 원본 키 자격 증명 모음의 SAS 토큰 비밀 이름 <br/>예: acrexportsas

### <a name="export-options"></a>내보내기 옵션

`options`내보내기 파이프라인에 대 한 속성은 선택적 부울 값을 지원 합니다. 권장 되는 값은 다음과 같습니다.

|매개 변수  |값  |
|---------|---------|
|옵션 | OverwriteBlobs-기존 대상 blob 덮어쓰기<br/>ContinueOnErrors-하나의 아티팩트 내보내기에 실패 하는 경우 소스 레지스트리에서 나머지 아티팩트를 계속 내보냅니다.

### <a name="create-the-resource"></a>리소스 만들기

다음 예제와 같이 [az deployment group create][az-deployment-group-create] 를 실행 하 여 *exportpipeline* 이라는 리소스를 만듭니다. 기본적으로 첫 번째 옵션을 사용 하면 예제 템플릿이 ExportPipeline 리소스에서 시스템 할당 id를 사용 하도록 설정 합니다. 

두 번째 옵션을 사용 하 여 리소스에 사용자 할당 id를 제공할 수 있습니다. 사용자 할당 id 생성은 표시 되지 않습니다.

두 옵션 중 하나를 사용 하는 경우 템플릿은 내보내기 키 자격 증명 모음에서 SAS 토큰에 액세스 하는 id를 구성 합니다. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>옵션 1: 리소스 만들기 및 시스템 할당 id 사용

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>옵션 2: 리소스 만들기 및 사용자 할당 id 제공

이 명령에서 사용자 할당 id의 리소스 ID를 추가 매개 변수로 제공 합니다.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

명령 출력에서 파이프라인의 리소스 ID ()를 기록해 둡니다 `id` . [Az deployment group show][az-deployment-group-show]를 실행 하 여 나중에 사용할 수 있도록이 값을 환경 변수에 저장할 수 있습니다. 예를 들면 다음과 같습니다.

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>리소스 관리자를 사용 하 여 ImportPipeline 만들기 

Azure Resource Manager 템플릿 배포를 사용 하 여 대상 컨테이너 레지스트리에서 ImportPipeline 리소스를 만듭니다. 기본적으로 파이프라인은 대상 환경의 저장소 계정에 아티팩트 blob이 있는 경우 자동으로 가져올 수 있습니다.

ImportPipeline 리소스 관리자 [템플릿 파일](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) 을 로컬 폴더에 복사 합니다.

파일에 다음 매개 변수 값을 입력 합니다 `azuredeploy.parameters.json` .

매개 변수  |값  |
|---------|---------|
|registryName     | 대상 컨테이너 레지스트리의 이름      |
|importPipelineName     |  가져오기 파이프라인에 대해 선택 하는 이름       |
|sourceUri     |  대상 환경 (가져오기 파이프라인의 원본)에 있는 저장소 컨테이너의 URI입니다.<br/>예: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  대상 키 자격 증명 모음의 이름 |
|sasTokenSecretName     |  대상 키 자격 증명 모음의 SAS 토큰 비밀 이름<br/>예: acr importsas |

### <a name="import-options"></a>가져오기 옵션

`options`가져오기 파이프라인의 속성은 선택적 부울 값을 지원 합니다. 권장 되는 값은 다음과 같습니다.

|매개 변수  |값  |
|---------|---------|
|옵션 | OverwriteTags-기존 대상 태그 덮어쓰기<br/>DeleteSourceBlobOnSuccess-대상 레지스트리로 성공적으로 가져온 후 원본 저장소 blob를 삭제 합니다.<br/>ContinueOnErrors-하나의 아티팩트 가져오기에 실패 하는 경우 대상 레지스트리에서 나머지 아티팩트를 계속 가져옵니다.

### <a name="create-the-resource"></a>리소스 만들기

다음 예제와 같이 [az deployment group create][az-deployment-group-create] 를 실행 하 여 *importpipeline* 이라는 리소스를 만듭니다. 기본적으로 첫 번째 옵션을 사용 하면 예제 템플릿이 ImportPipeline 리소스에서 시스템 할당 id를 사용 하도록 설정 합니다. 

두 번째 옵션을 사용 하 여 리소스에 사용자 할당 id를 제공할 수 있습니다. 사용자 할당 id 생성은 표시 되지 않습니다.

두 옵션 중 하나를 사용 하는 경우 템플릿은 가져오기 키 자격 증명 모음에서 SAS 토큰에 액세스 하는 id를 구성 합니다. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>옵션 1: 리소스 만들기 및 시스템 할당 id 사용

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>옵션 2: 리소스 만들기 및 사용자 할당 id 제공

이 명령에서 사용자 할당 id의 리소스 ID를 추가 매개 변수로 제공 합니다.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

가져오기를 수동으로 실행 하려는 경우 파이프라인의 리소스 ID ()를 기록해 둡니다 `id` . [Az deployment group show][az-deployment-group-show] 명령을 실행 하 여 나중에 사용할 수 있도록이 값을 환경 변수에 저장할 수 있습니다. 예를 들면 다음과 같습니다.

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>리소스 관리자로 내보내기에 대 한 PipelineRun 만들기 

Azure Resource Manager 템플릿 배포를 사용 하 여 원본 컨테이너 레지스트리에 대 한 PipelineRun 리소스를 만듭니다. 이 리소스는 이전에 만든 ExportPipeline 리소스를 실행 하 고 컨테이너 레지스트리에서 지정 된 아티팩트를 원본 저장소 계정에 blob으로 내보냅니다.

PipelineRun 리소스 관리자 [템플릿 파일](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) 을 로컬 폴더에 복사 합니다.

파일에 다음 매개 변수 값을 입력 합니다 `azuredeploy.parameters.json` .

|매개 변수  |값  |
|---------|---------|
|registryName     | 원본 컨테이너 레지스트리 이름      |
|pipelineRunName     |  실행에 대해 선택 하는 이름       |
|pipelineResourceId     |  내보내기 파이프라인의 리소스 ID입니다.<br/>예: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  원본 저장소 계정으로 내보낸 아티팩트 blob에 대해 선택 하는 이름 (예: *myblob* )
|artifacts | 태그 또는 매니페스트 다이제스트로 전송할 소스 아티팩트의 배열<br/>예: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

동일한 속성을 사용 하 여 PipelineRun 리소스를 다시 배포 하는 경우 [Forceupdatetag](#redeploy-pipelinerun-resource) 속성도 사용 해야 합니다.

[Az deployment group create][az-deployment-group-create] 를 실행 하 여 PipelineRun 리소스를 만듭니다. 다음 예에서는 배포 *exportPipelineRun*의 이름을로 합니다.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

나중에 사용 하려면 파이프라인 실행의 리소스 ID를 환경 변수에 저장 합니다.

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

아티팩트를 내보내는 데 몇 분 정도 걸릴 수 있습니다. 배포가 성공적으로 완료 되 면 원본 저장소 계정의 *전송* 컨테이너에서 내보낸 blob을 나열 하 여 아티팩트 내보내기를 확인 합니다. 예를 들어 [az storage blob list][az-storage-blob-list] 명령을 실행 합니다.

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Blob 전송 (옵션) 

AzCopy 도구나 다른 방법을 사용 하 여 원본 저장소 계정에서 대상 저장소 계정으로 [blob 데이터를 전송](../storage/common/storage-use-azcopy-blobs.md#copy-blobs-between-storage-accounts) 합니다.

예를 들어 다음 [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) 명령은 원본 계정의 *전송* 컨테이너에서 myblob을 대상 계정의 *전송* 컨테이너로 복사 합니다. 대상 계정에 blob이 있는 경우 덮어씁니다. 인증은 원본 및 대상 컨테이너에 대 한 적절 한 사용 권한이 있는 SAS 토큰을 사용 합니다. 토큰을 만드는 단계는 표시 되지 않습니다.

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>ImportPipeline 리소스 트리거

`sourceTriggerStatus`Importpipeline (기본값)의 매개 변수를 사용 하도록 설정한 경우 blob이 대상 저장소 계정에 복사 된 후 파이프라인이 트리거됩니다. 아티팩트를 가져오는 데 몇 분 정도 걸릴 수 있습니다. 가져오기가 성공적으로 완료 되 면 대상 컨테이너 레지스트리의 리포지토리를 나열 하 여 아티팩트 가져오기를 확인 합니다. 예를 들어 [az acr repository list][az-acr-repository-list]를 실행 합니다.

```azurecli
az acr repository list --name <target-registry-name>
```

가져오기 파이프라인의 매개 변수를 사용 하도록 설정 하지 않은 경우 `sourceTriggerStatus` 다음 섹션에 표시 된 것 처럼 importpipeline 리소스를 수동으로 실행 합니다. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>리소스 관리자를 사용 하 여 가져오기에 대 한 PipelineRun 만들기 (선택 사항) 
 
PipelineRun 리소스를 사용 하 여 대상 컨테이너 레지스트리에 아티팩트 가져오기를 위한 ImportPipeline을 트리거할 수도 있습니다.

PipelineRun 리소스 관리자 [템플릿 파일](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) 을 로컬 폴더에 복사 합니다.

파일에 다음 매개 변수 값을 입력 합니다 `azuredeploy.parameters.json` .

|매개 변수  |값  |
|---------|---------|
|registryName     | 대상 컨테이너 레지스트리의 이름      |
|pipelineRunName     |  실행에 대해 선택 하는 이름       |
|pipelineResourceId     |  가져오기 파이프라인의 리소스 ID입니다.<br/>예: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  저장소 계정에서 내보낸 아티팩트의 기존 blob의 이름 (예: *myblob* )

동일한 속성을 사용 하 여 PipelineRun 리소스를 다시 배포 하는 경우 [Forceupdatetag](#redeploy-pipelinerun-resource) 속성도 사용 해야 합니다.

[Az deployment group create][az-deployment-group-create] 를 실행 하 여 리소스를 실행 합니다.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

나중에 사용 하려면 파이프라인 실행의 리소스 ID를 환경 변수에 저장 합니다.

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)

When deployment completes successfully, verify artifact import by listing the repositories in the target container registry. For example, run [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>PipelineRun 리소스 다시 배포

*동일한 속성*을 사용 하 여 PipelineRun 리소스를 다시 배포 하는 경우 **forceupdatetag** 속성을 활용 해야 합니다. 이 속성은 구성이 변경 되지 않은 경우에도 PipelineRun 리소스를 다시 만들어야 함을 나타냅니다. PipelineRun 리소스를 다시 배포할 때마다 forceUpdateTag가 서로 다른 지 확인 하세요. 아래 예제에서는 내보내기에 대해 PipelineRun를 다시 만듭니다. 현재 datetime은 forceUpdateTag를 설정 하는 데 사용 되므로이 속성은 항상 고유 합니다.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>파이프라인 리소스 삭제

다음 명령 예제에서는 [az resource delete][az-resource-delete] 를 사용 하 여이 문서에서 만든 파이프라인 리소스를 삭제 합니다. 리소스 Id는 이전에 환경 변수에 저장 되었습니다.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>문제 해결

* **템플릿 배포 오류 또는 오류**
  * 파이프라인 실행이 실패 하면 `pipelineRunErrorMessage` 실행 리소스의 속성을 확인 합니다.
  * 일반적인 템플릿 배포 오류는 [ARM 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md) 을 참조 하세요.
* **저장소 blob 내보내기 또는 가져오기 문제**
  * SAS 토큰이 만료 되었거나 지정 된 내보내기 또는 가져오기 실행에 대 한 권한이 부족 한 것일 수 있습니다.
  * 여러 내보내기를 실행 하는 동안 원본 저장소 계정의 기존 저장소 blob을 덮어쓰지 못할 수 있습니다. OverwriteBlob 옵션이 내보내기 실행에 설정 되어 있고 SAS 토큰에 충분 한 권한이 있는지 확인 합니다.
  * 가져오기를 실행 한 후에는 대상 저장소 계정의 저장소 blob을 삭제할 수 없습니다. 가져오기 실행에 Deleteblob Onsuccess 옵션이 설정 되어 있고 SAS 토큰에 충분 한 권한이 있는지 확인 합니다.
  * 저장소 blob을 만들거나 삭제 하지 않았습니다. 내보내기 또는 가져오기 실행에 지정 된 컨테이너가 있는지 확인 하거나, 수동 가져오기 실행을 위해 지정 된 저장소 blob이 있는지 확인 합니다. 
* **AzCopy 문제**
  * [AzCopy 문제 해결](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues)을 참조 하세요.  
* **아티팩트 전송 문제**
  * 모든 아티팩트 또는 없음은 전송 되지 않습니다. 내보내기 실행에서 아티팩트의 철자와 내보내기 및 가져오기에서 blob의 이름을 확인 합니다. 최대 50 개의 아티팩트를 전송 하 고 있는지 확인 합니다.
  * 파이프라인 실행이 완료 되지 않았을 수 있습니다. 내보내기 또는 가져오기 실행에는 다소 시간이 걸릴 수 있습니다. 
  * 다른 파이프라인 문제의 경우 내보내기 실행 또는 가져오기 실행의 배포 [상관 관계 ID](../azure-resource-manager/templates/deployment-history.md) 를 Azure Container Registry 팀에 제공 합니다.


## <a name="next-steps"></a>다음 단계

공용 레지스트리 또는 다른 개인 레지스트리에서 단일 컨테이너 이미지를 Azure container registry로 가져오려면 [az acr import][az-acr-import] 명령 참조를 참조 하세요.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-resource-delete]: /cli/azure/resource#az-resource-delete
