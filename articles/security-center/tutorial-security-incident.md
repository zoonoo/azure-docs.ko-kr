---
title: 인시던트 응답 자습서 - Azure Security Center
description: 이 자습서에서는 보안 경고를 심사하고, 문제의 근본 원인 및 범위를 확인하고, 보안 데이터를 검색하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: memildin
ms.openlocfilehash: 971b93422e2b3f2053e0c5564c7ba924a631d810
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585780"
---
# <a name="tutorial-respond-to-security-incidents"></a>자습서: 보안 인시던트에 대응
Security Center는 고급 분석 및 위협 인텔리전스를 사용하여 하이브리드 클라우드 워크로드를 지속적으로 분석하여 악의적인 활동을 경고합니다. 또한 다른 보안 제품 및 서비스의 경고를 Security Center에 통합하고, 사용자 고유의 표시기 또는 인텔리전스 원본에 기반한 사용자 지정 경고를 만들 수 있습니다. 경고가 생성되면 조사하고 수정하기 위한 신속한 조치가 필요합니다. 이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 보안 경고 심사
> * 보안 인시던트의 근본 원인과 범위를 파악하기 위한 추가 조사
> * 조사에 도움이 되는 보안 데이터 검색

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항
이 자습서에서 설명하는 기능을 단계별로 실행하려면 Security Center의 표준 가격 책정 계층에 있어야 합니다. 비용 없이 Security Center 표준을 사용해 볼 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요. [Security Center 표준에 Azure 구독 온보딩](security-center-get-started.md) 빠른 시작을 통해 표준 계층으로 업그레이드하는 방법을 안내합니다.

## <a name="scenario"></a>시나리오
Contoso는 최근에 일부 가상 머신 기반 LOB(기간 업무) 워크로드 및 SQL 데이터베이스를 포함하여 온-프레미스 리소스 일부를 Azure에 마이그레이션했습니다. 현재 Contoso의 CSIRT(주요 컴퓨터 보안 사고 대응 팀)에는 현재의 사고 대응 도구와 통합되지 않는 보안 인텔리전스로 인해 보안 문제를 조사하는 데 문제가 있습니다. 부족한 조사로 인해 감지(너무 많은 가양성)뿐만 아니라 평가와 진단 단계에서도 문제가 발생하고 있습니다. 이 마이그레이션의 일부로 이 문제를 해결할 수 있도록 Security Center에 옵트인하기로 했습니다.

이 마이그레이션의 첫 번째 단계는 Security Center에서 모든 리소스를 등록하고 모든 보안 권장 사항을 해결한 후에 완료되었습니다. Contoso CSIRT는 컴퓨터 보안 사고를 처리하는 초점입니다. 팀은 보안 사고를 해결할 책임이 있는 사용자의 그룹으로 구성됩니다. 팀 구성원에게는 대응의 모든 영역을 다루어야 하는 의무가 명확하게 정의되었습니다.

이 시나리오의 목적상 Contoso CSIRT의 일부인 다음 가상 사용자의 역할에 집중하겠습니다.

![사고 대응 수명 주기](./media/tutorial-security-incident/security-center-incident-response.png)

Judy는 보안 운영 업무를 담당하고 있습니다. 책임은 다음과 같습니다.

* 보안 위협에 대한 24시간 항시 모니터링 및 대응
* 클라우드 워크로드 소유자, 또는 필요한 경우 보안 분석가로 에스컬레이션

Sam은 보안 분석가이며 다음과 같은 업무를 담당합니다.

* 공격 조사
* 경고 수정
* 워크로드 소유자로 활동하여 완화 확인 및 적용

여기서 볼 수 있듯이 Judy와 Sam은 서로 다른 책임을 맡고 있지만 Security Center 정보를 공유해야 합니다.

## <a name="triage-security-alerts"></a>보안 경고 심사
Security Center는 모든 보안 경고에 대해 통일된 보기를 제공합니다. 보안 경고는 심각도에 따라 순위가 결정되며, 가능한 경우 관련 경고가 보안 인시던트에 결합됩니다. 경고와 인시던트를 심사하는 경우 다음을 수행해야 합니다.

- 추가 조치가 필요하지 않은 경고를 해제합니다(예: 경고가 거짓 긍정인 경우).
- 악성 IP 주소에서 네트워크 트래픽을 차단하는 것과 같이 알려진 공격을 수정하는 작업을 수행합니다.
- 추가 조사가 필요한 경고를 확인합니다.


1. Security Center 주 메뉴의 **검색** 아래에서 **보안 경고**를 선택합니다.

   ![보안 경고](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)

