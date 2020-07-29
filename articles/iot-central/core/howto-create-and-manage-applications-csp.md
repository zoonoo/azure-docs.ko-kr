---
title: CSP 포털에서 Azure IoT Central 응용 프로그램 만들기 및 관리 | Microsoft Docs
description: CSP로서 고객을 대신하여 Azure IoT Central 애플리케이션을 만드는 방법.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 02481d5dcbaba15c9b17a27348207d9af64f3355
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982041"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>CSP 포털에서 Azure IoT Central 응용 프로그램 만들기 및 관리

Microsoft CSP(클라우드 솔루션 공급자) 프로그램은 Microsoft 재판매인 프로그램입니다. 이의 의도는 모든 Microsoft 상용 온라인 서비스를 재판매하기 위한 원스톱 프로그램을 채널 파트너에게 제공하는 것입니다. [클라우드 솔루션 공급자 프로그램](https://partner.microsoft.com/cloud-solution-provider)을 자세히 알아봅니다.

CSP로서 고객을 대신해 [Microsoft 파트너 센터](https://partnercenter.microsoft.com/partner/home)를 통해 Microsoft Azure IoT Central 애플리케이션을 만들고 관리할 수 있습니다. CSP가 고객을 대신해 Azure IoT Central 애플리케이션을 만들 경우 다른 CSP 관리 Azure 서비스와 마찬가지로 CSP가 고객에 대한 청구를 관리합니다. Azure IoT Central에 대한 요금은 Microsoft 파트너 센터의 총 청구서에 표시됩니다.

시작하려면 Microsoft 파트너 포털의 계정에 로그인하고 Azure IoT Central 애플리케이션을 만들어 주려는 고객을 선택합니다. 왼쪽 탐색 창에서 고객에 대 한 서비스 관리로 이동 합니다.

![Microsoft 파트너 센터, 고객 보기](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central은 관리할 수 있는 서비스로 나열됩니다. 페이지에서 Azure IoT Central 링크를 선택 하 여 새 응용 프로그램을 만들거나이 고객에 대 한 기존 응용 프로그램을 관리 합니다.

![관리할 수 있는 Azure IoT Central](media/howto-create-and-manage-applications-csp/image2.png)

Azure IoT Central 애플리케이션 관리자 페이지로 이동합니다. Azure IoT Central은 Microsoft 파트너 센터에서 제공하고 해당 특정 고객을 관리하기 위해 제공된 컨텍스트를 유지합니다. 애플리케이션 관리자 페이지의 헤더에 승인이 표시됩니다. 여기에서 이 고객을 위해 전에 만든 기존 애플리케이션으로 이동하거나 고객을 위한 새 애플리케이션을 만들 수 있습니다.

![CSP를 위한 관리자 만들기](media/howto-create-and-manage-applications-csp/image3.png)

Azure IoT Central 응용 프로그램을 만들려면 왼쪽 메뉴에서 **빌드** 를 선택 합니다. 산업 템플릿 중 하나를 선택 하거나 응용 프로그램을 처음부터 만들려면 **사용자 지정 앱** 을 선택 합니다. 그러면 애플리케이션 만들기 페이지가 로드됩니다. 이 페이지의 모든 필드를 완료한 다음, **만들기**를 선택해야 합니다. 아래에 각 필드에 대한 자세한 내용이 있습니다.

![CSP에 대한 애플리케이션 페이지 만들기](media/howto-create-and-manage-applications-csp/image4.png)

![CSP에 대한 애플리케이션 페이지 만들기](media/howto-create-and-manage-applications-csp/image4-1.png)

![Csp 청구 정보에 대 한 응용 프로그램 페이지 만들기](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>요금제

표준 요금제로 표준 가격 책정 요금제를 사용 하는 응용 프로그램을 만들 수 있습니다. 고객에 게 Azure IoT Central를 소개 하기 위해 무료 가격 책정 계획을 별도로 사용 하는 응용 프로그램을 만들 수 있습니다. [Azure IoT Central 가격 책정 페이지](https://azure.microsoft.com/pricing/details/iot-central/)에서 무료 및 표준 가격 책정 플랜에 대해 자세히 알아보세요.

표준 요금제로 표준 가격 책정 요금제를 사용 하는 응용 프로그램을 만들 수 있습니다. 고객에 게 Azure IoT Central를 소개 하기 위해 무료 가격 책정 계획을 별도로 사용 하는 응용 프로그램을 만들 수 있습니다. [Azure IoT Central 가격 책정 페이지](https://azure.microsoft.com/pricing/details/iot-central/)에서 무료 및 표준 가격 책정 플랜에 대해 자세히 알아보세요.

## <a name="application-name"></a>애플리케이션 이름

애플리케이션 이름은 **애플리케이션 관리자** 페이지 그리고 각 Azure IoT Central 애플리케이션 내에서 표시됩니다. Azure IoT Central 애플리케이션의 이름으로 원하는 이름을 선택할 수 있습니다. 본인에게 그리고 조직 내 다른 사람들에게 의미 있는 이름을 선택하면 됩니다.

## <a name="application-url"></a>애플리케이션 URL

애플리케이션 URL은 애플리케이션의 링크입니다. 브라우저에서 URL의 책갈피를 저장하거나 다른 사람들과 공유할 수 있습니다.

애플리케이션의 이름을 입력하면 애플리케이션 URL이 자동으로 생성됩니다. 원한다면 애플리케이션의 다른 URL을 선택해도 됩니다. 각 Azure IoT Central URL은 Azure IoT Central 내에서 고유해야 합니다. 선택한 URL이 이미 사용 중인 경우 오류 메시지가 표시됩니다.

## <a name="directory"></a>디렉터리

Azure IoT Central에 Microsoft 파트너 포털에서 선택한 고객을 관리하기 위해 제공된 컨텍스트가 있으므로 디렉터리 필드에서 해당 고객에 대한 Azure Active Directory 테넌트를 참조합니다. 

Azure Active Directory 테넌트에는 사용자 ID, 자격 증명 및 기타 조직 정보가 포함됩니다. 단일 Azure Active Directory 테넌트에 여러 Azure 구독을 연결할 수 있습니다.

자세한 내용은 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)를 참조하세요.

## <a name="azure-subscription"></a>Azure 구독

Azure 구독을 사용하여 Azure 서비스 인스턴스를 만들 수 있습니다. Azure IoT Central은 액세스 권한이 있는 고객의 모든 Azure 구독을 자동으로 찾아서 **애플리케이션 만들기** 페이지의 드롭다운에 표시합니다. 새 Azure IoT Central 애플리케이션을 만들 Azure 구독을 선택하세요.

Azure 구독이 없는 경우 Microsoft 파트너 센터에서 새로 만들 수 있습니다. Azure 구독을 만든 후 다시 **애플리케이션 만들기** 페이지로 돌아갑니다. **Azure 구독** 드롭다운에 새 구독이 표시됩니다.

자세한 내용은 [Azure 구독](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)을 참조하세요.

## <a name="location"></a>위치

**Location** 은 응용 프로그램을 만들려는 [지리](https://azure.microsoft.com/global-infrastructure/geographies/) 입니다. 일반적으로 최적의 성능을 얻으려면 디바이스와 물리적으로 가장 가까운 위치를 선택해야 합니다. 현재 **오스트레일리아**, **아시아 태평양**, **유럽** **, 미국, 영국**및 **일본** 지역에서 IoT Central 응용 **프로그램을 만들**수 있습니다. 위치를 선택하면 나중에 다른 위치로 애플리케이션을 이동할 수 없습니다.

## <a name="application-template"></a>애플리케이션 템플릿

응용 프로그램에 사용 하려는 응용 프로그램 템플릿을 선택 합니다.

## <a name="next-steps"></a>다음 단계

CSP로서 Azure IoT Central 애플리케이션을 만드는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
