---
title: Azure Marketplace에서 호스팅된 테스트 드라이브에 대 한 자세한 구성
description: 설명 상용 marketplace에서 호스팅된 테스트 드라이브에 대 한 구성 단계
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 11/06/2020
ms.openlocfilehash: 8bc83958e4aaea5f84654a404e4afbfd60b19b0d
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414999"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>호스트 된 테스트 드라이브에 대 한 자세한 구성

이 문서에서는 고객 Engagement 또는 Dynamics 365 for Operations에 대해 Dynamics 365에 대해 호스트 된 테스트 드라이브를 구성 하는 방법을 설명 합니다.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Dynamics 365 고객 참여 구성

1. [파트너 센터](https://partner.microsoft.com/)에 로그인합니다.
2. 위의 링크에 액세스할 수 없는 경우 응용 프로그램을 게시 하려면 [여기](https://appsource.microsoft.com/partners/list-an-app) 에 요청을 제출 해야 합니다. 요청을 검토 하면 게시 프로세스를 시작할 수 있는 권한이 부여 됩니다.
3. **고객 engagement 제품에 대 한 기존 dynamics 365** 을 찾거나 새 **Dynamics 365 for customer engagement** 를 만드세요.
4. **테스트 드라이브 사용** 확인란을 선택 하 고 **테스트 드라이브 유형** (아래 글머리 기호 참조)을 선택한 다음 **저장** 을 선택 합니다.

    [![' 테스트 드라이브 사용 ' 확인란을 선택 합니다.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **테스트 드라이브의 유형** – **Microsoft 호스트 (고객 Engagement & PowerApps의 경우 Dynamics 365)** 를 선택 합니다. 이는 Microsoft가 사용자 프로 비전 및 프로 비전 해제 테스트를 수행 하는 서비스를 호스트 하 고 유지 관리 함을 나타냅니다.

5. [이러한 지침](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)을 사용 하 여 테 넌 트에서 테스트 드라이브 사용자를 프로 비전 하 고 프로 비전 해제 하는 Microsoft AppSource 권한을 부여 합니다. 이 단계에서는 아래에 설명 된 **AZURE AD 앱 ID** 와 **Azure AD 앱 키** 값을 생성 합니다.
6. **Test drive 기술 구성** 페이지에서 이러한 필드를 완료 합니다.

    [![테스트 드라이브 기술 구성 페이지.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **최대 동시 테스트 드라이브** 수 – 활성 테스트 드라이브가 동시에 실행 될 수 있는 동시 사용자 수입니다. 테스트 드라이브가 활성화 되어 있는 동안 각 사용자는 Dynamics 라이선스를 사용 하므로 테스트 드라이브 사용자에 게이 많은 Dynamics 라이선스를 사용할 수 있는지 확인 합니다. 3 ~ 5를 권장 합니다.
    - **시험 사용 기간** – 사용자의 테스트 드라이브가 활성 상태로 유지 되는 시간입니다. 시간이 만료 되 면 사용자는 테 넌 트에서 프로 비전 해제 됩니다. 응용 프로그램의 복잡성에 따라 2-24 시간을 권장 합니다. 사용자는 항상 다른 테스트 드라이브를 요청 하 고 테스트 드라이브에 다시 액세스할 수 있습니다.
    - **인스턴스 URL** – 테스트 드라이브 사용자가 테스트 드라이브를 시작할 때 전송 되는 테스트 드라이브 사용자의 url입니다. 일반적으로 앱 및 샘플 데이터가 설치 된 Dynamics 365 인스턴스의 URL입니다. 예제 `https://testdrive.crm.dynamics.com` 값:
    - **인스턴스 웹 API url** – Dynamics 365 인스턴스의 웹 api url입니다. Microsoft Dynamics 365 인스턴스에 로그인 하 고 **Setting**  >  **사용자 지정** 개발자 리소스 인스턴스 웹 API 설정으로 이동 하 여이 값을 검색 하  >  **Developer Resources**  >  **Instance Web API** 고 주소 (URL)를 복사 합니다. 예를 들어 값:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="인스턴스 웹 API의 예입니다.":::

    - **역할 이름** – 시험 사용을 위해 만든 사용자 지정 Dynamics 365 보안 역할의 이름 이거나 기존 역할을 사용할 수 있습니다. 새 역할은 고객 Engagement 인스턴스에 로그인 하려면 역할에 필요한 최소 권한이 있어야 합니다. [Microsoft Dynamics 365에 로그인 하는 데 필요한 최소 권한](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365)을 참조 하세요. 이 역할은 테스트 드라이브 중에 사용자에 게 할당 되는 역할입니다. 예제 `testdriverole` 값:
    
        > [!IMPORTANT]
        > 보안 그룹 검사가 추가 되지 않았는지 확인 합니다. 이를 통해 사용자를 고객 Engagement 인스턴스와 동기화 할 수 있습니다.

    - **Azure Active Directory 테 넌 트 id** – Dynamics 365 인스턴스에 대 한 Azure 테 넌 트의 id입니다. 이 값을 검색 하려면 Azure Portal에 로그인 하 고 **Azure Active Directory** 속성으로 이동 하 여  >  **Properties** 디렉터리 ID를 복사 합니다. 값 예: 172f988bf-86f1-41af.
    - **Azure Active Directory 테 넌 트 이름** – Dynamics 365 인스턴스의 Azure 테 넌 트 이름입니다. `<tenantname>.onmicrosoft.com` 형식을 사용합니다. 예제 `testdrive.onmicrosoft.com` 값:
    - **Azure Active Directory 응용 프로그램 id** – 5 단계에서 만든 AZURE ACTIVE DIRECTORY (AD) 앱의 id입니다. 예제 `53852862-a2ae-4e43-9461-faa49650a096` 값:
    - **Azure Active Directory 응용 프로그램 클라이언트 암호** – 5 단계에서 만든 Azure AD 앱에 대 한 암호입니다. 예제 `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` 값:

7. Marketplace 목록 세부 정보를 제공 합니다. 추가 필수 필드를 보려면 **언어** 를 선택 합니다.

    [![Marketplace 목록 세부 정보 페이지입니다.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **설명** – 테스트 드라이브의 개요입니다. 이 텍스트는 테스트 드라이브를 프로 비전 하는 동안 사용자에 게 표시 됩니다. 서식 있는 콘텐츠를 제공하려는 경우 이 필드는 HTML을 지원합니다.
    - **사용자 수동** – 테스트 드라이브 사용자가 앱을 사용 하는 방법을 이해 하는 데 도움이 되는 PDF 사용자 설명서입니다.
    - **테스트 데모 비디오** – 앱을 소개 하는 비디오입니다 (선택 사항).

## <a name="configure-for-dynamics-365-operations"></a>Dynamics 365 작업 구성

2. 위의 링크에 액세스할 수 없는 경우 응용 프로그램을 게시 하려면 [여기](https://appsource.microsoft.com/partners/list-an-app) 에 요청을 제출 해야 합니다. 요청을 검토 하면 게시 프로세스를 시작할 수 있는 권한이 부여 됩니다.
3. 운영 제안의 기존 **dynamics 365** 을 찾거나, 운영 제안의 새 **dynamics 365** 를 만드세요.
4. **테스트 드라이브 사용** 확인란을 선택 하 고 **테스트 드라이브 유형** (아래 글머리 기호 참조)을 선택한 다음 **저장** 을 선택 합니다.

    [![' 테스트 드라이브 사용 ' 확인란을 선택 합니다.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **테스트 드라이브의 유형** – **작업에 대해 Dynamics 365을** 선택 합니다. 즉, Microsoft는 테스트 드라이브 사용자 프로 비전 및 프로 비전 해제를 수행 하는 서비스를 호스트 하 고 유지 관리 합니다.

5. [이러한 지침](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)을 사용 하 여 테 넌 트에서 테스트 드라이브 사용자를 프로 비전 하 고 프로 비전 해제 하는 Microsoft AppSource 권한을 부여 합니다. 이 단계에서는 아래에 설명 된 **AZURE AD 앱 ID** 와 **Azure AD 앱 키** 값을 생성 합니다.
6. **Test drive 기술 구성** 페이지에서 이러한 필드를 완료 합니다.

    [![Marketplace 기술 구성 페이지.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **최대 동시 테스트 드라이브** 수 – 활성 테스트 드라이브가 동시에 실행 될 수 있는 동시 사용자 수입니다. 테스트 드라이브가 활성화 되어 있는 동안 각 사용자는 Dynamics 라이선스를 사용 하므로 테스트 드라이브 사용자에 게이 많은 Dynamics 라이선스를 사용할 수 있는지 확인 합니다. 3 ~ 5를 권장 합니다.
    - **시험 사용 기간** – 사용자의 테스트 드라이브가 활성 상태로 유지 되는 시간입니다. 시간이 만료 되 면 사용자는 테 넌 트에서 프로 비전 해제 됩니다. 응용 프로그램의 복잡성에 따라 2-24 시간을 권장 합니다. 사용자는 항상 다른 테스트 드라이브를 요청 하 고 테스트 드라이브에 다시 액세스할 수 있습니다.
    - **인스턴스 URL** – 테스트 드라이브 사용자가 테스트 드라이브를 시작할 때 전송 되는 테스트 드라이브 사용자의 url입니다. 일반적으로 앱 및 샘플 데이터가 설치 된 Dynamics 365 인스턴스의 URL입니다. 예제 `https://testdrive.crm.dynamics.com` 값:
    - **Azure Active Directory 테 넌 트 id** – Dynamics 365 인스턴스에 대 한 Azure 테 넌 트의 id입니다. 이 값을 검색 하려면 Azure Portal에 로그인 하 고 **Azure Active Directory** 속성으로 이동 하 여  >  **Properties** 디렉터리 ID를 복사 합니다. 값 예: 172f988bf-86f1-41af.
    - **Azure Active Directory 테 넌 트 이름** – Dynamics 365 인스턴스의 Azure 테 넌 트 이름입니다. `<tenantname>.onmicrosoft.com` 형식을 사용합니다. 예제 `testdrive.onmicrosoft.com` 값:
    - **Azure Active Directory 응용 프로그램 id** – 5 단계에서 만든 AZURE ACTIVE DIRECTORY (AD) 앱의 id입니다. 예제 `53852862-a2ae-4e43-9461-faa49650a096` 값:
    - **Azure Active Directory 응용 프로그램 클라이언트 암호** – 5 단계에서 만든 Azure AD 앱에 대 한 암호입니다. 예제 `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` 값:
    - **평가판 법률 엔터티** – 평가판 사용자를 할당 하기 위한 법적 엔터티를 제공 합니다. [법적 엔터티 만들기 또는 수정](https://technet.microsoft.com/library/hh242184.aspx)에서 새 항목을 만들 수 있습니다.
    - **역할 이름** – 테스트 드라이브에 대해 만든 사용자 지정 Dynamics 365 보안 역할의 AOT (응용 프로그램 개체 트리) 이름입니다. 이 역할은 테스트 드라이브 중에 사용자에 게 할당 되는 역할입니다.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="보안 구성 페이지입니다.":::

7. Marketplace 목록 세부 정보를 제공 합니다. 추가 필수 필드를 보려면 **언어** 를 선택 합니다.

    [![Marketplace 목록 세부 정보 페이지입니다.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **설명** – 테스트 드라이브의 개요입니다. 이 텍스트는 테스트 드라이브를 프로 비전 하는 동안 사용자에 게 표시 됩니다. 서식 있는 콘텐츠를 제공하려는 경우 이 필드는 HTML을 지원합니다.
    - **사용자 수동** – 테스트 드라이브 사용자가 앱을 사용 하는 방법을 이해 하는 데 도움이 되는 PDF 사용자 설명서입니다.
    - **테스트 데모 비디오** – 앱을 소개 하는 비디오입니다 (선택 사항).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->
