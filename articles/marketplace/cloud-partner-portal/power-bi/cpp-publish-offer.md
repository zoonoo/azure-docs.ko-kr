---
title: Power BI 앱 제안 게시 | Azure Marketplace
description: Microsoft AppSource marketplace에 Power BI 앱 제품을 게시 합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: eb31520c81a4064edbe54a0256b694c4ad88fb49
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141706"
---
# <a name="publish-a-power-bi-app-offer"></a>Power BI 앱 제안 게시

>[!Important]
>2020 년 4 월 13 일부 터 파트너 센터에 Power BI 앱 제품 관리를 이동 하기 시작 합니다. 마이그레이션 후 파트너 센터에서 제품을 만들고 관리 합니다. [앱 만들기 개요 Power BI](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) 의 지침에 따라 마이그레이션된 제안을 관리 합니다.

마지막 단계는 Cloud 파트너 포털에서 제품을 정의 하 고 관련 기술 자산을 만든 후 게시를 위해 제품을 제출 하는 것입니다. 이 프로세스를 시작 하려면 **새 제품** 창의 왼쪽 창에서 **게시**를 선택 합니다. 자세한 내용은 [Azure Marketplace 및 AppSource 제안 게시](../manage-offers/cpp-publish-offer.md)를 참조하세요.


## <a name="publishing-steps"></a>게시 단계

다음은 게시 프로세스의 주요 단계입니다.

![Power BI 앱 제안에 대 한 프로세스 단계 게시](./media/publishing-process-steps.png)

이 표에서는 각 단계를 설명 하 고 예상 완료 시간을 제공 합니다.

|   게시 단계            |   시간     |   설명                                                                  |
| --------------------         |------------| ----------------                                                               |
| 필수 구성 요소 유효성 검사       | 15분     | 제안 정보 및 제안 설정의 유효성이 검사됩니다.                            |
| 인증                | 1-7일   | Power BI 인증 팀은 제품을 분석 합니다. 팀은 제공 된 설치 URL을 통해 앱을 설치 하 여 수동 확인 테스트를 통해 Power BI 앱을 실행 합니다. 기본 유효성 검사는이 문서의 뒷부분에서 설명 하는 앱 인증 프로세스의 일부로 수행 됩니다.         |
| 패키징                    | 1시간 미만  | 제품의 기술 자산은 고객이 사용할 수 있도록 패키지 됩니다.                        |
| 리드 생성 등록 | 1시간 미만  | 잠재 고객 시스템이 구성 및 배포됩니다.                                      |
| 게시자 확인            | \-         | 제품이 라이브 상태가 되기 전에 최종 검토 및 확인을 완료 합니다. 또한 제품을 미리 볼 수 있는 링크가 있습니다. 미리 보기가 표시 되는 방법에 만족 했으면 **상태** 탭에서 **라이브 이동** 을 선택 합니다. 그러면 온 보 딩 팀이 AppSource에서 앱을 나열 하는 요청을 보냅니다.    |
| 라이브                         | 3시간 미만 | 이제 제품이 공개적으로 나열 되 고 ("live"), 고객이 앱을 보고 Power BI 구독에 배포할 수 있습니다. 확인 전자 메일도 받게 됩니다. **모든 제품** 탭의 오른쪽 열에서 모든 제품의 상태를 볼 수 있습니다. **상태** 탭에서 제품에 대 한 자세한 게시 흐름 상태를 볼 수 있습니다. |
|   |   |

이 프로세스가 완료 될 때까지 최대 8 일이 소요 됩니다. 이러한 게시 단계를 진행 한 후에는 Power BI 앱 제품이 [Appsource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI 앱 섹션에 나열 됩니다.


### <a name="app-certification-process"></a>앱 인증 프로세스

Microsoft 온 보 딩 팀은이 프로세스를 사용 하 여 Power BI 앱 제안 제출의 유효성을 검사 합니다.

1. 법적 문서와 도움말 링크를 검토 합니다.
2. 지원 연락처 정보를 확인 합니다.
3. 설치 관리자 URL을 사용 하 여 적절 한 설치를 확인 합니다.
4. 앱에서 맬웨어 및 기타 악의적인 콘텐츠를 검색 합니다.
5. 표시 된 콘텐츠가 앱의 설명과 일치 하는지 확인 합니다.
6. Power BI에서 앱 관련 작업이 예상 대로 작동 하는지 확인 합니다. 팀은 샘플 데이터가 포함 된 보고서 및 대시보드를 열고, 사용자 지정 데이터 원본에 연결 하 고, 데이터를 새로 고칩니다.

문제가 발견되면 인증 팀에서 피드백을 제공합니다.  Power BI 앱 요구 사항에 대 한 자세한 내용은 [Power BI 앱 설명서](https://go.microsoft.com/fwlink/?linkid=2028636)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

[Appsource marketplace](https://appsource.microsoft.com)에서 앱을 정기적으로 모니터링 하는 것이 좋습니다.  또한 [Cloud 파트너 포털](https://cloudpartner.azure.com/#insights) 의 [판매자 insights](../../cloud-partner-portal-orig/si-getting-started.md) 기능을 사용 하 여 marketplace 고객과 앱 사용에 대 한 통찰력을 얻을 수 있습니다. 마지막으로 제품을 [업데이트할](./cpp-update-existing-offer.md)수 있습니다.