2. 이 인시던트에 대한 자세한 내용을 보려면 경고 목록에서 경고 모음인 보안 인시던트를 클릭합니다. **보안 인시던트가 검색됨**이 열립니다.

   ![보안 인시던트](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. 이 화면에는 보안 인시던트 설명이 맨 위에 있으며, 이 인시던트의 일부인 경고 목록이 있습니다. 자세한 내용을 보려면 추가로 조사하려는 경고를 클릭합니다.

   ![보안 인시던트](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   경고 유형은 다양할 수 있습니다. 경고 유형 및 잠재적 수정 단계에 대한 자세한 내용은 [Azure Security Center의 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)를 참조하세요. 안전하게 해제할 수 있는 경고의 경우 해당 경고를 마우스 오른쪽 단추로 클릭하고 **해제** 옵션을 선택할 수 있습니다.

   ![경고](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. 악의적인 활동의 근본 원인과 범위를 알 수 없는 경우 다음 단계로 진행하여 추가로 조사합니다.

## <a name="investigate-an-alert-or-incident"></a>경고 또는 인시던트 조사
1. **보안 경고** 페이지에서 **조사 시작** 단추를 클릭합니다(이미 시작된 경우 이름이 **조사 계속**으로 변경됨).

   ![조사](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   조사 맵은 이 보안 경고 또는 인시던트에 연결된 엔터티에 대한 그래픽 표현입니다. 맵에서 엔터티를 클릭하면 해당 엔터티에 대한 정보에 새 엔터티가 표시되고 맵이 확장됩니다. 페이지의 오른쪽 창에는 맵에서 선택된 엔터티의 해당 속성이 강조 표시됩니다. 각 탭에서 제공되는 정보는 선택한 엔터티에 따라 달라집니다. 조사 프로세스에서 공격자의 움직임을 더 잘 이해하려면 모든 관련 정보를 검토합니다.

2. 더 많은 증거가 필요하거나 조사 중에 발견된 엔터티를 더 자세히 조사해야 하는 경우 다음 단계로 진행합니다.

## <a name="search-data-for-investigation"></a>조사를 위한 데이터 검색

Security Center의 검색 기능을 사용하여 손상된 시스템에 대한 추가 증거 및 조사에 속하는 엔터티에 대한 자세한 정보를 찾을 수 있습니다.

검색을 수행하려면 **Security Center** 대시보드를 열고, 왼쪽 탐색 창에서 **검색**을 클릭하고, 검색하려는 엔터티가 포함된 작업 영역을 선택하고, 검색 쿼리를 입력한 다음, 검색 단추를 클릭합니다.

## <a name="clean-up-resources"></a>리소스 정리
이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 이후의 빠른 시작과 자습서를 계속 사용하려면 표준 계층을 계속 실행하고 자동 프로비저닝을 설정된 상태로 유지합니다. 계속하지 않거나 체험 계층으로 되돌리려면 다음을 수행합니다.

1. Security Center 주 메뉴로 돌아가서 **보안 정책**을 선택합니다.
2. 체험 계층으로 되돌리려는 구독 또는 정책을 선택합니다. **보안 정책**이 열립니다.
3. **정책 구성 요소** 아래에서 **가격 책정 계층**을 선택합니다.
4. **체험**을 선택하여 표준 계층에서 체험 계층으로 구독을 변경합니다.
5. **저장**을 선택합니다.

자동 프로비저닝을 사용하지 않도록 설정하려면 다음을 수행합니다.

1. Security Center 주 메뉴로 돌아가서 **보안 정책**을 선택합니다.
2. 자동 프로비저닝을 사용하지 않도록 설정할 구독을 선택합니다.
3. **보안 정책 - 데이터 수집**에서 **온보딩** 아래의 **해제**를 선택하여 자동 프로비저닝을 사용하지 않도록 설정합니다.
4. **저장**을 선택합니다.

>[!NOTE]
> 자동 프로비저닝을 해제해도 에이전트가 프로비저닝된 Azure VM에서 Log Analytics 에이전트가 제거되지는 않습니다. 자동 프로비저닝을 사용하지 않도록 설정하면 리소스에 대한 보안 모니터링이 제한됩니다.
>

## <a name="next-steps"></a>다음 단계
이 자습서에서는 보안 인시던트에 대응할 때 사용할 다음과 같은 Security Center 기능에 대해 알아보았습니다.

> [!div class="checklist"]
> * 리소스에 대한 관련 경고의 집계인 보안 인시던트
> * 보안 경고 또는 인시던트에 연결된 엔터티에 대한 그래픽 표현인 조사 맵
> * 손상된 시스템에 대한 추가 증거를 찾는 검색 기능