---
title: '자습서: NAT Gateway 만들기 및 테스트 - Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: 이 자습서에서는 Azure Portal을 사용하여 NAT Gateway를 만들고 NAT 서비스를 테스트하는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 7c26487018b9632b5780dcd7c56ab36f286f8cac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80059991"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>자습서: Azure Portal을 사용하여 NAT Gateway 만들기 및 NAT 서비스 테스트

이 자습서에서는 Azure에서 가상 머신에 대한 아웃바운드 연결을 제공하는 NAT 게이트웨이를 만듭니다. NAT 게이트웨이를 테스트하려면 원본 및 대상 가상 머신을 배포해야 합니다. 공용 IP 주소에 대한 원본 가상 머신에서 대상 가상 머신으로의 아웃바운드 연결을 설정하여 NAT 게이트웨이를 테스트합니다.  이 자습서에서는 간소화하기 위해 원본 및 대상을 동일한 리소스 그룹의 서로 다른 두 가상 네트워크에 배포합니다.

원할 경우 포털 대신 [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) 또는 [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md)을 사용하여 이러한 단계를 수행할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="prepare-the-source-for-outbound-traffic"></a>아웃바운드 트래픽에 대한 원본 준비

다음 단계에서는 전체 테스트 환경을 구성하고 테스트 자체를 실행하는 과정을 안내합니다. 먼저 이후 단계에서 만드는 NAT 게이트웨이 리소스를 사용하는 원본으로 시작합니다.

## <a name="virtual-network-and-parameters"></a>가상 네트워크 및 매개 변수

VM을 배포하고 NAT 게이트웨이를 사용하려면 먼저 리소스 그룹과 가상 네트워크를 만들어야 합니다.

이 섹션에서는 단계의 다음 매개 변수를 아래 정보로 바꾸어야 합니다.

| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | 미국 동부 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>원본 가상 머신 만들기

이제 NAT 서비스를 사용할 VM을 만듭니다. 이 VM에는 VM에 액세스할 수 있도록 인스턴스 수준 공용 IP로 사용할 공용 IP가 있습니다. NAT 서비스는 흐름 방향을 인식하며 서브넷의 기본 인터넷 대상을 대체합니다. VM의 공용 IP 주소는 아웃바운드 연결에 사용되지 않습니다.

NAT 게이트웨이를 테스트하기 위해 외부에서 이 VM에 액세스할 수 있도록 공용 IP 주소 리소스를 인스턴스 수준 공용 IP로 할당합니다. 이 주소는 테스트를 위해 액세스하는 데만 사용됩니다.  NAT 서비스를 다른 아웃바운드 옵션보다 우선적으로 적용하는 방법을 설명합니다.

또한 공용 IP 없이 이 VM을 만들고, 연습용으로 공용 IP가 없는 점프 박스로 사용할 다른 VM을 만들 수도 있습니다.

1. 포털의 왼쪽 위에서 **리소스 만들기** > **Compute** > **Ubuntu Server 18.04 LTS**를 차례로 선택하거나, Marketplace 검색에서 **Ubuntu Server 18.04 LTS**를 검색합니다.

2. **가상 머신 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.
   - **구독** > **리소스 그룹**: **myResourceGroupNAT**를 선택합니다.
   - **인스턴스 정보** > **가상 머신 이름**: **myVMsource**를 입력합니다.
   - **인스턴스 정보** > **지역**: **미국 동부 2**를 선택합니다.
   - **관리자 계정** > **인증 입력**: **암호**를 선택합니다.
   - **관리자 계정** > **사용자 이름**, **암호** 및 **암호 확인** 정보를 입력합니다.
   - **인바운드 포트 규칙** > **공용 인바운드 포트**: **선택한 포트 허용**을 선택합니다.
   - **인바운드 포트 규칙** > **인바운드 포트 선택**: **SSH(22)** 를 선택합니다.
   - **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹**을 차례로 선택합니다.

