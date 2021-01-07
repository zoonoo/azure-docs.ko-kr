---
title: Cloud 파트너 포털 API 참조-Microsoft 상업적 marketplace
description: 사용할 필수 조건에 대한 설명과 Marketplace API 작업 목록입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 57d3d1c6bb14db3eb2ca499069934a628d2f7fea
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425773"
---
# <a name="cloud-partner-portal-api-reference"></a>Cloud 파트너 포털 API 참조

> [!NOTE]
> Cloud 파트너 포털 Api는와 통합 되며 파트너 센터에서 계속 작업 합니다. 전환에는 작은 변화가 도입 됩니다. 파트너 센터로 전환 하 고 나면이 문서에 나열 된 [CPP api에 대 한 변경 내용을](#changes-to-cpp-apis-after-the-migration-to-partner-center) 검토 하 여 코드가 계속 작동 하는지 확인 합니다. CPP Api는 파트너 센터로 전환 하기 전에 이미 통합 된 기존 제품에만 사용 해야 합니다. 새 제품은 파트너 센터 제출 Api를 사용 해야 합니다.

Cloud 파트너 포털 REST API를 사용하면 프로그래밍 방식으로 워크로드, 제안 및 게시자 프로필을 검색 및 조작할 수 있습니다. Api는 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 처리 시 올바른 권한을 적용 합니다.

이 참조는 Cloud 파트너 포털 REST API에 대한 기술 세부 정보를 제공합니다. 이 문서의 페이로드 샘플은 참조용일 뿐이며 새 기능이 추가되면 변경될 수 있습니다.

## <a name="prerequisites-and-considerations"></a>필수 구성 요소 및 고려 사항

이 API를 사용하기 전에 다음을 검토해야 합니다.

- 계정에 서비스 주체를 추가하고 인증을 위한 Azure AD(Azure Active Directory) 액세스 토큰을 가져오는 방법을 알아보기 위한 [필수 구성 요소](./cloud-partner-portal-api-prerequisites.md) 문서
- 이러한 Api를 호출 하는 데 사용할 수 있는 두 가지 [동시성 제어](./cloud-partner-portal-api-concurrency-control.md) 전략입니다.
- 버전 관리 및 오류 처리와 같은 추가 API [고려 사항](./cloud-partner-portal-api-considerations.md)

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>파트너 센터로 마이그레이션한 후의 CPP Api 변경 내용

| **API** | **변경 내용 설명** | **영향** |
| ------- | ---------------------- | ---------- |
| POST 게시, GoLive, 취소 | 마이그레이션된 제품의 경우 응답 헤더의 형식은 서로 다르지만 작업 상태를 검색하기 위한 상대 경로를 나타내는 것과 동일한 방식으로 계속 작동합니다. | 제품에 대한 해당 POST 요청을 전송하는 경우 위치 헤더는 제품의 마이그레이션 상태에 따라 다음 두 가지 형식 중 하나가 됩니다.<ul><li>마이그레이션되지 않은 제품<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>마이그레이션된 제품<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 작업 | 이전에 응답에서 ' 알림-전자 메일 ' 필드를 지 원하는 제품 형식의 경우이 필드는 더 이상 사용 되지 않으며 마이그레이션된 제안에 대해 더 이상 반환 되지 않습니다. | 마이그레이션된 제품의 경우 요청에 지정된 이메일 목록에 더 이상 알림을 보내지 않습니다. 대신, API 서비스는 이메일을 보내기 위해 파트너 센터의 알림 이메일 프로세스와 정렬됩니다. 특히 알림은 파트너 센터에서 계정 설정의 판매자 연락처 정보 섹션에 설정된 이메일 주소에 전송되어 작업 진행률을 알립니다.<br><br>파트너 센터에서 [계정 설정](https://partner.microsoft.com/dashboard/account/management) 의 판매자 연락처 정보 섹션에 있는 전자 메일 주소 집합을 검토 하 여 알림에 올바른 전자 메일이 제공 되는지 확인 하세요.  |

## <a name="common-tasks"></a>일반 작업

이 참조는 다음과 같은 일반적인 작업을 수행하기 위한 API를 자세히 설명합니다.

### <a name="offers"></a>제품

- [모든 제안 검색](./cloud-partner-portal-api-retrieve-offers.md)
- [특정 제안 검색](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [제안 상태 검색](./cloud-partner-portal-api-retrieve-offer-status.md)
- [제품 만들기](./cloud-partner-portal-api-creating-offer.md)
- [제안 게시](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>작업

- [작업 검색](./cloud-partner-portal-api-retrieve-operations.md)
- [작업 취소](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>앱 게시

- [실시간 전송](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>기타 작업

- [가상 머신 제안에 대한 가격 책정 설정](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>문제 해결

- [인증 오류 문제 해결](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
