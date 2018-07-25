---
title: 개인 레지스트리에서 Azure Service Fabric Mesh로 앱 배포 | Microsoft Docs
description: Azure CLI를 사용하여 개인 컨테이너 레지스트리를 사용하는 앱을 Service Fabric Mesh에 배포하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089489"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>개인 컨테이너 이미지 레지스트리에서 Service Fabric Mesh 앱 배포

이 문서에서는 개인 컨테이너 이미지 레지스트리를 사용하는 Azure Service Fabric Mesh 앱을 배포하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

### <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI 설정 
Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 작업을 완료할 수 있습니다. 다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치합니다.

### <a name="install-docker"></a>Docker 설치

Service Fabric Mesh에서 사용하는 컨테이너화된 Service Fabric 앱을 지원하려면 Docker를 설치합니다.

### <a name="windows-10"></a>윈도우 10

최신 버전의 [Windows용 Docker Community Edition][download-docker]을 다운로드하여 설치합니다. 

설치하는 동안 요청 시 **Linux 컨테이너 대신 Windows 컨테이너 사용**을 선택합니다. 이후 로그아웃했다가 다시 로그인해야 합니다. 다시 로그인한 후에 이전에 Hyper-V를 사용하도록 설정하지 않은 경우, Hyper-V를 사용하도록 설정하라는 메시지가 표시될 수 있습니다. Hyper-V를 사용하도록 설정한 후 컴퓨터를 다시 시작합니다.

컴퓨터를 다시 시작한 후 Docker에서 **컨테이너** 기능을 사용하도록 설정하라는 메시지가 표시되면 기능을 사용하도록 설정하고 컴퓨터를 다시 시작합니다.

### <a name="windows-server-2016"></a>Windows Server 2016

다음 PowerShell 명령을 사용하여 Docker를 설치합니다. 자세한 내용은 [Windows Server용 Docker Enterprise Edition][download-docker-server]을 참조하세요.

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

컴퓨터를 다시 시작합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure에 로그인하고 활성 구독을 설정합니다.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>컨테이너 레지스트리를 만들고 이미지를 컨테이너 레지스트리에 푸시

[Azure CLI를 사용하여 Azure에서 개인 Docker 레지스트리 만들기](../container-registry/container-registry-get-started-azure-cli.md)의 지침에 따라 Azure Container Registry를 만듭니다. [ACR에 로그인](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr) 단계까지 단계를 수행합니다. 

### <a name="push-image-to-acr"></a>ACR에 이미지 푸시

Azure Container Registry에 이미지를 푸시하려면 먼저 이미지가 있어야 합니다. 로컬 컨테이너 이미지가 아직 없는 경우 Docker 허브에서 기존 이미지를 가져오는 다음 명령을 실행합니다.

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

레지스트리에 이미지를 푸시하기 전에 ACR 로그인 서버의 정규화된 이름을 사용하여 태그를 지정해야 합니다. 다음 명령을 실행하여 ACR 인스턴스의 전체 로그인 서버 이름을 가져옵니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag][docker-tag] 명령을 사용하여 이미지에 태그를 지정합니다. `<acrLoginServer>`를 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>컨테이너 이미지 나열

다음 예제는 레지스트리의 리포지토리를 나열합니다.

```azurecli
az acr repository list --name <acrName> --output table
```

출력:

```bash
Result
----------------
azure-mesh-helloworld
```

다음 예제는 **azure-mesh-helloworld** 리포지토리에서의 태그를 나열합니다.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

출력:

```bash
Result
--------
1.1-alpine
```
이전 출력에서는 개인 컨테이너 레지스트리에 `azure-mesh-helloworld:1.1-alpine`이 있는지 확인합니다.

## <a name="retrieve-credentials-for-the-registry"></a>레지스트리의 자격 증명 검색

만든 레지스트리에서 컨테이너 인스턴스를 배포하기 위해 배포하는 동안 자격 증명을 제공해야 합니다. 다음 명령을 사용하여 레지스트리에서 관리 사용자를 사용하도록 설정합니다.

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

다음 명령을 사용하여 레지스트리 서버 이름, 사용자 이름 및 암호를 가져옵니다.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

이전 명령에서 제공한 값은 다음 명령에서 `<acrLoginServer>`, `<acrUserName>` 및 `<acrPassword>`로 참조됩니다.


## <a name="deploy-the-template"></a>템플릿 배포

다음 명령을 사용하여 응용 프로그램 및 관련 리소스를 만들고 이전 단계의 자격 증명을 제공합니다.

템플릿의 `registry-password` 매개 변수는 `securestring`입니다. 배포 상태 및 `az mesh service show` 명령에는 표시되지 않습니다. 다음 명령에 올바르게 지정되었는지 확인합니다.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

몇 분 안에 명령에서 다음 내용이 반환됩니다.

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>응용 프로그램 열기
응용 프로그램이 성공적으로 배포된 후 서비스 끝점의 공용 IP 주소를 가져오고 브라우저에서 엽니다. Service Fabric Mesh 로고가 있는 웹 페이지가 표시됩니다.

배포 명령은 서비스 끝점의 공용 IP 주소를 반환합니다. 필요한 경우, 네트워크 리소스를 쿼리하여 서비스 끝점의 공용 IP 주소를 찾을 수도 있습니다. 
 
이 응용 프로그램의 네트워크 리소스 이름은 `helloWorldPrivateRegistryNetwork`입니다. 다음 명령을 사용하여 이 정보를 페치합니다. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>리소스 삭제

미리 보기 프로그램에 할당된 제한된 리소스를 절약하려면 리소스를 자주 삭제합니다. 이 예제와 관련된 리소스를 삭제하려면 배포된 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>다음 단계
- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld)에서 Hello World 응용 프로그램 예제를 확인합니다.
- Service Fabric 리소스 모델에 대한 자세한 내용은 [Service Fabric Mesh 리소스 모델](service-fabric-mesh-service-fabric-resources.md)을 참조하세요.
- Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/