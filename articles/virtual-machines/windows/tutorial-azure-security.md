---
title: "Azure Security Center로 Windows VM 보호 | Microsoft Docs"
description: "자습서 - Azure Security Center로 VM 보호"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: b6fc4e710de740caf78dd1dcebc5432824add06c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017

---
# <a name="monitor-vm-security-with-the-azure-security-center"></a>Azure Security Center로 VM 보안 모니터링

Azure Security Center는 보안 사례와 관련된 Azure 리소스 구성에 대한 정보를 제공합니다. 또한 위협을 감지할 수 있는 통합 보안 모니터링을 제공하며, 이 모니터링 기능이 없다면 위협을 모르고 지나칠 수 있습니다. 이 자습서에서는 Azure Security Center에 대한 간략한 개요를 제공하며 Azure 가상 컴퓨터와 함께 사용하는 방법을 설명합니다.   

## <a name="security-center-overview"></a>Security Center 개요

Azure Security Center는 잠재적 VM 구성 문제 및 대상 보안 위협을 식별하는 데 도움을 줍니다. 예를 들어 네트워크 보안 그룹이 없는 VM, 디스크가 암호화되지 않은 VM 및 무작위 RDP 공격을 식별합니다. 이 정보는 Azure Security Center 대시보드에 읽기 쉬운 그래프로 표시됩니다.

Azure Security Center 대시보드는 Azure Portal의 왼쪽 탐색 창에서 **Security Center**를 클릭하여 액세스할 수 있습니다. 대시보드는 리소스 상태, 보안 경고 및 구성 권장 사항에 대한 개략적인 보기를 제공합니다. 여기서 Azure 환경의 보안 상태를 보고, 보려면 현재 권장의 수를 찾고, 위협 경고의 현재 상태를 볼 수 있습니다. 각각의 개략적 차트를 확장할 수 있으며, 차트를 확장하면 포커스 영역에 대한 세부 정보가 제공됩니다.

![ASC 대시보드](./media/tutorial-azure-security/asc-dash.png)

Azure Security Center는 데이터 탐색에서 그치지 않고 감지된 문제에 대한 권장 사항을 제공합니다. 예를 들어 연결된 네트워크 보안 그룹 없이 VM이 배포된 경우 해결 단계가 포함된 권장 사항이 생성됩니다. 또한 이러한 권장 사항은 Azure Security Center의 컨텍스트를 벗어나지 않고 해결 자동화를 제공합니다.  

![추천](./media/tutorial-azure-security/recommendations.png)

## <a name="configure-data-collection"></a>데이터 수집 구성

VM 보안 구성에 대한 고급 정보를 얻으려면 Azure Security Center 데이터 수집을 구성해야 합니다. 여기에는 데이터 수집을 설정하고 수집된 데이터를 저장할 Azure 저장소 계정을 만드는 작업이 포함됩니다. 

1. Azure Security Center 대시보드에서 **보안 정책**을 클릭하고 구독을 선택합니다. 
2. **데이터 수집**에서 *켜기*를 선택합니다.
3. **저장소 계정 선택**을 클릭하고 새 저장소 계정을 만듭니다. 완료되면 **확인** 을 선택합니다.
4. **보안 정책** 블레이드에서 **저장**을 클릭합니다. 

이 작업이 완료되면 모든 가상 컴퓨터에 Azure Security Center 데이터 수집 에이전트가 설치되고 데이터 수집이 시작됩니다. 

## <a name="configure-security-policy"></a>보안 정책 구성

보안 정책에서 정의하는 보안 정책 항목에 따라 데이터가 수집되고 권장 사항이 생성됩니다. 기본적으로 Azure 리소스는 모든 정책 항목에 대해 평가됩니다. 개별 정책 항목을 모든 Azure 리소스에 대해 전역적으로 해제할 수도 있고 리소스 그룹별로 해제할 수도 있습니다. 이 구성은 서로 다른 Azure 리소스 집합에 서로 다른 보안 정책을 적용할 수 있게 해줍니다. Azure Security Center 보안 정책에 대한 자세한 내용은 [Azure Security Center에서 보안 정책 설정](../../security-center/security-center-policies.md)을 참조하세요. 

모든 Azure 리소스에 대한 보안 정책을 구성하려면:

1. Azure Security Center 대시보드에서 **보안 정책**을 클릭한 다음 구독을 선택합니다. 
2. **방지 정책**을 클릭합니다.
3. 모든 Azure 리소스에 적용할 정책 항목을 설정 또는 해제합니다.
4. 완료되면 **확인**을 클릭합니다.
5. **보안 정책** 블레이드에서 **저장**을 클릭합니다. 

특정 리소스 그룹에 대한 정책을 구성하려면 동일한 단계를 따릅니다. 단, 보안 정책 블레이드에서 구독을 선택하는 대신 리소스 그룹을 선택합니다. 정책을 구성할 때 *상속* 아래에서 **고유**를 선택합니다. 특정 리소스 그룹에 대해 데이터 수집을 해제하려는 경우에도 여기서 이 구성을 만들 수 있습니다.