3. **네트워킹** 탭에서 다음 항목이 선택되어 있는지 확인합니다.
   - **가상 네트워크**: **myVnetsource**
   - **서브넷**: **mySubnetsource**
   - **공용 IP**: **새로 만들기**를 선택합니다.  **공용 IP 주소 만들기** 창의 **이름** 필드에서 **myPublicIPsourceVM**을 입력합니다. **SKU**에 대해 **표준**을 선택합니다. 나머지는 기본값으로 두고 **확인**을 클릭합니다.
   - **NIC 네트워크 보안 그룹**: **기본**을 선택합니다.
   - **공용 인바운드 포트**: **선택한 포트 허용**을 선택합니다.
   - **인바운드 포트 선택**: **SSH**가 선택되었는지 확인합니다.

4. **관리** 탭의 **모니터링**에서 **부트 진단**을 **끄기**로 설정합니다.

5. **검토 + 만들기**를 선택합니다.

6. 설정을 검토하고 **만들기**를 클릭합니다.

## <a name="create-the-nat-gateway"></a>NAT Gateway 만들기

하나 이상의 공용 IP 주소 리소스, 공용 IP 접두사 또는 둘 모두는 NAT 게이트웨이에서 사용할 수 있습니다. 공용 IP 리소스, 공용 IP 접두사 및 NAT 게이트웨이 리소스를 추가합니다.

이 섹션에서는 NAT 게이트웨이 리소스를 사용하여 NAT 서비스의 다음 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
  - NAT 게이트웨이 리소스에서 변환하는 아웃바운드 흐름에 사용할 공용 IP 풀 및 공용 IP 접두사입니다.
  - 유휴 시간 제한을 4분(기본값)에서 10분으로 변경합니다.

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **공용 IP 주소**를 차례로 선택하거나, Marketplace 검색에서 **공용 IP 주소**를 검색합니다. 

2. **공용 IP 주소 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | IP 버전 | **IPv4**를 선택합니다.
    | SKU | **표준**을 선택합니다.
    | 속성 | **myPublicIPsource**를 입력합니다. |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **myResourceGroupNAT**를 선택합니다. |
    | 위치 | **미국 동부 2**를 선택합니다.|

3. 나머지는 기본값으로 두고, **만들기**를 선택합니다.

### <a name="create-a-public-ip-prefix"></a>공용 IP 접두사 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **공용 IP 접두사**를 차례로 선택하거나, Marketplace 검색에서 **공용 IP 접두사**를 검색합니다.

2. **공용 IP 접두사 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.
   - **구독** > **리소스 그룹**: **myResourceGroupNAT**>를 선택합니다.
   - **인스턴스 정보** > **이름**: **myPublicIPprefixsource**를 입력합니다.
   - **인스턴스 정보** > **지역**: **미국 동부 2**를 선택합니다.
   - **인스턴스 세부 정보** > **접두사 크기**: **/31(2개 주소)** 을 선택합니다.

3. 나머지는 기본값으로 두고 **검토 + 만들기**를 선택합니다.

4. 설정을 검토한 다음, **만들기**를 선택합니다.


### <a name="create-a-nat-gateway-resource"></a>NAT 게이트웨이 리소스 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **NAT 게이트웨이**를 차례로 선택하거나, Marketplace 검색에서 **NAT 게이트웨이**를 검색합니다.

2. **NAT(Network Address Translation) 게이트웨이 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.
   - **구독** > **리소스 그룹**: **myResourceGroupNAT**를 선택합니다.
   - **인스턴스 정보** > **NAT 게이트웨이 이름**: **myNATgateway**를 입력합니다.
   - **인스턴스 정보** > **지역**: **미국 동부 2**를 선택합니다.
   - **인스턴스 정보** > **유휴 시간 제한(분)** : **10**을 입력합니다.
   - **공용 IP** 탭을 선택하거나 **다음: 공용 IP**를 선택합니다.

3. **공용 IP** 탭에서 다음 값을 입력하거나 선택합니다.
   - **공용 IP 주소**: **myPublicIPsource**를 선택합니다.
   - **공용 IP 접두사**: **myPublicIPprefixsource**를 선택합니다.
   - **서브넷** 탭을 선택하거나 **다음: 서브넷**을 선택합니다.

