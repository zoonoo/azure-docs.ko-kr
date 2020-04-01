---
title: Windows 관리 센터를 Azure 보안 센터와 통합하는 방법 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 보안 센터를 Windows 관리 센터와 통합하는 방법에 대해 설명합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cbbdf724b9d7fe4948553e7526410b994f491b49
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435252"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Azure 보안 센터를 Windows 관리 센터와 통합

Windows 관리 센터는 Windows 서버의 관리 도구입니다. 시스템 관리자가 가장 일반적으로 사용되는 대부분의 관리 도구에 액세스할 수 있는 단일 위치입니다. Windows 관리 센터 내에서 온프레미 서버를 Azure 보안 센터에 직접 온보온할 수 있습니다. 그런 다음 Windows 관리 센터 환경에서 직접 보안 권장 사항 및 경고요약을 볼 수 있습니다.

> [!NOTE]
> Windows 관리 센터 통합을 사용하려면 Azure 구독 및 연결된 로그 분석 작업 영역 모두 보안 센터의 표준 계층을 사용하도록 설정해야 합니다.
> 구독 및 작업 영역에서 이전에 사용하지 않은 경우 표준 계층은 처음 30일 동안 무료입니다. 자세한 내용은 [가격 정보 페이지를](security-center-pricing.md)참조하십시오.
>

Windows 관리 센터에서 Azure 보안 센터로 서버를 성공적으로 온보온한 경우 다음을 수행할 수 있습니다.

* Windows 관리 센터의 보안 센터 확장 내 보안 경고 및 권장 사항 보기
* 보안 상태를 보고 Azure 포털 내(또는 API를 통해) 보안 센터에서 Windows 관리 센터 관리 서버의 추가 세부 정보를 검색합니다.

이 두 도구를 결합하여 보안 센터는 모든 보안 정보를 볼 수 있는 단일 창이 됩니다.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Windows 관리 센터를 보안 센터로 온보딩

1. Windows 관리 센터에서 서버 중 하나를 선택하고 **도구** 창에서 Azure 보안 센터 확장을 선택합니다.

    ![Windows 관리 센터의 Azure 보안 센터 확장](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 서버가 보안 센터에 이미 온보억되어 있으면 설정 창이 나타나지 않습니다.

1. **Azure에 로그인하고 설정합니다.**
    ![Azure 보안 센터에 대한 Windows 관리 센터 확장 온보딩](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 지침에 따라 서버를 보안 센터에 연결합니다. 필요한 세부 정보를 입력하고 확인한 후 보안 센터에서 다음 사항을 모두 적용할 수 있도록 필요한 구성을 변경합니다.
    * Azure 게이트웨이가 등록됩니다.
    * 서버에는 보고할 작업 영역과 연결된 구독이 있습니다.
    * 보안 센터의 표준 계층 로그 분석 솔루션은 작업 영역에서 활성화됩니다. 이 솔루션은 이 작업 영역에 보고하는 *모든* 서버 및 가상 시스템에 대한 보안 센터의 표준 계층 기능을 제공합니다.
    * 보안 센터의 가상 컴퓨터에 대한 표준 계층 가격 책정은 구독에서 활성화됩니다.
    * Log 분석 에이전트가 서버에 설치되고 선택한 작업 영역에 보고하도록 구성됩니다. 서버가 이미 다른 작업 영역에 보고하는 경우 새로 선택한 작업 영역에도 보고하도록 구성됩니다.

    > [!NOTE]
    > 권장 사항이 표시되려면 온보딩 후 시간이 걸릴 수 있습니다. 실제로 서버 활동에 따라 *경고를* 받지 못할 수 있습니다. 경고가 올바르게 작동하는지 테스트하는 테스트 경고를 생성하려면 [경고 유효성 검사 절차의](security-center-alert-validation.md)지침을 따르십시오.


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Windows 관리 센터에서 보안 권장 사항 및 경고 보기

온보던을 통해 Windows 관리 센터의 Azure 보안 센터 영역에서 직접 경고 및 권장 사항을 볼 수 있습니다. 권장 사항 또는 경고를 클릭하여 Azure 포털에서 볼 수 있습니다. 거기에서 추가 정보를 얻고 문제를 해결하는 방법을 알아봅니다.

[![Windows 관리 센터에서 볼 수 있는 보안 센터 권장 사항 및 경고](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>보안 센터에서 Windows 관리 센터 관리 서버에 대한 보안 권장 사항 및 경고 보기
Azure 보안 센터에서 오시면 됩니다.

* 모든 Windows 관리 센터 서버에 대한 보안 권장 사항을 보려면 **컴퓨팅 & 앱을** 열고 **VM 및 컴퓨터** 탭을 클릭합니다.

    [![Windows 관리 센터 관리 서버에 대한 보안 권장 사항 보기](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* 모든 Windows 관리 센터 서버에 대한 보안 경고를 보려면 **보안 경고를 엽니다.** **필터를** 클릭하고 "비Azure"만 선택되었는지 확인합니다. **only**

    ![Windows 관리 센터 관리 서버에 대한 보안 경고 필터링](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Windows 관리 센터 관리 서버에 대한 보안 경고 보기](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)