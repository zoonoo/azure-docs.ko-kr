---
title: 온-프레미스 데이터 게이트웨이 설치
description: Azure Logic Apps에서 온-프레미스의 데이터에 액세스하려면 온-프레미스 데이터 게이트웨이를 다운로드하고 설치해야 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 9e50cdb16ee6acbdb903681984dcfbd7bfe170fa
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386132"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps에 온-프레미스 데이터 게이트웨이 설치

[Azure Logic Apps에서 온-프레미스 데이터 원본에 연결](../logic-apps/logic-apps-gateway-connection.md)하려면 먼저 [온-프레미스 데이터 게이트웨이](https://aka.ms/on-premises-data-gateway-installer)를 다운로드하여 로컬 컴퓨터에 설치합니다. 게이트웨이는 온-프레미스 데이터 원본과 논리 앱 간에 빠른 데이터 전송 및 암호화를 제공하는 브리지로 작동합니다. Power BI, Power Automate, Power Apps 및 Azure Analysis Services와 같은 다른 클라우드 서비스에서도 동일한 게이트웨이 설치를 사용할 수 있습니다. 이러한 서비스에서 게이트웨이를 사용하는 방법에 대한 내용은 다음 문서를 참조하세요.

* [Microsoft Power Automate 온-프레미스 데이터 게이트웨이](/power-automate/gateway-reference)
* [Microsoft Power BI 온-프레미스 데이터 게이트웨이](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 온-프레미스 데이터 게이트웨이](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 온-프레미스 데이터 게이트웨이](../analysis-services/analysis-services-gateway.md)

이 문서에서는 Azure Logic Apps에서 온-프레미스 데이터 원본에 액세스할 수 있도록 온-프레미스 데이터 게이트웨이를 다운로드, 설치 및 설정하는 방법을 보여 줍니다. 이 문서의 뒷부분에서 [데이터 게이트웨이의 작동 방식](#gateway-cloud-service)에 대해 자세히 알아볼 수도 있습니다. 게이트웨이에 대한 자세한 내용은 [온-프레미스 게이트웨이란?](/data-integration/gateway/service-gateway-onprem)을 참조하세요. 게이트웨이를 설치하고 관리하는 작업을 자동화하려면 [DataGateway PowerShell cmdlet](https://www.powershellgallery.com/packages/DataGateway/3000.15.15)에 대한 PowerShell 갤러리를 방문하세요.

<a name="requirements"></a>

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 있는 Azure 계정이 없는 경우 [체험 Azure 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

  * Azure 계정은 회사 계정 또는 학교 계정 이어야 하며 다음과 같습니다 `username@contoso.com` . Azure B2B(게스트) 계정 또는 개인 Microsoft 계정(예: @hotmail.com 또는 @outlook.com)은 사용할 수 없습니다.

    > [!NOTE]
    > Office 365 제품에 가입했지만 회사 이메일 주소를 제공하지 않은 경우 주소는 `username@domain.onmicrosoft.com`과 같을 수 있습니다. 계정이 Azure AD 테 넌 트에 저장 됩니다. 대부분의 경우 Azure 계정에 대 한 UPN (사용자 계정 이름)은 전자 메일 주소와 동일 합니다.

    Microsoft 계정와 연결 된 [Visual Studio 표준 구독](https://visualstudio.microsoft.com/vs/pricing/) 을 사용 하려면 먼저 [Azure AD 테 넌 트를 만들거나](../active-directory/develop/quickstart-create-new-tenant.md) 기본 디렉터리를 사용 합니다. 암호가 있는 사용자를 디렉터리에 추가한 다음, 해당 사용자에게 Azure 구독에 대한 액세스 권한을 부여합니다. 그런 다음 이 사용자 이름 및 암호를 사용하여 게이트웨이 설치 중에 로그인할 수 있습니다.

  * Azure 계정은 단일 [Azure Active Directory (AZURE AD) 테 넌 트 또는 디렉터리](../active-directory/fundamentals/active-directory-whatis.md#terminology)에만 속해야 합니다. 로컬 컴퓨터에 게이트웨이를 설치 하 고 관리 하려면 동일한 Azure 계정을 사용 해야 합니다.

  * 게이트웨이를 설치할 때 azure 계정으로 로그인 합니다. 그러면 azure 계정에 게이트웨이 설치를 연결 하 고 해당 계정에만 연결 됩니다. 여러 Azure 계정 또는 Azure AD 테 넌 트에서 동일한 게이트웨이 설치를 연결할 수 없습니다.

  * Azure Portal 나중에 게이트웨이 설치에 연결 되는 Azure 게이트웨이 리소스를 만들기 위해 동일한 Azure 계정을 사용 해야 합니다. 하나의 게이트웨이 설치와 하나의 Azure 게이트웨이 리소스만 서로 연결할 수 있습니다. 그러나 Azure 계정은 각각 Azure 게이트웨이 리소스와 연결 된 여러 게이트웨이 설치에 연결할 수 있습니다. 그러면 논리 앱이 트리거 및 온-프레미스 데이터 원본에 액세스할 수 있는 작업에서이 게이트웨이 리소스를 사용할 수 있습니다.

* 로컬 컴퓨터에 대한 요구 사항은 다음과 같습니다.

  **최소 요구 사항**

  * .NET Framework 4.7.2
  * 64비트 버전의 Windows 7 또는 Windows Server 2008 R2 이상

  **권장 요구 사항**

  * 8코어 CPU
  * 8GB 메모리
  * 64비트 버전의 Windows Server 2012 R2 이상
  * 스풀링용 SSD(반도체 드라이브) 스토리지

  > [!NOTE]
  > Windows Server Core는 게이트웨이에서 지원하지 않습니다.

* **관련 고려 사항**

  * 온-프레미스 데이터 게이트웨이는 도메인 컨트롤러가 아니라 로컬 컴퓨터에만 설치합니다. 데이터 원본과 동일한 컴퓨터에 게이트웨이를 설치할 필요가 없습니다. 모든 데이터 원본에 대해 하나의 게이트웨이만 필요하므로 각 데이터 원본마다 게이트웨이를 설치할 필요가 없습니다.

    > [!TIP]
    > 대기 시간을 최소화하려면 권한이 있다고 가정하여 가능하면 데이터 원본과 가깝게 또는 동일한 컴퓨터에 게이트웨이를 설치하면 됩니다.

  * 유선 네트워크에 있고, 인터넷에 연결되어 있으며, 항상 켜져 있고, 절전 모드로 전환되지 않는 로컬 컴퓨터에 게이트웨이를 설치합니다. 그렇지 않으면 게이트웨이가 실행될 수 없으며 무선 네트워크를 통한 성능이 저하될 수 있습니다.

  * Windows 인증을 사용하려면 데이터 원본과 동일한 Active Directory 환경의 멤버인 컴퓨터에 게이트웨이를 설치해야 합니다.

  * 게이트웨이 설치를 위해 선택한 지역은 나중에 논리 앱에 대한 Azure 게이트웨이 리소스를 만들 때 선택해야 하는 위치와 동일합니다. 기본적으로 이 지역은 Azure 계정을 관리하는 Azure AD 테넌트와 동일한 위치입니다. 그러나 위치는 게이트웨이 설치 중에 변경할 수 있습니다.

  * 게이트웨이 설치를 업데이트하는 경우 더 깨끗한 환경을 위해 먼저 현재 게이트웨이를 제거합니다.

    지원되는 버전을 사용하는 것이 가장 좋습니다. Microsoft는 매월 온-프레미스 데이터 게이트웨이에 대한 새 업데이트를 릴리스하며, 현재 온-프레미스 데이터 게이트웨이에 대한 마지막 6개 릴리스만 지원합니다. 사용 중인 버전에 문제가 발생하는 경우 최신 버전에서 문제가 해결되었을 수 있으므로 [최신 버전으로 업그레이드](https://aka.ms/on-premises-data-gateway-installer)해 보세요.

  * 게이트웨이에는 Power BI에만 적용되는 표준 모드와 개인 모드의 두 가지 모드가 있습니다. 동일한 컴퓨터에서 동일한 모드로 실행되는 게이트웨이가 둘 이상 있을 수 없습니다.

  * Azure Logic Apps는 게이트웨이를 통한 읽기 및 쓰기 작업을 지원합니다. 그러나 이러한 작업에는 [페이로드 크기 제한](/data-integration/gateway/service-gateway-onprem#considerations)이 있습니다.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>데이터 게이트웨이 설치

1. [로컬 컴퓨터에서 게이트웨이 설치 관리자를 다운로드하고 실행합니다](https://aka.ms/on-premises-data-gateway-installer).

1. 최소 요구 사항을 검토하고, 기본 설치 경로를 유지하고, 사용 약관에 동의한 다음, **설치**를 선택합니다.

   ![요구 사항 검토 및 사용 약관 동의](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. 게이트웨이가 성공적으로 설치되면 Azure 계정에 대한 이메일 주소를 제공한 다음, **로그인**을 선택합니다. 예를 들어 다음과 같습니다.

   ![회사 또는 학교 계정으로 로그인](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   게이트웨이 설치는 하나의 Azure 계정에만 연결할 수 있습니다.

1. **이 컴퓨터에 새 게이트웨이를 등록합니다.**  > **다음**을 차례로 선택합니다. 이 단계에서는 게이트웨이 설치를 [게이트웨이 클라우드 서비스](#gateway-cloud-service)에 등록합니다.

   ![로컬 컴퓨터에서 게이트웨이 등록](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. 게이트웨이 설치에 대해 다음 정보를 제공합니다.

   * Azure AD 테넌트 전체에서 고유한 게이트웨이 이름
   * 사용하려는 복구 키(8자 이상이어야 함)
   * 복구 키 확인

   ![게이트웨이 설치에 대한 정보 제공](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > 복구 키를 저장한 후 안전한 장소에 보관합니다. 이 키는 게이트웨이 설치의 위치 변경, 이동, 복구 또는 인수를 수행하려는 경우에도 필요합니다.

   [고가용성 시나리오](#high-availability)를 위한 추가 게이트웨이를 설치하는 경우 선택하는 **기존 게이트웨이 클러스터에 추가** 옵션에 유의하세요.

1. 게이트웨이 설치에서 사용되는 게이트웨이 클라우드 서비스 및 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)에 대한 지역을 확인합니다. 이 지역은 기본적으로 Azure 계정에 대한 Azure AD 테넌트와 동일한 위치입니다.

   ![게이트웨이 서비스 및 Service Bus에 대한 지역 확인](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. 기본 지역을 그대로 적용하려면 **구성**을 선택합니다. 그러나 기본 지역이 가장 가까운 지역이 아니면 해당 지역을 변경할 수 있습니다.

   *게이트웨이 설치에 대한 지역을 변경하는 이유는 무엇인가요?*

   예를 들어, 대기 시간을 줄이기 위해 논리 앱과 동일한 지역으로 게이트웨이 지역을 변경할 수 있습니다. 또는 온-프레미스 데이터 원본에 가장 가까운 지역을 선택할 수 있습니다. *Azure의 게이트웨이 리소스*와 논리 앱은 서로 다른 위치에 있을 수 있습니다.

   1. 현재 지역 옆에 있는 **지역 변경**을 선택합니다.

      ![현재 게이트웨이 지역 변경](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. 다음 페이지에서 **지역 선택** 목록을 열고, 원하는 지역을 선택한 다음, **완료**를 선택합니다.

      ![게이트웨이 서비스에 대한 다른 지역 선택](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. 최종 확인 창에서 정보를 검토합니다. 이 예에서는 동일한 계정을 Logic Apps, Power BI, Power Apps 및 Power Automation에 사용하므로 게이트웨이를 이러한 모든 서비스에 사용할 수 있습니다. 준비가 되면 **닫기**를 선택합니다.

   ![데이터 게이트웨이 정보 확인](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. 이제 [게이트웨이 설치에 대한 Azure 리소스를 만듭니다](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>통신 설정 확인 또는 조정

온-프레미스 데이터 게이트웨이는 클라우드 연결을 위해 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)를 사용하며, 게이트웨이의 연결된 Azure 지역에 해당하는 아웃바운드 연결을 설정합니다. 작업 환경에서 트래픽이 프록시 또는 방화벽을 통과하여 인터넷에 액세스해야 하는 경우 이 제한으로 인해 온-프레미스 데이터 게이트웨이에서 게이트웨이 클라우드 서비스 및 Azure Service Bus에 연결하지 못할 수 있습니다. 게이트웨이에는 조정할 수 있는 몇 가지 통신 설정이 있습니다. 자세한 내용은 다음 항목을 참조하세요.

* [온-프레미스 데이터 게이트웨이에 대한 통신 설정 조정](/data-integration/gateway/service-gateway-communication)
* [온-프레미스 데이터 게이트웨이에 대한 프록시 설정 구성](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>고가용성 지원

온-프레미스 데이터 액세스의 단일 실패 지점을 방지하기 위해 여러 게이트웨이 설치(표준 모드만 해당)를 서로 다른 컴퓨터에 각각 설치하고 이를 클러스터 또는 그룹으로 설정할 수 있습니다. 이렇게 하면 기본 게이트웨이를 사용할 수 없는 경우 데이터 요청이 두 번째 게이트웨이로 라우팅되는 등등으로 수행됩니다. 컴퓨터에는 하나의 표준 게이트웨이만 설치할 수 있으므로 클러스터에 있는 각 추가 게이트웨이를 다른 컴퓨터에 설치해야 합니다. 온-프레미스 데이터 게이트웨이에서 작동하는 모든 커넥터는 고가용성을 지원합니다.

* 해당 설치에 대한 기본 게이트웨이 및 복구 키와 동일한 Azure 계정을 사용하는 하나 이상의 게이트웨이 설치가 이미 있어야 합니다.

* 기본 게이트웨이에서는 2017년 11월 또는 이후의 게이트웨이 업데이트가 실행되고 있어야 합니다.

기본 게이트웨이가 설정되면 다른 게이트웨이를 설치할 때 **기존 게이트웨이 클러스터에 추가**를 선택하고, 설치한 첫 번째 게이트웨이인 기본 게이트웨이를 선택하고, 해당 게이트웨이에 대한 복구 키를 제공합니다. 자세한 내용은 참조 [온-프레미스 데이터 게이트웨이에 대한 고가용성 클러스터](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)를 참조하세요.

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>위치 변경, 기존 게이트웨이 마이그레이션, 복원 또는 인수

게이트웨이 위치를 변경하거나, 게이트웨이 설치를 새 컴퓨터로 이동하거나, 손상된 게이트웨이를 복구하거나, 기존 게이트웨이에 대한 소유권을 가져와야 할 경우 게이트웨이 설치 동안 제공된 복구 키가 필요합니다.

> [!NOTE]
> 원래 게이트웨이 설치가 있는 컴퓨터에서 게이트웨이를 복원하려면 먼저 해당 컴퓨터에서 게이트웨이를 제거해야 합니다. 이 작업을 수행하면 원래 게이트웨이와의 연결이 끊어집니다.
> 클라우드 서비스에 대한 게이트웨이 클러스터를 제거하거나 삭제하면 해당 클러스터를 복원할 수 없습니다.

1. 기존 게이트웨이가 있는 컴퓨터에서 게이트웨이 설치 관리자를 실행합니다.

1. 설치 관리자가 열리면 게이트웨이를 설치하는 데 사용한 것과 동일한 Azure 계정으로 로그인합니다.

1. **기존 게이트웨이 마이그레이션, 복원 또는 인수** > **다음**을 차례로 선택합니다. 예를 들어 다음과 같습니다.

   ![Migrate, restore, or takeover an existing gateway(기존 게이트웨이 마이그레이션, 복원 또는 인수) 선택](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. 사용 가능한 클러스터와 게이트웨이에서 선택하고, 선택한 게이트웨이에 대한 복구 키를 입력합니다. 예를 들어 다음과 같습니다.

   ![게이트웨이 선택 및 복구 키 제공](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. 지역을 변경하려면 **지역 변경**, 새 지역을 차례로 선택합니다.

1. 준비가 되면 **구성**을 선택하여 작업을 완료합니다.

## <a name="tenant-level-administration"></a>테넌트 수준 관리

Azure AD 테넌트의 모든 온-프레미스 데이터 게이트웨이에 대한 가시성을 확보하기 위해 해당 테넌트의 전역 관리자는 테넌트 관리자 권한으로 [Power Platform 관리 센터](https://powerplatform.microsoft.com)에 로그인하고 **데이터 게이트웨이** 옵션을 선택할 수 있습니다. 자세한 내용은 [온-프레미스 데이터 게이트웨이에 대한 테넌트 수준 관리](/data-integration/gateway/service-gateway-tenant-level-admin)를 참조하세요.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>게이트웨이 다시 시작

로컬 컴퓨터의 게이트웨이 설치는 기본적으로 "온-프레미스 데이터 게이트웨이 서비스"라는 Windows 서비스 계정으로 실행됩니다. 그러나 게이트웨이 설치에는 "다른 사용자로 로그온" 계정 자격 증명에 `NT SERVICE\PBIEgwService` 이름이 사용되고 "서비스로 로그온" 권한이 있습니다.

> [!NOTE]
> Windows 서비스 계정은 온-프레미스 데이터 원본에 연결하는 데 사용되는 계정 및 클라우드 서비스에 로그인할 때 사용하는 Azure 계정과 다릅니다.

다른 Windows 서비스와 마찬가지로 게이트웨이를 다양한 방법으로 시작하고 중지할 수 있습니다. 자세한 내용은 [온-프레미스 데이터 게이트웨이 다시 시작](/data-integration/gateway/service-gateway-restart)을 참조하세요.

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>게이트웨이 작동 원리

조직의 사용자는 이미 액세스 권한이 부여된 온-프레미스 데이터에 액세스할 수 있습니다. 그러나 이러한 사용자가 온-프레미스 데이터 원본에 연결하려면 먼저 온-프레미스 데이터 게이트웨이를 설치하고 설정해야 합니다. 일반적으로 관리자는 게이트웨이를 설치하고 설정하는 사용자입니다. 이러한 작업을 수행하려면 서버 관리자 권한 또는 온-프레미스 서버에 대한 특별한 지식이 필요할 수 있습니다.

게이트웨이를 사용하면 백그라운드에서 더 빠르고 안전하게 통신할 수 있습니다. 이 통신은 클라우드의 사용자, 게이트웨이 클라우드 서비스 및 온-프레미스 데이터 원본 간에 전달됩니다. 게이트웨이 클라우드 서비스는 데이터 원본 자격 증명 및 게이트웨이 세부 정보를 암호화하고 저장합니다. 또한 이 서비스는 쿼리와 결과를 사용자, 게이트웨이 및 온-프레미스 데이터 원본 간에 라우팅합니다.

게이트웨이는 방화벽과 함께 작동하며 아웃바운드 연결만 사용합니다. 모든 트래픽은 게이트웨이 에이전트에서 보호되는 아웃바운드 트래픽으로 시작됩니다. 게이트웨이는 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)를 통해 암호화된 채널의 온-프레미스 원본에서 데이터를 릴레이합니다. 이 Service Bus는 게이트웨이와 호출 서비스 간에 채널을 만들지만 데이터를 저장하지는 않습니다. 게이트웨이를 통해 전송되는 모든 데이터는 암호화됩니다.

![온-프레미스 데이터 게이트웨이에 대한 아키텍처](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> 클라우드 서비스에 따라 게이트웨이에 대한 데이터 원본을 설정해야 할 수도 있습니다.

다음 단계에서는 온-프레미스 데이터 원본에 연결된 요소와 상호 작용할 때 발생하는 상황에 대해 설명합니다.

1. 클라우드 서비스는 데이터 원본에 대한 암호화된 자격 증명과 함께 쿼리를 만듭니다. 그런 다음, 서비스에서 처리를 위해 쿼리와 자격 증명을 게이트웨이 큐에 보냅니다.

1. 게이트웨이 클라우드 서비스에서 쿼리를 분석하고 요청을 Azure Service Bus로 푸시합니다.

1. Azure Service Bus는 보류 중인 요청을 게이트웨이에 보냅니다.

1. 게이트웨이에서 쿼리를 가져오고, 자격 증명의 암호를 해독하고, 해당 자격 증명을 사용하여 하나 이상의 데이터 원본에 연결합니다.

1. 게이트웨이에서 실행을 위해 쿼리를 데이터 원본에 보냅니다.

1. 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후 게이트웨이 클라우드 서비스로 전송됩니다. 게이트웨이 클라우드 서비스에서 해당 결과를 사용합니다.

### <a name="authentication-to-on-premises-data-sources"></a>온-프레미스 데이터 원본에 대한 인증

저장된 자격 증명은 게이트웨이에서 온-프레미스 데이터 원본으로 연결하는 데 사용됩니다. 게이트웨이는 사용자에 관계없이 저장된 자격 증명을 사용하여 연결합니다. Power BI의 Analysis Services에 대한 DirectQuery 및 LiveConnect와 같은 특정 서비스에 대한 인증 예외가 있을 수 있습니다.

### <a name="azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory)

Microsoft 클라우드 서비스는 [Azure AD](../active-directory/fundamentals/active-directory-whatis.md)를 사용하여 사용자를 인증합니다. Azure AD 테넌트에는 사용자 이름과 보안 그룹이 포함됩니다. 일반적으로 로그인하는 데 사용하는 이메일 주소는 계정에 대한 UPN(사용자 계정 이름)과 동일합니다.

### <a name="what-is-my-upn"></a>UPN이란?

도메인 관리자가 아닌 경우 UPN을 모를 수 있습니다. 계정에 대한 UPN을 찾으려면 워크스테이션에서 `whoami /upn` 명령을 실행합니다. 결과는 이메일 주소와 비슷하지만 로컬 도메인 계정에 대한 UPN입니다.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Azure AD와 온-프레미스 Active Directory 동기화

온-프레미스 Active Directory 계정 및 Azure AD 계정에 대한 UPN은 동일해야 합니다. 따라서 각 온-프레미스 Active Directory 계정이 Azure AD 계정과 일치하는지 확인합니다. 클라우드 서비스는 Azure AD 내의 계정에 대해서만 인식하고 있습니다. 따라서 계정을 온-프레미스 Active Directory에 추가할 필요가 없습니다. 계정이 Azure AD에 없으면 해당 계정을 사용할 수 없습니다.

온-프레미스 Active Directory 계정을 Azure AD와 일치시킬 수 있는 방법은 다음과 같습니다.

* 계정을 Azure AD에 수동으로 추가합니다.

  Azure Portal 또는 Microsoft 365 관리 센터에서 계정을 만듭니다. 계정 이름이 온-프레미스 Active Directory 계정의 UPN과 일치하는지 확인합니다.

* Azure Active Directory Connect 도구를 사용하여 로컬 계정을 Azure AD 테넌트에 동기화합니다.

  Azure AD Connect 도구는 디렉터리 동기화 및 인증 설정 옵션을 제공합니다. 이러한 옵션에는 암호 해시 동기화, 통과 인증 및 페더레이션이 포함됩니다. 테넌트 관리자 또는 로컬 도메인 관리자가 아닌 경우 IT 관리자에게 문의하여 Azure AD Connect를 설정합니다. Azure AD Connect는 Azure AD UPN이 로컬 Active Directory UPN과 일치하도록 합니다. 이 일치는 Power BI 또는 SSO(Single Sign-On) 기능을 사용하여 Analysis Services 라이브 연결하는 경우에 유용합니다.

  > [!NOTE]
  > 계정을 Azure AD Connect 도구와 동기화하면 새 계정이 Azure AD 테넌트에 만들어집니다.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>FAQ 및 문제 해결

* [온-프레미스 데이터 게이트웨이 FAQ](/data-integration/gateway/service-gateway-onprem-faq)
* [온-프레미스 데이터 게이트웨이 문제 해결](/data-integration/gateway/service-gateway-tshoot)
* [게이트웨이 성능 모니터링 및 최적화](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>다음 단계

* [논리 앱에서 온-프레미스 데이터에 연결](../logic-apps/logic-apps-gateway-connection.md)
* [엔터프라이즈 통합 기능](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps용 커넥터](../connectors/apis-list.md)
