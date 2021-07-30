---
title: Azure 리소스와 Azure DNS 통합 - Azure DNS
description: 이 문서에서는 Azure DNS를 함께 사용하여 Azure 리소스에 DNS를 제공하는 방법에 대해 알아봅니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 05/06/2021
ms.author: rohink
ms.openlocfilehash: c09c1840c9976a2ac14cb5ef73f8c4e15000c54f
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109634940"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Azure DNS를 사용하여 Azure 서비스에 대해 사용자 지정 도메인 설정 제공

Azure DNS는 사용자 지정 도메인을 지원하거나 FQDN(정규화된 도메인 이름)이 있는 모든 Azure 리소스에 대한 명명 확인을 제공합니다. 예를 들어 사용자가 `contoso.com` 또는 `www.contoso.com`을 FQDN으로 사용하여 액세스하도록 하려는 Azure 웹앱이 있습니다. 이 문서에서는 사용자 지정 도메인을 사용하기 위해 Azure DNS로 Azure 서비스를 구성하는 과정을 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure DNS를 사용자 지정 도메인에 사용하려면 먼저 도메인을 Azure DNS에 위임해야 합니다. 위임에 대한 이름 서버를 구성하는 방법에 대한 지침은 [Azure DNS에 도메인 위임](./dns-delegate-domain-azure-dns.md)을 참조하세요. 도메인이 Azure DNS 영역에 위임되면 이제 필요한 DNS 레코드를 구성할 수 있습니다.

Azure 함수 앱, 공용 IP 주소, App Service(Web Apps), Blob Storage 및 Azure CDN에 대해 베니티 또는 사용자 지정 도메인을 구성할 수 있습니다.

## <a name="azure-function-app"></a>Azure 함수 앱

Azure 함수 앱에 대해 사용자 지정 도메인을 구성하기 위해 함수 앱 자체에서 CNAME 레코드가 만들어지고 구성됩니다.
 
1. **Function App** 으로 이동하고 함수 앱을 선택합니다. *설정* 아래에서 **사용자 지정 도메인** 을 선택합니다. *할당된 사용자 지정 도메인* 아래의 **현재 URL** 을 적어 둡니다. 이 주소는 만든 DNS에 대한 별칭으로 사용됩니다.

    :::image type="content" source="./media/dns-custom-domain/function-app.png" alt-text="함수 앱에 대한 사용자 지정 도메인의 스크린샷":::

1. DNS 영역으로 이동하여 **+ 레코드 집합** 을 선택합니다. **레코드 집합 추가** 페이지에서 다음 정보를 입력하고, **확인** 을 선택하여 만듭니다.

    :::image type="content" source="./media/dns-custom-domain/function-app-record.png" alt-text="함수 앱 레코드 집합 추가 페이지의 스크린샷":::

    |속성  |값  |설명  |
    |---------|---------|---------|
    | 속성 | myfunctionapp | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다. |
    | Type | CNAME | 별칭을 사용하는 CNAME 레코드를 사용합니다. |
    | TTL | 1 | 1은 1시간 동안 사용됩니다.  |
    | TTL 단위 | 시간 | 시간 측정으로 시간(Hour)이 사용됩니다.  |
    | Alias | contosofunction.azurewebsites.net | 별칭을 만드는 DNS 이름입니다. 이 예에서는 함수 앱에 기본적으로 제공되는 contosofunction.azurewebsites.net DNS 이름입니다.        |
    
1. 함수 앱으로 돌아가서 *설정* 아래에서 **사용자 지정 도메인** 을 선택합니다. 그런 다음, **+ 사용자 지정 도메인 추가** 를 선택합니다.

    :::image type="content" source="./media/dns-custom-domain/function-app-add-domain.png" alt-text="함수 앱에 대한 사용자 지정 도메인 추가 단추의 스크린샷":::    

