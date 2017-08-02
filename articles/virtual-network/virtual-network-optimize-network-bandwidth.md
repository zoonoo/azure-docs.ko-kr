---
title: "VM 네트워크 처리량 최적화 | Microsoft Docs"
description: "Azure Virtual Machine 네트워크 처리량을 최적화하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/07/2017
ms.author: steveesp
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: a7c32f07516ca83bc2fb5ad5a9a526631932ad4a
ms.contentlocale: ko-kr
ms.lasthandoff: 07/08/2017


---

# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Azure Virtual Machine에 대한 네트워크 처리량 최적화

Azure VM(Virtual Machine)에는 네트워크 처리량에 대해 추가로 최적화할 수 있는 기본 네트워크 설정이 있습니다. 이 문서에서는 Ubuntu, CentOS 및 Red Hat과 같은 주요 배포판을 비롯한 Microsoft Azure Windows 및 Linux VM에 대해 네트워크 처리량을 최적화하는 방법을 설명합니다.

## <a name="windows-vm"></a>Windows VM

Windows VM이 [가속화된 네트워킹](virtual-network-create-vm-accelerated-networking.md)으로 지원되는 경우 해당 기능을 사용하도록 설정하는 것이 최적의 처리량을 얻기 위한 구성입니다. RSS(수신측 배율)를 사용하는 다른 모든 Windows VM은 RSS 없는 VM보다 더 높은 최대 처리량에 도달할 수 있습니다. RSS는 Windows VM에서 기본적으로 사용되지 않도록 설정될 수 있습니다. 다음 단계를 완료하여 RSS가 사용되도록 설정되어 있는지 여부를 확인하고 사용되지 않도록 설정되어 있으면 사용되도록 설정합니다.

1. `Get-NetAdapterRss` PowerShell 명령을 입력하여 RSS가 네트워크 어댑터에 대해 사용되도록 설정되어 있는지 확인합니다. `Get-NetAdapterRss`에서 반환된 다음 예제 출력에서 RSS는 사용되도록 설정되어 있지 않습니다.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. 다음 명령을 입력하여 RSS를 사용하도록 설정합니다.

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    이전 명령에는 출력이 없습니다. 이 명령은 NIC 설정을 변경했으므로 약 1분 동안 일시적으로 연결이 끊겼습니다. 연결이 끊긴 동안 다시 연결 중 대화 상자가 나타납니다. 일반적으로 세 번째 시도 후 연결이 복원합니다.
3. `Get-NetAdapterRss` 명령을 다시 입력하여 VM에서 RSS가 사용되도록 설정되어 있는지 확인합니다. 성공하면 다음 예제 출력이 반환됩니다.

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Linux VM

RSS는 Azure Linux VM에 기본적으로 항상 사용되도록 설정됩니다. 2017년 1월 이후에 출시된 Linux 커널에는 Linux VM이 더 높은 네트워크 처리량을 얻도록 하는 새로운 네트워크 최적화 옵션이 포함되어 있습니다.

### <a name="ubuntu"></a>Ubuntu

최적화를 얻으려면 먼저 지원되는 최신 버전으로 업데이트합니다. 예를 들어 2017년 6월이면 다음과 같습니다.
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
업데이트가 완료되면 다음 명령을 입력하여 최신 커널을 가져옵니다.

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

선택적 명령:

`apt-get -y dist-upgrade`

### <a name="centos"></a>CentOS

최적화를 얻으려면 먼저 지원되는 최신 버전으로 업데이트합니다. 예를 들어 2017년 5월이면 다음과 같습니다.
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
업데이트가 완료되면 최신 LIS(Linux Integration Services)를 설치합니다.
처리량 최적화 기능은 LIS 4.2부터 포함됩니다. 다음 명령을 입력하여 LIS를 설치합니다.

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

최적화를 얻으려면 먼저 지원되는 최신 버전으로 업데이트합니다. 예를 들어 2017년 1월이면 다음과 같습니다.
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7.3"
"Version": "7.3.2017062722"
```
업데이트가 완료되면 최신 LIS(Linux Integration Services)를 설치합니다.
처리량 최적화 기능은 LIS 4.2부터 포함됩니다. 다음 명령을 입력하여 LIS를 다운로드한 후 설치합니다.

```bash
mkdir lis4.2.1
cd lis4.2.1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.1-1.tar.gz
tar xvzf lis-rpms-4.2.1-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

[다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=55106)를 확인하여 Hyper-V용 Linux Integration Services 버전 4.2에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계
* 이제 VM이 최적화되었으므로 [Azure VM 대역폭/처리량 테스트](virtual-network-bandwidth-testing.md)를 통해 시나리오에 대한 결과를 확인하세요.
* [Azure Virtual Network FAQ(질문과 대답)](virtual-networks-faq.md)에 대해 자세히 알아보기

