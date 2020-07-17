---
title: Azure Active Directory를 사용 하 여 요청 시 사용자 프로 비전
description: 강제 동기화
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 78a56b6a848139c47d7934a47decb126afe00b7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85297531"
---
# <a name="on-demand-provisioning"></a>주문형 프로 비전
주문형 프로 비전을 사용 하면 몇 초 안에 응용 프로그램에 사용자를 프로 비전 할 수 있습니다. 이 기능을 사용 하 여 구성 문제를 신속 하 게 해결 하 고, 정의한 식의 유효성을 검사 하 고, 범위 지정 필터를 테스트할 수 있습니다. 

## <a name="how-to-use-on-demand-provisioning"></a>주문형 프로 비전을 사용 하는 방법 

1. **Azure Portal**에 로그인합니다.
2. **엔터프라이즈 애플리케이션**으로 이동합니다.
3. 응용 프로그램을 선택 하 고 프로 비전 구성 페이지로 이동 합니다.
4. 관리자 자격 증명을 제공 하 여 프로 비전을 구성 합니다.
5. **주문형 프로 비전**을 클릭 합니다.
6. 이름, 성, 표시 이름, 사용자 계정 이름 또는 전자 메일을 사용 하 여 사용자를 검색 합니다.
7. 페이지 맨 아래에서 프로 비전을 선택 합니다.

## <a name="understanding-the-provisioning-steps"></a>프로 비전 단계 이해
주문형 프로 비전 기능은 사용자를 프로 비전 할 때 프로 비전 서비스에서 수행 하는 단계를 표시 하려고 합니다. 일반적으로 사용자를 프로 비전 하는 데 5 단계가 있으며, 아래 단계 중 하나 이상이 주문형 프로 비전 환경에 표시 됩니다.

### <a name="step-1-test-connection"></a>1 단계: 연결 테스트
프로 비전 서비스는 "테스트 사용자"에 대 한 요청을 수행 하 여 대상 응용 프로그램에 대 한 액세스 권한을 부여 하려고 합니다. 프로 비전 서비스에는 프로 비전 단계를 계속할 수 있는 권한이 있음을 나타내는 응답이 필요 합니다. 이 단계는 단계가 실패 한 경우에만 표시 됩니다. 단계가 성공 하면 주문형 프로 비전 환경에 표시 되지 않습니다. 

**문제 해결 팁**
* 비밀 토큰 및 테 넌 트 URL과 같이 대상 응용 프로그램에 유효한 자격 증명을 제공 했는지 확인 합니다. 필요한 자격 증명은 응용 프로그램에 따라 달라 집니다. 자세한 구성 자습서는 [여기](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)를 참조 하세요. 
* 대상 응용 프로그램이 특성 매핑 블레이드에서 정의한 일치 특성에 대 한 필터링을 지원 하는지 확인 합니다. 응용 프로그램이 지 원하는 필터를 이해 하려면 응용 프로그램 개발자가 제공 하는 API 설명서를 확인 해야 할 수 있습니다.  
* SCIM 응용 프로그램의 경우 postman과 같은 도구를 사용 하 여 Azure AD 프로 비전 서비스에서 예상 하는 응용 프로그램이 권한 부여 요청에 응답 하는지 확인할 수 있습니다. 예제 요청은 [여기](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3)에서 찾을 수 있습니다.

### <a name="step-2-import-user"></a>2 단계: 사용자 가져오기
다음으로 프로 비전 서비스는 원본 시스템에서 사용자를 검색 합니다. 서비스에서 검색 하는 사용자 특성은 나중에 사용자가 프로 비전 범위에 있는지 여부를 평가 하 고, 대상 시스템에서 기존 사용자를 확인 하 고, 대상 시스템으로 내보낼 사용자 특성을 결정 하는 데 사용 됩니다. 

**세부 정보 보기**

세부 정보 보기 섹션에는 원본 시스템에서 가져온 사용자의 속성 (예: Azure AD)이 표시 됩니다.

**문제 해결 팁**
* 원본 시스템의 사용자 개체에 일치 하는 특성이 없으면 사용자 가져오기에 실패할 수 있습니다. 일치 특성의 값으로 사용자 개체를 업데이트 하거나 프로 비전 구성에서 일치 하는 특성을 변경 하 여이 오류를 해결할 수 있습니다.  
* 가져온 목록에 필요한 특성이 없는 경우 원본 시스템의 사용자 개체에 대 한 값이 특성에 포함 되어 있는지 확인 합니다. 프로 비전 서비스는 현재 null 특성의 프로 비전을 지원 하지 않습니다. 
* 프로 비전 구성 특성 매핑 페이지에 예상 되는 특성이 포함 되어 있는지 확인 합니다. 

### <a name="step-3-determine-if-user-is-in-scope"></a>3 단계: 사용자가 범위 내에 있는지 확인
다음으로 프로 비전 서비스는 사용자가 프로 비전 [범위](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) 에 있는지 여부를 확인 합니다. 서비스는 사용자가 응용 프로그램에 할당 되었는지 여부, 범위를 동기화 할당 됨 또는 모두 동기화로 설정할지 여부, 프로 비전 구성에 정의 된 범위 지정 필터 등의 측면을 고려 합니다.  

**세부 정보 보기**