1. **사용자 지정 도메인 추가** 페이지의 **사용자 지정 도메인** 텍스트 필드에서 CNAME 레코드를 입력하고, **유효성 검사** 를 선택합니다. 레코드가 발견되면 **사용자 지정 도메인 추가** 단추가 표시됩니다. **사용자 지정 도메인 추가** 를 선택하여 별칭을 추가합니다.

    :::image type="content" source="./media/dns-custom-domain/function-app-cname.png" alt-text="함수 앱에 대한 사용자 지정 도메인 추가 페이지의 스크린샷":::

## <a name="public-ip-address"></a>공용 IP 주소

Application Gateway, Load Balancer, Cloud Service, Resource Manager VM, 클래식 VM처럼 공용 IP 주소 리소스를 사용하는 서비스에 대해 사용자 지정 도메인을 구성하기 위해 A 레코드가 사용됩니다.

1. 공용 IP 리소스로 이동하여 **구성** 을 선택합니다. 표시된 IP 주소를 적어 둡니다.

    :::image type="content" source="./media/dns-custom-domain/public-ip.png" alt-text="공용 IP 구성 페이지의 스크린샷":::

1. DNS 영역으로 이동하여 **+ 레코드 집합** 을 선택합니다. **레코드 집합 추가** 페이지에서 다음 정보를 입력하고, **확인** 을 선택하여 만듭니다.

    :::image type="content" source="./media/dns-custom-domain/public-ip-record.png" alt-text="공용 IP 레코드 집합 페이지의 스크린샷.":::

    | 속성 | 값 | 설명 |
    | -------- | ----- | ------------|
    | 속성 | webserver1 | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다. |
    | 유형 | A | 리소스가 IP 주소이므로 A 레코드를 사용합니다. |
    | TTL | 1 | 1은 1시간 동안 사용됩니다. |
    | TTL 단위 | 시간 | 시간 측정으로 시간(Hour)이 사용됩니다. |
    | IP 주소 | `<your ip address>` | 공용 IP 주소입니다. |

1. A 레코드가 생성되면 `nslookup`을 실행하여 레코드 확인의 유효성을 검사합니다.

    :::image type="content" source="./media/dns-custom-domain/public-ip-nslookup.png" alt-text="cmd의 공용 IP에 대한 nslookup의 스크린샷":::

## <a name="app-service-web-apps"></a>App Service(Web Apps)

다음 단계는 앱 서비스 웹앱에 대해 사용자 지정 도메인을 구성하는 과정을 안내합니다.

1. **App Service** 로 이동하여 사용자 지정 도메인 이름을 구성할 리소스를 선택하고, *설정* 아래에서 **사용자 지정 도메인** 을 선택합니다. *할당된 사용자 지정 도메인* 아래의 **현재 URL** 을 적어 둡니다. 이 주소는 만든 DNS에 대한 별칭으로 사용됩니다.

    :::image type="content" source="./media/dns-custom-domain/web-app.png" alt-text="웹앱에 대한 사용자 지정 도메인의 스크린샷":::

1. DNS 영역으로 이동하여 **+ 레코드 집합** 을 선택합니다. **레코드 집합 추가** 페이지에서 다음 정보를 입력하고, **확인** 을 선택하여 만듭니다.

    :::image type="content" source="./media/dns-custom-domain/web-app.png" alt-text="웹앱 레코드 집합 페이지의 스크린샷":::

    | 속성  | 값 | 설명 |
    |---------- | ----- | ----------- |
    | 속성 | mywebserver | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다. |
    | Type | CNAME | 별칭을 사용하는 CNAME 레코드를 사용합니다. 리소스에서 IP 주소를 사용한 경우 A 레코드가 사용됩니다. |
    | TTL | 1 | 1은 1시간 동안 사용됩니다. |
    | TTL 단위 | 시간 | 시간 측정으로 시간(Hour)이 사용됩니다. |
    | Alias | contoso.azurewebsites.net | 별칭을 만드는 DNS 이름입니다. 이 예에서는 웹앱에 기본적으로 제공되는 contoso.azurewebsites.net DNS 이름입니다. |

