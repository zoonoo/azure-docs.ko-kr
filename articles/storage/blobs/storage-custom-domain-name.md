---
title: Azure 스토리지 계정에 대한 사용자 지정 도메인 이름 구성 | Microsoft Docs
description: Azure Portal을 사용하여 고유한 CNAME(정식 이름)을 Azure 스토리지 계정의 Blob 스토리지 또는 웹 엔드포인트에 매핑합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: 4f6776a5f15cf391f3a65aceb6e9e783d87a2078
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148923"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Azure 스토리지 계정에 대한 사용자 지정 도메인 이름 구성

Azure 저장소 계정에서 Blob 데이터에 액세스할 수 있도록 사용자 지정 도메인을 구성할 수 있습니다. Azure Blob 스토리지에 대한 기본 엔드포인트는 *\<storage-account-name>.blob.core.windows.net*입니다. 일부로 생성 되는 웹 끝점을 사용할 수도 있습니다는 [정적 웹 사이트 기능](storage-blob-static-website.md)합니다. 와 같은 사용자 지정 도메인과 하위 도메인을 매핑하는 경우 *www\.contoso.com*, 저장소 계정의 blob 또는 웹 끝점으로 사용자에 게 저장소 계정의 blob 데이터에 액세스 하려면 해당 도메인을 사용할 수 있습니다.

> [!IMPORTANT]
> Azure Storage는 아직 기본적으로 사용자 지정 도메인으로 HTTPS를 지원하지 않습니다. 현재 [Azure CDN을 사용하여 HTTP를 통해 사용자 지정 도메인으로 Blob에 액세스](storage-https-custom-domain-cdn.md)할 수 있습니다.
> 
> 
> [!NOTE]
> 저장소 계정은 현재 계정당 하나의 사용자 정의 도메인 이름만 지원합니다. 웹 및 Blob 서비스 엔드포인트 모두에 사용자 지정 도메인 이름을 매핑할 수는 없습니다.
> 
> [!NOTE]
> 하위 도메인에 대 한 매핑을 작동만 (예: www\.contoso.com). 루트 도메인 (예: contoso.com)에 사용 가능한 웹 끝점을 하도록 하려는 경우 필요가 [사용자 지정 도메인을 사용 하 여 Azure CDN 사용](storage-https-custom-domain-cdn.md)

다음 테이블에서는 이름이 *mystorageaccount*인 스토리지 계정에 있는 Blob 데이터의 여러 샘플 URL을 보여 줍니다. 저장소 계정에 등록 된 사용자 지정 하위 도메인이 *www\.contoso.com*:

| 리소스 종류 | 기본 URL | 사용자 지정 도메인 URL |
| --- | --- | --- |
| Storage 계정 | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| 루트 컨테이너 | http://mystorageaccount.blob.core.windows.net/myblob 또는 http://mystorageaccount.blob.core.windows.net/ $root/myblob| http://www.contoso.com/myblob 또는 http://www.contoso.com/ $root/myblob |
| 웹 |  http://mystorageaccount.[zone].web.core.windows.net/$web/[indexdoc] 또는 http://mystorageaccount.[zone].web.core.windows.net/[indexdoc] 또는 http://mystorageaccount.[zone].web.core.windows.net/$web 또는 http://mystorageaccount.[zone].web.core.windows.net/ | http://www.contoso.com/ $web 또는 http://www.contoso.com/ 또는 http://www.contoso.com/ $web/[indexdoc] 또는 http://www.contoso.com/ [indexdoc] |

> [!NOTE]  
> 다음 섹션에 표시된 대로 Blob 서비스 엔드포인트에 대한 모든 예제는 웹 서비스 엔드포인트에도 적용됩니다.

## <a name="direct-vs-intermediary-cname-mapping"></a>직접 도메인 매핑과 중간 CNAME 매핑

하위 도메인을 접두사로 추가 사용자 지정 도메인을 가리키도록 설정할 수 있습니다 (예: www\.contoso.com)을 두 가지 방법 중 하나에 대 한 저장소 계정의 blob 끝점: 
* 직접 CNAME 매핑을 사용합니다.
* *asverify* 중간 하위 도메인을 사용합니다.

