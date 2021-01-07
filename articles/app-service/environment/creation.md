---
title: App Service Environment 만들기
description: App Service Environment를 만드는 방법에 대해 알아봅니다.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 12c9fd43933f9f54e013752c5fe482ce31650536
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95238397"
---
# <a name="create-an-app-service-environment"></a>App Service Environment 만들기

> [!NOTE]
> 이 문서는 App Service Environment v3 (미리 보기)에 대 한 것입니다.
> 

[ASE (App Service Environment)][Intro] 는 Azure Virtual Network (VNet)에 삽입 하는 App Service의 단일 테 넌 트 배포입니다.  ASEv3는 Vnet의 개인 주소에 앱 노출을 지원 합니다. 미리 보기 중에 ASEv3가 생성 되 면 세 개의 리소스가 구독에 추가 됩니다.

- App Service 환경
- Azure DNS 개인 영역
- 프라이빗 엔드포인트

ASE를 배포 하려면 두 개의 서브넷을 사용 해야 합니다.  하나의 서브넷은 개인 끝점을 보유 합니다.  이 서브넷은 Vm과 같은 다른 작업에 사용할 수 있습니다.  다른 서브넷은 ASE에서 수행 되는 아웃 바운드 호출에 사용 됩니다.  ASE 이외의 항목에는이 서브넷을 사용할 수 없습니다. 

## <a name="before-you-create-your-ase"></a>ASE를 만들기 전에

ASE를 만든 후에는 변경할 수 없습니다.

- 위치
- 구독
- Resource group
- 사용 되는 Azure Virtual Network (VNet)
- 사용 되는 서브넷
- 서브넷 크기
- ASE의 이름입니다.

아웃 바운드 서브넷은 ASE 크기를 조정 하는 최대 크기를 수용할 만큼 충분히 커야 합니다. 만든 후에는 변경할 수 없으므로 최대 규모 요구를 지원할 수 있는 충분 한 서브넷을 선택 합니다. 권장 크기는/24 (256 주소 포함)입니다.

ASE를 만든 후에 앱을 추가할 수 있습니다. ASEv3에 App Service 계획이 없으면 ASE에 I1v2 App Service 계획의 인스턴스가 하나 있는 것 처럼 요금이 청구 됩니다.  

ASEv3는 select 지역 에서만 제공 됩니다. 미리 보기가 GA를 따라 이동 하면 더 많은 지역이 추가 됩니다. 

## <a name="creating-an-ase-in-the-portal"></a>포털에서 ASE 만들기

1. ASEv3을 만들려면 marketplace에서 **App Service Environment (미리 보기)** 를 검색 합니다.  
2. 기본 사항: 구독을 선택 하 고, 리소스 그룹을 선택 하거나 만들고, ASE의 이름을 입력 합니다.  Ase 이름은 ASE의 도메인 접미사에도 사용 됩니다.  ASE 이름이 *contoso* 인 경우 도메인 접미사가 *contoso.appserviceenvironment.net* 됩니다.  이 이름은 ASE가 배포 된 Vnet에서 사용 하는 Azure DNS 개인 영역에서 자동으로 설정 됩니다. 

    ![기본 App Service Environment 만들기 탭](./media/creation/creation-basics.png)

3. 호스팅: OS 기본 설정, 호스트 그룹 배포를 선택 합니다. OS 기본 설정은이 ASE에서 앱에 처음 사용할 운영 체제를 나타냅니다. ASE를 만든 후 다른 OS의 앱을 추가할 수 있습니다. 호스트 그룹 배포는 전용 하드웨어를 선택 하는 데 사용 됩니다. ASEv3를 사용 하면 사용을 선택 하 고 전용 하드웨어를 사용할 수 있습니다. ASE를 만든 후에 App Service 계획 인스턴스에 대 한 가격을 줄인 전체 전용 호스트에 대해 요금이 청구 됩니다. 

    ![App Service Environment 호스팅 선택](./media/creation/creation-hosting.png)

4. 네트워킹: Virtual Network을 선택 하거나 만들고, 인바운드 서브넷을 선택 하거나 만들고, 아웃 바운드 서브넷을 선택 하거나 만듭니다. 아웃 바운드에 사용 되는 모든 서브넷은 비어 있고 Microsoft. Web/hostingEnvironments에 위임 되어야 합니다. 포털을 통해 서브넷을 만드는 경우 서브넷이 자동으로 위임 됩니다.

    ![네트워킹 선택 App Service Environment](./media/creation/creation-networking.png)

5. 검토 및 만들기: 구성이 올바른지 확인 하 고 만들기를 선택 합니다. ASE를 만드는 데 약 1 시간이 소요 됩니다. 

    ![App Service Environment 검토 및 만들기](./media/creation/creation-review.png)

ASE 생성이 완료 되 면 앱을 만들 때 위치로 선택할 수 있습니다. 새 ASE에서 앱을 만드는 방법에 대 한 자세한 내용은 [App Service Environment 사용][UsingASE] 을 참조 하세요.

## <a name="os-preference"></a>OS 기본 설정
ASE에서 Windows 앱, Linux 앱 또는 둘 다를 사용할 수 있습니다. Asev2)에서 생성 중에 선택 된 초기 기본 설정은 ASE를 만드는 동안 해당 OS에 대 한 고가용성 인프라를 추가 합니다. 다른 OS의 앱을 추가 하려면 앱을 평소와 같이 만들고 원하는 OS를 선택 하면 됩니다. ASEv3에서는 백엔드 동작 appreciatively에 영향을 주지 않습니다.  

## <a name="dedicated-hosts"></a>전용 호스트
ASE는 일반적으로 다중 테 넌 트 하이퍼바이저에서 프로 비전 되는 Vm에 배포 됩니다. 하드웨어를 포함 하 여 전용 시스템에 배포 해야 하는 경우 ASE를 전용 호스트에 프로 비전 할 수 있습니다. 초기 ASEv3 미리 보기에서 전용 호스트는 쌍으로 제공 됩니다. 각 전용 호스트는 지역에서 허용 하는 경우 별도의 가용성 영역에 있습니다. 전용 호스트 기반 ASE 배포는 일반적인 경우와 다르게 가격이 책정 됩니다. 전용 호스트에 대 한 요금이 청구 되 고 각 App Service 계획 인스턴스에 대 한 요금이 청구 됩니다.  

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
[logalerts]: ../../azure-monitor/platform/alerts-log.md