1. 웹앱으로 돌아가서 *설정* 아래에서 **사용자 지정 도메인** 을 선택합니다. 그런 다음, **+ 사용자 지정 도메인 추가** 를 선택합니다.

    :::image type="content" source="./media/dns-custom-domain/web-app-add-domain.png" alt-text="웹앱에 대한 사용자 지정 도메인 추가 단추의 스크린샷":::  

1. **사용자 지정 도메인 추가** 페이지의 **사용자 지정 도메인** 텍스트 필드에서 CNAME 레코드를 입력하고, **유효성 검사** 를 선택합니다. 레코드가 발견되면 **사용자 지정 도메인 추가** 단추가 표시됩니다. **사용자 지정 도메인 추가** 를 선택하여 별칭을 추가합니다.

    :::image type="content" source="./media/dns-custom-domain/web-app-cname.png" alt-text="웹앱에 대한 사용자 지정 도메인 추가 페이지의 스크린샷":::

1. 프로세스가 완료되면 **nslookup** 을 실행하여 이름 확인이 작동하는지 확인합니다.

    :::image type="content" source="./media/dns-custom-domain/app-service-nslookup.png" alt-text="웹앱에 대한 nslookup의 스크린샷"::: 

사용자 지정 도메인을 App Service에 매핑하는 방법을 자세히 알아보려면 [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)을 참조하세요.

활성 DNS 이름을 마이그레이션하는 방법을 알아보려면 [Azure App Service로 활성 DNS 이름 마이그레이션](../app-service/manage-custom-dns-migrate-domain.md)을 참조하세요.

App Service에 대한 사용자 지정 도메인을 구매해야 하는 경우 [Azure Web Apps에 대한 사용자 지정 도메인 이름 구입](../app-service/manage-custom-dns-buy-domain.md)을 참조하세요.

## <a name="blob-storage"></a>Blob Storage

다음 단계에서는 asverify 메서드를 사용하여 Blob Storage 계정에 대해 CNAME 레코드를 구성하는 과정을 안내합니다. 이 메서드를 사용하면 가동 중지 시간이 발생하지 않습니다.

1. **스토리지 계정** 으로 이동하여 스토리지 계정을 선택하고, *설정* 아래에서 **네트워킹** 을 선택합니다. 그런 다음, **사용자 지정 도메인** 탭을 선택합니다. 2단계의 FQDN을 적어 둡니다. 이 이름은 첫 번째 CNAME 레코드를 만드는 데 사용됩니다.

    :::image type="content" source="./media/dns-custom-domain/blob-storage.png" alt-text="스토리지 계정에 대한 사용자 지정 도메인의 스크린샷":::

1. DNS 영역으로 이동하여 **+ 레코드 집합** 을 선택합니다. **레코드 집합 추가** 페이지에서 다음 정보를 입력하고, **확인** 을 선택하여 만듭니다.

    :::image type="content" source="./media/dns-custom-domain/storage-account-record.png" alt-text="스토리지 계정 레코드 집합 페이지의 스크린샷":::

    | 속성 | 값 | 설명 |
    | -------- | ----- | ----------- |
    | 속성 | asverify.mystorageaccount | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다. |
    | Type | CNAME | 별칭을 사용하는 CNAME 레코드를 사용합니다. |
    | TTL | 1 | 1은 1시간 동안 사용됩니다. |
    | TTL 단위 | 시간 | 시간 측정으로 시간(Hour)이 사용됩니다. |
    | Alias | asverify.contoso.blob.core.windows.net | 별칭을 만드는 DNS 이름입니다. 이 예에서는 스토리지 계정에 기본적으로 제공되는 asverify.contoso.blob.core.windows.net DNS 이름입니다. |

1. 스토리지 계정으로 돌아가서 **네트워킹** 을 선택한 다음, **사용자 지정 도메인** 탭을 선택합니다. 텍스트 상자에서 asverify 접두사 없이 만든 별칭을 입력하고, **간접 CNAME 유효성 검사 사용** 을 선택하고, **저장** 을 선택합니다. 

    :::image type="content" source="./media/dns-custom-domain/blob-storage-add-domain.png" alt-text="스토리지 계정 사용자 지정 도메인 추가 페이지의 스크린샷":::

