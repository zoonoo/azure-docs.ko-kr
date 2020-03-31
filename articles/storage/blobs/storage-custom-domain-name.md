---
title: Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑
titleSuffix: Azure Storage
description: Azure 저장소 계정의 Blob 저장소 또는 웹 끝점에 사용자 지정 도메인을 매핑합니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370477"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑

사용자 지정 도메인을 Blob 서비스 끝점 또는 [정적 웹 사이트](storage-blob-static-website.md) 끝점에 매핑할 수 있습니다. 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> 이 매핑은 하위 도메인(예: `www.contoso.com`)에만 작동합니다. 루트 도메인(예: `contoso.com`)에서 웹 끝점을 사용할 수 있도록 하려면 Azure CDN을 사용해야 합니다. 이 문서의 [HTTPS 사용 이 가능한 사용자 지정 도메인 맵](#enable-https) 섹션을 참조하세요. 사용자 지정 도메인의 루트 도메인을 사용하도록 설정하려면 이 문서의 해당 섹션으로 이동하므로 HTTPS를 사용하도록 설정하는 단계는 선택 사항입니다. 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>HTTP만 사용하도록 설정된 사용자 지정 도메인 매핑

이 방법은 더 쉽지만 HTTP 액세스만 가능합니다. 저장소 계정이 HTTPS를 통해 [안전하게 전송하도록](../common/storage-require-secure-transfer.md) 구성된 경우 사용자 지정 도메인에 대한 HTTPS 액세스를 사용하도록 설정해야 합니다. 

HTTPS 액세스를 사용하려면 이 문서의 HTTPS 사용 이 가능한 섹션을 [사용하여 사용자 지정 도메인 맵을](#enable-https) 참조하세요. 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>사용자 지정 도메인 매핑

> [!IMPORTANT]
> 구성을 완료하는 동안 사용자 지정 도메인을 사용자가 잠시 사용할 수 없습니다. 도메인이 현재 가동 중지 시간이 0이 필요한 SLA(서비스 수준 계약)가 있는 응용 프로그램을 지원하는 경우 이 문서의 [가동 중지 시간이 0인 사용자 지정 도메인 매핑의](#zero-down-time) 단계를 수행하여 DNS 매핑이 진행되는 동안 사용자가 도메인에 액세스할 수 있도록 합니다.

사용자가 도메인을 잠시 사용할 수 없는 것이 우려되지 않는 경우 다음 단계를 따르세요.

:heavy_check_mark: 1단계: 저장소 끝점의 호스트 이름을 가져옵니다.

:heavy_check_mark: 2단계: 도메인 공급자를 사용하여 표준 이름(CNAME) 레코드를 만듭니다.

:heavy_check_mark: 3단계: Azure에 사용자 지정 도메인을 등록합니다. 

:heavy_check_mark: 4단계: 사용자 지정 도메인을 테스트합니다.

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1단계: 스토리지 끝점의 호스트 이름 받기 

호스트 이름은 프로토콜 식별자와 후행 슬래시가 없는 저장소 끝점 URL입니다. 

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창에서 **설정**에서 **속성**을 선택합니다.  

3. **기본 Blob 서비스 끝점** 또는 **기본 정적 웹 사이트 끝점의** 값을 텍스트 파일에 복사합니다. 

4. 해당 문자열에서 프로토콜*식별자(예:* HTTPS) 및 후행 슬래시를 제거합니다. 다음 표에는 예제가 포함되어 있습니다.

   | 끝점의 유형 |  엔드포인트(endpoint) | 호스트 이름 |
   |------------|-----------------|-------------------|
   |Blob 서비스  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |정적 웹 사이트  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   나중에 이 값을 따로 설정합니다.

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>2단계: 도메인 공급자를 사용하여 표준 이름(CNAME) 레코드 만들기

호스트 이름을 가리키는 CNAME 레코드를 만듭니다. CNAME 레코드는 원본 도메인을 대상 도메인 이름에 매핑하는 DNS 레코드의 형식입니다.

1. 도메인 등록 기관의 웹 사이트에 로그인한 다음 DNS 설정을 관리하기 위한 페이지로 이동합니다.

   **도메인 이름**, **DNS** 또는 **이름 서버 관리**라는 섹션에서 해당 페이지를 찾을 수 있습니다.

2. CNAME 레코드를 관리하기 위한 섹션을 찾습니다. 

   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**을 찾아야 할 수 있습니다.

3. CNAME 레코드를 만듭니다. 이 레코드의 일부로 다음 항목을 제공합니다. 

   - 하위 도메인 별칭(예: `www` 또는). `photos` 하위 도메인이 필요하며 루트 도메인은 지원되지 않습니다. 
      
   - 이 문서의 앞에서 저장소 [끝점 섹션의 호스트 이름 받기에서](#endpoint) 얻은 호스트 이름입니다. 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>3단계: Azure에 사용자 지정 도메인 등록

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **Blob Service** 아래에서 **사용자 지정 도메인**을 선택합니다.  

   ![사용자 지정 도메인 옵션](./media/storage-custom-domain-name/custom-domain-button.png "사용자 지정 도메인")

   **사용자 지정 도메인** 창이 열립니다.

3. 도메인 **이름** 텍스트 상자에 하위 도메인을 포함한 사용자 지정 도메인의 이름을 입력합니다.  
   
   예를 들어 도메인이 *contoso.com* 하위 도메인 별칭이 *www인*경우 을 입력합니다. `www.contoso.com` 하위 도메인이 *사진인* `photos.contoso.com`경우 를 입력합니다.

4. 사용자 지정 도메인을 등록하려면 **저장** 단추를 선택합니다.

   CNAME 레코드가 DNS(도메인 이름 서버)를 통해 전파된 후 사용자에게 적절한 권한이 있는 경우 사용자 지정 도메인을 사용하여 Blob 데이터를 볼 수 있습니다.

#### <a name="step-4-test-your-custom-domain"></a>4단계: 사용자 지정 도메인 테스트

사용자 지정 도메인이 Blob 서비스 엔드포인트에 매핑되었는지 확인하려면 스토리지 계정 내의 공용 컨테이너에서 Blob을 만듭니다. 그런 다음, 웹 브라우저에서 `http://<subdomain.customdomain>/<mycontainer>/<myblob>` 형식의 URI를 사용하여 Blob에 액세스합니다.

예를 들어 *photos.contoso.com* 사용자 지정 하위 도메인의 *myforms* 컨테이너에서 웹 양식에 액세스하려면 `http://photos.contoso.com/myforms/applicationform.htm` URI를 사용할 수 있습니다.

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>가동 중지 시간이 0으로 사용자 지정 도메인 매핑

> [!NOTE]
> 사용자가 도메인을 잠시 사용할 수 없다는 것이 염려되지 않는 경우 이 문서의 [사용자 지정 도메인 맵](#map-a-domain) 섹션의 단계를 따르는 것이 좋습니다. 더 적은 단계로 더 간단한 접근 방식입니다.  

도메인이 현재 가동 중지 시간이 필요 없는 SLA(서비스 수준 계약)가 있는 응용 프로그램을 지원하는 경우 다음 단계를 수행하여 DNS 매핑이 수행되는 동안 사용자가 도메인에 액세스할 수 있도록 합니다. 

:heavy_check_mark: 1단계: 저장소 끝점의 호스트 이름을 가져옵니다.

:heavy_check_mark: 2단계: 도메인 공급자를 사용하여 중간 표준 이름(CNAME) 레코드를 만듭니다.

:heavy_check_mark: 3단계: Azure에 사용자 지정 도메인을 사전 등록합니다.

:heavy_check_mark: 4단계: 도메인 공급자를 사용하여 CNAME 레코드를 만듭니다.

:heavy_check_mark: 5단계: 사용자 지정 도메인을 테스트합니다.

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1단계: 스토리지 끝점의 호스트 이름 받기 

호스트 이름은 프로토콜 식별자와 후행 슬래시가 없는 저장소 끝점 URL입니다. 

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창에서 **설정**에서 **속성**을 선택합니다.  

3. **기본 Blob 서비스 끝점** 또는 **기본 정적 웹 사이트 끝점의** 값을 텍스트 파일에 복사합니다. 

4. 해당 문자열에서 프로토콜*식별자(예:* HTTPS) 및 후행 슬래시를 제거합니다. 다음 표에는 예제가 포함되어 있습니다.

   | 끝점의 유형 |  엔드포인트(endpoint) | 호스트 이름 |
   |------------|-----------------|-------------------|
   |Blob 서비스  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |정적 웹 사이트  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   나중에 이 값을 따로 설정합니다.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>2단계: 도메인 공급자를 사용하여 중간 표준 이름(CNAME) 레코드 만들기

호스트 이름을 가리키는 임시 CNAME 레코드를 만듭니다. CNAME 레코드는 원본 도메인을 대상 도메인 이름에 매핑하는 DNS 레코드의 형식입니다.

1. 도메인 등록 기관의 웹 사이트에 로그인한 다음 DNS 설정을 관리하기 위한 페이지로 이동합니다.

   **도메인 이름**, **DNS** 또는 **이름 서버 관리**라는 섹션에서 해당 페이지를 찾을 수 있습니다.

2. CNAME 레코드를 관리하기 위한 섹션을 찾습니다. 

   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**을 찾아야 할 수 있습니다.

3. CNAME 레코드를 만듭니다. 이 레코드의 일부로 다음 항목을 제공합니다. 

   - 하위 도메인 별칭(예: `www` 또는). `photos` 하위 도메인이 필요하며 루트 도메인은 지원되지 않습니다.

     별칭에 `asverify` 하위 도메인을 추가합니다. 예를 들어 `asverify.www` 또는 `asverify.photos`입니다.
       
   - 이 문서의 앞에서 저장소 [끝점 섹션의 호스트 이름 받기에서](#endpoint) 얻은 호스트 이름입니다. 

     호스트 이름에 `asverify` 하위 도메인을 추가합니다. 예: `asverify.mystorageaccount.blob.core.windows.net`

4. 사용자 지정 도메인을 등록하려면 **저장** 단추를 선택합니다.

   등록에 성공한 경우 포털에서 스토리지 계정이 성공적으로 업데이트되었음을 알립니다. 사용자 지정 도메인이 Azure에서 확인되었지만 도메인에 대한 트래픽은 아직 스토리지 계정으로 라우팅되지 않습니다.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>3단계: Azure에 사용자 지정 도메인 사전 등록

Azure에 사용자 지정 도메인을 미리 등록할 때 Azure에서 도메인에 대한 DNS 레코드를 수정하지 않고도 사용자 지정 도메인을 인식할 수 있습니다. 이렇게 하면 도메인에 대한 DNS 레코드를 수정하면 가동 중지 시간 없이 Blob 끝점에 매핑됩니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **Blob Service** 아래에서 **사용자 지정 도메인**을 선택합니다.  

   ![사용자 지정 도메인 옵션](./media/storage-custom-domain-name/custom-domain-button.png "사용자 지정 도메인")

   **사용자 지정 도메인** 창이 열립니다.

3. 도메인 **이름** 텍스트 상자에 하위 도메인을 포함한 사용자 지정 도메인의 이름을 입력합니다.  
   
   예를 들어 도메인이 *contoso.com* 하위 도메인 별칭이 *www인*경우 을 입력합니다. `www.contoso.com` 하위 도메인이 *사진인* `photos.contoso.com`경우 를 입력합니다.

4. **간접 CNAME 유효성 검사 사용** 확인란을 선택합니다.

5. 사용자 지정 도메인을 등록하려면 **저장** 단추를 선택합니다.
  
   CNAME 레코드가 DNS(도메인 이름 서버)를 통해 전파된 후 사용자에게 적절한 권한이 있는 경우 사용자 지정 도메인을 사용하여 Blob 데이터를 볼 수 있습니다.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>4단계: 도메인 공급자를 사용하여 CNAME 레코드 만들기

호스트 이름을 가리키는 임시 CNAME 레코드를 만듭니다.

1. 도메인 등록 기관의 웹 사이트에 로그인한 다음 DNS 설정을 관리하기 위한 페이지로 이동합니다.

   **도메인 이름**, **DNS** 또는 **이름 서버 관리**라는 섹션에서 해당 페이지를 찾을 수 있습니다.

2. CNAME 레코드를 관리하기 위한 섹션을 찾습니다. 

   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**을 찾아야 할 수 있습니다.

3. CNAME 레코드를 만듭니다. 이 레코드의 일부로 다음 항목을 제공합니다. 

   - 하위 도메인 별칭(예: `www` 또는). `photos` 하위 도메인이 필요하며 루트 도메인은 지원되지 않습니다.
      
   - 이 문서의 앞에서 저장소 [끝점 섹션의 호스트 이름 받기에서](#endpoint-2) 얻은 호스트 이름입니다. 

#### <a name="step-5-test-your-custom-domain"></a>5단계: 사용자 지정 도메인 테스트

사용자 지정 도메인이 Blob 서비스 엔드포인트에 매핑되었는지 확인하려면 스토리지 계정 내의 공용 컨테이너에서 Blob을 만듭니다. 그런 다음, 웹 브라우저에서 `http://<subdomain.customdomain>/<mycontainer>/<myblob>` 형식의 URI를 사용하여 Blob에 액세스합니다.

예를 들어 *photos.contoso.com* 사용자 지정 하위 도메인의 *myforms* 컨테이너에서 웹 양식에 액세스하려면 `http://photos.contoso.com/myforms/applicationform.htm` URI를 사용할 수 있습니다.

### <a name="remove-a-custom-domain-mapping"></a>사용자 지정 도메인 매핑 제거

사용자 지정 도메인 매핑을 제거하려면 사용자 지정 도메인의 등록을 취소합니다. 다음 절차 중 하나를 수행하십시오.

#### <a name="portal"></a>[포털](#tab/azure-portal)

사용자 지정 도메인 설정을 제거하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

2. 메뉴 창의 **Blob Service** 아래에서 **사용자 지정 도메인**을 선택합니다.  
   **사용자 지정 도메인** 창이 열립니다.

3. 사용자 지정 도메인 이름이 포함된 텍스트 상자의 콘텐츠를 지웁니다.

4. **저장** 단추를 선택합니다.

사용자 지정 도메인이 성공적으로 제거되면 저장소 계정이 성공적으로 업데이트되었다는 포털 알림이 표시됩니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

사용자 지정 도메인 등록을 제거하려면 [az storage account update](https://docs.microsoft.com/cli/azure/storage/account) CLI 명령을 사용한 다음, `--custom-domain` 인수 값에 빈 문자열(`""`)을 지정합니다.

* 명령 형식:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* 명령 예:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

사용자 지정 도메인 등록을 제거하려면 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet을 사용한 다음, `-CustomDomainName` 인수 값에 빈 문자열(`""`)을 지정합니다.

* 명령 형식:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* 명령 예:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>HTTPS를 사용하도록 설정한 사용자 지정 도메인 매핑

이 방법은 더 많은 단계를 포함하지만 HTTPS 액세스를 가능하게 합니다. 

사용자가 HTTPS를 사용하여 Blob 또는 웹 콘텐츠에 액세스할 필요가 없는 경우 이 문서의 [HTTP 사용 섹션만 있는 사용자 지정 도메인 맵을](#enable-http) 참조하세요. 

사용자 지정 도메인을 매핑하고 HTTPS 액세스를 활성화하려면 다음을 수행합니다.

1. Blob 또는 웹 끝점에서 [Azure CDN을](../../cdn/cdn-overview.md) 사용하도록 설정합니다. 

   Blob 저장소 끝점의 경우 [Azure CDN과 Azure 저장소 계정 통합을](../../cdn/cdn-create-a-storage-account-with-cdn.md)참조하십시오. 

   정적 웹 사이트 끝점은 [Azure CDN과 정적 웹 사이트 통합을](static-website-content-delivery-network.md)참조하십시오.

2. [Azure CDN 콘텐츠를 사용자 지정 도메인에 매핑합니다](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Azure CDN 사용자 지정 도메인에서 HTTPS를 활성화합니다.](../../cdn/cdn-custom-ssl.md)

   > [!NOTE] 
   > 정적 웹 사이트를 업데이트할 때 CDN 끝점을 제거하여 CDN 에지 서버에서 캐시된 콘텐츠를 지워야 합니다. 자세한 내용은 [Azure CDN 엔드포인트 제거](../../cdn/cdn-purge-endpoint.md)를 참조하세요.

4. (선택 사항) 다음 지침을 복습한다.

   * [Azure CDN을 가진 공유 액세스 서명(SAS) 토큰.](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures)

   * [Azure CDN을 사용하여 HTTP-HTTPS 리디렉션.](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)

   * [Azure CDN에서 Blob 저장소를 사용할 때 가격 및 청구.](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)

## <a name="next-steps"></a>다음 단계

* [Azure Blob 저장소에서 정적 웹 사이트 호스팅에 대해 알아보기](storage-blob-static-website.md)
