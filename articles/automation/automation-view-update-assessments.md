---
title: Azure Automation 업데이트 평가 보기
description: 이 문서에서는 업데이트 관리를 배포하기 위한 업데이트 평가를 살펴보는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 321146364897d46a403bdfd6789fcb219179d88c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830602"
---
# <a name="view-update-assessments"></a>업데이트 평가 보기

Azure Automation 계정에서 **업데이트 관리**를 선택하여 머신 상태를 확인합니다.

이 보기에서는 컴퓨터, 누락 업데이트, 업데이트 배포 및 예약된 업데이트 배포에 대한 정보를 제공합니다. **준수 열**에서 머신을 마지막으로 평가한 시간을 확인할 수 있습니다. **업데이트 에이전트 준비** 열에서 업데이트 에이전트의 상태를 확인할 수 있습니다. 문제가 있는 경우 링크를 선택하여 문제 해결을 도와주는 문제 해결 설명서로 이동합니다.

머신, 업데이트 또는 배포에 대한 정보를 반환하는 로그 검색을 실행하려면 목록에서 해당 항목을 선택합니다. 그러면 선택한 항목에 대한 쿼리가 있는 로그 검색 창이 열립니다.

![업데이트 관리 기본 보기](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>누락 업데이트 보기

**누락 업데이트**를 선택하여 컴퓨터에서 누락된 업데이트 목록을 표시합니다. 각 업데이트가 나열되며 선택 가능합니다. 업데이트가 필요한 머신 수, 운영 체제 세부 정보, 자세한 정보 링크가 표시됩니다. 로그 검색 창에는 업데이트에 대한 자세한 정보도 표시됩니다.

![누락 업데이트](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>업데이트 분류 작업

다음 표에는 각 분류에 대한 정의와 함께 업데이트 관리에서 지원하는 업데이트 분류가 나열되어 있습니다.

### <a name="windows"></a>Windows

|분류  |Description  |
|---------|---------|
|중요 업데이트     | 보안과 관련이 없는 중요한 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 보안과 관련이 있는 제품 관련 업데이트입니다.        |
|업데이트 롤업     | 간편하게 배포할 수 있도록 함께 패키지로 제공되는 핫픽스 세트입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 애플리케이션에 적용되는 핫픽스 세트입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일의 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치된 애플리케이션 또는 파일의 업데이트입니다.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|분류  |Description  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 본질적으로 중요하지 않거나 보안 업데이트가 아닌 그 외의 모든 업데이트입니다.        |

Linux의 경우 평가 데이터를 표시하는 동안 업데이트 관리에서는 중요한 업데이트와 보안 업데이트를 구분할 수 있습니다. (이 세분성은 클라우드의 데이터 보강 때문에 가능합니다.) 패치의 경우, 업데이트 관리는 컴퓨터에서 사용할 수 있는 분류 데이터에 의존합니다. 다른 배포판과 달리, CentOS는 제품의 RTM 버전에서 이 정보를 사용할 수 없습니다. CentOS 머신이 다음 명령에 대해 보안 데이터를 반환하도록 구성된 경우 업데이트 관리에서는 분류에 따라 패치를 수행할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재는 CentOS에서 네이티브 분류 데이터 가용성을 지원하는 메서드가 없습니다. 지금은 직접 이 기능을 사용하도록 설정한 고객에게만 최선의 지원이 제공됩니다.

Red Hat Enterprise 버전 6의 업데이트를 분류하려면 yum-보안 플러그 인을 설치해야 합니다. Red Hat Enterprise Linux 7에서는 플러그 인이 이미 yum 자체에 포함되어 있으므로 아무 것도 설치할 필요가 없습니다. 자세한 내용은 다음 Red Hat [기술 항목](https://access.redhat.com/solutions/10021)을 참조하세요.

## <a name="next-steps"></a>다음 단계

일반 정보는 [Azure VM에 대한 업데이트 및 패치 관리](automation-tutorial-update-management.md)를 참조하세요.
