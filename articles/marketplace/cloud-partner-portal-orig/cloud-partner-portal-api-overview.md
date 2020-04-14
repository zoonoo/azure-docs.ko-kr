---
title: 클라우드 파트너 포털 API 참조 | Azure 마켓플레이스
description: 사용할 필수 조건에 대한 설명과 Marketplace API 작업 목록입니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256419"
---
# <a name="cloud-partner-portal-api-reference"></a>Cloud 파트너 포털 API 참조

> [!NOTE]
> 클라우드 파트너 포털 API는 파트너 센터와 통합되며 오퍼가 파트너 센터로 마이그레이션된 후에도 계속 작동합니다. 통합은 작은 변화를 도입합니다. 이 문서에 나열된 [CPP API 변경 내용을](#changes-to-cpp-apis-after-the-migration-to-partner-center) 검토하여 파트너 센터로 마이그레이션한 후에도 코드가 계속 작동하는지 확인합니다.

Cloud 파트너 포털 REST API를 사용하면 프로그래밍 방식으로 워크로드, 제안 및 게시자 프로필을 검색 및 조작할 수 있습니다. 이 API는 RBAC(역할 기반 액세스 제어)를 사용하여 처리 시에 올바른 권한을 적용합니다.

이 참조는 Cloud 파트너 포털 REST API에 대한 기술 세부 정보를 제공합니다. 이 문서의 페이로드 샘플은 참조용일 뿐이며 새 기능이 추가되면 변경될 수 있습니다.

## <a name="prerequisites-and-considerations"></a>필수 구성 요소 및 고려 사항

이 API를 사용하기 전에 다음을 검토해야 합니다.

- 계정에 서비스 주체를 추가하고 인증을 위한 Azure AD(Azure Active Directory) 액세스 토큰을 가져오는 방법을 알아보기 위한 [필수 구성 요소](./cloud-partner-portal-api-prerequisites.md) 문서
- 이러한 API를 호출하는 데 사용할 수 있는 두 가지 [동시성 제어](./cloud-partner-portal-api-concurrency-control.md) 전략입니다.
- 버전 관리 및 오류 처리와 같은 추가 API [고려 사항](./cloud-partner-portal-api-considerations.md)

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>파트너 센터로 마이그레이션한 후 CPP API 변경

| **API** | **변경 내용 설명** | **영향** |
| ------- | ---------------------- | ---------- |
| 포스트 게시, GoLive, 취소 | 마이그레이션된 오퍼의 경우 응답 헤더는 다른 형식을 가지지만 작업 상태를 검색하는 상대 경로를 나타내는 동일한 방식으로 계속 작동합니다. | 오퍼에 대한 해당 POST 요청을 보낼 때 위치 헤더는 오퍼의 마이그레이션 상태에 따라 두 가지 형식 중 하나를 갖습니다.<ul><li>마이그레이션되지 않은 오퍼<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>마이그레이션된 오퍼<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| 작업 수행 | 응답에서 이전에 '알림-전자 메일' 필드를 지원했던 오퍼 유형의 경우 이 필드는 더 이상 사용되지 않으며 마이그레이션된 오퍼에 대해 더 이상 반환되지 않습니다. | 마이그레이션된 오퍼의 경우 요청에 지정된 이메일 목록에 더 이상 알림이 전송되지 않습니다. 대신 API 서비스는 파트너 센터의 알림 이메일 프로세스와 일치하여 이메일을 보냅니다. 특히 파트너 센터의 계정 설정의 판매자 연락처 정보 섹션에 설정된 이메일 주소로 알림이 전송되어 운영 진행 상황을 알려줍니다.<br><br>파트너 센터의 [계정 설정의](https://partner.microsoft.com/dashboard/account/management) 판매자 연락처 정보 섹션에 설정된 이메일 주소를 검토하여 알림에 대한 올바른 이메일이 제공되는지 확인하십시오.  |

## <a name="common-tasks"></a>일반 작업

이 참조는 다음과 같은 일반적인 작업을 수행하기 위한 API를 자세히 설명합니다.

### <a name="offers"></a>제품

- [모든 제안 검색](./cloud-partner-portal-api-retrieve-offers.md)
- [특정 제안 검색](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [제안 상태 검색](./cloud-partner-portal-api-retrieve-offer-status.md)
- [제안 만들기](./cloud-partner-portal-api-creating-offer.md)
- [제안 게시](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>작업

- [작업 검색](./cloud-partner-portal-api-retrieve-operations.md)
- [작업 취소](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>앱 게시

- [실시간 전송](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>다른 작업

- [가상 머신 제안에 대한 가격 책정 설정](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>문제 해결

- [인증 오류 문제 해결](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
