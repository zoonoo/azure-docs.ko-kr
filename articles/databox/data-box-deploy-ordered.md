---
title: Azure Data Box 주문 자습서 | Microsoft 설명서
description: 배포 필수 구성 요소 및 Azure Data Box를 주문하는 방법을 알아봅니다
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392485"
---
# <a name="tutorial-order-azure-data-box"></a>자습서: Azure Data Box 주문

Azure Data Box는 빠르고 쉽게 신뢰할 수 있는 방식으로 온-프레미스 데이터를 Azure로 가져올 수 있는 하이브리드 솔루션입니다. Microsoft에서 제공한 80TB(사용 가능한 용량) 스토리지 디바이스에 데이터를 전송한 다음, 디바이스를 다시 제공합니다. 그런 다음, 이 데이터는 Azure에 업로드됩니다.

이 자습서에서는 Azure Data Box를 주문하는 방법에 대해 설명합니다. 이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * Data Box를 배포하기 위한 필수 구성 요소
> * Data Box 주문
> * 주문 추적
> * 주문 취소

## <a name="prerequisites"></a>필수 구성 요소

# <a name="portal"></a>[포털](#tab/portal)

디바이스를 배포하기 전에 Data Box 서비스 및 디바이스에 대해 다음 필수 구성 요소를 완료합니다.

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

Azure에 로그인하고 다음 두 방법 중 하나로 Azure CLI 명령을 실행할 수 있습니다.

* CLI를 설치하고 로컬로 CLI를 실행합니다.
* Azure Portal 내에서 Azure Cloud Shell을 사용하여 CLI 명령을 실행합니다.

이 자습서에서는 Windows PowerShell을 통해 Azure CLI를 사용하지만, 어떤 옵션을 선택해도 됩니다.

