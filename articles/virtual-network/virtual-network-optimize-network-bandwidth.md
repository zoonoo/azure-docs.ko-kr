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
ms.date: 02/01/2017
ms.author: steveesp
translationtype: Human Translation
ms.sourcegitcommit: 50be31e179bf52e009596fbc68339dfb5a1aa1e4
ms.openlocfilehash: d53b1cae9845be32bd053ef196203ea83df06b10
ms.lasthandoff: 02/15/2017


---

# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Azure Virtual Machine에 대한 네트워크 처리량 최적화

Azure VM(Virtual Machine)에는 네트워크 처리량에 대해 추가로 최적화할 수 있는 기본 네트워크 설정이 있습니다. 이 문서에서는 Ubuntu, CentOS 및 Red Hat과 같은 주요 배포판을 비롯한 Microsoft Azure Windows 및 Linux VM에 대해 네트워크 처리량을 최적화하는 방법을 설명합니다.

## <a name="windows-vm"></a>Windows VM

RSS(수신측 배율)를 사용하는 VM은 RSS 없는 VM보다 더 높은 최대 처리량에 도달할 수 있습니다. RSS는 Windows VM에서 기본적으로 사용되지 않도록 설정될 수 있습니다. 다음 단계를 완료하여 RSS가 사용되도록 설정되어 있는지 여부를 확인하고 사용되지 않도록 설정되어 있으면 사용되도록 설정합니다.

1. `Get-NetAdapterRss` PowerShell 명령을 입력하여 RSS가 네트워크 어댑터에 대해 사용되도록 설정되어 있는지 확인합니다. `Get-NetAdapterRss`에서 반환된 다음 예제 출력에서 RSS는 사용되도록 설정되어 있지 않습니다.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. 다음 명령을 입력하여 RSS를 사용하도록 설정합니다.

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    이전 명령에는 출력이 없습니다. 이 명령은 NIC 설정을 변경했으므로 약&1;분 동안 일시적으로 연결이 끊겼습니다. 연결이 끊긴 동안 다시 연결 중 대화 상자가 나타납니다. 일반적으로 세 번째 시도 후 연결이 복원합니다.
3. `Get-NetAdapterRss` 명령을 다시 입력하여 VM에서 RSS가 사용되도록 설정되어 있는지 확인합니다. 성공하면 다음 예제 출력이 반환됩니다.

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : True
    ```

## <a name="linux-vm"></a>Linux VM

RSS는 Azure Linux VM에 기본적으로 항상 사용되도록 설정됩니다. 2017년 1월 이후에 출시된 Linux 커널에는 Linux VM이 더 높은 네트워크 처리량을 얻도록 하는 새로운 네트워크 최적화 옵션이 포함되어 있습니다.

### <a name="ubuntu"></a>Ubuntu

최적화를 얻으려면 먼저 지원되는 최신 버전으로 업데이트합니다. 예를 들어 2017년 1월이면 다음과 같습니다.
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

최적화를 얻으려면 먼저 지원되는 최신 버전으로 업데이트합니다. 예를 들어 2017년 1월이면 다음과 같습니다.
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
업데이트가 완료되면 최신 LIS(Linux Integration Services)를 설치합니다.
처리량 최적화 기능은 LIS 4.1.3부터 포함됩니다. 다음 명령을 입력하여 LIS를 설치합니다.

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

최적화를 얻으려면 먼저 지원되는 최신 버전으로 업데이트합니다. 예를 들어 2017년 1월이면 다음과 같습니다.

"Publisher": "RedHat" "Offer": "RHEL" "Sku": "7.3" "Version": "7.3.20161104"

업데이트가 완료되면 최신 LIS(Linux Integration Services)를 설치합니다.
처리량 최적화 기능은 LIS 4.1.3부터 포함됩니다. 다음 명령을 입력하여 LIS를 다운로드한 후 설치합니다.

```bash
mkdir lis4.1.3
cd lis4.1.3
wget https://download.microsoft.com/download/7/6/B/76BE7A6E-E39F-436C-9353-F4B44EF966E9/lis-rpms-4.1.3.tar.gz
tar xvzf lis-rpms-4.1.3.tar.gz
cd LISISO
install.sh #or upgrade.sh if previous LIS was previously installed
```
 
[다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=51612)를 확인하여 Hyper-V용 Linux Integration Services 버전 4.1에 대해 자세히 알아보세요.