4. **서브넷** 탭에서 다음 값을 입력하거나 선택합니다.
   - **Virtual Network**: **myResourceGroupNAT** > **myVnetsource**를 차례로 선택합니다.
   - **서브넷 이름**: **mySubnetsource** 옆의 상자를 선택합니다.

5. **검토 + 만들기**를 선택합니다.

6. 설정을 검토한 다음, **만들기**를 선택합니다.

이제 인터넷 대상으로의 모든 아웃바운드 트래픽에서 NAT 서비스를 사용합니다.  UDR을 구성할 필요가 없습니다.


## <a name="prepare-destination-for-outbound-traffic"></a>아웃바운드 트래픽에 대한 대상 준비

이제 NAT 서비스에서 변환하는 아웃바운드 트래픽의 대상을 만들어 테스트할 수 있습니다.


## <a name="virtual-network-and-parameters-for-destination"></a>대상에 대한 가상 네트워크 및 매개 변수

대상 VM을 배포하기 전에 대상 가상 머신이 상주할 수 있는 가상 네트워크를 만들어야 합니다. 다음 단계는 대상 엔드포인트를 공개하기 위해 약간 변경되는 원본 VM에 적용되는 것과 동일합니다.

이 섹션에서는 단계의 다음 매개 변수를 아래 정보로 바꾸어야 합니다.

| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | 미국 동부 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>대상 가상 머신 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **Compute** > **Ubuntu Server 18.04 LTS**를 차례로 선택하거나, Marketplace 검색에서 **Ubuntu Server 18.04 LTS**를 검색합니다.

2. **가상 머신 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.
   - **구독** > **리소스 그룹**: **myResourceGroupNAT**를 선택합니다.
   - **인스턴스 정보** > **가상 머신 이름**: **myVMdestination**을 입력합니다.
   - **인스턴스 정보** > **지역**: **미국 동부 2**를 선택합니다.
   - **관리자 계정** > **인증 입력**: **암호**를 선택합니다.
   - **관리자 계정** > **사용자 이름**, **암호** 및 **암호 확인** 정보를 입력합니다.
   - **인바운드 포트 규칙** > **공용 인바운드 포트**: **선택한 포트 허용**을 선택합니다.
   - **인바운드 포트 규칙** > **인바운드 포트 선택**: **SSH(22)** 및 **HTTP(80)** 를 선택합니다.
   - **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹**을 차례로 선택합니다.

3. **네트워킹** 탭에서 다음 항목이 선택되어 있는지 확인합니다.
   - **가상 네트워크**: **myVnetdestination**
   - **서브넷**: **mySubnetdestination**
   - **공용 IP**: **새로 만들기**를 선택합니다.  **공용 IP 주소 만들기** 창의 **이름** 필드에서 **myPublicIPdestinationVM**을 입력합니다. **SKU**에 대해 **표준**을 선택합니다. 나머지는 기본값으로 두고 **확인**을 클릭합니다.
   - **NIC 네트워크 보안 그룹**: **기본**을 선택합니다.
   - **공용 인바운드 포트**: **선택한 포트 허용**을 선택합니다.
   - **인바운드 포트 선택**: **SSH** 및 **HTTP**가 선택되었는지 확인합니다.

4. **관리** 탭의 **모니터링**에서 **부트 진단**을 **끄기**로 설정합니다.

5. **검토 + 만들기**를 선택합니다.

6. 설정을 검토한 다음, **만들기**를 선택합니다.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>대상 VM에서 웹 서버 및 테스트 페이로드 준비

먼저 대상 VM의 IP 주소를 검색해야 합니다. 

1. 포털의 왼쪽에서 **리소스 그룹**을 선택합니다.
2. **myResourceGroupNAT**를 선택합니다.
3. **myVMdestination**을 선택합니다.
4. **개요**에서 **공용 IP 주소** 값을 복사하여 VM에 액세스하는 데 사용할 수 있도록 메모장에 붙여넣습니다.

>[!IMPORTANT]
>공용 IP 주소를 복사한 다음, 이후 단계에서 사용할 수 있도록 메모장에 붙여넣습니다. 대상 가상 머신임을 나타냅니다.