### <a name="direct-cname-mapping"></a>직접 CNAME 매핑

첫 번째 가장 단순한 방법은 사용자 지정 도메인 및 하위 도메인을 Blob 엔드포인트에 직접 매핑하는 CNAME(정식 이름) 레코드를 만드는 것입니다. CNAME 레코드는 원본 도메인을 대상 도메인에 매핑하는 DNS(Domain Name System) 기능입니다. 이 예제에서 원본 도메인은 사용자 고유의 사용자 지정 도메인 및 하위 도메인 (*www\.contoso.com*예를 들어). 대상 도메인은 Blob service 엔드포인트입니다(예: *mystorageaccount.blob.core.windows.net*).

직접 매핑 방법은 “사용자 지정 도메인 등록”에서 설명합니다.

### <a name="intermediary-mapping-with-asverify"></a>*asverify*를 사용하여 중간 매핑

두 번째 매핑 방법도 CNAME 레코드를 사용합니다. 그러나 가동 중지 시간을 방지하려면 먼저 Azure에서 인식하는 특수한 하위 도메인 *asverify*를 사용합니다.

사용자 지정 도메인을 Blob 엔드포인트에 매핑하면 [Azure Portal](https://portal.azure.com)에서 도메인을 등록하는 동안 잠시 가동 중지될 수 있습니다. 현재 도메인에서 가동 중지 시간이 없어야 하는 SLA(서비스 수준 계약)가 설정된 애플리케이션을 지원하고 있다면 Azure *asverify* 하위 도메인을 중간 등록 단계로 사용합니다. 이 단계를 통해 DNS 매핑이 진행되는 동안 사용자가 도메인에 액세스할 수 있어야 합니다.

중간 방법은 *asverify* 하위 도메인을 사용하여 사용자 지정 도메인 등록에서 설명합니다.

## <a name="register-a-custom-domain"></a>사용자 지정 도메인 등록
다음 명령문을 적용하는 경우 이 섹션의 프로시저를 사용하여 도메인을 등록합니다.
* 도메인을 사용자가 잠깐 사용할 수 없는 경우는 문제가 되지 않습니다.
* 사용자 지정 도메인은 현재 애플리케이션을 호스팅하고 있지 않습니다. 

Azure DNS를 사용하여 Azure Blob 저장소에 대한 사용자 지정 DNS 이름을 구성할 수 있습니다. 자세한 내용은 [Azure DNS를 사용하여 Azure 서비스에 대해 사용자 지정 도메인 설정 제공](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage)을 참조하세요.

사용자 지정 도메인이 현재 가동 중지 시간이 없어야 하는 애플리케이션을 지원하고 있다면 *asverify* 하위 도메인을 사용하여 사용자 지정 도메인 등록의 절차를 사용합니다.

사용자 지정 도메인 이름을 구성하려면 DNS에서 새 CNAME 레코드를 만듭니다. CNAME 레코드에서 도메인 이름의 별칭을 지정합니다. 해당 예제에서는 사용자 지정 도메인의 주소를 스토리지 계정의 Blob 스토리지 엔드포인트에 매핑합니다.

일반적으로 도메인 등록 기관의 웹 사이트에서 도메인의 DNS 설정을 관리할 수 있습니다. 각 등록 기관은 서로 유사하면서 약간 다른 방법으로 CNAME 레코드를 지정하지만 개념은 동일합니다. 일부 기본 도메인 등록 패키지에서 DNS 구성을 제공하지 않으므로 CNAME 레코드를 만들려면 먼저 도메인 등록 패키지를 업그레이드해야 할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

1. 메뉴 창의 **Blob Service** 아래에서 **사용자 지정 도메인**을 선택합니다.  
   **사용자 지정 도메인** 창이 열립니다.

1. 도메인 등록 기관의 웹 사이트에 로그인한 다음, DNS 관리 페이지로 이동합니다.  
   **도메인 이름**, **DNS** 또는 **이름 서버 관리**라는 섹션에서 해당 페이지를 찾을 수 있습니다.

1. CNAME을 관리하기 위한 섹션을 찾습니다.  
   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**을 찾아야 할 수 있습니다.

1. 새 CNAME 레코드를 만들고와 같은 하위 도메인 별칭을 입력 **www** 하거나 **사진** (하위 도메인은 필수, 루트 도메인에 사용할 수 없습니다) 호스트 이름을 제공 합니다.  
   호스트 이름은 Blob 서비스 엔드포인트입니다. 해당 형식은 *mystorageaccount*가 스토리지 계정의 이름인 *\<mystorageaccount>.blob.core.windows.net*입니다. 사용할 호스트 이름이 [Azure Portal](https://portal.azure.com)에서 **사용자 지정 도메인** 창의 항목 #1에 표시됩니다. 

1. **사용자 지정 도메인** 창의 텍스트 상자에 하위 도메인을 포함하여 사용자 지정 도메인 이름을 입력합니다.  
   예를 들어, 도메인이 *contoso.com* 하위 도메인 별칭이 며 *www*를 입력 **www\.contoso.com**합니다. 하위 도메인이 *photos*이면 **photos.contoso.com**을 입력합니다.

1. 사용자 지정 도메인을 등록하려면 **저장**을 선택합니다.  
   등록에 성공한 경우 포털에서 스토리지 계정이 성공적으로 업데이트되었음을 알립니다.

새 CNAME 레코드가 DNS를 통해 전파된 후 사용자에게 적절한 사용 권한이 있는 경우 사용자는 사용자 지정 도메인을 사용하여 Blob 데이터를 확인할 수 있습니다.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>*asverify* 하위 도메인을 통해 사용자 지정 도메인 등록
현재 사용자 지정 도메인이 가동 중지 시간이 없어야 하는 SLA가 있는 애플리케이션을 지원하는 경우 이 섹션의 프로시저를 통해 사용자 지정 도메인을 등록하세요. *asverify.\<subdomain>.\<customdomain>* 에서 *asverify.\<storageaccount>.blob.core.windows.net*까지 가리키는 CNAME을 만들어 Azure에 도메인을 미리 등록할 수 있습니다. 그런 다음, *\<subdomain>.\<customdomain>* 에서 *\<storageaccount>.blob.core.windows.net*까지 가리키는 두 번째 CNAME을 만들 수 있습니다. 그러면 사용자 지정 도메인에 대한 트래픽이 Blob 엔드포인트로 직접 보내집니다.

*asverify* 하위 도메인은 Azure에서 인식하는 특수한 하위 도메인입니다. 고유한 하위 도메인 앞에 *asverify* 를 추가하면 도메인의 DNS 레코드를 수정하지 않아도 Azure에서 사용자 지정 도메인을 인식할 수 있습니다. 도메인의 DNS 레코드를 수정하면 가동 중지 시간 없이 Blob 엔드포인트에 매핑됩니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

1. 메뉴 창의 **Blob Service** 아래에서 **사용자 지정 도메인**을 선택합니다.  
   **사용자 지정 도메인** 창이 열립니다.

1. DNS 공급자의 웹 사이트에 로그인한 다음, DNS 관리 페이지로 이동합니다.  
   **도메인 이름**, **DNS** 또는 **이름 서버 관리**라는 섹션에서 해당 페이지를 찾을 수 있습니다.

1. CNAME을 관리하기 위한 섹션을 찾습니다.  
   고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**을 찾아야 할 수 있습니다.

1. 새 CNAME 레코드 만들고 **asverify.www** 또는 **asverify.photos** 같은 *asverify* 하위 도메인을 포함하는 하위 도메인 별칭을 지정한 다음, 호스트 이름을 지정합니다.  
   호스트 이름은 Blob 서비스 엔드포인트입니다. 해당 형식은 *mystorageaccount*가 스토리지 계정의 이름인 *asverify.\<mystorageaccount>.blob.core.windows.net*입니다. 사용할 호스트 이름이 [Azure Portal](https://portal.azure.com)에서 *사용자 지정 도메인* 창의 항목 #2에 표시됩니다.

1. **사용자 지정 도메인** 창의 텍스트 상자에 하위 도메인을 포함하여 사용자 지정 도메인 이름을 입력합니다.  
   *asverify*를 포함하지 않습니다. 예를 들어, 도메인이 *contoso.com* 하위 도메인 별칭이 며 *www*를 입력 **www\.contoso.com**합니다. 하위 도메인이 *photos*이면 **photos.contoso.com**을 입력합니다.

1. **간접 CNAME 유효성 검사 사용** 확인란을 선택합니다.

1. 사용자 지정 도메인을 등록하려면 **저장**을 선택합니다.  
   등록에 성공한 경우 포털에서 스토리지 계정이 성공적으로 업데이트되었음을 알립니다. 사용자 지정 도메인이 Azure에서 확인되었지만 도메인에 대한 트래픽은 아직 스토리지 계정으로 라우팅되지 않습니다.

1. DNS 공급자의 웹 사이트로 돌아간 다음, 하위 도메인을 Blob 서비스 엔드포인트에 매핑한 CNAME 레코드를 하나 더 만듭니다.  
   예를 들어 하위 도메인을 *www* 또는 *photos*(*asverify* 없이)로 지정하고, 호스트 이름을 *mystorageaccount*가 스토리지 계정의 이름인 *\<mystorageaccount>.blob.core.windows.net*로 지정합니다. 이 단계에서 사용자 지정 도메인 등록이 완료됩니다.

1. 마지막으로 중간 단계로만 필요한, *asverify* 하위 도메인을 포함하는 새로 만든 CNAME 레코드를 삭제할 수 있습니다.

새 CNAME 레코드가 DNS를 통해 전파된 후 사용자에게 적절한 사용 권한이 있는 경우 사용자는 사용자 지정 도메인을 사용하여 Blob 데이터를 확인할 수 있습니다.

## <a name="test-your-custom-domain"></a>사용자 지정 도메인 테스트

사용자 지정 도메인이 Blob 서비스 엔드포인트에 매핑되었는지 확인하려면 스토리지 계정 내의 공용 컨테이너에서 Blob을 만듭니다. 그런 다음, 웹 브라우저에서 `http://<subdomain.customdomain>/<mycontainer>/<myblob>` 형식의 URI를 사용하여 Blob에 액세스합니다.

예를 들어 *photos.contoso.com* 사용자 지정 하위 도메인의 *myforms* 컨테이너에서 웹 양식에 액세스하려면 `http://photos.contoso.com/myforms/applicationform.htm` URI를 사용할 수 있습니다.

## <a name="deregister-a-custom-domain"></a>사용자 지정 도메인 등록 취소

Blob Storage 엔드포인트에 대한 사용자 지정 도메인 등록을 취소하려면 다음 절차 중 하나를 사용합니다.

### <a name="azure-portal"></a>Azure portal

사용자 지정 도메인 설정을 제거하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.

1. 메뉴 창의 **Blob Service** 아래에서 **사용자 지정 도메인**을 선택합니다.  
   **사용자 지정 도메인** 창이 열립니다.

1. 사용자 지정 도메인 이름이 포함된 텍스트 상자의 콘텐츠를 지웁니다.

1. **저장** 단추를 선택합니다.

사용자 지정 도메인이 성공적으로 제거된 경우 스토리지 계정이 성공적으로 업데이트되었다는 포털 알림이 표시됩니다.

### <a name="azure-cli"></a>Azure CLI

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

### <a name="powershell"></a>PowerShell

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

## <a name="next-steps"></a>다음 단계
* [사용자 지정 도메인을 Azure CDN(Content Delivery Network) 엔드포인트에 매핑](../../cdn/cdn-map-content-to-custom-domain.md)
* [Azure CDN을 사용하여 HTTP를 통해 사용자 지정 도메인으로 Blob 액세스](storage-https-custom-domain-cdn.md)
* [Azure Blob 스토리지에서 정적 웹 사이트 호스팅(미리 보기)](storage-blob-static-website.md)
