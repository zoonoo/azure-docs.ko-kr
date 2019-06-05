---
title: Azure CLI를 사용하여 Azure DNS 프라이빗 영역 만들기
description: 이 자습서에서는 Azure DNS에 프라이빗 DNS 영역 및 레코드를 만들고 테스트합니다. Azure CLI를 사용하여 첫 번째 프라이빗 DNS 영역 및 레코드를 만들고 관리하는 단계별 가이드입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 2758817d58fdd2e80b302b5f833308dbde1a6b63
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "65916047"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure DNS 프라이빗 영역 만들기

이 자습서에서는 Azure CLI를 사용하여 첫 번째 프라이빗 DNS 영역 및 레코드를 만드는 단계를 설명합니다.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 가상 네트워크에 프라이빗 DNS 영역에 게시하려면 영역 내에서 레코드를 확인하도록 허용된 가상 네트워크 목록을 지정합니다.  이것을 *확인 가상 네트워크*라고 합니다. 또한 VM이 생성되고, IP가 변경되고, 삭제될 때마다 Azure DNS가 호스트 이름 레코드를 유지 관리하게 되는 가상 네트워크를 지정할 수 있습니다.  이것을 *등록 가상 네트워크*라고 합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * DNS 프라이빗 영역 만들기
> * 테스트 가상 머신 만들기
> * 추가 DNS 레코드 만들기
> * 프라이빗 영역 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

원하는 경우 [Azure PowerShell](private-dns-getstarted-powershell.md)을 사용하여 이 자습서를 완료할 수 있습니다.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

먼저 DNS 영역을 포함할 리소스 그룹을 만듭니다. 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>DNS 프라이빗 영역 만들기

DNS 영역은 **ZoneType** 매개 변수 값에 *Private* 값이 포함된 `az network dns zone create` 명령을 사용하여 만듭니다. 다음 예제에서는 **private.contoso.com**이라는 DNS 영역을 **MyAzureResourceGroup**이라는 리소스 그룹에 만들고, **MyAzureVnet**이라는 가상 네트워크에서 DNS 영역을 사용할 수 있도록 합니다.

**ZoneType** 매개 변수를 생략하면 영역이 공용 영역으로 만들어지므로 사설 영역을 만들어야 합니다.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com \
  --zone-type Private \
  --registration-vnets myAzureVNet
```

이름 확인(자동 호스트 이름 생성 제외)을 위한 영역을 만들려는 경우 *registration-vnets* 매개 변수 대신 *resolution-vnets* 매개 변수를 사용할 수 있습니다.

> [!NOTE]
> 자동으로 만들어진 호스트 이름 레코드를 볼 수 없습니다. 하지만 나중에 존재하는지 테스트합니다.

### <a name="list-dns-private-zones"></a>DNS 프라이빗 영역 나열

DNS 영역을 열거하려면 `az network dns zone list`를 사용합니다. 도움말을 보려면 `az network dns zone list --help`을 참조하세요.

리소스 그룹을 지정하면 리소스 그룹 내의 해당 영역만 나열합니다.

```azurecli
az network dns zone list \
  --resource-group MyAzureResourceGroup
```

리소스 그룹을 생략하면 구독의 모든 영역이 나열됩니다.

```azurecli
az network dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>테스트 가상 머신 만들기

이제 프라이빗 DNS 영역을 테스트할 수 있도록 두 대의 가상 머신을 만듭니다.

```azurecli
az vm create \
 -n myVM01 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter
```

이 설정은 완료하는 데 몇 분 정도 걸립니다.

## <a name="create-an-additional-dns-record"></a>추가 DNS 레코드 만들기

DNS 레코드를 만들려면 `az network dns record-set [record type] add-record` 명령을 사용합니다. 도움말과 A 레코드를 추가하는 도움말은 `azure network dns record-set A add-record --help`를 참조하세요.

 다음 예제에서는 리소스 그룹 **MyAzureResourceGroup**의 DNS 영역 **private.contoso.com**에 상대적 이름 **db**가 포함된 레코드를 만듭니다. 레코드 집합의 정규화된 이름은 **db.private.contoso.com**입니다. 레코드 형식은 "A"이고, IP 주소는 "10.2.0.4"입니다.

```azurecli
az network dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>DNS 레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 실행하세요.

```azurecli
az network dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```
두 대의 테스트 가상 머신에 대해 자동으로 생성된 A 레코드가 표시되지 않습니다.

## <a name="test-the-private-zone"></a>프라이빗 영역 테스트

이제 **private.contoso.com** 프라이빗 영역에 대한 이름 확인을 테스트할 수 있습니다.

### <a name="configure-vms-to-allow-inbound-icmp"></a>인바운드 ICMP를 허용하도록 VM 구성

이름 확인을 테스트하는 ping 명령을 사용할 수 있습니다. 따라서 인바운드 ICMP 패킷을 허용하도록 두 대의 가상 머신에서 방화벽을 구성합니다.

1. myVM01에 연결하고, 관리자 권한으로 Windows PowerShell 창을 엽니다.
2. 다음 명령 실행:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

myVM02에서 반복

### <a name="ping-the-vms-by-name"></a>이름별로 VM ping

1. myVM02 Windows PowerShell 명령 프롬프트에서 자동으로 등록된 호스트 이름을 사용하여 myVM01을 ping합니다.
   ```
   ping myVM01.private.contoso.com
   ```
   다음과 유사한 결과가 표시됩니다.
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. 이제 이전에 만든 **db** 이름을 ping합니다.
   ```
   ping db.private.contoso.com
   ```
   다음과 유사한 결과가 표시됩니다.
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>모든 리소스 삭제

더 이상 필요 없다면 **MyAzureResourceGroup** 리소스 그룹을 삭제하여 이 빠른 시작에서 만든 리소스를 삭제합니다.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 프라이빗 DNS 영역을 배포하고, DNS 레코드를 만들고, 영역을 테스트했습니다.
다음으로, 프라이빗 DNS 영역에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [개인 도메인에 Azure DNS 사용](private-dns-overview.md)