### <a name="sign-in-to-destination-vm"></a>대상 VM에 로그인

브라우저에서 [Azure Cloud Shell](https://shell.azure.com)을 엽니다. 이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

로그인하면 다음 명령을 복사하여 붙여넣습니다.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

이러한 명령은 가상 머신을 업데이트하고, nginx를 설치하며, 100KB 파일을 만듭니다. 이 파일은 NAT 서비스를 사용하여 원본 VM에서 검색됩니다.

대상 VM을 사용하여 SSH 세션을 닫습니다.

## <a name="prepare-test-on-source-vm"></a>원본 VM에서 테스트 준비

먼저 원본 VM의 IP 주소를 검색해야 합니다.

1. 포털의 왼쪽에서 **리소스 그룹**을 선택합니다.
2. **myResourceGroupNAT**를 선택합니다.
3. **myVMsource**를 선택합니다.
4. **개요**에서 **공용 IP 주소** 값을 복사하여 VM에 액세스하는 데 사용할 수 있도록 메모장에 붙여넣습니다.

>[!IMPORTANT]
>공용 IP 주소를 복사한 다음, 이후 단계에서 사용할 수 있도록 메모장에 붙여넣습니다. 원본 가상 머신임을 나타냅니다.

### <a name="log-into-source-vm"></a>원본 VM에 로그인

브라우저에서 [Azure Cloud Shell](https://shell.azure.com)에 대한 새 탭을 엽니다.  이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

다음 명령을 복사하고 붙여넣어 NAT 서비스 테스트를 준비합니다.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

이 명령은 가상 머신을 업데이트하고, go를 설치하고, GitHub에서 [hey](https://github.com/rakyll/hey)를 설치하고, 셸 환경을 업데이트합니다.

이제 NAT 서비스를 테스트할 준비가 되었습니다.

## <a name="validate-nat-service"></a>NAT 서비스 유효성 검사

원본 VM에 로그인한 상태에서 **curl** 및 **hey**를 사용하여 대상 IP 주소에 대한 요청을 생성할 수 있습니다.

curl을 사용하여 100KB 파일을 검색합니다.  아래 예제의 **\<ip-address-destination>** 을 이전에 복사한 대상 IP 주소로 바꿉니다.  **--output** 매개 변수는 검색된 파일이 삭제됨을 나타냅니다.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

또한 **hey**를 사용하여 일련의 요청을 생성할 수도 있습니다. 다시 한 번 **\<ip-address-destination>** 을 이전에 복사한 대상 IP 주소로 바꿉니다.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

이 명령은 TCP 연결을 다시 사용하지 않고 30초의 시간 제한에서 동시에 100개의 요청을 10회 생성합니다.  각 요청에서 100KB를 검색합니다.  실행이 완료되면 **hey**에서 수행된 NAT 서비스의 작동 상태에 대한 몇 가지 통계를 보고합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, NAT 게이트웨이 및 모든 관련 리소스를 삭제합니다. NAT 게이트웨이가 포함된**myResourceGroupNAT** 리소스 그룹을 선택한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 NAT 게이트웨이를 만들고, 원본 및 대상 VM을 만든 다음, NAT 게이트웨이를 테스트했습니다.

Azure Monitor에서 메트릭을 검토하여 NAT 서비스의 작동 상태를 확인합니다. 사용 가능한 SNAT 포트의 리소스 소모와 같은 문제를 진단합니다.  이러한 SNAT 포트의 리소스 소모는 공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 둘 모두를 추가하여 쉽게 해결할 수 있습니다.

- [Virtual Network NAT](./nat-overview.md)에 대한 자세한 정보
- [NAT 게이트웨이 리소스](./nat-gateway-resource.md)에 대한 자세한 정보
- [Azure CLI를 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-cli.md)를 배포하기 위한 빠른 시작.
- [Azure PowerShell을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-powershell.md)를 배포하기 위한 빠른 시작.
- [Azure Portal을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-portal.md)를 배포하기 위한 빠른 시작.

> [!div class="nextstepaction"]

