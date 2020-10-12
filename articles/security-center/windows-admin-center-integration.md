---
title: Azure Security Center를 사용 하 여 Windows 관리 센터 서버를 보호 하는 방법
description: 이 문서에서는 Windows 관리 센터와 Azure Security Center를 통합 하는 방법을 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: 36f519ce41ccfbfb48ca696ed2a61c6131a75998
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906344"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Security Center를 사용 하 여 Windows 관리 센터 리소스 보호

Windows 관리 센터는 Windows server에 대 한 관리 도구입니다. 시스템 관리자가 가장 일반적으로 사용 되는 대부분의 관리 도구에 액세스할 수 있는 단일 위치입니다. Windows 관리 센터 내에서 온-프레미스 서버를 Azure Security Center에 직접 등록할 수 있습니다. 그러면 Windows 관리 센터 환경에서 직접 보안 권장 사항 및 경고에 대 한 요약을 볼 수 있습니다.

> [!NOTE]
> Azure 구독과 연결 된 Log Analytics 작업 영역에는 Windows 관리 센터 통합을 사용 하도록 설정 하기 위해 Azure Defender를 사용 하도록 설정 해야 합니다.
> Azure Defender는 구독 및 작업 영역에서 이전에 사용 하지 않은 경우 처음 30 일 동안 무료입니다. 자세한 내용은 [가격 책정 정보 페이지](security-center-pricing.md)를 참조 하세요.
>

Windows 관리 센터에서 Azure Security Center 서버를 성공적으로 등록 하는 경우 다음을 수행할 수 있습니다.

* Windows 관리 센터의 Security Center 확장 내에서 보안 경고 및 권장 사항 보기
* 보안 상태를 확인 하 고 Azure Portal 내 Security Center에서 또는 API를 통해 Windows 관리 센터 관리 서버에 대 한 추가 세부 정보를 검색 합니다.

이러한 두 도구를 결합 하 여 Security Center 모든 보안 정보를 볼 수 있는 단일 창이 유리 합니다. Windows 관리 센터에서 관리 되는 온-프레미스 서버, Vm 및 추가 PaaS 워크 로드를 보호 합니다.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Security Center로 Windows 관리 센터 관리 서버를 등록 합니다.

1. Windows 관리 센터에서 서버 중 하나를 선택 하 고 **도구** 창에서 Azure Security Center 확장을 선택 합니다.

    ![Windows 관리 센터의 Azure Security Center 확장](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 서버가 이미 Security Center 등록 되 면 설정 창이 표시 되지 않습니다.

1. **Azure에 로그인 및 설정**을 클릭 합니다.
    ![Azure Security Center에 대 한 Windows 관리 센터 확장 온 보 딩](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 지침에 따라 Security Center에 서버를 연결 합니다. 필요한 세부 정보를 입력 하 고 확인 한 후에는 필요한 구성 변경을 수행 하 여 다음 모두에 해당 하는지 확인 Security Center 합니다.
    * Azure 게이트웨이가 등록 됩니다.
    * 서버에는 보고할 작업 영역 및 연결 된 구독이 있습니다.
    * Security Center의 Log Analytics 솔루션은 작업 영역에서 사용 하도록 설정 됩니다. 이 솔루션은이 작업 영역에 보고 하는 *모든* 서버 및 가상 컴퓨터에 대 한 Azure Defender의 기능을 제공 합니다.
    * 서버에 대 한 Azure Defender는 구독에서 사용 하도록 설정 됩니다.
    * Log Analytics 에이전트가 서버에 설치 되어 있고 선택한 작업 영역에 보고 하도록 구성 되어 있습니다. 서버에서 이미 다른 작업 영역에 보고 하는 경우 새로 선택한 작업 영역에도 보고 하도록 구성 됩니다.

    > [!NOTE]
    > 권장 사항이 표시 되려면 등록 후 시간이 다소 걸릴 수 있습니다. 실제로 서버 작업에 *따라 경고를* 수신 하지 못할 수 있습니다. 경고가 제대로 작동 하는지 테스트 하기 위해 테스트 경고를 생성 하려면 [경고 유효성 검사 절차](security-center-alert-validation.md)의 지침을 따르세요.


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Windows 관리 센터에서 보안 권장 사항 및 경고 보기

등록 하면 Windows 관리 센터의 Azure Security Center 영역에서 직접 경고 및 권장 사항을 볼 수 있습니다. 권장 사항 또는 경고를 클릭 하 여 Azure Portal에 표시 합니다. 여기에서 추가 정보를 얻고 문제를 해결 하는 방법을 알아보세요.

[![Windows 관리 센터에 표시 된 것 처럼 Security Center 권장 사항 및 경고](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Security Center에서 Windows 관리 센터 관리 서버에 대 한 보안 권장 사항 및 경고를 확인 합니다.
Azure Security Center에서:

* 모든 Windows 관리 센터 서버에 대 한 보안 권장 사항을 보려면 [자산 인벤토리](asset-inventory.md) 를 열고 조사 하려는 컴퓨터 유형으로 필터링 합니다. **vm 및 컴퓨터** 탭을 선택 합니다.

* 모든 Windows 관리 센터 서버에 대 한 보안 경고를 보려면 **보안 경고**를 엽니다. **필터** 를 클릭 하 여 "비 Azure" **만** 선택 되어 있는지 확인 합니다.

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Windows 관리 센터 관리 서버에 대 한 보안 경고 필터링" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::