세부 정보 보기 섹션에는 평가 된 범위 지정 조건이 표시 됩니다. 다음 속성 중 하나가 표시 될 수 있습니다.
* **원본 시스템에서 활성** 은 사용자가 Azure AD에서 속성이 true로 설정 되어 있음을 나타냅니다.
* **응용 프로그램에 할당** 됨은 사용자가 Azure AD의 응용 프로그램에 할당 됨을 나타냅니다.
* **모든 범위 동기화** 는 범위 설정이 테 넌 트의 모든 사용자 및 그룹을 허용 함을 나타냅니다.
* **사용자가 필요한 역할** 은 사용자에 게 응용 프로그램에 프로 비전 하는 데 필요한 역할이 있음을 나타냅니다. 
* 응용 프로그램에 대 한 범위 지정 필터를 정의한 경우에도 **범위 지정 필터가** 표시 됩니다. 필터는 다음과 같은 형식으로 표시 됩니다. {범위 지정 필터 제목} {범위 지정 필터 특성} {범위 지정 필터 연산자} {범위 지정 필터 값}. 

**문제 해결 팁**
* 올바른 범위 지정 역할을 정의 했는지 확인 하세요. 예를 들어 정수 값이 아닌 값을 사용 하 여 ["보다 큼"](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) 연산자를 사용 하지 마세요. 
* 사용자에 게 필요한 역할이 없으면 [여기](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role)에 설명 된 팁을 검토 합니다. 

### <a name="step-4-match-user-between-source-and-target"></a>4 단계: 원본 및 대상 간에 사용자 일치
이 단계에서는 서비스는 가져오기 단계에서 검색 된 사용자를 대상 시스템의 사용자와 일치 시 키 려 고 시도 합니다. 

**세부 정보 보기**

세부 정보 보기 페이지는 대상 시스템에서 일치 된 사용자의 속성을 표시 합니다. 컨텍스트 창에 표시 되는 속성은 다음과 같이 달라 집니다.
* 대상 시스템에서 일치 하는 사용자가 없는 경우에는 속성이 표시 되지 않습니다.
* 대상 시스템에서 한 명의 사용자가 일치 하는 경우 대상 시스템에서 일치 하는 사용자의 속성이 표시 됩니다.
* 여러 사용자가 일치 하는 경우 일치 하는 사용자의 속성이 모두 표시 됩니다.
* 특성 매핑의 일부가 일치 하는 특성이 여러 개인 경우 일치 하는 각 특성은 순차적으로 평가 되 고 일치 하는 사용자는 표시 됩니다. 

**문제 해결 세부 정보**
* 프로 비전 서비스에서 원본의 사용자를 대상의 사용자와 고유 하 게 일치 시킬 수 없습니다. 이는 일치 특성이 고유한 지 확인 하 여 해결할 수 있습니다. 
* 대상 응용 프로그램에서 일치 하는 특성으로 정의 된 특성에 대 한 필터링을 지원 하는지 확인 합니다.  

### <a name="step-5-perform-action"></a>5 단계: 작업 수행
마지막으로 프로 비전 서비스는 사용자 만들기, 업데이트, 삭제 또는 건너뛰기를 같은 작업을 수행 합니다. 

**세부 정보 보기**

세부 정보 보기 섹션에는 대상 응용 프로그램에서 수정 된 특성이 표시 됩니다. 이는 프로 비전 서비스 활동의 최종 출력과 내보낸 특성을 나타냅니다. 이 단계가 실패 하면 표시 되는 특성은 프로 비전 서비스에서 수정 하려고 시도 하는 특성을 나타냅니다.  

**문제 해결 팁**
* 변경 내용 내보내기에 대 한 오류는 크게 다를 수 있습니다. 일반적인 오류는 프로 비전 로그 [설명서](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) 를 참조 하세요.


## <a name="frequently-asked-questions"></a>자주 묻는 질문
**주문형 프로 비전을 사용 하려면 프로 비전을 해제 해야 하나요?** 권한 부여를 위해 수명이 긴 전달자 토큰 또는 사용자 이름 및 암호를 사용 하는 응용 프로그램의 경우 추가 단계가 필요 하지 않습니다. 권한 부여를 위해 OAuth를 사용 하는 응용 프로그램은 현재 주문형 프로 비전을 사용 하기 전에 프로 비전 작업이 중지 되어야 합니다. G Suite, Box, Facebook 별 작업 공간 및 여유 공간과 같은 응용 프로그램은이 범주에 속합니다. 프로 비전을 중지할 필요 없이 모든 응용 프로그램에 대해 주문형 프로 비전을 실행할 수 있도록 하는 작업이 진행 중입니다. 

**주문형 프로 비전은 얼마나 걸립니까?** 일반적으로 30 초 이내에 수행 됩니다. 

## <a name="known-limitations"></a>알려진 제한 사항
오늘 몇 가지 알려진 제한 사항이 있습니다. 향후 개선 사항에 대 한 우선 순위를 높일 수 있도록 [UserVoice](https://aka.ms/appprovisioningfeaturerequest) 에 게시 하세요. 이러한 제한 사항은 주문형 프로 비전 기능과 관련이 있습니다. 응용 프로그램에서 프로 비전 그룹, 삭제 등을 지원 하는지 여부에 대 한 자세한 내용은 응용 프로그램 자습서를 확인 하세요. 

* Workday, AWS, SuccessFactors 응용 프로그램은 주문형 프로 비전을 지원 하지 않습니다.
* 요청 시의 프로 비전 그룹과 역할은 지원 되지 않습니다.
* 사용자 및 그룹의 비활성화 또는 삭제는 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [프로 비전 문제 해결](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
