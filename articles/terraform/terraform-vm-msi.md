---
title: Azure Marketplace 이미지를 사용하여 관리 서비스 ID가 있는 Terraform Linux Virtual Machine 만들기
description: Marketplace 이미지를 사용하여 관리 서비스 ID 및 원격 상태 관리가 있는 Terraform Linux Virtual Machine을 만들어 Azure에 리소스를 쉽게 배포합니다.
keywords: Terraform, DevOps, MSI, 가상 머신, 원격 상태, Azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Azure Marketplace 이미지를 사용하여 관리 서비스 ID가 있는 Terraform Linux Virtual Machine 만들기

이 문서에서는 [Terraform Marketplace 이미지](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview)를 사용하여 최신 [Terraform](https://www.terraform.io/intro/index.html) 버전이 설치되고 [MSI(관리 서비스 ID)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)를 사용하여 구성된 `Ubuntu Linux VM (16.04 LTS)`을 만드는 방법을 보여 줍니다. 또한 이 이미지는 Terraform을 사용하여 [원격 상태](https://www.terraform.io/docs/state/remote.html) 관리를 사용할 수 있도록 하는 원격 백 엔드도 구성합니다. Terraform Marketplace 이미지를 사용하면 수동으로 Terraform을 설치하고 인증을 구성할 필요 없이 Azure에서 Terraform을 몇 분 만에 쉽게 시작할 수 있습니다. 

이 Terraform VM 이미지에는 소프트웨어 요금이 부과되지 않습니다. 프로비전되는 가상 머신의 크기에 따라 평가되는 Azure 하드웨어 사용 요금만 지불하면 됩니다. 컴퓨팅 요금에 대한 자세한 내용은 [Linux Virtual Machines 가격 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
Linux Terraform Virtual Machine을 만들기 전에 Azure 구독이 있어야 합니다. 아직 없을 경우 [지금 무료 Azure 계정 만들기](https://azure.microsoft.com/free/)를 참조하세요.  

## <a name="create-your-terraform-virtual-machine"></a>Terraform Virtual Machine 만들기 

Linux Terraform Virtual Machine의 인스턴스를 만드는 단계는 다음과 같습니다. 

1. Azure Portal에서 [리소스 만들기](https://ms.portal.azure.com/#create/hub) 목록으로 이동합니다.
2. `Search the Marketplace` 검색 창에서 `Terraform`을 검색합니다. `Terraform` 템플릿을 선택합니다. 오른쪽 아래의 Terraform 세부 정보 탭에서 **만들기** 단추를 선택합니다.
![대체 텍스트](media\terraformmsi.png)
3. 다음 섹션에서는 Terraform Linux Virtual Machine을 만들기 위해 마법사의 각 단계(**오른쪽에 열거됨**)에 대한 입력을 제공합니다.  이러한 각 단계를 구성하는 데 필요한 입력은 다음과 같습니다.

## <a name="details-in-create-terraform-tab"></a>Terraform 만들기 탭의 세부 정보

Terraform 만들기 탭에 입력해야 하는 세부 정보는 다음과 같습니다.

a. **기본 사항**
    
* **이름**: Terraform Virtual Machine의 이름입니다.
* **사용자 이름**: 첫 번째 계정 로그인 ID입니다.
* **암호**: 첫 번째 계정 암호입니다. 암호 대신 SSH 공개 키를 사용할 수 있습니다.
* **구독**: 둘 이상의 구독을 보유한 경우, 컴퓨터를 만들고 요금을 청구할 구독 하나를 선택합니다. 이 구독에 대한 리소스 만들기 권한이 있어야 합니다.
* **리소스 그룹**: 새 그룹을 만들거나 기존 그룹을 사용할 수 있습니다.
* **위치**: 가장 적합한 데이터 센터를 선택합니다. 이 위치는 대개 대부분의 데이터가 저장되어 있는 위치이거나 사용자의 실제 위치에 가장 가까운 위치(이 경우 네트워크에 가장 빠르게 액세스할 수 있음)입니다.

나. **추가 설정**

* 크기: Virtual Machine의 크기입니다.
* VM 디스크 유형: SSD 및 HDD 중에서 선택합니다.

다. **Terraform 요약**

* 입력한 모든 정보가 올바른지 확인합니다. 

d. **구입**

* 프로비전을 시작하려면 [구입]을 클릭합니다. 트랜잭션의 조건에는 링크가 제공됩니다. VM에는 [크기] 단계에서 선택한 서버 크기에 대한 계산 이외의 추가 요금이 없습니다.

Terraform VM 이미지에서 수행하는 단계는 다음과 같습니다.

* Ubuntu 16.04 LTS 이미지에 따라 시스템에 할당된 ID를 사용하여 VM을 만듭니다.
* VM에 MSI 확장을 설치하여 Azure 리소스에 대해 OAuth 토큰을 발급할 수 있도록 합니다.
* RBAC 권한을 관리 서비스 ID에 할당하여 리소스 그룹에 대한 소유자 권한을 부여합니다.
* Terraform 템플릿 폴더(tfTemplate)를 만듭니다.
* Azure 백 엔드를 사용하여 Terraform 원격 상태를 미리 구성합니다.

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>Linux Terraform Virtual Machine에 액세스하고 구성하는 방법

VM을 만든 후 SSH를 사용하여 해당 VM에 로그인할 수 있습니다. 텍스트 셸 인터페이스에 대해 3단계의 [기본 사항] 섹션에서 만든 계정 자격 증명을 사용합니다. Windows에서는 [Putty](http://www.putty.org/)와 같은 SSH 클라이언트 도구를 다운로드할 수 있습니다.

`SSH`를 사용하여 Virtual Machine에 연결했으면 Virtual Machine에서 전체 구독에 대한 참가자 권한을 관리 서비스 ID에 부여해야 합니다. 참가자 권한은 VM의 MSI에서 Terraform을 사용하여 VM 리소스 그룹 외부의 리소스를 만드는 데 도움이 됩니다. 이 작업은 스크립트를 한 번 실행하여 쉽게 수행할 수 있습니다. 이 작업을 수행하는 명령은 다음과 같습니다.

`. ~/tfEnv.sh`

이전 스크립트는 [AZ CLI v 2.0 대화형 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) 메커니즘을 사용하여 Azure에서 인증하고 전체 구독에 대한 관리 서비스 ID 참가자 권한을 Virtual Machine에 할당합니다. 

 VM에 Terraform 원격 상태 백 엔드가 만들어지고 Terraform 배포에서 이를 사용하려면 tfTemplate 디렉터리의 remoteState.tf 파일을 Terraform 스크립트의 루트에 복사해야 합니다.  

 `cp  ~/tfTemplate/remoteState.tf .`

 원격 상태 관리에 대한 자세한 내용은 [여기](https://www.terraform.io/docs/state/remote.html)를 참조하세요. 저장소 액세스 키는 이 파일에 공개되어 있으며, 원본 제어를 주의 깊게 확인해야 합니다.  

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure에서 Terraform Linux Virtual Machine을 설정하는 방법을 알아보았습니다. Azure의 Terraform에 대해 자세히 알아볼 수 있는 몇 가지 추가 리소스는 다음과 같습니다. 

 [Microsoft.com의 Terraform 허브](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure 공급자 설명서](http://aka.ms/terraform)  
 [Terraform Azure 공급자 원본](http://aka.ms/tfgit)  
 [Terraform Azure 모듈](http://aka.ms/tfmodules)
 

















