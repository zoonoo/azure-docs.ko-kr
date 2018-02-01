---
title: "빠른 시작 - PowerShell을 사용하여 Azure에서 개인 Docker 레지스트리 만들기"
description: "PowerShell을 사용한 개인 Docker 컨테이너 레지스트리 만들기에 대해 빠르게 알아봅니다."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 10/08/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c7d74395b1c8b386ce190906aa5b63b48c1bb1bf
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>PowerShell을 사용하여 Azure Container Registry 만들기

Azure Container Registry는 개인 Docker 컨테이너 이미지를 저장하는 데 사용되는 관리되는 Docker 컨테이너 레지스트리 서비스입니다. 이 가이드에서는 PowerShell을 사용하여 관리되는 Azure Container Registry 인스턴스를 만드는 방법에 대해 자세히 설명합니다.

이 빠른 시작에서는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 모든 [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 또는 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

`Login-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

[New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) 명령을 사용하여 ACR 인스턴스를 만듭니다.

레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 다음 예제에서는 *myContainerRegistry007*을 사용합니다. 이를 고유한 값으로 업데이트합니다.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>ACR에 로그인

컨테이너 이미지를 밀어넣고 끌어오려면 먼저 ACR 인스턴스에 로그인해야 합니다. 먼저, [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) 명령을 사용하여 ACR 인스턴스에 대한 관리자 자격 증명을 가져옵니다.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

다음으로, [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 ACR 인스턴스에 로그인합니다.

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

이 명령은 완료되면 '로그인했습니다.'라는 메시지를 반환합니다.

## <a name="push-image-to-acr"></a>ACR에 이미지 푸시

Azure Container Registry에 이미지를 푸시하려면 먼저 이미지가 있어야 합니다. 필요한 경우에 다음 명령을 실행하여 미리 만든 이미지를 Docker Hub에서 끌어옵니다.

```bash
docker pull microsoft/aci-helloworld
```

이미지는 ACR 로그인 서버 이름으로 태그가 지정되어야 합니다. [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) 명령을 실행하여 ACR 인스턴스의 로그인 서버 이름을 반환합니다.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 명령을 사용하여 이미지에 태그를 지정합니다. *acrLoginServer*를 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

마지막으로 [docker push](https://docs.docker.com/engine/reference/commandline/push/)를 사용하여 ACR 인스턴스로 이미지를 푸시합니다. *acrLoginServer*를 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹, ACR 인스턴스 및 모든 컨테이너 이미지를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Container Registry를 만들었습니다. Azure Container Instances와 함께 Azure Container Registry를 사용하려는 경우 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](../container-instances/container-instances-tutorial-prepare-app.md)