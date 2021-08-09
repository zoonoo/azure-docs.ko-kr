---
title: 도메인 영역 파일 가져오기 및 내보내기 - Azure CLI
titleSuffix: Azure DNS
description: Azure CLI를 사용하여 Azure DNS(Domain Name System)에 DNS 영역 파일을 가져오고 내보내는 방법을 알아봅니다.
services: dns
author: rohinkoul
ms.service: dns
ms.date: 04/29/2021
ms.author: rohink
ms.topic: how-to
ms.openlocfilehash: 949e497057646507fcaa04907d032beb5b8ef47c
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108316896"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Azure CLI를 사용하여 DNS 영역 파일 가져오기 및 내보내기

이 문서에서는 Azure CLI를 사용하여 Azure DNS에서 DNS 영역 파일을 가져오고 내보내는 방법을 알아봅니다.

## <a name="introduction-to-dns-zone-migration"></a>DNS 영역 마이그레이션 소개

DNS 영역 파일은 영역의 모든 DNS(Domain Name System) 레코드에 대한 정보를 포함하는 텍스트 파일입니다. 표준 형식을 따르며 이는 DNS 시스템 간에 DNS 레코드를 전송하는 데 적합하도록 만듭니다. 영역 파일을 사용하는 것은 DNS 영역을 Azure DNS로 가져오는 빠르고 편리한 방법입니다. Azure DNS에서 영역 파일을 내보내 다른 DNS 시스템과 함께 사용할 수도 있습니다.

Azure DNS는 Azure CLI(명령줄 인터페이스I)를 사용하여 영역 파일 가져오기 및 내보내기를 지원합니다. 영역 파일 가져오기는 현재 Azure PowerShell 또는 Azure Portal에서 지원되지 **않습니다**.

Azure CLI는 Azure 서비스를 관리하는 데 사용하는 플랫폼 간 명령줄 도구입니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)에서 Windows, Mac 및 Linux용으로 사용할 수 있습니다.

## <a name="obtain-your-existing-dns-zone-file"></a>기존 DNS 영역 파일 가져오기

Azure DNS에 DNS 영역 파일을 가져오기 전에 영역 파일의 복사본을 가져와야 합니다. 이 파일의 원본은 DNS 영역이 현재 호스팅되는 위치에 따라 달라집니다.

* DNS 영역이 현재 파트너 서비스에 의해 호스트되는 경우 DNS 영역 파일을 다운로드할 수 있는 방법이 있습니다. 파트너 서비스에는 도메인 등록 기관, 전용 DNS 호스팅 공급자 또는 대체 클라우드 공급자가 포함됩니다.
* 사용자의 DNS 영역이 Windows DNS에서 호스팅되는 경우 영역 파일의 기본 폴더는 **%systemroot%\system32\dns** 입니다. 또한 각 영역 파일의 전체 경로는 DNS 콘솔의 **일반** 탭에 표시됩니다.
* DNS 영역이 BIND를 사용하여 호스팅되는 경우 각 영역에 대한 영역 파일의 위치는 바인딩 구성 파일 **named.conf** 에 지정됩니다.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Azure DNS에 DNS 영역 파일 가져오기

영역이 아직 없는 경우 영역 파일을 가져오면 Azure DNS에서 새 영역을 만듭니다. 영역이 존재하는 경우 영역 파일의 레코드 집합은 기존 레코드 집합과 병합됩니다.

### <a name="merge-behavior"></a>병합 동작

* 기본적으로 새 레코드 집합은 기존 레코드 집합과 병합됩니다. 병합된 레코드 집합 내의 동일한 레코드는 중복을 제거합니다.
* 레코드 집합을 병합하는 경우 기존 레코드 집합의 TTL(time to live)이 사용됩니다.
* SOA(Start of Authority) 매개 변수(`host` 제외)는 항상 가져온 영역 파일에서 가져옵니다. 영역 루트에 설정된 이름 서버 레코드도 항상 가져온 영역 파일에서 가져온 TTL을 사용합니다.
* 가져온 CNAME 레코드는 기존 CNAME 레코드를 동일한 이름으로 바꾸지 않습니다.  
* CNAME 레코드와 이름은 같고 유형은 다른 레코드 간에 충돌이 발생하면 기존 레코드가 사용됩니다.

### <a name="additional-information-about-importing"></a>가져오기에 대한 추가 정보

다음 정보는 영역 가져오기 프로세스에 대한 추가 기술 세부 정보를 제공합니다.