다음 예제에서는 *myResoureGroup*이라는 리소스 그룹에 대한 고유 정책을 만들었습니다. 이 정책에서 디스크 암호화 및 웹 응용 프로그램 방화벽 권장 사항이 모두 해제되었습니다.

![고유 정책](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>VM 구성 상태 보기

데이터 수집을 설정하고 보안 정책을 구성하면 Azure Security Center가 경고 및 권장 사항을 제공하기 시작합니다. VM이 배포되면 데이터 수집 에이전트가 설치되고 Azure Security Center가 이러한 새 VM의 데이터로 채워집니다. VM 구성 상태에 대한 자세한 내용은 [Azure Security Center에서 가상 컴퓨터 보호](../../security-center/security-center-virtual-machine-recommendations.md)를 참조하세요. 

데이터가 수집되면 각 VM 및 관련 Azure 리소스의 리소스 상태가 집계되어 읽기 쉬운 차트로 표시됩니다. 리소스 상태를 보려면 Azure Security Center 대시보드로 돌아갑니다. **리소스 보안 상태**에서 **계산**을 클릭합니다. 마지막으로 **계산** 블레이드에서 **가상 컴퓨터**를 클릭합니다. 이 보기는 모든 VM의 구성 상태 요약 정보를 제공합니다.

![상태 계산](./media/tutorial-azure-security/compute-health.png)

각 VM을 선택하면 해당 VM에 대한 모든 권장 사항이 표시됩니다. 권장 사항에 대한 내용은 이 자습서의 다음 섹션에 자세히 설명되어 있습니다.

## <a name="remediate-configuration-issues"></a>구성 문제 해결

Azure Security Center가 구성 데이터로 채워지기 시작하면 구성된 보안 정책에 따라 권장 사항이 생성됩니다. 예를 들어 연결된 네트워크 보안 그룹 없이 VM이 구성된 경우 네트워크 보안 그룹을 만들라는 권장이 생성됩니다. 모든 권장 사항 목록을 보려면: 

1. Azure Security Center 대시보드에서 **권장 사항**을 클릭합니다.
3. 특정 권장 사항을 선택하면 해당 권장 사항이 적용되는 모든 리소스 목록을 표시하는 블레이드가 열립니다.
4. 해결할 특정 리소스를 선택합니다.
5. 화면에 표시되는 지침에 따라 해결 단계를 수행합니다. 

대부분의 경우 Azure Security Center에서는 Azure Security Center의 컨텍스트를 벗어나지 않고 권장 사항을 해결할 수 있는 실행 가능한 단계를 제공합니다. 예를 들어 다음 예제에서는 인바운드 규칙이 제한되지 않은 NSG가 검색되었습니다. 이 권장 사항의 경우 **인바운드 규칙 편집** 단추를 선택할 수 있습니다. 그러면 규칙을 수정하는 데 필요한 적절한 UI가 제공됩니다. 

![추천](./media/tutorial-azure-security/remediation.png)

권장 사항이 해결되면 해결되었다고 표시됩니다. 

## <a name="view-detected-threats"></a>검색된 위협 보기

Azure Security Center는 리소스 구성 권장 사항 외에도 위협 감지 경고를 제공합니다. 보안 경고 기능은 각 VM, Azure 네트워킹 로그 및 연결된 파트너 솔루션에서 수집된 데이터를 집계하여 Azure 리소스에 대한 보안 위협을 검색합니다. Azure Security Center 위협 감지 기능에 대한 자세한 내용은 [Azure Security Center 감지 기능](../../security-center/security-center-detection-capabilities.md)을 참조하세요.

보안 경고 기능을 사용하려면 Azure Security Center 가격 책정 계층을 *무료**에서 *표준**으로 높여야 합니다. 이렇게 하면 30일 **평가판**을 사용할 수 있습니다. 가격 책정 계층을 변경하려면:  

1. Azure Security Center 대시보드에서 **보안 정책**을 클릭한 다음 구독을 선택합니다.
2. **가격 책정 계층**을 클릭합니다.
3. 새 계층을 선택하고 **선택**을 클릭합니다.
5. **보안 정책** 블레이드에서 **저장**을 클릭합니다. 

사용하도록 설정하면 보안 위협이 감지된 경우 보안 경고 그래프가 채워지기 시작합니다.

![보안 경고](./media/tutorial-azure-security/security-alerts.png)

경고를 선택하면 위협에 대한 설명, 감지된 시간, 위협 시도, 권장 해결책 등의 정보를 볼 수 있습니다. 이 예제에서는 294회의 RDP 시도 실패와 함께 RDP 무차별 암호 대입 공격이 발견되었으며 해결 방법이 제공되었습니다.

![RDP 공격](./media/tutorial-azure-security/rdp-attack.png)
