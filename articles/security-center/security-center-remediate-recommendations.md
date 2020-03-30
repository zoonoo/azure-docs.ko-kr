---
title: Azure 보안 센터의 권장 사항 수정 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Security Center에서 권장 사항을 수정하여 리소스를 보호하고 보안 정책을 준수하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603508"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Security Center의 권장 사항 교정

권장 사항은 리소스를 더 잘 보호하는 방법에 대한 제안을 제공합니다. 권장 사항에 제공된 수정 단계를 수행하여 권장 사항을 구현합니다.

## <a name="remediation-steps"></a>수정 단계<a name="remediation-steps"></a>

모든 권장 사항을 검토한 후 먼저 수정할 권장 사항을 결정합니다. [보안 점수 영향을](security-center-recommendations.md#monitor-recommendations) 사용하여 먼저 수행할 작업을 우선 순위를 지정하는 것이 좋습니다.

1. 목록에서 권장 사항을 클릭합니다.

1. **수정 단계** 섹션의 지침을 따릅니다. 각 권장 사항에는 고유한 지침 집합이 있습니다. 다음 스크린샷은 HTTPS를 통해 트래픽을 허용하도록 응용 프로그램을 구성하기 위한 수정 단계를 보여 주며 있습니다.

    ![권장 사항 세부 정보](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 작업이 완료되면 업데이트 적용이 성공했는지 알리는 알림이 나타납니다.

## <a name="quick-fix-remediation"></a>빠른 수정 수정<a name="one-click"></a>

빠른 수정을 사용하면 여러 리소스에 대한 권장 사항을 신속하게 수정할 수 있습니다. 특정 권장 사항에 대해서만 사용할 수 있습니다. 빠른 수정은 수정을 단순화하고 보안 점수를 빠르게 증가하여 환경의 보안을 개선할 수 있도록 합니다.

빠른 수정 수정 수정을 구현하려면 다음을 수행하십시오.

1. **빠른 수정이** 권장 사항 목록에서! 추천을 클릭합니다.

    [![빠른 수정을 선택하십시오!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. **비정상 리소스** 탭에서 권장 사항을 구현할 리소스를 선택하고 **을 클릭합니다.**

    > [!NOTE]
    > 나열된 리소스 중 일부를 수정할 수 있는 적절한 권한이 없으므로 사용하지 않도록 설정될 수 있습니다.

1. 확인 상자에서 수정 세부 정보 및 의미를 읽습니다.

    ![빠른 수정](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 의미는 **remediate를** 클릭한 후 열리는 리소스 수정 창의 회색 상자에 **나열됩니다.** 빠른 수정 수정 을 진행할 때 어떤 변경 사항이 있는지 나열합니다.

1. 필요한 경우 관련 매개 변수를 삽입하고 수정을 승인합니다.

    > [!NOTE]
    > 수정이 완료된 후 **정상 리소스** 탭의 리소스를 보려면 몇 분 정도 걸릴 수 있습니다. 업데이트 적용 작업을 보려면 [활동 로그를](#activity-log)확인합니다.

1. 작업이 완료되면 업데이트 적용이 성공했는지 알리는 알림이 나타납니다.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>활동 로그의 수정 문제 해결 로깅<a name="activity-log"></a>

업데이트 적용 작업은 템플릿 배포 또는 REST PATCH API 호출을 사용하여 리소스에 구성을 적용합니다. 이러한 작업은 Azure [활동 로그에](../azure-resource-manager/management/view-activity-logs.md)기록됩니다.


## <a name="recommendations-with-quick-fix-remediation"></a>빠른 수정 수정이 있는 권장 사항

|권장|암시|
|---|---|
|SQL 서버에서 감사를 사용하도록 설정해야 합니다.|이 작업을 수행하면 이러한 서버와 해당 데이터베이스에서 SQL 감사를 수행할 수 있습니다. <br>**고**: <ul><li>선택한 SQL 서버의 각 영역에 대해 감사 로그를 저장하기 위한 저장소 계정이 해당 지역의 모든 서버에서 만들어지고 공유됩니다.</li><li>적절한 감사를 위해 리소스 그룹 또는 저장소 계정의 이름을 삭제하거나 이름을 바꾸지 마십시오.</li></ul>|
|SQL 관리형 인스턴스에서 Advanced Data Security를 사용하도록 설정해야 합니다.|이 작업을 수행하면 선택한 SQL 관리 인스턴스에서 SQL 고급 데이터 보안(ADS)을 사용할 수 있습니다. <br>**고**: <ul><li>선택한 SQL 관리 인스턴스의 각 지역 및 리소스 그룹에 대해 해당 지역의 모든 인스턴스에서 검색 결과를 저장하기 위한 저장소 계정이 만들어지고 공유됩니다.</li><li> ADS는 SQL 관리 인스턴스당 15USD가 부과됩니다.</li></ul>|
|SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.|이 작업을 수행하면 선택한 SQL 관리 인스턴스에서 SQL 취약성 평가를 수행할 수 있습니다. <br>**고**:<ul><li>SQL 취약성 평가는 SQL 고급 데이터 보안(ADS) 패키지의 일부입니다. ADS가 아직 활성화되어 있지 않으면 관리되는 인스턴스에서 ADS가 자동으로 활성화됩니다.</li><li>선택한 SQL 관리 인스턴스의 각 지역 및 리소스 그룹에 대해 검색 결과를 저장하기 위한 저장소 계정이 해당 지역의 모든 인스턴스에서 만들어지고 공유됩니다.</li><li>ADS는 SQL 서버당 $15의 요금이 부과됩니다.</li></ul>||
|SQL 서버에서 고급 데이터 보안을 사용하도록 설정해야 합니다.|이 작업을 수행하면 선택한 서버 및 해당 데이터베이스에서 ADS(고급 데이터 보안)를 사용할 수 있습니다. <br>**고**:<ul><li>선택한 SQL 서버의 각 지역 및 리소스 그룹에 대해 검색 결과를 저장하기 위한 저장소 계정이 해당 region의 모든 서버에서 만들어지고 공유됩니다.<</li><li>ADS는 SQL 서버당 $15의 요금이 부과됩니다.</li></ul>||
|SQL 서버에서 취약점 평가를 사용하도록 설정해야 합니다.|이 작업을 수행하면 선택한 서버 및 해당 데이터베이스에서 SQL 취약성 평가를 수행할 수 있습니다. <br>**고**:<ul><li>SQL 취약성 평가는 SQL 고급 데이터 보안(ADS) 패키지의 일부입니다. ADS가 아직 활성화되지 않은 경우 SQL 서버에서 ADS가 자동으로 활성화됩니다.</li><li>선택한 SQL 서버의 각 지역 및 리소스 그룹에 대해 검색 결과를 저장하기 위한 저장소 계정이 해당 지역의 모든 인스턴스에서 만들어지고 공유됩니다.</li><li>ADS는 SQL 서버당 $15의 요금이 부과됩니다.</li></ul>||
|SQL 데이터베이스의 투명한 데이터 암호화를 사용하도록 설정해야 합니다.|이 작업을 사용하면 선택한 데이터베이스에서 SQL 데이터베이스 투명 데이터 암호화(TDE)를 사용할 수 있습니다. <br>**참고**: 기본적으로 서비스 관리 TDE 키가 사용됩니다.
|Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.|이 작업은 저장소 계정 보안을 업데이트하여 보안 연결에 의한 요청만 허용하도록 합니다. (HTTPS)를 참조하십시오. <br>**고**:<ul><li>HTTP를 사용하는 모든 요청은 거부됩니다.</li><li>Azure 파일 서비스를 사용하는 경우 SMB 2.1, 암호화 없이 SMB 3.0을 사용하는 시나리오 및 Linux SMB 클라이언트의 일부 풍미를 포함하여 암호화 없이 연결이 실패합니다. 자세히 알아봅니다.</li></ul>|
|웹 애플리케이션에 HTTPS를 통해서만 액세스 가능|이 작업은 선택한 리소스에서 HTTP에서 HTTPS로 모든 트래픽을 리디렉션합니다. <br>**고**:<ul><li>SSL 인증서가 없는 HTTPS 끝점은 '개인 정보 오류'와 함께 브라우저에 표시됩니다. 따라서 사용자 지정 도메인이 있는 사용자는 SSL 인증서를 설정했는지 확인해야 합니다.</li><li>앱 서비스를 보호하는 패킷 및 웹 응용 프로그램 방화벽을 확인하고 HTTPS 세션 전달을 허용합니다.</li></ul>|
|함수 앱에 HTTPS를 통해서만 액세스 가능|이 작업은 선택한 리소스에서 HTTP에서 HTTPS로 모든 트래픽을 리디렉션합니다. <br>**고**:<ul><li>SSL 인증서가 없는 HTTPS 끝점은 '개인 정보 오류'와 함께 브라우저에 표시됩니다. 따라서 사용자 지정 도메인이 있는 사용자는 SSL 인증서를 설정했는지 확인해야 합니다.</li><li>앱 서비스를 보호하는 패킷 및 웹 응용 프로그램 방화벽을 확인하고 HTTPS 세션 전달을 허용합니다.</li></ul>|
|API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.|이 작업은 선택한 리소스에서 HTTP에서 HTTPS로 모든 트래픽을 리디렉션합니다. <br>**고**:<ul><li>SSL 인증서가 없는 HTTPS 끝점은 '개인 정보 오류'와 함께 브라우저에 표시됩니다. 따라서 사용자 지정 도메인이 있는 사용자는 SSL 인증서를 설정했는지 확인해야 합니다.</li><li>앱 서비스를 보호하는 패킷 및 웹 응용 프로그램 방화벽을 확인하고 HTTPS 세션 전달을 허용합니다.</li></ul>|
|웹 애플리케이션에 대해 원격 디버깅을 해제해야 함|이 작업은 원격 디버깅을 비활성화합니다.|
|함수 앱에 대해 원격 디버깅을 해제해야 함|이 작업은 원격 디버깅을 비활성화합니다.|
|API 앱에 대해 원격 디버깅을 해제해야 함|이 작업은 원격 디버깅을 비활성화합니다.|
|CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 함|이 작업은 다른 도메인이 웹 응용 프로그램에 액세스하지 못하도록 차단합니다. 특정 도메인을 허용하려면 허용된 원한 필드(쉼표로 구분됨)에 도메인을 입력합니다. <br>**참고**: 필드를 비워 두면 모든 교차 원점 호출이 차단됩니다.'Param 필드 제목: '허용된 원점'|
|CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용하지 않아야 함|이 작업은 다른 도메인이 함수 응용 프로그램에 액세스하지 못하도록 차단합니다. 특정 도메인을 허용하려면 허용된 원한 필드(쉼표로 구분됨)에 도메인을 입력합니다. <br>**참고**: 필드를 비워 두면 모든 교차 원점 호출이 차단됩니다.'Param 필드 제목: '허용된 원점'|
|CORS에서 모든 리소스가 API 앱에 액세스하도록 허용해서는 안 됩니다.|이 작업은 다른 도메인이 API 응용 프로그램에 액세스하지 못하도록 차단합니다. 특정 도메인을 허용하려면 허용된 원한 필드(쉼표로 구분됨)에 도메인을 입력합니다. <br>**참고**: 필드를 비워 두면 모든 교차 원점 호출이 차단됩니다.'Param 필드 제목: '허용된 원점'|
|모니터링 에이전트는 가상 컴퓨터에서 활성화해야 합니다.|이 작업은 선택한 가상 시스템에 모니터링 에이전트를 설치합니다. 에이전트가 보고할 작업 영역을 선택합니다.<ul><li>업데이트 정책이 자동으로 설정되면 새 기존 인스턴스에 배포됩니다.</li><li>업데이트 정책이 수동으로 설정되어 있고 기존 인스턴스에 에이전트를 설치하려면 확인란 옵션을 선택합니다. [자세히 알아보기](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Key Vault의 진단 로그를 사용하도록 설정해야 합니다.|이 작업을 사용하면 키 자격 증명 모음에 대한 진단 로그를 사용할 수 있습니다. 진단 로그 및 메트릭은 선택한 작업 영역에 저장됩니다.|
|서비스 버스의 진단 로그를 사용하도록 설정해야 합니다.|이 작업을 사용하면 서비스 버스의 진단 로그를 사용할 수 있습니다. 진단 로그 및 메트릭은 선택한 작업 영역에 저장됩니다.|

## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 센터에서 권장 사항을 수정하는 방법을 보여 주어 도보였습니다. 보안 센터에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](tutorial-security-policy.md) - Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