* `$TTL` 지시어는 선택적이며 지원됩니다. `$TTL` 지시어를 지정하지 않는 경우 기본 TTL 3600초로 설정하고 명시적 TTL이 없는 레코드를 가져올 수 있습니다. 동일한 레코드 집합의 두 레코드가 다른 TTL을 지정하는 경우 낮은 값이 사용됩니다.
* `$ORIGIN` 지시어는 선택적이며 지원됩니다. `$ORIGIN`이 설정되지 않은 경우 사용되는 기본값은 마침표 "."를 포함하여 명령줄에 지정된 영역 이름입니다.
* `$INCLUDE` 및 `$GENERATE` 지시문은 지원되지 않습니다.
* A, AAAA, CAA, CNAME, MX, NS, SOA, SRV, TXT 등의 레코드 형식을 지원합니다.
* SOA 레코드는 영역이 만들어질 때 Azure DNS에서 자동으로 생성됩니다. 영역 파일을 가져오는 경우 `host` 매개 변수를 *제외한* 모든 SOA 매개 변수는 영역 파일에서 가져옵니다. 이 매개 변수는 Azure DNS에서 제공하는 기본 이름 서버를 참조해야 하므로 Azure DNS에서 제공하는 값을 사용합니다.
* 또한 영역을 만들 때 역영 광선의 이름 서버 레코드 집합은 Azure DNS에서 자동으로 만들어집니다. 이 레코드 집합의 TTL만을 가져옵니다. 이러한 레코드는 Azure DNS에서 제공하는 이름 서버 이름을 포함합니다. 레코드 데이터를 가져온 영역 파일에 포함된 값으로 덮어쓰지 않습니다.
* 공개 미리 보기 중에 Azure DNS는 단일 문자 TXT 레코드만 지원합니다. 다중 문자열 TXT 레코드는 연결되어 255자로 잘립니다.

### <a name="cli-format-and-values"></a>CLI 형식 및 값

DNS 영역을 가져오는 Azure CLI 명령 형식은 다음과 같습니다.

```azurecli-interactive-interactive
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

값

* `<resource group>` 은(는) Azure DNS의 영역에 대한 리소스 그룹의 이름입니다.
* `<zone name>` 은(는) 영역의 이름입니다.
* `<zone file name>` 은(는) 가져올 영역 파일의 경로/이름입니다.

이 이름을 가진 영역이 리소스 그룹에 아직 없는 경우 자동으로 만들어집니다. 기존 영역의 경우 가져온 레코드 집합이 기존 레코드 집합과 병합됩니다. 

### <a name="import-a-zone-file"></a>영역 파일 가져오기

**contoso.com** 영역에 대한 영역 파일을 가져오려면

1. 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create --resource-group myresourcegroup -l westeurope
    ```

1. **contoso.com.txt** 파일에서 **myresourcegroup** 리소스 그룹의 새 DNS 영역으로 **contoso.com** 영역을 가져오려면 `az network dns zone import` 명령을 실행합니다.

    이 명령은 영역 파일을 로드하여 구문을 분석합니다. 이 명령은 Azure DNS 서비스에서 일련의 작업을 실행하여 영역 및 영역의 모든 레코드 집합을 만듭니다. 이 명령은 모든 오류 또는 경고뿐만 아니라 콘솔 창에 진행률도 보고합니다. 레코드 집합이 계열에 만들어지기 때문에 큰 영역 파일을 가져오는 데 몇 분 정도 걸릴 수 있습니다.

    ```azurecli-interactive
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="verify-the-zone"></a>영역 유효성 검사

파일을 가져온 후 다음 방법 중 하나를 사용하여 DNS 영역을 확인할 수 있습니다.

* 레코드를 나열하려면 다음 Azure CLI 명령을 사용합니다.

    ```azurecli-interactive
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Azure CLI 명령 `az network dns record-set ns list`를 사용하여 레코드를 나열할 수도 있습니다.
* `nslookup`을 사용하여 레코드의 이름을 확인합니다. 영역이 아직 위임되지 않은 경우 올바른 Azure DNS 이름 서버를 명시적으로 지정해야 합니다. 다음 샘플은 영역에 할당된 이름 서버 이름을 검색하는 방법을 보여 줍니다.

    ```azurecli-interactive
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    Windows 명령 프롬프트에서 `nslookup` 명령으로 "www" 레코드를 쿼리합니다.

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="update-dns-delegation"></a>DNS 위임 업데이트

영역을 올바르게 가져왔는지 확인한 후 Azure DNS 이름 서버를 가리키도록 DNS 위임을 업데이트해야 합니다. 자세한 내용은 [DNS 위임 업데이트](dns-domain-delegation.md)를 참조하세요.

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Azure DNS에서 DNS 영역 파일 내보내기

DNS 영역을 내보내려면 다음 Azure CLI 명령을 사용합니다.

```azurecli-interactive
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

값

* `<resource group>` 은(는) Azure DNS의 영역에 대한 리소스 그룹의 이름입니다.
* `<zone name>` 은(는) 영역의 이름입니다.
* `<zone file name>` 은(는) 내보낼 영역 파일의 경로/이름입니다.

영역 가져오기와 마찬가지로 먼저 로그인하고 구독을 선택한 다음 리소스 관리자 모드를 사용하도록 Azure CLI를 구성해야 합니다.

### <a name="to-export-a-zone-file"></a>영역 파일을 내보내려면

**myresourcegroup** 리소스 그룹의 기존 **contoso.com** Azure DNS 영역을 현재 폴더의 **contoso.com.txt** 파일로 내보내려면 `azure network dns zone export` 명령을 실행합니다. 이 명령은 Azure DNS 서비스를 호출하여 영역에서 레코드 집합을 열거하고 BIND와 호환 가능한 영역 파일에 결과를 내보냅니다.

```azurecli-interactive
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>다음 단계

* DNS 영역에서 [레코드 집합 및 레코드 관리](./dns-getstarted-cli.md) 방법을 알아봅니다.

* [Azure DNS에 도메인을 위임](dns-domain-delegation.md)하는 방법을 알아봅니다.