1. DNS 영역으로 돌아가서 asverify 접두사 없이 CNAME 레코드를 만듭니다.  이후에는 asverify 접두사가 있는 CNAME 레코드를 안전하게 삭제할 수 있습니다.

    :::image type="content" source="./media/dns-custom-domain/storage-account-record-set.png" alt-text="asverify 접두사가 없는 스토리지 계정 레코드의 스크린샷":::

1. `nslookup`을 실행하여 DNS 확인의 유효성을 검사합니다.

사용자 지정 도메인을 Blob Storage 엔드포인트에 매핑하는 방법에 대해 자세히 알아보려면 [Blob Storage 엔드포인트에 대한 사용자 지정 도메인 이름 구성](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)을 참조하세요.

## <a name="azure-cdn"></a>Azure CDN

다음 단계에서는 cdnverify 메서드를 사용하여 CDN 엔드포인트에 대해 CNAME 레코드를 구성하는 과정을 안내합니다. 이 메서드를 사용하면 가동 중지 시간이 발생하지 않습니다.

1. CDN 프로필로 이동하여 작업 중인 엔드포인트를 선택합니다. **+ 사용자 지정 도메인** 을 선택합니다. 이 값으로 **엔드포인트 호스트 이름** 은 CNAME 레코드가 가리키는 레코드입니다.

    :::image type="content" source="./media/dns-custom-domain/cdn.png" alt-text="CDN 사용자 지정 도메인 페이지의 스크린샷":::

1. DNS 영역으로 이동하여 **+ 레코드 집합** 을 선택합니다. **레코드 집합 추가** 페이지에서 다음 정보를 입력하고, **확인** 을 선택하여 만듭니다.

    :::image type="content" source="./media/dns-custom-domain/cdn-record.png" alt-text="CDN 레코드 집합 페이지의 스크린샷":::

    | 속성 | 값 | 설명 |
    | -------- | ----- | ----------- |
    | 속성 | cdnverify.mycdnendpoint | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다. |
    | Type | CNAME | 별칭을 사용하는 CNAME 레코드를 사용합니다. |
    | TTL | 1 | 1은 1시간 동안 사용됩니다. |
    | TTL 단위 | 시간 | 시간 측정으로 시간(Hour)이 사용됩니다. |
    | Alias | cdnverify.contoso.azureedge.net | 별칭을 만드는 DNS 이름입니다. 이 예에서는 CDN 엔드포인트에 기본적으로 제공되는 cdnverify.contoso.azureedge.net DNS 이름입니다. |

1. CDN 엔드포인트로 돌아가서 **+ 사용자 지정 도메인** 을 선택합니다. cdnverify 접두사 없이 CNAME 레코드 별칭을 입력하고, **추가** 를 선택합니다.

    :::image type="content" source="./media/dns-custom-domain/cdn-add.png" alt-text="CDN 엔드포인트에 대한 사용자 지정 도메인 추가 페이지의 스크린샷":::

1. DNS 영역으로 돌아가서 cdnverify 접두사 없이 CNAME 레코드를 만듭니다.  이후에는 cdnverify 접두사가 있는 CNAME 레코드를 안전하게 삭제할 수 있습니다.

    :::image type="content" source="./media/dns-custom-domain/cdn-record-set.png" alt-text="cdnverify 접두사가 없는 CDN 레코드의 스크린샷":::

CDN에 대한 자세한 정보 및 중간 등록 단계 없이 사용자 지정 도메인을 구성하는 방법은 [Azure CDN 콘텐츠를 사용자 지정 도메인에 매핑](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure에서 호스트되는 서비스에 대해 역방향 DNS 구성](dns-reverse-dns-for-azure-services.md) 방법에 대해 알아봅니다.
