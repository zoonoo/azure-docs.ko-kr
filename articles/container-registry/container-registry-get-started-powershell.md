---
title: 빠른 시작 - PowerShell을 사용하여 Azure에서 개인 Docker 레지스트리 만들기
description: PowerShell을 사용한 개인 Docker 컨테이너 레지스트리 만들기에 대해 빠르게 알아봅니다.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ae21fc7ab016071d075324bf813243cef58dcd04
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Container Registry 만들기

Azure Container Registry는 개인 Docker 컨테이너 이미지를 저장하는 데 사용되는 관리되는 Docker 컨테이너 레지스트리 서비스입니다. 이 가이드는 PowerShell을 사용하여 Azure Container Registry 인스턴스 만들기, 컨테이너 이미지를 레지스트리로 푸시, 마지막으로 레지스트리의 컨테이너에서 ACI(Azure Container Instances)로 배포를 설명합니다.

이 빠른 시작에서는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 모든 [Mac][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

`Connect-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzureRmAccount
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

다음으로, [docker login][docker-login] 명령을 사용하여 ACR 인스턴스에 로그인합니다.

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

완료되면 이 명령은 `Login Succeeded`를 반환합니다. `--password-stdin` 매개 변수를 사용하도록 권장하는 보안 경고가 표시될 수 있습니다. 이 문서의 범위 외부에서 사용하는 경우 이 모범 사례를 따르는 것이 좋습니다. 자세한 내용은 [docker login][docker-login] 명령 참조를 참조하세요.

## <a name="push-image-to-acr"></a>ACR에 이미지 푸시

Azure Container Registry에 이미지를 푸시하려면 먼저 이미지가 있어야 합니다. 필요한 경우에 다음 명령을 실행하여 미리 만든 이미지를 Docker Hub에서 끌어옵니다.

```powershell
docker pull microsoft/aci-helloworld
```

이미지는 ACR 로그인 서버 이름으로 태그가 지정되어야 합니다. [docker tag][docker-tag] 명령을 사용하여 이를 수행합니다.

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

마지막으로 [docker push][docker-push]를 사용하여 ACR로 이미지를 푸시합니다.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>ACI에 이미지 배포
ACI(Azure Container Instances)에서 컨테이너 인스턴스로 이미지를 배포하려면 먼저 레지스트리 자격 증명을 PSCredential로 변환합니다.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

1개의 CPU 코어 및 1GB 메모리로 컨테이너 레지스트리에서 컨테이너 이미지를 배포하려면 다음 명령을 실행합니다.

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Azure Resource Manager에서 컨테이너의 세부 정보와 함께 초기 응답을 다시 가져와야 합니다. 컨테이너의 상태를 모니터링하고 실행될 때 확인하려면 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]을 반복합니다. 이 설치는 1분 이내에 완료되어야 합니다.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

예제 출력: `Succeeded`

## <a name="view-the-application"></a>응용 프로그램 보기
ACI에 대한 배포가 성공하면 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] 명령을 사용하여 컨테이너의 공용 IP 주소를 가져옵니다.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

예제 출력: `"13.72.74.222"`

실행 중인 응용 프로그램을 보려면 원하는 브라우저에서 공용 IP 주소로 이동합니다. 다음과 같이 표시됩니다.

![브라우저의 Hello World 앱][qs-portal-15]

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] 명령을 사용하여 리소스 그룹, Azure Container Registry 및 모든 Azure Container Instances를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Container Registry를 만들고, Azure Container Instances에서 해당 인스턴스를 시작했습니다. ACI에 대해 자세히 알아보기 위해 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
