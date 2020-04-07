---
title: 파워 BI 앱 오퍼 게시 | Azure 마켓플레이스
description: Microsoft 앱소스 마켓플레이스에 Power BI 앱 오퍼를 게시합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d52270e623a713d7188ac5ff907ebb82ada13283
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745610"
---
# <a name="publish-a-power-bi-app-offer"></a>Power BI 앱 오퍼 게시

>[!Important]
>2020년 3월 30일부터 Power BI 앱 제공의 관리를 파트너 센터로 이전합니다. 마이그레이션 후 파트너 센터에서 오퍼를 만들고 관리합니다. Power BI [앱 만들기 개요의](https://aka.ms/AzureCreatePBIServiceApp) 지침을 따라 마이그레이션된 오퍼를 관리합니다.

클라우드 파트너 포털에서 오퍼를 정의하고 관련 기술 자산을 만든 후 마지막 단계는 게시를 위한 제안을 제출하는 것입니다. 이 프로세스를 시작하려면 **새 오퍼** 창의 왼쪽 창에서 **게시를**선택합니다. 자세한 내용은 [Azure Marketplace 및 AppSource 제안 게시](../manage-offers/cpp-publish-offer.md)를 참조하세요.


## <a name="publishing-steps"></a>게시 단계

다음은 게시 프로세스의 주요 단계입니다.

![Power BI 앱 오퍼에 대한 프로세스 단계 게시](./media/publishing-process-steps.png)

이 표에서는 각 단계를 설명하고 예상 완료 시간을 제공합니다.

|   게시 단계            |   Time     |   설명                                                                  |
| --------------------         |------------| ----------------                                                               |
| 필수 구성 요소 유효성 검사       | 15분     | 제안 정보 및 제안 설정의 유효성이 검사됩니다.                            |
| 인증                | 1-7일   | Power BI 인증 팀은 오퍼를 분석합니다. 팀은 제공된 설치 URL을 통해 앱을 설치하여 수동 확인 테스트를 통해 Power BI 앱을 실행합니다. 기본 유효성 검사는 앱 인증 프로세스의 일부로 수행됩니다(이 문서의 후반부 설명).         |
| 패키징                    | 1시간 미만  | 오퍼의 기술 자산은 고객용으로 포장됩니다.                        |
| 잠재 고객 생성 등록 | 1시간 미만  | 잠재 고객 시스템이 구성 및 배포됩니다.                                      |
| 게시자 확인            | \-         | 오퍼가 진행되기 전에 최종 검토 및 확인을 완료합니다. 이제 오퍼링을 미리 볼 수 있는 링크도 있습니다. 미리 보기의 모양이 만족스러워진 후에는 **상태** 탭에서 **라이브 로이동을** 선택합니다. 이렇게 하면 온보딩 팀에 AppSource에 앱을 나열하라는 요청이 전송됩니다.    |
| 라이브                         | 3시간 미만 | 이제 AppSource에 제품이 공개적으로 나열되고 고객은 앱을 보고 Power BI 구독에 배포할 수 있습니다. 확인 이메일도 받게 됩니다. **All 오퍼** 탭의 오른쪽 열에서 모든 오퍼의 상태를 확인할 수 있습니다. **상태** 탭에서 오퍼의 자세한 게시 흐름 상태를 확인할 수 있습니다. |
|   |   |

이 프로세스가 완료될 수 있도록 최대 8일이 허용됩니다. 이러한 게시 단계를 거치면 Power BI 앱 앱 이 [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI 앱 섹션에 나열됩니다.


### <a name="app-certification-process"></a>앱 인증 프로세스

Microsoft 온보딩 팀은 이 프로세스를 사용하여 Power BI 앱 제품 제출의 유효성을 검사합니다.

1. 법률 문서 및 도움말 링크를 검토합니다.
2. 지원 연락처 정보의 유효성을 검사합니다.
3. 설치 관리자 URL을 사용하여 적절한 설치를 확인합니다.
4. 앱을 검색하여 맬웨어 및 기타 악의적인 콘텐츠를 검색합니다.
5. 표시된 콘텐츠가 앱 설명과 일치하는지 확인합니다.
6. Power BI에서 앱 관련 작업이 예상대로 작동하는지 확인합니다. 팀은 샘플 데이터로 보고서 및 대시보드를 열고, 사용자 지정 데이터 원본에 연결하고, 데이터를 새로 고치는 등의 등입니다.

문제가 발견되면 인증 팀에서 피드백을 제공합니다.  Power BI 앱 요구 사항에 대한 자세한 내용은 [Power BI 앱 설명서를](https://go.microsoft.com/fwlink/?linkid=2028636)참조하십시오.


## <a name="next-steps"></a>다음 단계

[AppSource 마켓플레이스에서](https://appsource.microsoft.com)앱을 정기적으로 모니터링하는 것이 좋습니다.  또한 [클라우드 파트너 포털의](https://cloudpartner.azure.com/#insights) [판매자 인사이트](../../cloud-partner-portal-orig/si-getting-started.md) 기능을 사용하여 마켓플레이스 고객 및 앱 사용에 대한 통찰력을 얻어야 합니다. 마지막으로 [쿠폰을 업데이트할](./cpp-update-existing-offer.md)수 있습니다.
