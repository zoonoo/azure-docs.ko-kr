---
title: SAP 성공 요인 특성 참조 | 마이크로 소프트 문서
description: SuccessFactors에서 지원되는 속성 알아보기 성공요인-HR 기반 프로비저닝
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522359"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP 성공 요인 특성 참조

## <a name="supported-successfactors-entities-and-attributes"></a>지원되는 성공요소 엔터티 및 특성

아래 표는 다음 두 프로비저닝 앱에서 지원하는 SuccessFactors 특성 목록을 캡처합니다. 
* [활성 디렉터리 사용자 프로비저닝에 대한 성공 요인](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Azure AD 사용자 프로비저닝에 대한 성공 요인](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | 성공요인 엔티티                  | 성공 요인 속성     | 작업 유형 |
|----|----------------------------------------|------------------------------|----------------|
| 1  | 퍼퍼스(Perperson)                              | personId외부             | 읽기           |
| 2  | 퍼퍼스(Perperson)                              | personId                     | 읽기           |
| 3  | 퍼퍼스(Perperson)                              | 퍼퍼스누이드                | 읽기           |
| 4  | 퍼스널 퍼스널                            | displayName                  | 읽기           |
| 5  | 퍼스널 퍼스널                            | firstName                    | 읽기           |
| 6  | 퍼스널 퍼스널                            | gender                       | 읽기           |
| 7  | 퍼스널 퍼스널                            | lastName                     | 읽기           |
| 8  | 퍼스널 퍼스널                            | middleName                   | 읽기           |
| 9  | 퍼스널 퍼스널                            | 기본 설정 이름                | 읽기           |
| 10 | 사용자                                   | 주소라인1                 | 읽기           |
| 11 | 사용자                                   | 주소라인2                 | 읽기           |
| 12 | 사용자                                   | 주소LIne3                 | 읽기           |
| 13 | 사용자                                   | 비즈니스 폰                | 읽기           |
| 14 | 사용자                                   | 핸드폰                    | 읽기           |
| 15 | 사용자                                   | city                         | 읽기           |
| 16 | 사용자                                   | country                      | 읽기           |
| 17 | 사용자                                   | 사용자 정의01                     | 읽기           |
| 18 | 사용자                                   | 사용자 정의02                     | 읽기           |
| 19 | 사용자                                   | 사용자 정의03                     | 읽기           |
| 20 | 사용자                                   | 사용자 정의04                     | 읽기           |
| 21 | 사용자                                   | 사용자 정의05                     | 읽기           |
| 22 | 사용자                                   | 사용자 정의06                     | 읽기           |
| 23 | 사용자                                   | 사용자 정의07                     | 읽기           |
| 24 | 사용자                                   | 사용자 정의08                     | 읽기           |
| 25 | 사용자                                   | 사용자 정의09                     | 읽기           |
| 26 | 사용자                                   | 사용자 정의10                     | 읽기           |
| 27 | 사용자                                   | 사용자 정의11                     | 읽기           |
| 28 | 사용자                                   | 사용자 정의12                     | 읽기           |
| 29 | 사용자                                   | 사용자 정의13                     | 읽기           |
| 30 | 사용자                                   | 사용자 정의14                     | 읽기           |
| 31 | 사용자                                   | Empid                        | 읽기           |
| 32 | 사용자                                   | homePhone                    | 읽기           |
| 33 | 사용자                                   | 작업 가족                    | 읽기           |
| 34 | 사용자                                   | nickname                     | 읽기           |
| 35 | 사용자                                   | state                        | 읽기           |
| 36 | 사용자                                   | timeZone                     | 읽기           |
| 37 | 사용자                                   | 사용자 이름                     | 읽기           |
| 38 | 사용자                                   | 우편 번호                      | 읽기           |
| 39 | 폰 (동음이의)                               | 영역 코드                     | 읽기           |
| 40 | 폰 (동음이의)                               | countryCode                  | 읽기           |
| 41 | 폰 (동음이의)                               | 확장                    | 읽기           |
| 42 | 폰 (동음이의)                               | phoneNumber                  | 읽기           |
| 43 | 폰 (동음이의)                               | 전화 유형                    | 읽기           |
| 44 | 이메일 당                               | emailAddress                 | 읽기, 쓰기    |
| 45 | 이메일 당                               | 이메일 유형                    | 읽기           |
| 46 | Emp고용                          | 퍼스트데이트작업              | 읽기           |
| 47 | Emp고용                          | 라스트데이트작업               | 읽기           |
| 48 | Emp고용                          | userId                       | 읽기           |
| 49 | Emp고용                          | is스우발워커           | 읽기           |
| 50 | 엠프잡                                 | 컨트리오브컴퍼니             | 읽기           |
| 51 | 엠프잡                                 | 엠플상태                   | 읽기           |
| 52 | 엠프잡                                 | endDate                      | 읽기           |
| 53 | 엠프잡                                 | startDate                    | 읽기           |
| 54 | 엠프잡                                 | jobTitle                     | 읽기           |
| 55 | 엠프잡                                 | position                     | 읽기           |
| 65 | 엠프잡                                 | 사용자 지정 문자열13               | 읽기           |
| 56 | 엠프잡                                 | Managerid                    | 읽기           |
| 57 | 엠포잡\.비즈니스유닛                   | 비즈니스 단위                 | 읽기           |
| 58 | 엠포잡\.비즈니스유닛                   | 비즈니스유니드               | 읽기           |
| 59 | 엠프잡\.컴퍼니                        | company                      | 읽기           |
| 60 | 엠프잡\.컴퍼니                        | companyId                    | 읽기           |
| 61 | Empjob\.\.회사 국가등록 | 두 개의차르컨트리코드           | 읽기           |
| 62 | 엠포잡\.코스트센터                     | costCenter                   | 읽기           |
| 63 | 엠포잡\.코스트센터                     | 비용센터Id                 | 읽기           |
| 64 | 엠포잡\.코스트센터                     | 비용센터 설명        | 읽기           |
| 65 | 엠프잡\.                     | department                   | 읽기           |
| 66 | 엠프잡\.                     | departmentId                 | 읽기           |
| 67 | 엠프잡\.디비전                       | division                     | 읽기           |
| 68 | 엠프잡\.디비전                       | 디비전Id                   | 읽기           |
| 69 | 엠포잡\.작업 코드                        | 작업 코드                      | 읽기           |
| 70 | 엠포잡\.작업 코드                        | 작업코드ID                    | 읽기           |
| 71 | 엠프잡\.위치                       | 위치 이름                 | 읽기           |
| 72 | 엠프잡\.위치                       | 사무실위치주소        | 읽기           |
| 73 | 엠프잡\.위치                       | 오피스위치시티           | 읽기           |
| 74 | 엠프잡\.위치                       | 오피스위치사용자 정의 스트링4  | 읽기           |
| 75 | 엠프잡\.위치                       | 오피스위치우편코드        | 읽기           |
| 76 | 엠포잡\.페이그레이드                       | 급여 그레이드                     | 읽기           |
| 77 | Emp고용해지               | 액티브 채용 카운트       | 읽기           |
| 78 | Emp고용해지               | 최신 TerminationDate        | 읽기           |


## <a name="default-attribute-mapping"></a>기본 특성 매핑

아래 표는 위에 나열된 SuccessFactors 특성과 AD/Azure AD 특성 간의 기본 특성 매핑을 제공합니다. Azure AD 프로비저닝 앱 "매핑" 블레이드에서 이 기본 매핑을 수정하여 위의 목록의 특성을 포함할 수 있습니다. 

| \# | 성공요인 엔티티                  | 성공 요인 속성 | 기본 광고/Azure AD 특성 매핑   | 처리 비고                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | 퍼퍼스(Perperson)                              | personId외부         | employeeId                              | 일치하는 특성으로 사용                                                                   |
| 2  | 퍼퍼스(Perperson)                              | 퍼퍼스누이드            | \[소스 앵커로 사용되는 매핑되지 \- 않음\] | 초기 동기화 중에 프로비저닝 서비스는 personUuid를 기존 objectGuid\에 연결합니다.  |
| 3  | 퍼스널 퍼스널                            | displayName              | displayName                             | 해당 없음                                                                                           |
| 4  | 퍼스널 퍼스널                            | firstName                | givenName                               | 해당 없음                                                                                           |
| 5  | 퍼스널 퍼스널                            | lastName                 | sn                                      | 해당 없음                                                                                           |
| 6  | 사용자                                   | 주소라인1             | streetAddress                           | 해당 없음                                                                                           |
| 7  | 사용자                                   | city                     | l                                       | 해당 없음                                                                                           |
| 8  | 사용자                                   | country                  | co                                      | 해당 없음                                                                                           |
| 9  | 사용자                                   | state                    | st                                      | 해당 없음                                                                                           |
| 10 | 사용자                                   | 사용자 이름                 | samAccountName                          | 해당 없음                                                                                           |
| 11 | 사용자                                   | 우편 번호                  | postalCode                              | 해당 없음                                                                                           |
| 12 | 이메일 당                               | emailAddress             | mail                                    | 해당 없음                                                                                           |
| 13 | 엠프잡                                 | jobTitle                 | title                                   | 해당 없음                                                                                           |
| 14 | 엠프잡                                 | Managerid                | manager                                 | 해당 없음                                                                                           |
| 15 | Empjob\.\.회사 국가등록 | 두 개의차르컨트리코드       | c                                       | 해당 없음                                                                                           |
| 16 | 엠프잡\.                     | department               | department                              | 해당 없음                                                                                           |
| 17 | 엠프잡\.디비전                       | division                 | company                                 | 해당 없음                                                                                           |
| 18 | 엠프잡\.위치                       | 사무실위치주소    | streetAddress                           | 해당 없음                                                                                           |
| 19 | 엠프잡\.위치                       | 오피스위치우편코드    | postalCode                              | 해당 없음                                                                                           |
| 20 | Emp고용해지               | 액티브 채용 카운트   | accountEnabled                          | 활성고용계산=0인 경우 계정을 비활성화합니다\.                                           |

