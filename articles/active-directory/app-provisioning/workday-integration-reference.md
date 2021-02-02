---
title: Azure Active Directory 및 Workday 통합 참조
description: Workday-HR 기반 프로 비전에 대 한 기술 심층 살펴보기
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: f260bca196839a091ae7d12be6d5f85912bf92db
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255987"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Azure Active Directory 프로비저닝이 Workday와 통합 되는 방법

[Azure Active Directory 사용자 프로 비전 서비스](../app-provisioning/user-provisioning.md) 는 [Workday hcm](https://www.workday.com) 과 통합 되어 사용자의 id 수명 주기를 관리 합니다. Azure Active Directory는 미리 작성 된 세 가지 통합을 제공 합니다. 

* [Workday에서 온-프레미스 Active Directory 사용자 프로 비전](../saas-apps/workday-inbound-tutorial.md)
* [Workday Azure Active Directory 사용자 프로 비전](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday 쓰기 저장](../saas-apps/workday-writeback-tutorial.md)

이 문서에서는 통합이 작동 하는 방식과 여러 HR 시나리오에 대 한 프로 비전 동작을 사용자 지정 하는 방법을 설명 합니다. 

## <a name="establishing-connectivity"></a>연결 설정 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Azure AD 끝점에 대 한 Workday API 액세스 제한
Azure AD 프로 비전 서비스는 기본 인증을 사용 하 여 Workday 웹 서비스 API 끝점에 연결 합니다.  

Azure AD 프로 비전 서비스와 Workday 간의 연결을 더욱 안전 하 게 보호 하기 위해 지정 된 통합 시스템 사용자가 허용 되는 Azure AD IP 범위에서 Workday Api에만 액세스 하도록 액세스를 제한할 수 있습니다. Workday 테 넌 트에서 다음 구성을 완료 하려면 Workday 관리자에 게 문의 하세요. 

1. Azure 공용 클라우드의 [최신 IP 범위](https://www.microsoft.com/download/details.aspx?id=56519) 를 다운로드 합니다. 
1. 파일을 열고 **AzureActiveDirectory** 태그를 검색 합니다. 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 범위](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. *AddressPrefixes* 요소 내에 나열 된 모든 ip 주소 범위를 복사 하 고 범위를 사용 하 여 ip 주소 목록을 작성 합니다.
1. Workday 관리 포털에 로그인 합니다. 
1. **Ip 범위 유지 관리** 작업에 액세스 하 여 Azure 데이터 센터에 대 한 새 ip 범위를 만듭니다. IP 범위 (CIDR 표기법 사용)를 쉼표로 구분 된 목록으로 지정 합니다.  
1. 인증 정책 **관리** 작업에 액세스 하 여 새 인증 정책을 만듭니다. 인증 정책에서 인증 허용 목록을 사용 하 여 Azure AD IP 범위 및이 IP 범위에서 액세스를 허용 하는 보안 그룹을 지정 합니다. 변경 내용을 저장합니다. 
1. 변경 내용을 확인 하려면 **모든 보류 중인 인증 정책 변경 내용 활성화** 작업에 액세스 합니다.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>제한 된 보안 그룹을 사용 하 여 Workday의 작업자 데이터에 대 한 액세스 제한

[Workday 통합 시스템 사용자를 구성](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) 하는 기본 단계에서는 workday 테 넌 트의 모든 사용자를 검색할 수 있는 액세스 권한을 부여 합니다. 특정 통합 시나리오에서 특정 감독 기관과 이러한 조직에만 속하는 사용자가 Get_Workers API 호출에서 반환 되 고 Workday Azure AD 커넥터에서 처리 되도록 액세스를 제한할 수 있습니다. 

Workday 관리자에 게 작업 하 고 제한 된 통합 시스템 보안 그룹을 구성 하 여이 요구 사항을 수행할 수 있습니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [이 workday 커뮤니티 문서](https://community.workday.com/forums/customer-questions/620393) 를 참조 하세요 (*이 문서에 액세스 하려면 workday 커뮤니티 로그인 자격 증명이 필요 함*).

제한 된 ISSG (통합 시스템 보안 그룹)를 사용 하 여 액세스를 제한 하는 이러한 전략은 특히 다음과 같은 시나리오에서 유용 합니다. 
* **단계적 출시 시나리오**: workday 테 넌 트가 크고 Workday에서 Azure AD 자동화 된 프로 비전을 단계적으로 출시 하는 계획입니다. 이 시나리오에서는 Azure AD 범위 지정 필터를 사용 하 여 현재 단계의 범위에 없는 사용자를 제외 하는 것이 아니라, 범위 내 작업자만 Azure AD에 표시 되도록 제한 된 ISSG를 구성 하는 것이 좋습니다.
* **여러 프로 비전 작업 시나리오**: 각각 다른 비즈니스 단위/부서/회사를 지 원하는 대규모 Workday 테 넌 트와 여러 AD 도메인이 있습니다. 이 토폴로지를 지원 하려면 특정 작업자 집합을 프로 비전 하는 각 작업을 사용 하 여 여러 Workday에서 Azure AD 프로 비전 작업을 실행 하려고 합니다. 이 시나리오에서는 Azure AD 범위 지정 필터를 사용 하 여 작업자 데이터를 제외 하는 대신, 관련 작업자 데이터만 Azure AD에 표시 되도록 제한 된 ISSG를 구성 하는 것이 좋습니다. 

### <a name="workday-test-connection-query"></a>Workday 테스트 연결 쿼리

Workday에 대 한 연결을 테스트 하기 위해 Azure AD는 다음 *Get_Workers* Workday 웹 서비스 요청을 보냅니다. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>전체 동기화 작동 방법

Workday 기반 프로 비전의 컨텍스트에서 **전체 동기화** 는 workday에서 모든 id를 가져오고 각 작업자 개체에 적용할 프로 비전 규칙을 결정 하는 프로세스를 말합니다. 처음으로 프로 비전을 켜면 Azure Portal 또는 Graph Api를 사용 하 여 *프로 비전을 다시 시작* 하는 경우 전체 동기화가 수행 됩니다. 

Azure AD는 다음 *Get_Workers* Workday 웹 서비스 요청을 보내 작업자 데이터를 검색 합니다. 이 쿼리는 전체 동기화 실행에 해당 하는 시간 동안 유효 날짜가 있는 모든 작업자 항목에 대해 Workday 트랜잭션 로그를 조회 합니다. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
*Response_Group* 노드는 Workday에서 인출할 작업자 특성을 지정 하는 데 사용 됩니다. *Response_Group* 노드의 각 플래그에 대 한 설명은 WORKDAY [Get_Workers API 설명서](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)를 참조 하세요. 

*Response_Group* 노드에 지정 된 특정 플래그 값은 WORKDAY Azure AD 프로 비전 응용 프로그램에 구성 된 특성을 기반으로 계산 됩니다. 플래그 값을 설정 하는 데 사용 되는 조건에 대해 *지원 되는 엔터티에* 대 한 섹션을 참조 하세요. 

위의 쿼리에 대해 Workday의 *Get_Workers* 응답에는 작업자 레코드 수 및 페이지 수를 포함 합니다.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
결과 집합의 다음 페이지를 검색 하기 위해 다음 *Get_Workers* 쿼리는 페이지 번호를 *Response_Filter* 의 매개 변수로 지정 합니다.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Azure AD 프로 비전 서비스는 각 페이지를 처리 하 고 전체 동기화 중에 모든 유효 작업자를 반복 합니다. Workday에서 가져온 각 작업자 항목에 대해 다음을 수행 합니다.
* 이 [XPATH 식은](workday-attribute-reference.md) Workday에서 특성 값을 검색 하는 데 적용 됩니다.
* 특성 매핑 및 일치 규칙이 적용 됩니다. 
* 서비스는 대상 (Azure AD/AD)에서 수행할 작업을 결정 합니다. 

처리가 완료 되 면 전체 동기화의 시작과 관련 된 타임 스탬프를 워터 마크로 저장 합니다. 이 워터 마크는 증분 동기화 주기의 시작 지점으로 사용 됩니다. 

## <a name="how-incremental-sync-works"></a>증분 동기화 작동 방법

전체 동기화 후에 Azure AD 프로 비전 서비스는 유지 관리 하 `LastExecutionTimestamp` 고이를 사용 하 여 증분 변경 내용을 검색 하는 델타 쿼리를 만듭니다. 증분 동기화 중에 Azure AD는 다음 유형의 쿼리를 Workday로 보냅니다. 

* [수동 업데이트 쿼리](#query-for-manual-updates)
* [개시 날짜 업데이트 및 종료 쿼리](#query-for-effective-dated-updates-and-terminations)
* [미래의 날짜 고용 쿼리](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>수동 업데이트 쿼리

다음 *Get_Workers* 는 마지막 실행과 현재 실행 시간 사이에 발생 한 수동 업데이트에 대 한 쿼리를 요청 합니다. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>개시 날짜 업데이트 및 종료 쿼리

다음 *Get_Workers* 는 마지막 실행과 현재 실행 시간 사이에 발생 한 개시 날짜 업데이트에 대 한 쿼리를 요청 합니다. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>미래의 날짜 고용 쿼리

위의 쿼리에서 이후 날짜 고용를 반환 하는 경우 다음 *Get_Workers* 요청을 사용 하 여 향후 발표 된 새 채용에 대 한 정보를 가져옵니다. 새 고용의 *WID* 특성은 조회를 수행 하는 데 사용 되 고 개시 날짜는 채용 날짜 및 시간으로 설정 됩니다. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>작업자 데이터 특성 검색

*Get_Workers* API는 작업자와 연결 된 다른 데이터 집합을 반환할 수 있습니다. 프로 비전 스키마에 구성 된 [XPATH API 식](workday-attribute-reference.md) 에 따라 Azure AD 프로 비전 서비스는 Workday에서 검색할 데이터 집합을 결정 합니다. 따라서 *Response_Group* 플래그는 *Get_Workers* 요청에 설정 됩니다. 

아래 표에서는 특정 데이터 집합을 검색 하는 데 사용할 구성에 대 한 지침을 제공 합니다. 

| \# | Workday 엔터티                       | 기본적으로 포함됨 | 기본이 아닌 엔터티를 인출 하기 위해 매핑할 지정 하는 XPATH 패턴입니다.             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | 개인 데이터                        | 예                 | wd: Worker \_ data/wd: 개인 \_ 데이터                                             |
| 2  | 고용 데이터                      | 예                 | wd: Worker \_ 데이터/wd: 고용 \_ 데이터                                           |
| 3  | 추가 작업 데이터                  | 예                 | wd: worker \_ data/wd: 고용 \_ 데이터/wd: 작업자 \_ 작업 \_ 데이터 \[ @wd:Primary \_ 작업 = 0\]|
| 4  | 조직 데이터                    | 예                 | wd: Worker \_ data/wd: 조직 \_ 데이터                                         |
| 5  | 관리 체인 데이터                | 예                 | wd: Worker \_ data/wd: 관리 \_ 체인 \_ 데이터                                    |
| 6  | 감독 기관과 이러한 조직             | 예                 | 감독 기관과 이러한                                                                 |
| 7  | 회사                              | 예                 | 회사가                                                                     |
| 8  | 조합                        | 아니요                  | ' 사업부 \_ '                                                              |
| 9  | 비즈니스 단위 계층 구조              | 아니요                  | ' 사업부 \_ \_ 계층 구조 '                                                   |
| 10 | 회사 계층 구조                    | 아니요                  | ' 회사 \_ 계층 구조 '                                                          |
| 11 | Cost Center                          | 아니요                  | ' 비용 \_ 센터 '                                                                |
| 12 | 비용 센터 계층 구조                | 아니요                  | ' 비용 \_ 센터 \_ 계층 구조 '                                                     |
| 13 | 모금                                 | 아니요                  | 모금                                                                        |
| 14 | 기금 계층                       | 아니요                  | ' 펀드 \_ 계층 구조 '                                                             |
| 15 | 선물                                 | 아니요                  | 선물                                                                        |
| 16 | 선물 계층                       | 아니요                  | ' 선물 \_ 계층 구조 '                                                             |
| 17 | 허용                                | 아니요                  | 부여                                                                       |
| 18 | 계층 구조 부여                      | 아니요                  | ' 권한 부여 \_ 계층 구조 '                                                            |
| 19 | 비즈니스 사이트 계층 구조              | 아니요                  | ' 비즈니스 \_ 사이트 \_ 계층 구조 '                                                   |
| 20 | 행렬 조직                  | 아니요                  | 행렬                                                                      |
| 21 | 지불 그룹                            | 아니요                  | ' 지불 \_ 그룹 '                                                                  |
| 22 | 프로그램                             | 아니요                  | 프로그램인                                                                    |
| 23 | 프로그램 계층 구조                    | 아니요                  | ' 프로그램 \_ 계층 구조 '                                                          |
| 24 | Region                               | 아니요                  | ' 지역 \_ 계층 구조 '                                                           |
| 25 | 위치 계층 구조                   | 아니요                  | ' 위치 \_ 계층 구조 '                                                         |
| 26 | 계정 프로 비전 데이터            | 아니요                  | wd: Worker \_ data/wd: 계정 \_ 프로 비전 \_ 데이터                                |
| 27 | 백그라운드 확인 데이터                | 아니요                  | wd: Worker \_ data/wd: Background \_ Check \_ 데이터                                    |
| 28 | 혜택 자격 데이터             | 아니요                  | wd: Worker \_ data/wd: 혜택 \_ 자격 \_ 데이터                                 |
| 29 | 혜택 등록 데이터              | 아니요                  | wd: Worker \_ data/wd: 혜택 \_ 등록 \_ 데이터                                  |
| 30 | 커리어 데이터                          | 아니요                  | wd: Worker \_ 데이터/wd: 커리어 \_ 데이터                                               |
| 31 | 보정 데이터                    | 아니요                  | wd: Worker \_ data/wd: 보정 \_ 데이터                                         |
| 32 | 불확정 작업자 세금 기관 데이터 | 아니요                  | wd: Worker \_ Data/wd: 불확정 \_ 작업자 \_ 세금 \_ 기관 \_ 양식 \_ 형식 \_ 데이터       |
| 33 | 개발 항목 데이터                | 아니요                  | wd: Worker \_ data/wd: 개발 \_ 항목 \_ 데이터                                    |
| 34 | 직원 계약 데이터              | 아니요                  | wd: Worker \_ data/wd: Employee \_ 계약 \_ 데이터                                  |
| 35 | 직원 검토 데이터                 | 아니요                  | wd: Worker \_ 데이터/wd: 직원 \_ 검토 \_ 데이터                                     |
| 36 | 사용자 의견 수신 데이터               | 아니요                  | wd: Worker \_ data/wd: 피드백 \_ 수신 \_ 데이터                                   |
| 37 | 작업자 목표 데이터                     | 아니요                  | wd: Worker \_ data/wd: worker \_ 목표 \_ 데이터                                         |
| 38 | 사진 데이터                           | 아니요                  | wd: Worker \_ 데이터/wd: 사진 \_ 데이터                                                |
| 39 | 자격 데이터                   | 아니요                  | wd: Worker \_ data/wd: 자격 \_ 데이터                                        |
| 40 | 관련 된 사람 데이터                 | 아니요                  | wd: Worker \_ data/wd: 관련 된 \_ 사람 \_ 데이터                                     |
| 41 | 역할 데이터                            | 아니요                  | wd: Worker \_ 데이터/wd: 역할 \_ 데이터                                                 |
| 42 | 기술 데이터                           | 아니요                  | wd: Worker \_ data/wd: 기술 \_ 데이터                                                |
| 43 | 연속 프로필 데이터              | 아니요                  | wd: Worker \_ data/wd: 연속 \_ 프로필 \_ 데이터                                  |
| 44 | 인재 평가 데이터               | 아니요                  | wd: Worker \_ data/wd: 인재 \_ 평가 \_ 데이터                                   |
| 45 | 사용자 계정 데이터                    | 아니요                  | wd: Worker \_ data/wd: 사용자 \_ 계정 \_ 데이터                                        |
| 46 | 작업자 문서 데이터                 | 아니요                  | wd: Worker \_ data/wd: worker \_ 문서 \_ 데이터                                     |

>[!NOTE]
>테이블에 나열 된 각 Workday 엔터티는 Workday의 **도메인 보안 정책** 에 의해 보호 됩니다. 올바른 XPATH를 설정한 후 엔터티와 연결 된 특성을 검색할 수 없는 경우 Workday 관리자에 게 문의 하 여 해당 도메인 보안 정책이 프로 비전 앱과 연결 된 통합 시스템 사용자에 대해 구성 되어 있는지 확인 합니다. 예를 들어 *기술 데이터* 를 검색 하려면 Workday 도메인 *작업자 데이터: 기술 및 환경* 에 대 *한 액세스 권한이* 필요 합니다. 

특정 요구 사항을 충족 하기 위해 Workday 통합을 확장 하는 방법에 대 한 몇 가지 예는 다음과 같습니다. 

**예제 1**

Workday에서 다음 데이터 집합을 검색 하 고 프로 비전 규칙에서 사용 하려는 경우를 가정해 보겠습니다.

* 비용 센터
* 비용 센터 계층 구조
* 지불 그룹

위의 데이터 집합은 기본적으로 포함 되어 있지 않습니다. 이러한 데이터 집합을 검색 하려면:
1. Azure Portal에 로그인 하 고 Workday를 AD/Azure AD 사용자 프로 비전 앱으로 엽니다. 
1. 프로 비전 블레이드에서 매핑을 편집 하 고 고급 섹션에서 Workday 특성 목록을 엽니다. 
1. 다음 특성 정의를 추가 하 고 "Required"로 표시 합니다. 이러한 특성은 AD 또는 Azure AD의 특성에 매핑되지 않습니다. 이는 비용 센터, 비용 센터 계층 및 지불 그룹 정보를 검색 하는 커넥터에 대 한 신호로만 사용 됩니다. 

     > [!div class="mx-tdCol2BreakAll"]
     >| 특성 이름 | XPATH API 식 |
     >|---|---|
     >| CostCenterHierarchyFlag  |  wd: Worker/wd: Worker_Data/wd: Organization_Data/wd: Worker_Organization_Data [wd: Organization_Data/wd: Organization_Type_Reference/wd: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  wd: Worker/wd: Worker_Data/wd: Organization_Data/wd: Worker_Organization_Data [wd: Organization_Data/wd: Organization_Type_Reference/wd: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER ']/wd: Organization_Data/wd: Organization_Code/text () |
     >| PayGroupFlag  |  wd: Worker/wd: Worker_Data/wd: Organization_Data/wd: Worker_Organization_Data [wd: Organization_Data/wd: Organization_Type_Reference/wd: ID [ @wd:type = ' Organization_Type_ID '] = ' PAY_GROUP ']/wd: Organization_Data/wd: Organization_Reference_ID/text () |

1. 비용 센터 및 지불 그룹 데이터 집합을 *Get_Workers* 응답에서 사용할 수 있게 되 면 아래 XPATH 값을 사용 하 여 비용 센터 이름, 비용 센터 코드 및 급여 그룹을 검색할 수 있습니다. 

     > [!div class="mx-tdCol2BreakAll"]
     >| 특성 이름 | XPATH API 식 |
     >|---|---|
     >| CostCenterName  | wd: Worker/wd: Worker_Data/wd: Organization_Data/wd: Worker_Organization_Data/wd: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' Cost Center ']/wd: Organization_Name/text () |
     >| CostCenterCode | wd: Worker/wd: Worker_Data/wd: Organization_Data/wd: Worker_Organization_Data/wd: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' Cost Center ']/wd: Organization_Code/text () |
     >| PayGroup | wd: Worker/wd: Worker_Data/wd: Organization_Data/wd: Worker_Organization_Data/wd: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' 종 량 그룹 ']/wd: Organization_Name/text () |

**예제 2**

사용자와 연결 된 인증을 검색 하려는 경우를 가정해 보겠습니다. 이 정보는 *자격 데이터* 집합의 일부로 사용할 수 있습니다. 이 데이터 집합을 *Get_Workers* 응답의 일부로 가져오려면 다음 XPATH를 사용 합니다. 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**예 3**

작업자에 할당 된 *프로 비전 그룹* 을 검색 하려는 경우를 가정해 보겠습니다. 이 정보는 *계정 프로 비전 데이터* 집합의 일부로 사용할 수 있습니다. 이 데이터 집합을 *Get_Workers* 응답의 일부로 가져오려면 다음 XPATH를 사용 합니다. 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>다음 단계

* [Workday Active Directory 프로 비전을 구성 하는 방법 알아보기](../saas-apps/workday-inbound-tutorial.md)
* [Workday에 대 한 쓰기를 다시 구성 하는 방법 알아보기](../saas-apps/workday-writeback-tutorial.md)
* [인바운드 프로비저닝에 지원되는 Workday 특성에 대해 자세히 알아봅니다.](workday-attribute-reference.md)

