---
title: 자습서 - Terraform을 사용하여 Azure Marketplace 이미지에서 관리 ID로 Linux VM 만들기
description: Azure Marketplace 이미지를 사용하여 관리 ID 및 원격 상태 관리로 Terraform Linux VM 만들기
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 233012d6caf1280914a6d2439ae856d69570fff7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838045"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>자습서: Terraform을 사용하여 Azure Marketplace 이미지에서 관리 ID로 Linux VM 만들기

이 문서에서는 [Terraform Marketplace 이미지](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview)를 사용하여 최신 [Terraform](https://www.terraform.io/intro/index.html) 버전이 설치되고 [Azure 리소스용 관리 ID](/azure/active-directory/managed-service-identity/overview)를 사용하여 구성된 Ubuntu Linux VM(16.04 LTS)을 만드는 방법을 보여줍니다. 또한 이 이미지는 Terraform을 사용하여 [원격 상태](https://www.terraform.io/docs/state/remote.html) 관리를 사용할 수 있도록 하는 원격 백 엔드도 구성합니다. 

Terraform Marketplace 이미지를 사용하면 수동으로 Terraform을 설치하고 구성할 필요 없이 Azure에서 Terraform을 쉽게 시작할 수 있습니다. 

이 Terraform VM 이미지에는 소프트웨어 요금이 부과되지 않습니다. 프로비저닝된 VM의 크기를 기반으로 하는 Azure 하드웨어 사용 요금만 지불합니다. 

컴퓨팅 요금에 대한 자세한 내용은 [Linux VM 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
Linux Terraform VM을 만들기 전에 Azure 구독이 있어야 합니다. 아직 없을 경우 [지금 무료 Azure 계정 만들기](https://azure.microsoft.com/free/)를 참조하세요.  

## <a name="create-your-terraform-vm"></a>Terraform VM 만들기 

Linux Terraform VM의 인스턴스를 만드는 단계는 다음과 같습니다. 

1. Azure Portal에서 [리소스 만들기](https://ms.portal.azure.com/#create/hub) 목록으로 이동합니다.

1. **Marketplace 검색** 검색 표시줄에서 **Terraform**을 검색합니다. 

1. **만들기**를 선택합니다. 

1. 다음 섹션에서는 Terraform Linux VM을 만들기 위해 마법사의 각 단계에 대한 입력을 제공합니다. 다음 섹션에서는 이러한 각 단계를 구성하는 데 필요한 입력을 나열합니다.

## <a name="details-on-the-create-terraform-tab"></a>Terraform 만들기 탭의 세부 정보

**Terraform 만들기** 탭에 대한 다음 세부 정보를 입력합니다.

1. **기본 사항**
    
   * **이름**: Terraform VM의 이름입니다.
   * **사용자 이름**: 첫 번째 계정의 로그인 ID입니다.
   * **암호**: 첫 번째 계정의 암호입니다. (암호 대신 SSH 공개 키를 사용할 수 있습니다.)
   * **구독**: 머신이 만들어지고 청구되는 구독입니다. 이 구독에 대한 리소스 만들기 권한이 있어야 합니다.
   * **리소스 그룹**: 새 또는 기존 리소스 그룹입니다.
   * **위치**: 가장 적합한 데이터 센터입니다. 이 위치는 대개 대부분의 데이터가 저장되어 있는 위치이거나 사용자의 실제 위치에 가장 가까운 위치(이 경우 네트워크에 가장 빠르게 액세스할 수 있음)입니다.

2. **추가 설정**

   * **크기**: VM의 크기. 
   * **VM 디스크 유형**: SSD 또는 HDD입니다.

3. **Terraform 요약**

   * 입력한 모든 정보가 올바른지 확인합니다. 

4. **구입**

   * 프로비전 프로세스를 시작하려면 **구입**을 선택합니다. 트랜잭션의 조건에는 링크가 제공됩니다. VM에는 크기 단계에서 선택한 서버 크기에 대한 컴퓨팅 이외의 추가 요금이 없습니다.

Terraform VM 이미지에서 수행하는 단계는 다음과 같습니다.

* Ubuntu 16.04 LTS 이미지에 따라 시스템에 할당된 ID를 사용하여 VM을 만듭니다.
* VM에 Azure 리소스 확장에 대해 관리 ID를 설치하여 Azure 리소스에 대해 OAuth 토큰을 발급할 수 있도록 합니다.
* RBAC 권한을 관리 서비스 ID에 할당하여 리소스 그룹에 대한 소유자 권한을 부여합니다.
* Terraform 템플릿 폴더(tfTemplate)를 만듭니다.
* Azure 백 엔드를 사용하여 Terraform 원격 상태를 미리 구성합니다.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Linux Terraform VM에 액세스 및 구성

VM을 만든 후 다음 단계를 수행합니다.

1. SSH를 사용하여 VM에 로그인합니다. 이전 섹션에서 만든 계정 자격 증명을 사용합니다. Windows에서는 [Putty](https://www.putty.org/)와 같은 SSH 클라이언트 도구를 다운로드할 수 있습니다.

1. VM의 Azure 리소스에 대한 관리 ID에 전체 구독에 대한 기여자 권한을 부여합니다. 

    기여자 권한은 VM의 Azure 리소스에 대한 관리 ID에서 Terraform을 사용하여 VM 리소스 그룹 외부의 리소스를 만드는 데 도움이 됩니다. 다음 스크립트를 실행하여 이 작업을 수행합니다. 
    
    ```bash
    . ~/tfEnv.sh
    ```

    이 스크립트는 [Azure CLI 대화형 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) 메커니즘을 사용하여 Azure에 인증합니다. 이 프로세스는 전체 구독에 대한 [관리 ID 기여자 권한](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)을 할당합니다. 

1. VM에는 Terraform 원격 상태 백 엔드가 있습니다. Terraform 배포에서 이를 사용하려면 `remoteState.tf` 파일을 Terraform 스크립트의 루트에 복사해야 합니다.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    원격 상태 관리에 대한 자세한 내용은 [Terraform 원격 상태](https://www.terraform.io/docs/state/remote.html)를 참조하세요. 스토리지 액세스 키는 이 파일에서 노출됩니다. Terraform 구성 파일을 소스 제어에 커밋하기 전에 제외합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/terraform)