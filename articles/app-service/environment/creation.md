---
title: App Service Environment 만들기
description: App Service Environment를 만드는 방법을 알아봅니다.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 021c85037ea105ff7d8ff642e3a9f28ed3ebe991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594112"
---
# <a name="create-an-app-service-environment"></a>App Service Environment 만들기

> [!NOTE]
> 이 문서는 App Service Environment v3에 관한 내용입니다(미리 보기)
> 

[ASE(App Service Environment)][Intro]는 Azure VNet(Virtual Network)에서 실행되는 App Service의 단일 테넌트 배포입니다.  ASEv3는 Vnet의 프라이빗 주소에 앱을 노출시키는 기능만 지원합니다. 미리 보기 중에 ASEv3가생성되면 이러한 리소스는 구독에 추가됩니다.

- App Service 환경
- 프라이빗 엔드포인트

ASE를 배포하려면 두 개의 서브넷을 사용해야 합니다.  하나의 서브넷은 프라이빗 엔드포인트를 보유합니다.  이 서브넷은 VM과 같은 다른 작업에 사용할 수 있습니다.  다른 서브넷은 ASE에서 수행되는 아웃바운드 호출에 사용됩니다.  ASE 이외의 항목에는 서브넷을 사용할 수 없습니다. 

## <a name="before-you-create-your-ase"></a>ASE를 만들기 전에

ASE를 만든 후에는 다음 사항을 변경할 수 없습니다.

- 위치
- 구독
- Resource group
- 사용되는 Azure VNet(Virtual Network)
- 사용되는 서브넷
- 서브넷 크기
- ASE의 이름

아웃바운드 서브넷은 ASE 크기를 스케일링하는 최대 크기를 수용할 만큼 충분히 커야 합니다. 만든 후에는 변경할 수 없으므로 최대 규모 요구량을 지원할 수 있는 충분한 서브넷을 선택합니다. 256개 주소를 사용하는 /24가 권장되는 크기입니다.

ASE를 만든 후에 앱을 추가할 수 있습니다. ASEv3에 App Service 요금제가 없으면 ASE에 I1v2 App Service 요금제의 인스턴스가 하나 있는 것처럼 요금이 청구됩니다.  

ASEv3는 선택 지역에서만 제공됩니다. 미리 보기가 GA대로 진행되면 더 많은 지역이 추가될 예정입니다. 

## <a name="creating-an-ase-in-the-portal"></a>포털에 ASE 생성

1. ASEv3을 만들려면 마켓플레이스에서 **App Service Environment(미리 보기)** 를 검색하세요.  
2. 기본 사항: 구독을 선택하고, 리소스 그룹을 선택하거나 만들고, ASE의 이름을 입력합니다.  ASE 이름은 ASE의 도메인 접미사에도 사용됩니다.  ASE 이름이 *contoso* 인 경우 도메인 접미사가 *contoso.appserviceenvironment.net* 이 됩니다.  이 이름은 ASE가 배포된 Vnet에서 사용하는 Azure DNS 프라이빗 영역에서 자동으로 설정됩니다. 

    ![App Service Environment 만들기 기본 사항 탭](./media/creation/creation-basics.png)

3. 호스팅: OS 기본 설정, 호스트 그룹 배포를 선택합니다. OS 기본 설정은 이 ASE에서 앱에 처음 사용할 운영 체제를 나타냅니다. ASE를 만든 후 다른 OS의 앱을 추가할 수 있습니다. 호스트 그룹 배포는 전용 하드웨어를 선택하는 데 사용됩니다. ASEv3에서 사용을 선택하여 전용 하드웨어를 활용할 수 있습니다. ASE를 만든 후에는 전체 전용 호스트에 대해 요금이 청구되며 App Service 요금제 인스턴스 가격이 할인됩니다. 

    ![App Service Environment 호스팅 선택](./media/creation/creation-hosting.png)

4. 네트워킹: Virtual Network, 인바운드 서브넷, 아웃바운드 서브넷을 선택하거나 만듭니다. 아웃바운드에 사용되는 모든 서브넷은 비어 있어야 하며, Microsoft.Web/hostingEnvironments에 위임되어 있어야 합니다. 포털을 통해 서브넷을 만드는 경우 서브넷이 자동으로 위임됩니다.

    ![App Service Environment 네트워킹 선택 영역](./media/creation/creation-networking.png)

5. 검토 및 만들기: 구성이 올바른지 확인하고 만들기를 선택합니다. ASE를 만드는 데 약 1시간이 소요됩니다. 

    ![App Service Environment 검토 및 만들기](./media/creation/creation-review.png)

ASE 만들기가 완료되면 앱을 만들 때 위치로 선택할 수 있습니다. 새 ASE에서 앱을 만드는 방법에 대한 자세한 내용은 [App Service Environment 사용][UsingASE]을 참조하세요

## <a name="os-preference"></a>OS 기본 설정
ASE에서 Windows 앱, Linux 앱 또는 둘 다를 사용할 수 있습니다. ASEv2에서 만드는 중에 선택된 초기 기본 설정은 ASE를 만드는 동안 해당 OS에 대한 고가용성 인프라를 추가합니다. 다른 OS의 앱을 추가하려면 앱을 평소와 같이 만들고 원하는 OS를 선택하면 됩니다. ASEv3에서 이러한 작업은 백 엔드 동작에 크게 영향을 주지 않습니다.  

## <a name="dedicated-hosts"></a>전용 호스트
ASE는 일반적으로 다중 테넌트 하이퍼바이저에서 프로비저닝되는 VM에 배포됩니다. 하드웨어를 포함하여 전용 시스템에 배포해야 하는 경우 ASE를 전용 호스트에 프로비저닝할 수 있습니다. 초기 ASEv3 미리 보기에서 전용 호스트는 함께 제공됩니다. 각 전용 호스트는 그 지역에서 허용하는 경우 별도의 가용성 영역에 있습니다. 전용 호스트 기반 ASE 배포는 일반적인 경우와 다르게 가격이 책정됩니다. 전용 호스트에 대한 요금이 청구되고 각 App Service 요금제 인스턴스에 대한 추가의 요금이 청구됩니다.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
