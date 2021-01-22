---
title: 일반적인 Azure Automanage 등록 오류 문제 해결
description: 일반적인 Automanage 등록 오류 및 문제 해결 방법
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 13388bf289c1d10a2e7da04a4dd5d26be109535e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697863"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>일반적인 Automanage 등록 오류 문제 해결
Automanage는 컴퓨터를 서비스에 등록 하지 못할 수 있습니다. 이 문서에서는 배포 실패 문제를 해결 하는 방법을 설명 하 고, 배포가 실패할 수 있는 몇 가지 일반적인 이유를 공유 하 고, 완화의 잠재적인 다음 단계를 설명 합니다.

## <a name="troubleshooting-deployment-failures"></a>배포 오류 문제 해결
Automanage에 컴퓨터를 온 보 딩 하면 Azure Resource Manager 배포가 생성 됩니다. 등록에 실패 하는 경우 배포에 실패 한 이유에 대 한 자세한 내용을 문의 하는 것이 유용할 수 있습니다. 아래 그림에 나와 있는 실패 세부 정보 플라이 아웃의 배포에 대 한 링크가 있습니다.

:::image type="content" source="media\automanage-common-errors\failure-flyout.png" alt-text="오류 세부 정보 플라이 아웃을 자동으로 관리 합니다.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>실패 한 VM을 포함 하는 리소스 그룹의 배포를 확인 합니다.
실패 플라이 아웃에는 등록에 실패 한 컴퓨터를 포함 하는 리소스 그룹 내 배포에 대 한 링크와 함께 배포를 필터링 하는 데 사용할 수 있는 접두사 이름이 포함 됩니다. 링크를 클릭 하면 배포 블레이드로 이동 하 여 컴퓨터에 대 한 자동 관리 배포를 확인 하는 배포를 필터링 할 수 있습니다. 여러 지역에 걸쳐 배포 하는 경우 올바른 지역에서 배포를 클릭 해야 합니다.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>실패 한 VM을 포함 하는 구독에 대 한 배포를 확인 합니다.
리소스 그룹 배포에 오류가 표시 되지 않으면 다음 단계는 등록에 실패 한 VM을 포함 하는 구독의 배포를 확인 하는 것입니다. 실패 플라이 아웃의 **구독에 대 한 배포** 링크를 클릭 하 고 **Automanage-defaultresourcegroup** 필터를 사용 하 여 배포를 필터링 합니다. 실패 블레이드의 리소스 그룹 이름을 사용 하 여 배포를 필터링 합니다. 배포 이름 뒤에는 지역 이름이 붙습니다. 여러 지역에 걸쳐 배포 하는 경우 올바른 지역에서 배포를 클릭 해야 합니다.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Log Analytics 작업 영역에 연결 된 구독에서 배포 확인
실패 한 VM을 포함 하는 리소스 그룹 또는 구독에서 실패 한 배포가 표시 되지 않고 실패 한 VM이 다른 구독의 Log Analytics 작업 영역에 연결 된 경우 Log Analytics 작업 영역에 연결 된 구독으로 이동 하 여 실패 한 배포를 확인 합니다.

## <a name="common-deployment-errors"></a>일반적인 배포 오류

Error |  완화 방법
:-----|:-------------|
계정 권한 부족 자동 관리 오류 | 이는 최근에 새 Automanage 계정이 포함 된 구독을 새 테 넌 트로 이동한 경우에 발생할 수 있습니다. 이 문제를 해결 하는 단계는 [여기](./repair-automanage-account.md)에 있습니다.
영역 매핑 요구 사항이 일치 하지 않는 작업 영역 영역 | Automanage에서 컴퓨터를 온보드 할 수 없지만 컴퓨터가 현재 연결 되어 있는 Log Analytics 작업 영역이 지원 되는 자동화 지역에 매핑되지 않았습니다. 기존 Log Analytics 작업 영역 및 Automation 계정이 지원 되는 [지역 매핑에](https://docs.microsoft.com/azure/automation/how-to/region-mappings)있는지 확인 합니다.
"할당에 실패 했습니다. 사용할 수 있는 추가 정보가 없습니다. " | Microsoft Azure 지원 되는 사례를 여세요.

## <a name="next-steps"></a>다음 단계

* [Azure Automanage에 대해 자세히 알아보기](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Azure Portal에서 가상 컴퓨터에 대해 Automanage를 사용 하도록 설정](quick-create-virtual-machines-portal.md)

