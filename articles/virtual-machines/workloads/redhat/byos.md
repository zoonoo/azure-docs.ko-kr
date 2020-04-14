---
title: 레드 햇 엔터프라이즈 리눅스 가져-당신의 자신의 구독 Azure 이미지 | 마이크로 소프트 문서
description: Azure에서 Red Hat Enterprise Linux에 대한 사용자 고유의 구독 이미지 가져오기에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 9ab578b4b688c02c9150dfb23fce53fbb82df405
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273174"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>레드 햇 엔터프라이즈 리눅스 가져-당신의 자신의 구독 골드 이미지 Azure에서

Red Hat 엔터프라이즈 Linux(RHEL) 이미지는 Azure에서 종량제 또는 BYOS(Red Hat Gold Image) 모델을 통해 사용할 수 있습니다. 이 문서에서는 Azure의 Red Hat 골드 이미지에 대한 개요를 제공합니다.

>[!NOTE]
> RHEL BYOS 골드 이미지는 Azure 퍼블릭(상업용) 및 Azure 정부 클라우드에서 사용할 수 있습니다. Azure China 또는 Azure 블랙포레스트 클라우드에서는 사용할 수 없습니다.

## <a name="important-points-to-consider"></a>고려해야 할 중요 사항

- 이 프로그램에서 제공하는 Red Hat 골드 이미지는 Azure Marketplace의 RHEL 종량제 이미지와 유사한 프로덕션 지원 RHEL 이미지입니다.
- 이미지는 [Azure의 Red Hat Enterprise Linux 이미지에](./redhat-images.md)설명된 현재 정책을 따릅니다.
- 표준 지원 정책은 이러한 이미지에서 만든 VM에 적용됩니다.
- Red Hat 골드 이미지에서 프로비저닝된 VM은 RHEL 종량제 이미지와 관련된 RHEL 요금을 부과하지 않습니다.
- 이미지의 제목이 없습니다. Red Hat 구독 관리자를 사용하여 VM을 등록하고 구독해야 Red Hat에서 직접 업데이트를 받을 수 있습니다.
- 현재 는 리눅스 이미지에 대 한 BYOS와 종량제 청구 모델 사이를 동적으로 전환할 수 없습니다. 청구 모델을 전환하려면 각 이미지에서 VM을 다시 배포해야 합니다.