### <a name="install-the-cli-locally"></a>로컬에서 CLI 설치

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 버전 2.0.67 이상을 설치합니다. 또는 [MSI를 사용하여 설치](https://aka.ms/installazurecliwindows)해도 됩니다.

#### <a name="sign-in-to-azure"></a>Azure에 로그인

Windows PowerShell 명령 창을 열고 [az login](/cli/azure/reference-index#az-login)을 사용하여 Azure에 로그인합니다.

```azurecli
PS C:\Windows> az login
```

다음은 성공적인 로그인의 출력입니다.

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>Azure Data Box CLI 확장 설치

Azure Data Box CLI 명령을 사용하려면 먼저 확장을 설치해야 합니다. Azure CLI 확장은 아직 핵심 CLI의 일부로 제공되지 않는 실험적 명령과 시험판 명령에 대한 액세스를 제공합니다. 확장에 대한 자세한 내용은 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.

Azure Data Box 확장을 설치하려면 `az extension add --name databox` 명령을 실행합니다.

```azurecli

    PS C:\Windows> az extension add --name databox
```

확장이 성공적으로 설치되면 다음 출력이 표시됩니다.

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell 사용

브라우저를 통해 Azure 호스팅 대화형 셸 환경인 [Azure Cloud Shell](https://shell.azure.com/)을 사용하여 CLI 명령을 실행할 수 있습니다. Azure Cloud Shell은 Azure 서비스에서 Bash 또는 Windows PowerShell을 지원합니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성됩니다. Azure Portal의 오른쪽 위 섹션에 있는 메뉴에서 Cloud Shell 단추를 클릭합니다.

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

이 단추는 이 방법 문서에 설명된 단계를 실행하는 데 사용할 수 있는 대화형 셸을 시작합니다.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>Data Box 주문

# <a name="portal"></a>[포털](#tab/portal)

디바이스를 주문하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 다음 URL에서 로그인합니다. [https://portal.azure.com](https://portal.azure.com)
2. **+ 리소스 만들기**를 클릭하고 *Azure Data Box*를 검색합니다. **Azure Data Box**를 클릭합니다.

   [![Azure Data Box 1 검색](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. **만들기**를 클릭합니다.

4. 해당 지역에서 Data Box 서비스를 사용할 수 있는지 확인합니다. 다음 정보를 입력하거나 선택하고 **적용**을 클릭합니다.

    |설정  |값  |
    |---------|---------|
    |Subscription     | Data Box 서비스에 대한 EA, CSP 또는 Azure 스폰서쉽 구독을 선택합니다. <br> 구독은 대금 청구 계정에 연결됩니다.       |
    |전송 형식     | **Azure로 가져오기**를 선택합니다.        |
    |원본 국가/지역    |    현재 데이터가 있는 국가/지역을 선택합니다.         |
    |대상 Azure 지역     |     데이터를 전송하려는 Azure 지역을 선택합니다.        |

5. **Data Box**를 선택합니다. 단일 주문의 최대 사용 가능한 용량은 80TB입니다. 더 큰 데이터 크기에 대해 여러 개의 주문을 만들 수 있습니다.

      [![Data Box 옵션 1 선택](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. **주문**에서 **주문 세부 정보**를 지정합니다. 다음 정보를 입력하거나 선택하고 **다음**을 클릭합니다.

    |설정  |값  |
    |---------|---------|
    |Name     |  주문을 추적하는 데 친숙한 이름을 입력합니다. <br> 이 이름은 2~24자 사이의 문자, 숫자 및 하이픈일 수 있습니다. <br> 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.      |
    |Resource group     |    기존 그룹을 사용하거나 새 그룹을 만듭니다. <br> 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다.         |
    |대상 Azure 지역     | 스토리지 계정에 대한 지역을 선택합니다. <br> 자세한 내용은 [지역 가용성](data-box-overview.md#region-availability)을 참조하세요.        |
    |스토리지 대상     | 스토리지 계정이나 관리형 디스크 또는 둘 다를 선택합니다. <br> 지정된 Azure 지역에 따라 필터링된 기존 스토리지 계정 목록에서 하나 이상의 스토리지 계정을 선택합니다. Data Box는 최대 10개의 스토리지 계정과 연결할 수 있습니다. <br> 새 **범용 v1**, **범용 v2** 또는 **Blob Storage 계정**도 만들 수 있습니다. <br>가상 네트워크를 사용하는 스토리지 계정은 지원됩니다. Data Box 서비스에서 보안 스토리지 계정을 사용하려면 스토리지 계정 네트워크 방화벽 설정 내에서 신뢰할 수 있는 서비스를 사용하도록 설정합니다. 자세한 내용은 [Azure Data Box를 신뢰할 수 있는 서비스로 추가](../storage/common/storage-network-security.md#exceptions)하는 방법을 참조하세요.|

    스토리지 계정을 스토리지 대상으로 사용하는 경우 다음 스크린샷을 참조하세요.

    ![스토리지 계정에 대한 Data Box 주문](media/data-box-deploy-ordered/order-storage-account.png)

    Data Box를 사용하여 온-프레미스 VHD(가상 하드 디스크)에서 관리 디스크를 만드는 경우에도 다음 정보를 제공해야 합니다.

    |설정  |값  |
    |---------|---------|
    |리소스 그룹     | 온-프레미스 VHD에서 관리형 디스크를 만들려는 경우 새 리소스 그룹을 만듭니다. 이전에 Data Box 서비스를 통해 관리형 디스크의 Data Box 주문을 만들 때 리소스 그룹을 만든 경우에만 기존 리소스 그룹을 사용할 수 있습니다. <br> 세미콜론으로 구분해서 여러 리소스 그룹을 지정합니다. 최대 10개의 리소스 그룹이 지원됩니다.|

    ![관리형 디스크에 대한 Data Box 주문](media/data-box-deploy-ordered/order-managed-disks.png)

    관리형 디스크에 대해 지정한 스토리지 계정은 스테이징 스토리지 계정으로 사용됩니다. Data Box 서비스는 VHD를 관리형 디스크로 변환한 후 리소스 그룹으로 이동하기 전에 페이지 Blob으로 스테이징 스토리지 계정에 업로드합니다. 자세한 내용은 [Azure에 대한 데이터 업로드 확인](data-box-deploy-picked-up.md#verify-data-upload-to-azure)을 참조하세요.

7. **배송 주소**에 사용자의 성과 이름, 회사의 이름과 우편 주소 및 유효한 전화 번호를 입력합니다. **주소 확인**을 클릭합니다. 서비스에서 서비스 가용성을 위해 배송 주소의 유효성을 검사합니다. 지정한 배송 주소에 대해 서비스를 사용할 수 있으면 해당 알림을 받게 됩니다.

   주문이 성공적으로 발주된 후 자체 관리형 배송을 선택하면 이메일 알림을 받게 됩니다. 자체 관리형 배송에 대한 자세한 내용은 [자체 관리형 배송 사용](data-box-portal-customer-managed-shipping.md)을 참조하세요.

8. 배송 정보가 성공적으로 확인되면 **다음**을 클릭합니다.

9. **알림 세부 정보**에서 이메일 주소를 지정합니다. 서비스에서는 주문 상태에 대한 모든 업데이트와 관련된 이메일 알림을 지정한 이메일 주소로 보냅니다.

    그룹의 관리자가 떠나는 경우에도 계속 알림을 받으려면 그룹 이메일을 사용하는 것이 좋습니다.

10. 주문, 연락처, 알림 및 개인 정보 처리 방침과 관련된 정보 **요약**을 검토합니다. 개인 정보 처리 방침에 대한 계약에 해당하는 확인란을 선택합니다.

11. **주문**을 클릭합니다. 주문을 만드는 데 몇 분 정도 걸립니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

디바이스를 주문하려면 Azure CLI를 사용하여 다음 단계를 수행합니다.

1. Data Box 주문에 대한 설정을 적습니다. 이러한 설정에는 개인/회사 정보, 구독 이름, 디바이스 정보 및 배송 정보가 포함됩니다. CLI 명령을 실행하여 Data Box 주문을 작성할 때 이러한 설정을 매개 변수로 사용해야 합니다. 다음 표에서는 `az databox job create`에 사용되는 매개 변수 설정을 보여줍니다.

   | 설정(매개 변수) | Description |  샘플 값 |
   |---|---|---|
   |resource-group| 기존 그룹을 사용하거나 새 그룹을 만듭니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |name| 작성하는 주문의 이름입니다. | "mydataboxorder"|
   |contact-name| 배송 주소와 연결된 이름입니다. | "Gus Poland"|
   |phone| 주문한 제품을 받을 사람 또는 회사의 전화 번호입니다.| "14255551234"
   |위치| 디바이스를 배송할 가장 가까운 Azure 지역입니다.| "미국 서부"|
   |sku| 주문하는 특정 Data Box 디바이스입니다. 유효한 값은 다음과 같습니다. "DataBox", "DataBoxDisk" 및 "DataBoxHeavy"| "DataBox" |
   |email-list| 주문과 연결된 이메일 주소입니다.| "gusp@contoso.com" |
   |street-address1| 주문한 제품이 배송되는 주소입니다. | "15700 NE 39th St" |
   |street-address2| 아파트 번호 또는 건물 번호와 같은 보조 주소 정보입니다. | "Bld 123" |
   |city| 디바이스가 배송될 도시입니다. | "Redmond" |
   |state-or-province| 디바이스가 배송될 시/도입니다.| "WA" |
   |country| 디바이스가 배송될 국가입니다. | "미국" |
   |postal-code| 배송 주소와 연결된 우편 번호입니다.| "98052"|
   |company-name| 근무하는 회사의 이름입니다.| "Contoso, LTD" |
   |스토리지 계정 만들기| 데이터를 가져올 Azure Storage 계정입니다.| "mystorageaccount"|
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다.  | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

2. 원하는 터미널의 명령 프롬프트에서 [az databox job create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create)를 사용하여 Azure Data Box 주문을 작성합니다.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   다음은 명령 사용의 예입니다.

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   다음은 명령 실행의 출력입니다.

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. 모든 Azure CLI 명령은 사용자가 변경하지 않는 한 기본적으로 json을 출력 형식으로 사용합니다. 글로벌 매개 변수 `--output <output-format>`을 사용하여 출력 형식을 변경할 수 있습니다. 형식을 "table"로 변경하면 출력 가독성이 향상됩니다.

   다음은 방금 실행한 명령을 약간 조정하여 형식을 변경하는 명령입니다.

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   다음은 명령 실행의 출력입니다.

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>주문 추적

# <a name="portal"></a>[포털](#tab/portal)

주문이 완료되면 Azure Portal에서 주문 상태를 추적할 수 있습니다. Data Box 주문, **개요**로 차례로 이동하여 상태를 확인합니다. 포털에서는 해당 주문을 **주문됨** 상태로 표시합니다.

디바이스를 사용할 수 없는 경우 알림을 받습니다. 디바이스를 사용할 수 있으면 Microsoft에서는 배송할 디바이스를 확인하고 배송을 준비합니다. 디바이스를 준비하는 동안 수행되는 작업은 다음과 같습니다.

* 디바이스와 연결된 각 스토리지 계정에 대해 SMB 공유가 생성됩니다.
* 각 공유에 대한 사용자 이름 및 암호와 같은 액세스 자격 증명이 생성됩니다.
* 디바이스의 잠금을 해제하는 데 도움이 되는 디바이스 암호도 생성됩니다.
* Data Box는 언제든지 디바이스에 대한 권한이 없는 액세스를 방지하기 위해 잠겨 있습니다.

디바이스 준비가 완료되면 포털에서는 해당 주문을 **처리됨** 상태로 표시합니다.

![Data Box 주문 처리됨](media/data-box-overview/data-box-order-status-processed.png)

그런 다음, Microsoft에서는 디바이스를 준비하고 지역 배송업체를 통해 발송합니다. 디바이스가 배송되면 추적 번호를 받게 됩니다. 포털에서 해당 작업을 **발송됨** 상태로 표시합니다.

![Data Box 주문 발송됨](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>단일 주문 추적

단일 기존 Azure Data Box 주문에 대한 추적 정보를 가져오려면 [az databox job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show)를 실행합니다. 이 명령은 이름, 리소스 그룹, 추적 정보, 구독 ID, 연락처 정보, 배송 유형, 디바이스 sku 등의 주문 정보를 표시합니다.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   다음 표에서는 `az databox job show`에 대한 매개 변수 정보를 보여줍니다.

   | 매개 변수 | Description |  샘플 값 |
   |---|---|---|
   |resource-group [필수]| 주문과 연결된 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |name [필수]| 표시할 주문의 이름입니다. | "mydataboxorder"|
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다. | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

   다음은 출력 형식이 "table"로 설정된 명령의 예입니다.

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   다음은 명령 실행의 출력입니다.

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> 주문 나열은 구독 수준에서 지원할 수 있으며, 따라서 리소스 그룹은 필수 매개 변수가 아닌 선택적 매개 변수입니다.

### <a name="list-all-orders"></a>모든 주문 나열

여러 디바이스를 주문한 경우 [az databox job list](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list)를 실행하여 모든 Azure Data Box 주문을 볼 수 있습니다. 이 명령은 특정 리소스 그룹에 속한 모든 주문을 나열합니다. 또한 주문 이름, 배송 상태, Azure 지역, 배달 유형, 주문 상태가 출력에 표시됩니다. 취소된 주문도 목록에 포함됩니다.
뿐만 아니라 이 명령은 각 주문의 타임스탬프를 표시합니다.

```azurecli
az databox job list --resource-group <resource-group>
```

다음 표에서는 `az databox job list`에 대한 매개 변수 정보를 보여줍니다.

   | 매개 변수 | Description |  샘플 값 |
   |---|---|---|
   |resource-group [필수]| 주문을 포함하는 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다. | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

   다음은 출력 형식이 "table"로 설정된 명령의 예입니다.

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   다음은 명령 실행의 출력입니다.

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>주문 취소

# <a name="portal"></a>[포털](#tab/portal)

이 주문을 취소하려면 Azure Portal에서 **개요**로 이동하고, 명령 모음에서 **취소**를 선택합니다.

주문이 완료되면 주문 상태가 처리됨으로 표시되기 전까지는 취소할 수 있습니다.

취소된 주문을 삭제하려면 **개요**로 이동하고, 명령 모음에서 **삭제**를 선택합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>주문 취소

Azure Data Box 주문을 취소하려면 [az databox job cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel) 명령을 실행합니다. 주문을 취소하는 이유를 지정해야 합니다.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   다음 표에서는 `az databox job cancel`에 대한 매개 변수 정보를 보여줍니다.

   | 매개 변수 | Description |  샘플 값 |
   |---|---|---|
   |resource-group [필수]| 삭제할 주문과 연결된 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |name [필수]| 삭제할 주문의 이름입니다. | "mydataboxorder"|
   |reason [필수]| 주문을 취소하는 이유입니다. | "잘못된 정보를 입력하여 주문을 취소해야 합니다." |
   |예| 확인을 묻는 메시지를 표시하지 마세요. | --yes (-y)| --yes -y |
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다. | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

   다음은 출력이 있는 명령의 예입니다.

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   다음은 명령 실행의 출력입니다.

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>주문 삭제

Azure Data Box 주문을 취소한 경우 [az databox job delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete)를 실행하여 주문을 삭제할 수 있습니다.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   다음 표에서는 `az databox job delete`에 대한 매개 변수 정보를 보여줍니다.

   | 매개 변수 | Description |  샘플 값 |
   |---|---|---|
   |resource-group [필수]| 삭제할 주문과 연결된 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |name [필수]| 삭제할 주문의 이름입니다. | "mydataboxorder"|
   |subscription| Azure 구독의 이름 또는 ID(GUID)입니다. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |예| 확인을 묻는 메시지를 표시하지 마세요. | --yes (-y)| --yes -y |
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다. | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

다음은 출력이 있는 명령의 예입니다.

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   다음은 명령 실행의 출력입니다.

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 문서의 다음 내용을 알아보았습니다.

> [!div class="checklist"]
>
> * Data Box를 배포하기 위한 필수 구성 요소
> * Data Box 주문
> * 주문 추적
> * 주문 취소

Data Box를 설정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Box 설정](./data-box-deploy-set-up.md)
