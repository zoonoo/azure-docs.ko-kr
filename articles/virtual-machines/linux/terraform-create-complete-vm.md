---
title: "Terraform을 사용하여 Azure에서 기본 인프라 만들기 | Microsoft Docs"
description: "Terraform을 사용하여 Azure 리소스를 만드는 자세한 정보"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 5377f84a29482e07ae089c7025dc41b8e12bfa3a
ms.contentlocale: ko-kr
ms.lasthandoff: 08/11/2017

---

# <a name="create-basic-infrastructure-in-azure-by-using-terraform"></a>Terraform을 사용하여 Azure에서 기본 인프라 만들기
이 문서에서는 기본 인프라와 함께 가상 컴퓨터를 Azure에 프로비전하기 위해 사용하는 단계를 설명합니다. Terraform 스크립트를 작성하는 방법 및 클라우드 인프라에서 적용하기 전에 변경 내용을 시각화하는 방법을 알아봅니다. 또한 Terraform을 사용하여 Azure에서 인프라를 만드는 방법도 배웁니다.

시작하려면 원하는 텍스트 편집기(Visual Studio Code/Sublime/Vim/etc)에서 \terraform_azure101.tf라는 파일을 만듭니다. Terraform이 폴더 이름을 매개 변수로 사용하고 폴더에 있는 모든 스크립트가 실행되기 때문에 파일의 정확한 이름은 중요하지 않습니다. 다음 코드를 새 파일에 붙여 넣습니다.

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_appId_from_script_execution"
  client_secret   = "your_password_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
스크립트의 `provider` 섹션에서 Terraform에게 Azure 공급자를 사용하여 스크립트의 리소스를 프로비전하도록 지시합니다. subscription_id, appId, 암호 및 tenant_id에 대한 값을 가져오려면 [Terraform 설치 및 구성](terraform-install-configure.md) 가이드를 참조하세요. 이 블록에 값에 대한 환경 변수를 만든 경우 값을 포함하지 않아도 됩니다. 

`azurerm_resource_group` 리소스는 Terraform이 새 리소스 그룹을 만들도록 지시합니다. 이 문서의 뒷부분에 나오는 Terraform에서 사용할 수 있는 더 많은 리소스 유형을 확인할 수 있습니다.

## <a name="execute-the-script"></a>스크립트 실행
스크립트를 실행한 후에 콘솔/명령줄을 끝내고 다음을 입력합니다.
```
terraform init
```
 Azure에 대한 Terraform 공급자를 초기화하려면 디렉터리를 **terraformscripts**로 변경하고 다음 명령을 실행합니다.
```
terraform plan
```
`plan` Terraform 명령을 사용하여 스크립트에 정의된 리소스를 살펴봅니다. 이 명령을 Terraform에서 저장한 상태 정보와 비교한 후 Azure에서 실제 리소스를 생성하지 _않고_ 계획된 실행을 출력합니다. 

이전 명령을 실행한 후에 다음과 같은 화면이 표시되어야 합니다.

![Terraform 계획](./media/terraform/tf_plan2.png)

모든 항목이 올바르면 다음을 실행하여 Azure에 새 리소스 그룹을 프로비전합니다. 
```
terraform apply
```
Azure Portal에서 `terraformtest`라는 비어 있는 새 리소스 그룹이 표시되어야 합니다. 다음과 같은 섹션에서 가상 컴퓨터와 가상 컴퓨터에 대한 모든 지원 인프라를 해당 리소스 그룹에 추가합니다.

## <a name="provision-an-ubuntu-vm-with-terraform"></a>Terraform으로 Ubuntu VM 프로비전
Ubuntu를 실행하는 가상 컴퓨터를 프로비전하는 데 필요한 세부 사항을 사용하여 만든 Terraform 스크립트를 확장해 보겠습니다. 다음 섹션에서 프로비전한 리소스는 다음과 같습니다.

* 단일 서브넷이 있는 네트워크
* 네트워크 인터페이스 카드 
* 저장소 컨테이너가 있는 저장소 계정
* 공용 IP
* 모든 이전 리소스를 활용하는 가상 컴퓨터 

각 Azure Terraform 리소스에 대한 자세한 설명서는 [Terraform 설명서](https://www.terraform.io/docs/providers/azurerm/index.html)를 참조하세요.

편의를 위해 정식 버전의 [프로비전 스크립트](#complete-terraform-script)도 제공됩니다.

### <a name="extend-the-terraform-script"></a>Terraform 스크립트 확장
다음 리소스를 사용하여 만든 스크립트를 확장합니다. 
~~~~
# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}
~~~~
이전 스크립트는 가상 네트워크와 가상 네트워크 내에 있는 서브넷을 만듭니다. 가상 네트워크 및 서브넷 정의 모두에서 "${azurerm_resource_group.helloterraform.name}"을 통해 이미 만든 리소스 그룹에 대한 참조를 기록해 둡니다.

~~~~
# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}
~~~~
이전 스크립트 코드 조각은 공용 IP와 생성된 공용 IP를 활용하는 네트워크 인터페이스를 만듭니다. subnet_id 및 public_ip_address_id에 대한 참조를 기록해 둡니다. Terraform에는 네트워크 인터페이스가 해당 네트워크 인터페이스를 만들기 전에 생성해야 하는 리소스에 대한 종속성을 포함하고 있음을 알고 있는 기본 제공 인텔리전스 기능이 있습니다.

~~~~
# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}
~~~~
여기에서 저장소 계정을 만들고 해당 저장소 계정 내에서 저장소 컨테이너를 정의합니다. 이 저장소 계정은 만들 가상 컴퓨터의 VHD(가상 하드 디스크)를 저장하는 위치입니다.

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
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
~~~~
마지막으로, 이전 코드 조각은 이미 프로비전된 모든 리소스를 활용하는 가상 컴퓨터를 만듭니다. VHD의 저장소 계정 및 컨테이너, 공용 IP 및 서브넷을 지정한 네트워크 인터페이스 및 이미 만든 리소스 그룹이 있습니다. vm_size 속성을 기록해 둡니다. 여기서 스크립트는 Azure A0 SKU를 지정합니다.

### <a name="execute-the-script"></a>스크립트 실행
전체 스크립트가 저장되었으면 콘솔/명령줄을 끝내고 다음을 입력합니다.
```
terraform apply
```
일정 시간이 지난 후 가상 컴퓨터를 비롯한 리소스가 Azure Portal의 `terraformtest` 리소스 그룹에 나타납니다.

## <a name="complete-terraform-script"></a>전체 Terraform 스크립트

편의를 위해 이 문서에서 설명하는 모든 인프라를 프로 비전하는 전체 Terraform 스크립트는 아래와 같습니다.

```
variable "resourcesname" {
  default = "helloterraform"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = "XXX"
  client_id       = "XXX"
  client_secret   = "XXX"
  tenant_id       = "XXX"
}

# create a resource group if it doesn't exist
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}

# create virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "tfvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "tfsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}


# create public IPs
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}

# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
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

## <a name="next-steps"></a>다음 단계
Terraform을 사용하여 Azure에서 기본 인프라를 만들었습니다. 부하 분산 장치 및 가상 컴퓨터 확장 집합을 사용하는 예제를 비롯한 보다 복잡한 시나리오는 여러 가지 [Azure에 대한 Terraform 예제](https://github.com/hashicorp/terraform/tree/master/examples)를 참조하세요. 지원되는 최신 Azure 공급자 전체 목록은 [Terraform 설명서](https://www.terraform.io/docs/providers/azurerm/index.html)를 참조하세요.

