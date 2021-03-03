---
title: 사용자 지정 도메인 이름 구입
description: App Service 도메인을 구입 하 고 앱 Azure App Service에 대 한 사용자 지정 도메인으로 사용 하는 방법을 알아봅니다.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: cdcf22a42375949cc4d6be0b4f3062cee26219d6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704857"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Azure App Service의 사용자 지정 도메인 이름 구입

App Service 도메인은 Azure에서 직접 관리 되는 사용자 지정 도메인입니다. 이 도메인을 통해 [Azure App Service](overview.md)의 사용자 지정 도메인을 쉽게 관리할 수 있습니다. 이 자습서에서는 App Service 도메인을 구입하고 DNS 이름을 Azure App Service에 할당하는 방법을 보여 줍니다.

Azure VM 또는 Azure Storage의 경우 [Azure VM 또는 Azure Storage에 App Service 도메인 할당](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage)을 참조하세요. Cloud Services의 경우 [Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* [App Service 앱을 만들거나](./index.yml) 다른 자습서에서 만든 앱을 사용합니다. 앱은 Azure 공용 지역에 있어야 합니다. 지금은 Azure 국가별 클라우드가 지원 되지 않습니다.
* [구독에 대한 지출 한도를 삭제합니다](../cost-management-billing/manage/spending-limit.md#remove). 체험 구독 크레딧으로는 App Service 도메인을 구입할 수 없습니다.

## <a name="buy-an-app-service-domain"></a>App Service 도메인 구입

App Service 도메인에 대 한 가격 책정 정보를 보려면 [App Service 가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/windows/) 를 방문 하 App Service 도메인으로 스크롤합니다.

1. [Azure 포털](https://portal.azure.com)에서 Azure 계정으로 로그인합니다.

1. 검색 창에서 **App Service 도메인** 을 검색 하 고 선택 합니다.

    ![Azure App Service 도메인에 대 한 포털 탐색](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **App Service 도메인** 보기에서 **추가** 를 클릭 합니다.

    ![App Service 도메인에서 추가를 클릭 합니다.](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. **최신 버전의 App Service 도메인 만들기 환경을 시도 하려면 클릭** 하십시오 .를 선택 합니다.

    ![새 환경을 사용 하 여 App Service 도메인 만들기](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>기본 사항 탭

1. **기본 사항** 탭에서 다음 표를 사용 하 여 설정을 구성 합니다.  

   | 설정  | Description |
   | -------- | ----------- |
   | **구독** | 도메인을 구입 하는 데 사용할 구독입니다. |
   | **리소스 그룹** | 도메인을 배치할 리소스 그룹입니다. 예를 들어 앱이 있는 리소스 그룹입니다. |
   | **도메인** | 원하는 도메인을 입력 합니다. 예를 들면 **contoso.com** 입니다. 원하는 도메인을 사용할 수 없는 경우 사용 가능한 도메인의 제안 목록에서 선택 하거나 다른 도메인을 사용해 볼 수 있습니다. |

    > [!NOTE]
    > App Service 도메인에서 지원되는 [최상위 도메인](https://wikipedia.org/wiki/Top-level_domain)은 _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ 및 _co.in_ 입니다.
    >
    >
    
2. 완료 되 면 **다음: 연락처 정보** 를 클릭 합니다.

### <a name="contact-information-tab"></a>연락처 정보 탭

1. 도메인 등록을 위해 [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) 에 필요한 정보를 제공 합니다. 

    모든 필수 필드를 가능한 한 정확하게 기입해야 합니다. 연락처 정보에 대 한 잘못 된 데이터로 인해 도메인을 구입 하지 못할 수 있습니다.

1. 완료 되 면 **다음: 고급** 을 클릭 합니다.

### <a name="advanced-tab"></a>고급 탭

1. **고급** 탭에서 옵션 설정을 구성 합니다.  

   | 설정  | Description |
   | -------- | ----------- |
   | **자동 갱신** | 기본적으로 사용하도록 설정되어 있습니다. App Service 도메인은 1 년 단위로 사용자에 게 등록 됩니다. 자동 갱신은 도메인 등록이 만료 되지 않고 도메인의 소유권을 유지 하도록 합니다. 갱신할 때 Azure 구독에 연간 도메인 등록 요금이 자동으로 청구 됩니다. 옵트아웃 하려면 **사용 안 함** 을 선택 합니다. 자동 갱신을 사용 하지 않도록 설정한 경우 [수동으로 갱신할](#renew-the-domain)수 있습니다. |
   | **개인 정보 보호** | 기본적으로 사용하도록 설정되어 있습니다. 개인 정보 보호는 WHOIS 데이터베이스에서 도메인 등록 담당자 정보를 숨깁니다. 개인 정보 보호는 매년 도메인 등록 요금에 이미 포함 되어 있습니다. 옵트아웃 하려면 **사용 안 함** 을 선택 합니다. |

2. 완료 되 면 **다음: 태그** 를 클릭 합니다.

### <a name="finish"></a>Finish

1. **태그** 탭에서 App Service 도메인에 대해 원하는 태그를 설정 합니다. 태그 지정은 App Service 도메인을 사용 하는 데 필요 하지 않지만 [리소스를 관리 하는 데 도움이 되는 Azure의 기능](../azure-resource-manager/management/tag-resources.md)입니다.

1. **다음: 검토 + 만들기** 를 클릭 합니다.

1. **검토 + 만들기** 탭에서 도메인 순서를 검토 합니다. 작업을 마쳤으면 **만들기** 를 클릭합니다.

    > [!NOTE]
    > App Service 도메인은 도메인 등록에 GoDaddy를 사용 하 고 도메인을 호스트 하는 Azure DNS 합니다. 연간 도메인 등록 요금 외에도 Azure DNS에 대 한 사용 요금이 적용 됩니다. 자세한 내용은 [Azure DNS 가격 책정](https://azure.microsoft.com/pricing/details/dns/)을 참조하세요.
    >
    >

1. 도메인 등록이 완료 되 면 **리소스로 이동** 단추가 표시 됩니다. 관리 페이지를 표시 하려면이 항목을 선택 합니다.

    ![App Service 도메인을 만들었습니다. 리소스로 이동](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

이제이 사용자 지정 도메인에 App Service 앱을 할당할 준비가 되었습니다.

## <a name="prepare-the-app"></a>앱 준비

사용자 지정 DNS 이름을 웹앱에 매핑하려면 웹앱의 [App Service 요금제](https://azure.microsoft.com/pricing/details/app-service/)가 유료 계층(공유, 기본, 표준, 프리미엄 또는 Azure Functions의 경우 사용량)이어야 합니다. 이 단계에서는 App Service 앱이 지원되는 가격 책정 계층에 속하는지 확인해야 합니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Azure Portal에서 앱으로 이동합니다.

1. 위쪽 검색 창에서를 검색 하 고 **App Services** 를 선택 합니다.

    ![App Services 검색](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. 앱의 이름을 선택 합니다.

    ![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

    App Service 앱의 관리 페이지가 표시됩니다.  

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

1. 앱 페이지의 왼쪽 탐색 영역에서 **설정** 섹션으로 스크롤하고 **강화(App Service 계획)** 를 선택합니다.

    ![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. 앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **F1** 계층에 속해 있지 않은지 확인합니다. 사용자 지정 DNS는 **F1** 계층에서는 지원되지 않습니다. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="수직 확장 (App Service 계획)이 선택 된 앱 페이지의 왼쪽 탐색 메뉴 스크린샷":::

1. App Service 계획이 **F1** 계층이 아닌 경우 **스케일업** 페이지를 닫고 [도메인 구입](#buy-an-app-service-domain)으로 건너뜁니다.

### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

1. 유료 계층(**D1**, **B1**, **B2**, **B3** 또는 **프로덕션** 범주의 모든 계층) 중 하나를 선택합니다. 추가 옵션을 보려면 **추가 옵션 보기** 를 클릭합니다.

1. **적용** 을 클릭합니다.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="프로덕션 범주의 사용자 지정 도메인 가격 책정 계층에서 프로덕션 탭, B1 계획 및 적용 단추가 강조 표시 된 스크린샷":::

    다음 알림이 표시되면 강화 작업이 완료됩니다.

    ![크기 조정 작업 확인](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>앱에 App Service 도메인 매핑

동일한 구독에 있는 한 App Service 도메인의 호스트 이름을 App Service 앱에 쉽게 매핑할 수 있습니다. 앱에서 직접 App Service 도메인 또는 하위 도메인을 매핑하고 Azure에서 필요한 DNS 레코드를 만듭니다.

> [!NOTE]
> 도메인 및 앱이 다른 구독에 있는 경우 [외부에서 구매한 도메인 매핑과](app-service-web-tutorial-custom-domain.md)마찬가지로 App Service 도메인을 앱에 매핑합니다. 이 경우 Azure DNS는 외부 도메인 공급자 이며 [필요한 DNS 레코드를 수동으로 추가](#manage-custom-dns-records)해야 합니다.
>

### <a name="map-the-domain"></a>도메인 매핑

1. 앱 페이지의 왼쪽 탐색 영역에서 **설정** 섹션으로 스크롤하고 **사용자 지정 도메인** 을 선택 합니다.

    ![사용자 지정 도메인 메뉴를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **사용자 지정 도메인 추가** 를 선택합니다.

    ![호스트 이름 추가 항목을 보여주는 스크린샷](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. App Service 도메인 (예: **contoso.com**) 또는 하위 도메인 (예: **www.contoso.com**)을 입력 하 고 **유효성 검사** 를 클릭 합니다.

    > [!NOTE]
    > App Service 도메인 이름에서 철자가 잘못 된 경우 페이지 맨 아래에 확인 오류가 표시 되어 일부 DNS 레코드를 누락 하는 것을 알 수 있습니다. App Service 도메인에 대해 이러한 레코드를 수동으로 추가할 필요가 없습니다. 도메인 이름을 올바르게 입력 했는지 확인 하 고 **유효성 검사** 를 다시 클릭 합니다.
    >
    > ![확인 오류를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. **호스트 이름 레코드 유형을** 수락 하 고 **사용자 지정 도메인 추가** 를 클릭 합니다.

    ![사용자 지정 도메인 추가 단추를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. 새 사용자 지정 도메인이 앱의 **사용자 지정 도메인** 페이지에 반영될 때까지 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고칩니다.

    ![CNAME 레코드 추가를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > 사용자 지정 도메인에 대 한 **보안 되지 않은** 레이블은 아직 TLS/SSL 인증서에 바인딩되어 있지 않음을 의미 합니다. 브라우저에서 사용자 지정 도메인으로 전송되는 HTTPS 요청은 브라우저에 따라 오류 또는 경고를 수신합니다. TLS 바인딩을 추가하려면 [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조하세요.
    
### <a name="test-the-custom-domain"></a>사용자 지정 도메인 테스트

사용자 지정 도메인을 테스트 하려면 브라우저에서 해당 도메인으로 이동 합니다.

## <a name="renew-the-domain"></a>도메인 갱신

구입한 App Service 도메인은 구입 시점으로부터 1년 동안 유효합니다. 도메인은 기본적으로 다음 해의 결제 방법을 부과하여 자동으로 갱신되도록 구성됩니다. 도메인 이름을 수동으로 갱신할 수 있습니다.

자동 갱신을 해제하거나 도메인을 수동으로 갱신하려면 다음 단계를 따릅니다.

1. 검색 창에서 **App Service 도메인** 을 검색 하 고 선택 합니다.

    ![Azure App Service 도메인에 대 한 포털 탐색](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **App Service 도메인** 섹션에서 구성하려는 도메인을 선택합니다.

1. 도메인의 왼쪽 탐색 영역에서 **도메인 갱신** 을 선택합니다. 도메인 갱신을 자동으로 중지 하려면 **끄기** 를 선택 합니다. 설정은 즉시 적용됩니다.

    ![도메인을 자동으로 갱신 하는 옵션을 보여 주는 스크린샷](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > 페이지에서 다른 곳으로 이동 하는 경우 **확인** 을 클릭 하 여 "저장 하지 않은 편집 내용이 삭제 됩니다." 오류를 무시 합니다.
    >

도메인을 수동으로 갱신하려면 **도메인 갱신** 을 선택합니다. 그러나이 단추는 [도메인 만료가 90 일 전에](#when-domain-expires)는 활성화 되지 않습니다.

도메인 갱신이 성공적으로 완료 되 면 24 시간 이내에 전자 메일 알림을 받게 됩니다.

## <a name="when-domain-expires"></a>도메인이 만료 되는 경우

Azure는 다음과 같이 만료 되거나 만료 된 App Service 도메인을 다룹니다.

* 자동 갱신을 사용 하지 않도록 설정 하는 경우: 90 일 전에 도메인 만료 전 갱신 알림 전자 메일이 전송 되 고 포털에서 **도메인 갱신** 단추가 활성화 됩니다.
* 자동 갱신을 사용 하도록 설정 하는 경우: 도메인 만료 날짜가 지난 날에 Azure는 도메인 이름 갱신에 대 한 요금을 청구 하려고 합니다.
* 자동 갱신 중에 오류가 발생 하는 경우 (예: 파일의 카드가 만료 된 경우) 또는 자동 갱신을 사용 하지 않도록 설정 하 고 도메인 만료를 허용 하는 경우 Azure는 도메인 만료를 알리고 도메인 이름을 파킹 합니다. 도메인을 [수동으로 갱신할](#renew-the-domain) 수 있습니다.
* 만료 후 4 일 및 12 일에 Azure는 추가 알림 전자 메일을 보냅니다. 도메인을 [수동으로 갱신할](#renew-the-domain) 수 있습니다. 만료 후 5 일이 지나면 만료 된 도메인에 대 한 DNS 확인이 중지 됩니다.
* 만료 후 19 일 일에 도메인은 보류 상태로 유지 되지만 상환 요금이 적용 됩니다. 고객 지원에 문의 하 여 해당 갱신 및 상환 요금에 따라 도메인 이름을 갱신할 수 있습니다.
* 만료 후 25 일이 지나면 Azure는 도메인 이름 산업 경매 서비스를 사용 하 여 경매에 도메인을 배치 합니다. 고객 지원에 문의 하 여 해당 갱신 및 상환 요금에 따라 도메인 이름을 갱신할 수 있습니다.
* 만료 후 30 일이 지나면 더 이상 도메인을 사용할 수 없습니다.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>사용자 지정 DNS 레코드 관리

Azure에서 App Service 도메인에 대한 DNS 레코드는 [Azure DNS](https://azure.microsoft.com/services/dns/)를 사용하여 관리됩니다. 외부에서 구매한 도메인처럼 DNS 레코드를 추가, 제거 및 업데이트할 수 있습니다. 사용자 지정 DNS 레코드를 관리 하려면:

1. 검색 창에서 **App Service 도메인** 을 검색 하 고 선택 합니다.

    ![Azure App Service 도메인에 대 한 포털 탐색](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **App Service 도메인** 섹션에서 구성하려는 도메인을 선택합니다.

1. **개요** 페이지에서 **DNS 레코드 관리** 를 선택 합니다.

    ![DNS 레코드에 액세스할 수 있는 위치를 보여 주는 스크린샷](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

DNS 레코드를 편집하는 방법에 대한 자세한 내용은 [Azure Portal에서 DNS 영역을 관리하는 방법](../dns/dns-operations-dnszones-portal.md)을 참조하세요.

## <a name="cancel-purchase-delete-domain"></a>구매 취소(도메인 삭제)

App Service 도메인을 구매한 후 5일 이내에 구매를 취소하고 전액 환불 받을 수 있습니다. 5일이 지나면 App Service 도메인을 삭제할 수 있지만 환불은 불가능합니다.

1. 검색 창에서 **App Service 도메인** 을 검색 하 고 선택 합니다.

    ![Azure App Service 도메인에 대 한 포털 탐색](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **App Service 도메인** 섹션에서 구성하려는 도메인을 선택합니다.

1. 도메인의 왼쪽 탐색 영역에서 **호스트 이름 바인딩** 을 선택 합니다. 모든 Azure 서비스의 호스트 이름 바인딩이 여기에 나열됩니다.

    ![호스트 이름 바인딩 페이지를 보여 주는 스크린샷](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. **...를 선택**  >  하 여 각 호스트 이름 바인딩을 삭제 합니다. **삭제**. 모든 바인딩을 삭제한 후 **저장** 을 선택합니다.

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. 도메인의 왼쪽 탐색 영역에서 **개요** 를 선택 합니다. 

1. 구매한 도메인에 대한 취소 기간이 경과되지 않은 경우 **구매 취소** 를 선택합니다. 그렇지 않으면 **삭제** 단추가 대신 표시됩니다. 환불하지 않고 도메인을 삭제하려면 **삭제** 를 선택합니다.

    ![구매한 도메인을 삭제 하거나 취소할 위치를 보여 주는 스크린샷](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. **예** 를 선택 하 여 작업을 확인 합니다.

    작업이 완료되면 구독에서 도메인이 해제되어 누구든지 다시 구매할 수 있게 됩니다. 

## <a name="direct-default-url-to-a-custom-directory"></a>사용자 지정 디렉터리로 기본 URL 전달

App Service는 기본적으로 웹 요청을 앱 코드의 루트 디렉터리로 보냅니다. 과 같은 하위 디렉터리로 전달 하려면 `public` [사용자 지정 디렉터리로 리디렉션](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

App Service에 사용자 지정 TLS/SSL 인증서를 바인딩하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service에서 TLS 바인딩을 사용 하 여 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
