---
title: "Terraform 모듈을 사용하여 Azure에서 부하가 분산된 Linux VM 인프라 만들기"
description: "Terraform 모듈을 사용하여 Azure에서 부하 분산 장치가 있는 Linux 가상 컴퓨터 클러스터를 만드는 방법 알아보기"
keywords: "terraform, devops, 가상 컴퓨터, 네트워크, 모듈"
author: rloutlaw
manager: justhe
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 10/19/2017
ms.author: routlaw
ms.openlocfilehash: 86fee5446065ffa8b5e8186ba8d8f56aa3efc2ea
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="create-a-vm-cluster-with-terraform-using-custom-hcl"></a>사용자 지정 HCL을 사용하여 Terraform으로 VM 클러스터 만들기

이 자습서에서는 [HCL(Hashicorp Configuration Language)](https://www.terraform.io/docs/configuration/syntax.html)을 사용하여 작은 계산 클러스터를 만드는 방법을 보여줍니다. 구성은 부하 분산 장치, [가용성 집합](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)의 Linux VM 두 개 및 필요한 모든 네트워킹 리소스를 만듭니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure로 인증 설정
> * Terraform 템플릿 만들기
> * plan으로 변경 내용 시각화
> * 구성을 적용하여 클러스터 만들기

## <a name="set-up-authentication-with-azure"></a>Azure로 인증 설정

> [!TIP]
> [Terraform 환경 변수를 사용](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables)하거나 [Azure Cloud Shell](/azure/cloud-shell/overview)에서 자습서를 실행하는 경우 이 단계를 건너뜁니다.

 Azure 서비스 주체를 만들려면 [Terraform을 설치하고 Azure에 대한 액세스 구성](/azure/virtual-machines/linux/terraform-install-configure)을 검토하세요. 이 서비스 주체를 사용하여 빈 디렉터리에 있는 새 파일 `azureProviderAndCreds.tf`를 다음 코드로 채웁니다.

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>템플릿 만들기

다음 코드로 `main.tf`라는 새 Terraform 템플릿을 만듭니다. 

```tf
resource "azurerm_resource_group" "test" {
  name     = "acctestrg"
  location = "West US 2"
}

resource "azurerm_virtual_network" "test" {
  name                = "acctvn"
  address_space       = ["10.0.0.0/16"]
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"
}

resource "azurerm_subnet" "test" {
  name                 = "acctsub"
  resource_group_name  = "${azurerm_resource_group.test.name}"
  virtual_network_name = "${azurerm_virtual_network.test.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "test" {
  name                         = "publicIPForLB"
  location                     = "${azurerm_resource_group.test.location}"
  resource_group_name          = "${azurerm_resource_group.test.name}"
  public_ip_address_allocation = "static"
}

resource "azurerm_lb" "test" {
  name                = "loadBalancer"
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"

  frontend_ip_configuration {
    name                 = "publicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.test.id}"
  }
}

resource "azurerm_lb_backend_address_pool" "test" {
  resource_group_name = "${azurerm_resource_group.test.name}"
  loadbalancer_id     = "${azurerm_lb.test.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_network_interface" "test" {
  count               = 2
  name                = "acctni${count.index}"
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"

  ip_configuration {
    name                          = "testConfiguration"
    subnet_id                     = "${azurerm_subnet.test.id}"
    private_ip_address_allocation = "dynamic"
    load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
  }
}

resource "azurerm_managed_disk" "test" {
  count                = 2
  name                 = "datadisk_existing_${count.index}"
  location             = "${azurerm_resource_group.test.location}"
  resource_group_name  = "${azurerm_resource_group.test.name}"
  storage_account_type = "Standard_LRS"
  create_option        = "Empty"
  disk_size_gb         = "1023"
}

resource "azurerm_availability_set" "avset" {
  name                         = "avset"
  location                     = "${azurerm_resource_group.test.location}"
  resource_group_name          = "${azurerm_resource_group.test.name}"
  platform_fault_domain_count  = 2
  platform_update_domain_count = 2
  managed                      = true
}

resource "azurerm_virtual_machine" "test" {
  count                 = 2
  name                  = "acctvm${count.index}"
  location              = "${azurerm_resource_group.test.location}"
  availability_set_id   = "${azurerm_availability_set.avset.id}"
  resource_group_name   = "${azurerm_resource_group.test.name}"
  network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
  vm_size               = "Standard_DS1_v2"

  # Uncomment this line to delete the OS disk automatically when deleting the VM
  # delete_os_disk_on_termination = true

  # Uncomment this line to delete the data disks automatically when deleting the VM
  # delete_data_disks_on_termination = true

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "myosdisk${count.index}"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  # Optional data disks
  storage_data_disk {
    name              = "datadisk_new_${count.index}"
    managed_disk_type = "Standard_LRS"
    create_option     = "Empty"
    lun               = 0
    disk_size_gb      = "1023"
  }

  storage_data_disk {
    name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
    managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
    create_option   = "Attach"
    lun             = 1
    disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
  }

  os_profile {
    computer_name  = "hostname"
    admin_username = "testadmin"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }

  tags {
    environment = "staging"
  }
}
```

## <a name="visualize-the-changes-with-plan"></a>plan으로 변경 내용 시각화

`terraform plan`을 실행하여 템플릿으로 만든 가상 컴퓨터 인프라를 미리 봅니다.

![Terraform Plan](media/terraformPlanVmsWithModules.png)


## <a name="create-the-virtual-machines-with-apply"></a>apply로 가상 컴퓨터 만들기

`terraform apply`를 실행하여 Azure에서 VM 클러스터를 프로비전합니다.

![Terraform Apply](media/terraformApplyVmsWithModules.png)

## <a name="next-steps"></a>다음 단계

- [Azure Terraform 모듈](https://registry.terraform.io/modules/Azure) 목록 찾아보기
- [Terraform으로 가상 컴퓨터 확장 집합]() 만들기