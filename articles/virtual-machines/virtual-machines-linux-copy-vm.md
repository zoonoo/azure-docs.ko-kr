---
title: Azure Linux VM 복사본 만들기 | Microsoft Docs
description: Resource Manager 배포 모델에서 Azure Linux 가상 컴퓨터의 복사본을 만드는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn

---
# Azure에서 실행되는 Linux 가상 컴퓨터의 복사본 만들기
이 문서는 Resource Manager 배포 모델에서 Linux를 실행하는 Azure VM(가상 컴퓨터)의 복사본을 만드는 방법에 대해 설명합니다. 먼저 운영 체제와 데이터 디스크를 새 컨테이너로 복사한 다음 네트워크 리소스를 설정하고 새 가상 컴퓨터를 만듭니다.

[사용자 지정 디스크 이미지에서 VM 업로드 및 만들](virtual-machines-linux-upload-vhd.md) 수도 있습니다.

## 시작하기 전에
다음 단계를 시작하기 전에 다음 필수 조건을 충족하는지 확인합니다.

* 컴퓨터에 [Azure CLI](../xplat-cli-install.md)를 다운로드하여 설치했습니다.
* 또한 기존 Azure Linux VM에 대한 다음과 같은 몇 가지 정보가 필요합니다.

| 소스 VM 정보 | 가져올 수 있는 위치 |
| --- | --- |
| VM 이름 |`azure vm list` |
| 리소스 그룹 이름 |`azure vm list` |
| 위치 |`azure vm list` |
| 저장소 계정 이름 |`azure storage account list -g <resourceGroup>` |
| 컨테이너 이름 |`azure storage container list -a <sourcestorageaccountname>` |
| 소스 VM VHD 파일 이름 |`azure storage blob list --container <containerName>` |

* 새 VM에 대해 다음과 같은 몇 가지 선택 항목을 선택해야 합니다. <br> -컨테이너 이름 <br> -VM 이름 <br> -VM 크기 <br> -vNet 이름 <br> -서브넷 이름 <br> -IP 이름 <br> -NIC 이름

## 로그인 및 구독 설정
1. CLI에 로그인합니다.
   
        azure login
2. Resource Manager 모드에 있는지 확인합니다.
   
        azure config mode arm
3. 올바른 구독을 설정합니다. 'azure account list'를 사용하여 모든 구독을 볼 수 있습니다.
   
        azure account set <SubscriptionId>

## VM을 중지합니다.
중지하고 소스 VM 할당을 취소합니다. 'azure vm list'를 사용하여 구독의 모든 VM 목록과 해당 리소스 그룹 이름을 가져올 수 있습니다.

        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## VHD 복사
`azure storage blob copy start` 사용하여 소스 저장소에서 대상으로 VHD를 복사할 수 있습니다. 이 예제에서는 VHD를 동일한 저장소 계정의 다른 컨테이너에 VHD를 복사하게 됩니다.

동일한 저장소 계정의 다른 컨테이너에 VHD를 복사하려면 다음을 입력합니다.

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>


## 새 VM에 대한 가상 네트워크 설정
새 VM에 대한 가상 네트워크 및 NIC를 설정합니다.

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## 새 VM 만들기
이제 [Resource Manager 템플릿을 사용하거나](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) 다음을 입력하여 복사한 디스크에 대한 URI을 지정하는 방식으로 CLI를 통해 업로드된 가상 디스크에서 VM을 만들 수 있습니다.

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## 다음 단계
Azure CLI를 사용하여 새 가상 컴퓨터를 관리하는 방법을 알아보려면 [Azure Resource Manager의 Azure CLI 명령](azure-cli-arm-commands.md)을 참조하세요.

<!---HONumber=AcomDC_0803_2016-->