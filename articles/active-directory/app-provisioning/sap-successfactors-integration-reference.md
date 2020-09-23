---
title: Azure Active Directory 및 SAP SuccessFactors 통합 참조
description: SAP SuccessFactors-HR 기반 프로 비전에 대 한 기술 심층 살펴보기
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 805cdc0713afd43502bb224cce60167adbc418ee
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969530"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Azure Active Directory 프로비저닝이 SAP SuccessFactors와 통합 되는 방법 

[Azure Active Directory 사용자 프로 비전 서비스](../app-provisioning/user-provisioning.md) 는 [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 과 통합 되어 사용자의 id 수명 주기를 관리 합니다. Azure Active Directory는 미리 작성 된 세 가지 통합을 제공 합니다. 

* [SuccessFactors to 온-프레미스 Active Directory 사용자 프로 비전](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors 사용자 프로 비전을 Azure Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SuccessFactors 쓰기 저장](../saas-apps/sap-successfactors-writeback-tutorial.md)

이 문서에서는 통합이 작동 하는 방식과 여러 HR 시나리오에 대 한 프로 비전 동작을 사용자 지정 하는 방법을 설명 합니다. 

## <a name="establishing-connectivity"></a>연결 설정 
Azure AD 프로 비전 서비스는 기본 인증을 사용 하 여 Employee Central OData API 끝점에 연결 합니다. SuccessFactors 프로 비전 앱을 설정할 때 *관리자 자격 증명* 섹션에서 *테 넌 트 url* 매개 변수를 사용 하 여 [API 데이터 센터 URL](https://apps.support.sap.com/sap/support/knowledge/en/2215682)을 구성 합니다. 

Azure AD 프로 비전 서비스와 SuccessFactors 간의 연결을 더욱 안전 하 게 보호 하기 위해 아래에 설명 된 단계를 사용 하 여 SuccessFactors IP 허용 목록에 Azure AD IP 범위를 추가할 수 있습니다.

1. Azure 공용 클라우드의 [최신 IP 범위](https://www.microsoft.com/download/details.aspx?id=56519) 다운로드 
1. 파일을 열고 **AzureActiveDirectory** 태그를 검색 합니다. 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 범위](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. *AddressPrefixes* 요소 내에 나열 된 모든 ip 주소 범위를 복사 하 고 범위를 사용 하 여 ip 주소 제한 목록을 작성 합니다.
1. CIDR 값을 IP 범위로 변환 합니다.  
1. SuccessFactors 관리 포털에 로그인 하 여 허용 목록에 IP 범위를 추가 합니다. SAP [support note 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200)을 참조 하세요. 이제이 도구에 [IP 범위를 입력할](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) 수 있습니다. 

## <a name="supported-entities"></a>지원 되는 엔터티
SuccessFactors의 모든 사용자에 대해 Azure AD 프로 비전 서비스는 다음 엔터티를 검색 합니다. 각 엔터티는 OData API *$expand* 쿼리 매개 변수를 사용 하 여 확장 됩니다. 아래의 *검색 규칙* 열을 참조 하세요. 일부 엔터티는 기본적으로 확장 되지만 일부 엔터티는 매핑에 특정 특성이 있는 경우에만 확장 됩니다. 

| \# | SuccessFactors 엔터티                  | OData 노드     | 검색 규칙 |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *루트 노드*                  | 항상           |
| 2  | PerPersonal                            | personalInfoNav              | 항상           |
| 3  | PerPhone                               | phoneNav                     | 항상           |
| 4  | PerEmail                               | emailNav                     | 항상           |
| 5  | EmpEmployment                          | employmentNav                | 항상           |
| 6  | 사용자                                   | employmentNav/userNav        | 항상           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | 항상           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | 항상           |
| 9  | 회사                              | employmentNav/jobInfoNav/companyNav | `company`또는 `companyId` 특성만 매핑되는 경우에만 |
| 10 | 기타 부서                           | employmentNav/jobInfoNav/departmentNav | `department`또는 `departmentId` 특성만 매핑되는 경우에만 |
| 11 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | `businessUnit`또는 `businessUnitId` 특성만 매핑되는 경우에만 |
| 12 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | `costCenter`또는 `costCenterId` 특성만 매핑되는 경우에만 |
| 13 | 구분                             | employmentNav/jobInfoNav/divisionNav  | `division`또는 `divisionId` 특성만 매핑되는 경우에만 |
| 14 | 코드 코드                              | employmentNav/jobInfoNav/jobCodeNav  | `jobCode`또는 `jobCodeId` 특성만 매핑되는 경우에만 |
| 15 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | `payGrade`특성이 매핑되는 경우에만 |
| 16 | 위치                             | employmentNav/jobInfoNav/locationNav  | `location`특성이 매핑되는 경우에만 |
| 17 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | 매핑에 다음 특성 중 하나가 포함 된 경우: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 18 | 이벤트 이유                          | employmentNav/jobInfoNav/eventReasonNav  | `eventReason`특성이 매핑되는 경우에만 |
| 19 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | `assignmentType`가 매핑될 경우에만 |
| 20 | EmploymentType 선택 목록                | employmentNav/jobInfoNav/employmentTypeNav | `employmentType`가 매핑될 경우에만 |
| 21 | EmployeeClass 선택 목록                 | employmentNav/jobInfoNav/employeeClassNav | `employeeClass`가 매핑될 경우에만 |
| 22 | EmplStatus 선택 목록                    | employmentNav/jobInfoNav/emplStatusNav | `emplStatus`가 매핑될 경우에만 |
| 23 | AssignmentType 선택 목록                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | `assignmentType`가 매핑될 경우에만 |

## <a name="how-full-sync-works"></a>전체 동기화 작동 방법
특성 매핑을 기반으로 전체 동기화를 수행 하는 동안 Azure AD 프로 비전 서비스는 다음 "GET" OData API 쿼리를 전송 하 여 모든 활성 사용자의 유효 데이터를 가져옵니다. 

> [!div class="mx-tdCol2BreakAll"]
>| 매개 변수 | 설명 |
>| ----------|-------------|
>| OData API 호스트 | *테 넌 트 URL*에 https를 추가 합니다. 예: `https://api4.successfactors.com` |
>| OData API 끝점 | `/odata/v2/PerPerson` |
>| OData $format 쿼리 매개 변수 | `json` |
>| OData $filter 쿼리 매개 변수 | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand 쿼리 매개 변수 | 이 매개 변수 값은 매핑된 특성에 따라 다릅니다. 예: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize 쿼리 매개 변수 | `100` |

> [!NOTE]
> 첫 번째 초기 전체 동기화 중에 Azure AD 프로 비전 서비스는 비활성/종료 된 작업자 데이터를 가져오지 않습니다.

각 SuccessFactors 사용자에 대해 프로 비전 서비스는 매핑에 정의 된 일치 특성을 사용 하 여 대상 (Azure AD/온-프레미스 Active Directory)의 계정을 찾습니다. 예: *personIdExternal* 가 *employeeid* 에 매핑되고 일치 특성으로 설정 된 경우 프로 비전 서비스는 *personIdExternal* 값을 사용 하 여 *employeeid* 필터를 사용 하는 사용자를 검색 합니다. 사용자 일치 항목이 발견 되 면 대상 특성을 업데이트 합니다. 일치 항목을 찾을 수 없는 경우 대상에 새 항목을 만듭니다. 

특정에 대해 OData API 끝점에서 반환 된 데이터의 유효성을 검사 하려면 api `personIdExternal` `SuccessFactorsAPIEndpoint` 데이터 센터 서버 URL을 사용 하 여 아래 api 쿼리의를 업데이트 하 고 [postman](https://www.postman.com/downloads/) 과 같은 도구를 사용 하 여 쿼리를 호출 합니다. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>증분 동기화 작동 방법

전체 동기화 후에 Azure AD 프로 비전 서비스는 유지 관리 하 `LastExecutionTimestamp` 고이를 사용 하 여 증분 변경 내용을 검색 하는 델타 쿼리를 만듭니다. ,, 및와 같은 각 SuccessFactors 엔터티에 있는 타임 스탬프 특성은 `lastModifiedDateTime` `startDate` 변경 내용이 `endDate` `latestTerminationDate` 와 사이에 있는지 여부를 확인 하기 위해 평가 됩니다 `LastExecutionTimestamp` `CurrentExecutionTime` . 예 이면 항목 변경 내용이 적용 되 고 동기화를 위해 처리 되는 것으로 간주 됩니다. 

## <a name="reading-attribute-data"></a>특성 데이터 읽기

Azure AD 프로 비전 서비스 쿼리가 SuccessFactors 되 면 JSON 결과 집합을 검색 합니다. JSON 결과 집합은 Employee Central에 저장 된 여러 특성을 포함 합니다. 기본적으로 프로 비전 스키마는 해당 특성의 하위 집합만 검색 하도록 구성 됩니다. 

추가 특성을 검색 하려면 아래 나열 된 단계를 따르세요.
    
1. **엔터프라이즈 응용 프로그램**  ->  **SuccessFactors 앱**  ->  **프로 비전**  ->  **편집 프로**비전  ->  **특성 매핑 페이지**로 이동 합니다.
1. 아래로 스크롤하고 **고급 옵션 표시**를 클릭 합니다.
1. **SuccessFactors에 대 한 특성 목록 편집**을 클릭 합니다. 

   > [!NOTE] 
   > **SuccessFactors에 대 한 특성 목록 편집** 옵션이 Azure Portal에 표시 되지 않으면 URL을 사용 하 여 *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* 페이지에 액세스 합니다. 

1. 이 보기의 **API 식** 열에는 커넥터에서 사용 하는 JSONPath 식이 표시 됩니다.

   >[!div class="mx-imgBorder"] 
   >![API 식](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. 기존 JSONPath 값을 편집 하거나 유효한 JSONPath 식으로 새 특성을 스키마에 추가할 수 있습니다. 

다음 섹션에서는 JSONPath 값을 편집 하는 일반적인 시나리오의 목록을 제공 합니다. 

## <a name="handling-different-hr-scenarios"></a>여러 HR 시나리오 처리

JSONPath는 XML에 대 한 XPath와 유사한 JSON에 대 한 쿼리 언어입니다. XPath와 마찬가지로 JSONPath는 JSON 페이로드에서 데이터를 추출 하 고 필터링 할 수 있도록 합니다.

JSONPath 변환을 사용 하 여 Azure AD 프로 비전 앱의 동작을 사용자 지정 하 여 사용자 지정 특성을 검색 하 고 rehire, 작업자 변환 및 전역 할당과 같은 시나리오를 처리할 수 있습니다. 

이 섹션에서는 다음과 같은 HR 시나리오에 대 한 프로 비전 앱을 사용자 지정할 수 있는 방법을 설명 합니다. 
* [추가 특성 검색](#retrieving-additional-attributes)
* [사용자 지정 특성 검색](#retrieving-custom-attributes)
* [작업자 변환 시나리오 처리](#handling-worker-conversion-scenario)
* [Rehire 시나리오 처리](#handling-rehire-scenario)
* [전역 할당 시나리오 처리](#handling-global-assignment-scenario)
* [동시 작업 처리 시나리오](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>추가 특성 검색

기본 Azure AD SuccessFactors 프로 비전 앱 스키마는 [90 개 이상의 미리 정의 된 특성과](sap-successfactors-attribute-reference.md)함께 제공 됩니다. 프로 비전 스키마에 더 많은 SuccessFactors 특성을 추가 하려면 아래 나열 된 단계를 사용 합니다. 

1. 아래 OData 쿼리를 사용 하 여 Employee Central에서 유효한 테스트 사용자에 대 한 데이터를 검색 합니다. 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. 특성과 연결 된 Employee Central 엔터티를 확인 합니다.
   * 특성이 *EmpEmployment* 엔터티의 일부인 경우 *employmentNav* node 아래에서 특성을 찾습니다. 
   * 특성이 *사용자* 엔터티의 일부인 경우 *EmploymentNav/usernav* 노드 아래에서 특성을 찾습니다.
   * 특성이 *EmpJob* 엔터티의 일부인 경우 *EmploymentNav/jobInfoNav* 노드 아래에서 특성을 찾습니다. 
1. 특성과 연결 된 JSON 경로를 생성 하 고이 새 특성을 SuccessFactors 특성 목록에 추가 합니다. 
   * 예제 1: *employmentNav* 엔터티의 일부인 *Oktorehire*특성을 추가 하 고 JSONPath를 사용 하는 경우를 가정해 보겠습니다.`$.employmentNav.results[0].okToRehire`
   * 예제 2: *Usernav* 엔터티의 일부인 특성 *timeZone*를 추가 하 고 JSONPath를 사용 하는 경우를 가정해 보겠습니다.`$.employmentNav.results[0].userNav.timeZone`
   * 예 3: *Jobinfonav* 엔터티의 일부인 *flsastatus*특성을 추가 하려는 경우 JSONPath를 사용 합니다.`$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. 스키마를 저장합니다. 
1. 프로 비전을 다시 시작 합니다.

### <a name="retrieving-custom-attributes"></a>사용자 지정 특성 검색

기본적으로 다음과 같은 사용자 지정 특성은 Azure AD SuccessFactors 프로 비전 앱에서 미리 정의 됩니다. 
* *custom01-* 사용자 (usernav) 엔터티의 custom15
* *empNavCustomString1-empNavCustomString15* 라는 EmpEmployment (employmentNav) 엔터티의 *customString1-customString15*
* *customString1-customString15* (jobInfoNav) 엔터티의 *empJobNavCustomString1-empNavJobCustomString15*

직원 중심 인스턴스에서 *EmpJobInfo* 의 *customString35* attribute는 위치 설명을 저장 합니다. 이 값을 Active Directory *physicalDeliveryOfficeName* 특성으로 이동 하려고 합니다. 이 시나리오에 대 한 특성 매핑을 구성 하려면 아래 지정 된 단계를 사용 합니다. 

1. SuccessFactors 특성 목록을 편집 하 여 *empJobNavCustomString35*라는 새 특성을 추가 합니다.
1. 이 특성에 대 한 JSONPath API 식을 다음과 같이 설정 합니다. `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Azure Portal에서 매핑 변경을 저장 하 고 다시 로드 합니다.  
1. 특성 매핑 블레이드에서 *empJobNavCustomString35* 을 *physicalDeliveryOfficeName*에 매핑합니다.
1. 매핑을 저장 합니다.

이 시나리오를 확장 합니다. 
* *사용자* 엔터티에서 *custom35* attribute를 매핑하려면 JSONPath를 사용 합니다.`$.employmentNav.results[0].userNav.custom35`
* *EmpEmployment* 엔터티에서 *customString35* attribute를 매핑하려면 JSONPath를 사용 합니다.`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>작업자 변환 시나리오 처리

작업자 변환은 기존 정규 직원을 계약자 또는 계약자로 변환 하는 프로세스입니다. 이 시나리오에서 Employee Central은 동일한 *Person* 엔터티에 대해 새 *사용자* 엔터티와 함께 새 *EmpEmployment* 엔터티를 추가 합니다. 이전 *EmpEmployment* 엔터티 아래에 중첩 된 *사용자* 엔터티는 null로 설정 됩니다. 변환이 발생할 때 새 고용 데이터가 표시 되도록이 시나리오를 처리 하려면 아래 나열 된 단계를 사용 하 여 프로 비전 앱 스키마를 대량 업데이트할 수 있습니다.  

1. SuccessFactors 프로 비전 앱의 특성 매핑 블레이드를 엽니다. 
1. 아래로 스크롤하고 **고급 옵션 표시**를 클릭 합니다.
1. **여기에서 스키마 검토** 링크를 클릭 하 여 스키마 편집기를 엽니다. 

   >![스키마 편집기를 여는 여기의 스키마 검토 링크를 보여 주는 스크린샷](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. 편집 하기 전에 **다운로드** 링크를 클릭 하 여 스키마 복사본을 저장 합니다. 

   >![다운로드를 사용 하는 스키마 편집기를 보여 주는 스크린샷 스키마 복사본을 저장 하려면 선택 합니다.](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. 스키마 편집기에서 Ctrl + H 키를 눌러 찾기-바꾸기 컨트롤을 엽니다.
1. 찾기 텍스트 상자에서 값을 복사 하 여 붙여넣습니다. `$.employmentNav.results[0]`
1. 바꾸기 텍스트 상자에서 값을 복사 하 여 붙여넣습니다 `$.employmentNav.results[?(@.userNav != null)]` . 연산자 주위의 공백은 `!=` JSONPath 식을 성공적으로 처리 하는 데 중요 합니다. 
   >![찾기-바꾸기-변환](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. "모두 바꾸기" 옵션을 클릭 하 여 스키마를 업데이트 합니다. 
1. 스키마를 저장합니다. 
1. 위의 프로세스는 다음과 같이 모든 JSONPath 식을 업데이트 합니다. 
   * 이전 JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 새 JSONPath: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. 프로 비전을 다시 시작 합니다. 

### <a name="handling-rehire-scenario"></a>Rehire 시나리오 처리

일반적으로 다시 고용를 처리 하는 두 가지 옵션이 있습니다. 
* 옵션 1: Employee Central에서 새 사람 프로필 만들기
* 옵션 2: Employee Central에서 기존 사용자 프로필 다시 사용

HR 프로세스에서 옵션 1을 사용 하는 경우 프로 비전 스키마를 변경할 필요가 없습니다. HR 프로세스에서 옵션 2를 사용 하는 경우 Employee Central은 동일한 *Person* 엔터티에 대해 새 *사용자* 엔터티와 함께 새 *EmpEmployment* 엔터티를 추가 합니다. 변환 시나리오와 달리 이전 *EmpEmployment* 엔터티의 *사용자* 엔터티는 null로 설정 되지 않습니다. 

다시 고용 프로필에 대 한 최신 고용 데이터가 표시 되도록이 rehire 시나리오 (옵션 2)를 처리 하려면 아래 나열 된 단계를 사용 하 여 프로 비전 앱 스키마를 대량 업데이트할 수 있습니다.  

1. SuccessFactors 프로 비전 앱의 특성 매핑 블레이드를 엽니다. 
1. 아래로 스크롤하고 **고급 옵션 표시**를 클릭 합니다.
1. **여기에서 스키마 검토** 링크를 클릭 하 여 스키마 편집기를 엽니다.   
1. 편집 하기 전에 **다운로드** 링크를 클릭 하 여 스키마 복사본을 저장 합니다.   
1. 스키마 편집기에서 Ctrl + H 키를 눌러 찾기-바꾸기 컨트롤을 엽니다.
1. 찾기 텍스트 상자에서 값을 복사 하 여 붙여넣습니다. `$.employmentNav.results[0]`
1. 바꾸기 텍스트 상자에서 값을 복사 하 여 붙여넣습니다 `$.employmentNav.results[-1:]` . 이 JSONPath 식은 최신 *EmpEmployment* 레코드를 반환 합니다.   
1. "모두 바꾸기" 옵션을 클릭 하 여 스키마를 업데이트 합니다. 
1. 스키마를 저장합니다. 
1. 위의 프로세스는 다음과 같이 모든 JSONPath 식을 업데이트 합니다. 
   * 이전 JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 새 JSONPath: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. 프로 비전을 다시 시작 합니다. 

이 스키마 변경은 작업자 변환 시나리오도 지원 합니다. 

### <a name="handling-global-assignment-scenario"></a>전역 할당 시나리오 처리

Employee Central의 사용자가 전역 할당에 대해 처리 되 면 SuccessFactors는 새 *EmpEmployment* 엔터티를 추가 하 고 *ASSIGNMENTCLASS* 를 "GA"로 설정 합니다. 또한 새 *사용자* 엔터티를 만듭니다. 따라서 사용자에 게는 다음이 포함 됩니다.
* *EmpEmployment*  +  *AssignmentClass* 가 "ST"로 설정 된 home 할당에 해당 하는 1 개의 EmpEmployment*사용자* 엔터티입니다. 
* *EmpEmployment*  +  *AssignmentClass* 가 "GA"로 설정 된 전역 할당에 해당 하는 다른 EmpEmployment*사용자* 엔터티입니다.

표준 할당 및 전역 할당 사용자 프로필에 속한 특성을 페치 하려면 아래 나열 된 단계를 사용 합니다. 

1. SuccessFactors 프로 비전 앱의 특성 매핑 블레이드를 엽니다. 
1. 아래로 스크롤하고 **고급 옵션 표시**를 클릭 합니다.
1. **여기에서 스키마 검토** 링크를 클릭 하 여 스키마 편집기를 엽니다.   
1. 편집 하기 전에 **다운로드** 링크를 클릭 하 여 스키마 복사본을 저장 합니다.   
1. 스키마 편집기에서 Ctrl + H 키를 눌러 찾기-바꾸기 컨트롤을 엽니다.
1. 찾기 텍스트 상자에서 값을 복사 하 여 붙여넣습니다. `$.employmentNav.results[0]`
1. 바꾸기 텍스트 상자에서 값을 복사 하 여 붙여넣습니다 `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
1. "모두 바꾸기" 옵션을 클릭 하 여 스키마를 업데이트 합니다. 
1. 스키마를 저장합니다. 
1. 위의 프로세스는 다음과 같이 모든 JSONPath 식을 업데이트 합니다. 
   * 이전 JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 새 JSONPath: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. 앱의 특성 매핑 블레이드를 다시 로드 합니다. 
1. 아래로 스크롤하고 **고급 옵션 표시**를 클릭 합니다.
1. **SuccessFactors에 대 한 특성 목록 편집**을 클릭 합니다.
1. 새 특성을 추가 하 여 전역 할당 데이터를 인출 합니다. 예를 들어 전역 할당 프로필에 연결 된 부서 이름을 페치 하려는 경우 JSONPath 식이로 설정 된 *globalAssignmentDepartment* 특성을 추가할 수 있습니다 `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
1. 이제 두 부서 값을 Active Directory 특성으로 이동 하거나 식 매핑을 사용 하 여 선택적으로 값을 전달할 수 있습니다. 예: 아래 식은 AD *부서* 특성의 값을 *globalAssignmentDepartment* 로 설정 하 고, 다른 값은 표준 할당과 연결 된 *학과* 로 설정 합니다. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. 매핑을 저장 합니다. 
1. 프로 비전을 다시 시작 합니다. 

### <a name="handling-concurrent-jobs-scenario"></a>동시 작업 처리 시나리오

Employee Central의 사용자에 게 동시/여러 작업이 있는 경우 *AssignmentClass* 가 "ST"로 설정 된 두 개의 *EmpEmployment* 및 *사용자* 엔터티가 있습니다. 두 작업에 모두 속하는 특성을 페치 하려면 아래 나열 된 단계를 사용 합니다. 

1. SuccessFactors 프로 비전 앱의 특성 매핑 블레이드를 엽니다. 
1. 아래로 스크롤하고 **고급 옵션 표시**를 클릭 합니다.
1. **SuccessFactors에 대 한 특성 목록 편집**을 클릭 합니다.
1. 작업 1 및 작업 2와 연결 된 부서를 풀 려 한다고 가정해 보겠습니다. 미리 정의 된 특성 *부서* 는 첫 번째 작업에 대 한 학과의 가치를 이미 페치합니다. *SecondJobDepartment* 라는 새 특성을 정의 하 고 JSONPath 식을로 설정할 수 있습니다.`$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. 이제 두 부서 값을 Active Directory 특성으로 이동 하거나 식 매핑을 사용 하 여 선택적으로 값을 전달할 수 있습니다. 
1. 매핑을 저장 합니다. 
1. 프로 비전을 다시 시작 합니다. 

## <a name="writeback-scenarios"></a>쓰기 저장 시나리오

이 섹션에서는 다양 한 쓰기/뒤로 시나리오에 대해 설명 합니다. SuccessFactors에서 전자 메일 및 전화 번호를 설정 하는 방법에 따라 구성 방법을 권장 합니다.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>전화 및 전자 메일 쓰기에 대해 지원 되는 시나리오 

| \# | 시나리오 요구 사항 | 전자 메일 기본 <br> 플래그 값 | 회사 전화 <br> 기본 플래그 값 | 휴대폰 <br> 기본 플래그 값 | 회사 전화 <br> 매핑 | 휴대폰 <br> 매핑 |
|--|--|--|--|--|--|--|
| 1 | * 비즈니스 전자 메일을 기본으로 설정 합니다. <br> * 전화 번호를 설정 하지 않습니다. | true | true | false | \[설정되지 않음\] | \[설정되지 않음\] | 
| 2 | * SuccessFactors, 비즈니스 전자 메일 및 회사 전화는 주입니다. <br> * 항상 Azure AD 전화 번호를 회사 전화로 이동 하 고 휴대폰을 휴대폰으로 이동 합니다. | true | true | false | telephoneNumber | mobile | 
| 3 | * SuccessFactors, 비즈니스 전자 메일 및 휴대폰은 기본입니다. <br> * 항상 Azure AD 전화 번호를 회사 전화로 이동 하 고 휴대폰을 휴대폰으로 이동 합니다. | true | false | true |  telephoneNumber | mobile | 
| 4 | * SuccessFactors 비즈니스 전자 메일이 기본입니다. <br> * Azure AD에서 직장 전화 번호가 있는지 확인 하 고 (있는 경우) 휴대폰 번호가 있는지 확인 하 고 휴대폰 번호가 있는 경우에만 회사 전화 번호를 기본으로 표시를 선택 합니다. | true | 식 매핑 사용: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | 식 매핑 사용: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobile | 
| 5 | * SuccessFactors 비즈니스 전자 메일 및 회사 전화는 주입니다. <br> * Azure AD에서 mobile을 사용할 수 있는 경우 회사 전화로 설정 하 고, 그렇지 않으면 telephoneNumber를 사용 합니다. | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[설정되지 않음\] | 

* 다시 쓰기 특성 매핑에 전화 번호에 대 한 매핑이 없는 경우에는 전자 메일만 쓰기 상태로 포함 됩니다.
* 직원 중앙에서 신규 고용 온 보 딩 중에는 비즈니스 전자 메일 및 전화 번호를 사용할 수 없습니다. 온 보 딩 중에 비즈니스 전자 메일 및 회사 전화를 기본으로 설정 하는 것이 필수 인 경우 새 고용 생성 중에 회사 전화 및 전자 메일에 대 한 더미 값을 설정할 수 있습니다 .이 값은 나중에 나중 쓰기 앱에서 업데이트 됩니다.
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>전화 및 전자 메일 쓰기를 지원 하지 않는 시나리오

* 직원 중앙에서 개인 전자 메일 및 개인 전화를 등록 하는 동안 기본으로 설정 됩니다. 나중 쓰기 앱은이 설정을 전환 하 고 비즈니스 전자 메일 및 회사 전화를 기본으로 설정할 수 없습니다.
* Employee Central에서 근무처 전화는 주로 설정 됩니다. 나중 쓰기 앱은이를 변경 하 고 휴대폰으로 휴대폰을 설정할 수 없습니다.
* 나중 쓰기 앱은 현재 기본 플래그 설정을 읽고 쓰기 작업에 동일한 값을 사용할 수 없습니다. 특성 매핑에 구성 된 플래그 값은 항상 사용 됩니다. 

## <a name="next-steps"></a>다음 단계

* [Active Directory 프로 비전에 대해 SuccessFactors를 구성 하는 방법을 알아봅니다.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors에 쓰기 저장을 구성 하는 방법 알아보기](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [인바운드 프로 비전에 대해 지원 되는 SuccessFactors 특성에 대 한 자세한 정보](sap-successfactors-attribute-reference.md)










