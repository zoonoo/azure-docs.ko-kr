---
title: Power BI 앱 제안 게시 - Azure Marketplace | Microsoft Docs
description: Power BI 앱 제안을 Microsoft AppSource Marketplace에 게시합니다.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665714"
---
# <a name="publish-power-bi-app-offer"></a>Power BI 앱 제안 게시

포털에서 제안을 정의하고 관련 기술 자산을 만든 후 마지막 단계는 제안을 제출하여 게시하는 것입니다.  이 프로세스를 시작하려면 **새 제안** 창의 세로 메뉴에서 **게시** 단추를 클릭합니다.  자세한 내용은 [Azure Marketplace 및 AppSource 제안 게시](../manage-offers/cpp-publish-offer.md)를 참조하세요.


## <a name="publishing-steps"></a>게시 단계

다음 다이어그램에서는 "라이브 상태 만들기"에 대한 게시 프로세스의 주요 단계를 보여 줍니다.

![Power BI 앱에 대한 게시 프로세스 단계](./media/publishing-process-steps.png)

다음 표에서는 이러한 단계를 설명하고, 예상되는 최대 완료 시간을 제공합니다.

|   게시 단계            |   Time     |   설명                                                                  |
| --------------------         |------------| ----------------                                                               |
| 필수 구성 요소 유효성 검사       | 15분     | 제안 정보 및 제안 설정의 유효성이 검사됩니다.                            |
| 인증                | 1-7일   | Power BI 인증 팀에서 제안을 분석합니다. 제공된 설치 URL을 통해 Power BI 앱을 설치하여 수동 확인 테스트를 실행합니다. 기본 유효성 검사는 앱 인증 프로세스의 일부로 수행됩니다(아래 참조).         |
| 패키징                    | 1시간 미만  | 제안의 기술 자산은 고객이 사용할 수 있도록 패키지로 제공됩니다.                        |
| 창출된 잠재 고객 등록 | 1시간 미만  | 잠재 고객 시스템이 구성 및 배포됩니다.                                      |
| 게시자 확인            | \-         | 제안이 라이브 상태가 되기 전에 최종 게시자가 검토 및 확인합니다. 또한 제안을 미리 볼 수 있는 링크도 제공됩니다. 미리 보기의 모양에 방법에 만족하면 **상태** 탭에서 **라이브 상태 만들기** 단추를 클릭합니다. 이 작업은 AppSource에 앱을 나열하도록 요구하는 요청을 온보딩 팀에 보냅니다.    |
| 라이브                         | 3시간 미만 | 이제 제안이 AppSource에 공개적으로 나열되며("라이브"), 고객은 자신의 Power BI 구독에서 해당 앱을 보고 배포할 수 있습니다. 확인 이메일도 받을 수 있습니다. 언제든지 **모든 제안** 탭을 클릭하여 오른쪽 열에 나열된 모든 제안에 대한 상태를 볼 수 있습니다. **상태** 탭을 클릭하면 제안에 대한 게시 흐름 상태가 자세히 표시됩니다. |
|   |   |

이 프로세스가 완료되는 데 최대 8일이 걸릴 수 있습니다. 이러한 게시 단계가 완료되면 Power BI 앱 제안이 [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI 앱 섹션에 나열됩니다.


### <a name="app-certification-process"></a>앱 인증 프로세스

Microsoft 온보딩 팀에서 다음 프로세스를 사용하여 Power BI 제안 제출의 유효성을 검사합니다.

1. 법률 문서 및 도움말 링크를 검토합니다.
2. 지원 연락처 정보의 유효성을 검사합니다.
3. 설치 관리자 URL을 사용하여 올바른 설치를 확인합니다. 
4. 앱에 맬웨어 및 기타 악성 콘텐츠가 있는지 검사합니다. 
5. 표시된 콘텐츠가 앱의 설명과 일치하는지 확인합니다.
6. 애플리케이션 관련 작업이 Power BI에서 예상대로 작동하는지 확인합니다. 즉 샘플 데이터를 사용하여 보고서 및 대시보드 열기, 사용자 지정 데이터 원본에 연결, 새로 고침 등을 수행합니다.

문제가 발견되면 인증 팀에서 피드백을 제공합니다.  Power BI 앱 요구 사항에 대한 자세한 내용은 [Power BI 앱 설명서](https://go.microsoft.com/fwlink/?linkid=2028636)를 참조하세요.


## <a name="next-steps"></a>다음 단계

[AppSource Marketplace](https://appsource.microsoft.com)에서 앱을 정기적으로 모니터링하는 것이 좋습니다.  또한 [Cloud 파트너 포털](https://cloudpartner.azure.com/#insights)의 [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) 기능을 정기적으로 사용하여 마켓플레이스 고객 및 사용량에 대한 인사이트를 제공해야 합니다.  [제안에 대한 특정 업데이트](./cpp-update-existing-offer.md)를 수행할 수도 있습니다.