>[!NOTE]
> 2세대 RHEL BYOS 이미지는 현재 마켓플레이스 오퍼를 통해 사용할 수 없습니다. 2세대 RHEL BYOS 이미지가 필요한 경우 Red Hat 구독 관리의 클라우드 액세스 대시보드를 방문하십시오. 자세한 내용은 Red [Hat 문서를](https://access.redhat.com/articles/4847681)참조하십시오.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>레드햇 골드 이미지에 액세스하기 위한 요구 사항 및 조건

1. Red Hat [클라우드 액세스 프로그램](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 용어에 대해 잘 알고 있습니다. Red Hat [서브스크립션 관리자에서](https://access.redhat.com/management/cloud)클라우드 액세스에 대한 Red Hat 구독을 활성화합니다. 클라우드 액세스에 등록할 Azure 구독이 준비되어 있어야 합니다.

1. 클라우드 액세스에 대해 사용하도록 설정한 Red Hat 구독이 자격 요건을 충족하는 경우 Azure 구독은 골드 이미지 액세스에 대해 자동으로 활성화됩니다.

### <a name="expected-time-for-image-access"></a>이미지 액세스에 대한 예상 시간

클라우드 액세스 사용 단계를 완료한 후 Red Hat은 Red Hat 골드 이미지에 대한 적격성을 확인합니다. 유효성 검사가 성공하면 3시간 이내에 골드 이미지에 액세스할 수 있습니다.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Azure 포털에서 Red Hat 골드 이미지 사용

1. Azure 구독에서 Red Hat 골드 이미지에 대한 액세스 권한을 얻은 후 [Azure 포털에서](https://portal.azure.com)해당 이미지를 찾을 수 있습니다.  > 리소스 **만들기로**이동**모두 보기**.

1. 페이지 상단에 비공개 오퍼가 있음을 알 수 있습니다.

    ![마켓플레이스 프라이빗 오퍼](./media/rhel-byos-privateoffers.png)

1. 보라색 링크를 선택하거나 페이지 하단으로 스크롤하여 비공개 오퍼를 확인합니다.

1. UI의 프로비저닝의 나머지 부분도 다른 기존 Red Hat 이미지와 다르지 않습니다. RHEL 버전을 선택하고 프롬프트에 따라 VM을 프로비전합니다. 이 프로세스를 사용하면 마지막 단계에서 이미지 약관에 동의할 수도 있습니다.

>[!NOTE]
>지금까지의 이러한 단계는 프로그래밍 방식 배포를 위해 Red Hat 골드 이미지를 사용할 수 없습니다. "추가 정보" 섹션에 설명된 대로 추가 단계가 필요합니다.

이 문서의 나머지 부분에서는 이미지에 대한 용어를 프로비전하고 수락하는 CLI 방법에 중점을 둡니다. UI와 CLI는 최종 결과(프로비저닝된 RHEL 골드 이미지 VM)와 관련된 한 완전히 상호 교환할 수 있습니다.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Azure CLI의 레드햇 골드 이미지 사용

다음 지침은 Azure CLI를 사용하여 RHEL VM에 대한 초기 배포 프로세스를 안내합니다. 이 지침은 [Azure CLI가 설치되어](https://docs.microsoft.com/cli/azure/install-azure-cli)있다고 가정합니다.

>[!IMPORTANT]
>다음 명령에 대해 게시자, 제안, 계획 및 이미지 참조의 모든 소문자를 사용해야 합니다.

1. 원하는 구독에 있는지 확인합니다.

    ```azurecli
    az account show -o=json
    ```

1. Red Hat 골드 이미지 VM용 리소스 그룹을 만듭니다.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 이미지 용어에 동의합니다.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >이러한 조건은 *Azure 구독당 이미지 SKU*당 한 번씩 수락해야 합니다.

1. (선택 사항) 다음 명령을 통해 VM 배포의 유효성을 검사합니다.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. 인수 없이 이전 예제에 표시된 것과 동일한 명령을 `--validate` 실행하여 VM을 프로비전합니다.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. VM에 SSH를 입력하고 권한이 없는 이미지가 있는지 확인합니다. 이 단계를 수행하려면 `sudo yum repolist`을 실행합니다. RHEL 8의 `sudo dnf repolist`경우 을 사용합니다. 출력은 구독 관리자를 사용하여 Red Hat에 VM을 등록하도록 요청합니다.

>[!NOTE]
>RHEL `dnf` 8에서, `yum` 그리고 교환 할 수 있습니다. 자세한 내용은 [RHEL 8 관리자 가이드를](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index)참조하십시오.

## <a name="use-the-red-hat-gold-images-from-powershell"></a>파워쉘의 레드햇 골드 이미지 사용

다음 스크립트는 예제입니다. 리소스 그룹, 위치, VM 이름, 로그인 정보 및 기타 변수를 선택한 구성으로 바꿉니다. 게시자 및 계획 정보는 소문자여야 합니다.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>암호화 레드 햇 엔터프라이즈 리눅스 가져 - 당신의 자신의 가입 골드 이미지

레드 햇 엔터프라이즈 리눅스 BYOS 골드 이미지 [Azure 디스크 암호화를](../../linux/disk-encryption-overview.md)사용 하 여 보안될 수 있습니다. 암호화를 활성화하려면 먼저 구독을 *등록해야 합니다.* RHEL BYOS 골드 이미지를 등록하는 방법에 대한 자세한 내용은 [Red Hat 구독 관리자를 사용하여 Red Hat 고객 포털에 시스템을 등록하고 구독하는 방법을](https://access.redhat.com/solutions/253273)참조하세요. 활성 Red Hat 구독이 있는 경우 [Red Hat 고객 포털 활성화 키 만들기를](https://access.redhat.com/articles/1378093)읽을 수도 있습니다.

Azure 디스크 암호화는 [Red Hat 사용자 지정 이미지에서](../../linux/redhat-create-upload-vhd.md)지원되지 않습니다. 추가 Azure 디스크 암호화 요구 사항 및 필수 구성 사항은 [Linux VM에 대한 Azure 디스크 암호화에](../../linux/disk-encryption-overview.md#additional-vm-requirements)설명되어 있습니다.

Azure 디스크 암호화를 적용하는 단계는 [Linux VM](../../linux/disk-encryption-linux.md) 및 관련 문서에서 Azure 디스크 암호화 시나리오를 참조하세요.

## <a name="additional-information"></a>추가 정보

- 이 오퍼에 대해 활성화되지 않은 구독에 VM을 프로비전하려고 하면 다음과 같은 메시지가 표시됩니다.

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    이 경우 Microsoft 또는 Red Hat에 문의하여 구독을 활성화하십시오.

- RHEL BYOS 이미지에서 스냅샷을 수정하고 해당 사용자 지정 이미지를 [공유 이미지 갤러리에](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)게시하려고 하면 스냅숏의 원래 원본과 일치하는 계획 정보를 제공해야 합니다. 예를 들어 명령은 다음과 같을 수 있습니다.

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    최종 줄의 평면도 매개변수를 기록합니다.

    [Azure 디스크 암호화는](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) 사용자 지정 이미지에서 지원되지 않습니다.

- 자동화를 사용하여 RHEL BYOS 이미지에서 VM을 프로비전하는 경우 샘플 명령에 표시된 것과 유사한 계획 매개 변수를 제공해야 합니다. 예를 들어 Terraform을 사용하는 경우 [계획 블록에](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)계획 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계

- Red Hat 클라우드 액세스에 대한 자세한 내용은 [Red Hat 퍼블릭 클라우드 설명서에서](https://access.redhat.com/public-cloud) 확인할 수 있습니다.
- 클라우드 액세스에 대한 단계별 가이드 및 프로그램 세부 정보는 [Red Hat 클라우드 액세스 설명서를](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)참조하십시오.
- Red Hat 업데이트 인프라에 대한 자세한 내용은 [Azure Red Hat 업데이트 인프라를](./redhat-rhui.md)참조하십시오.
- Azure의 모든 Red Hat 이미지에 대한 자세한 내용은 [설명서 페이지를](./redhat-images.md)참조하십시오.
- 모든 버전의 RHEL에 대한 Red Hat 지원 정책에 대한 자세한 내용은 [Red Hat 엔터프라이즈 Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지를 참조하십시오.
- RHEL 골드 이미지에 대한 추가 설명서는 [Red Hat 설명서를](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)참조하십시오.
