---
title: 자습서 - Terraform에 대한 Azure Cloud Shell 구성
description: 이 자습서에서는 Azure Cloud Shell로 Terraform을 사용하여 인증 및 템플릿 구성을 간소합니다.
keywords: azure devops terraform 클라우드 셸
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945342"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>자습서: Terraform에 대한 Azure Cloud Shell 구성

Terraform은 macOS, Windows 또는 Linux의 Bash 명령줄에서 잘 작동합니다. [Azure Cloud Shell](/azure/cloud-shell/overview)의 Bash 환경에서 Terraform 구성을 실행하면 몇 가지 고유한 장점이 있습니다. 이 자습서에서는 Cloud Shell를 사용하여 Azure에 배포하는 Terraform 스크립트를 작성하는 방법을 보여 줍니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>자동 자격 증명 구성

Terraform이 설치되면 Cloud Shell에서 즉시 사용할 수 있습니다. Terraform 스크립트는 추가 구성없이 인프라를 관리하기 위해 Cloud Shell에 로그인할 때 Azure로 인증합니다. 자동 인증은 다음과 같은 두 가지 수동 프로세스를 무시합니다.
- Azure Active Directory 서비스 주체 만들기
- Azure Terraform 공급자 변수 구성


## <a name="use-modules-and-providers"></a>모듈 및 공급자 사용

Azure Terraform 모듈에는 Azure 리소스에 액세스하고 수정하기 위한 자격 증명이 필요합니다. Cloud Shell에서 Terraform 모듈을 사용하려면 다음 코드를 추가합니다.


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell은 임의의 `terraform` CLI 명령을 사용할 때 환경 변수를 통해 `azurerm` 공급자에 필요한 값을 전달합니다.

## <a name="other-cloud-shell-developer-tools"></a>다른 Cloud Shell 개발자 도구

파일 및 셸 상태는 Cloud Shell 세션 간에 Azure Storage에 유지됩니다. [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer)를 사용하여 로컬 컴퓨터에서 Cloud Shell에 파일을 복사하고 업로드합니다.

Azure CLI는 Cloud Shell에서 사용할 수 있으며 `terraform apply` 또는 `terraform destroy`가 완료된 후 구성을 테스트하고 작업을 확인하는 데 유용한 도구입니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [모듈 레지스트리를 사용하여 작은 VM 클러스터 만들기](terraform-create-vm-cluster-module.md)