---
title: Azure Stack 포털에 대 한 서비스로 유효성 검사를 사용 하 여 첫 번째 테스트를 예약 하려면 | Microsoft Docs
description: 서비스로 Azure Stack 포털에 대 한 유효성 검사를 사용 하 여 첫 번째 테스트를 예약 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 8ff78f46283d3ee4baa7310aa56bcdabde85f2cc
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159625"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>빠른 시작: 첫 번째 테스트를 예약 하려면 서비스 포털로 유효성 검사 사용

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

하드웨어를 확인 하려면 서비스 (VaaS) 포털로 유효성 검사를 사용 하 여 첫 번째 테스트를 예약 하는 방법에 알아봅니다. 로컬 에이전트는 유효성 검사 테스트를 실행 하기 전에 유효성이 검사 되는 Azure Stack 솔루션에 배포 되어야 합니다.

이 빠른 시작에서 수행할 추가 솔루션 하 고 테스트를 실행 합니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 수행 하기 전에 하도록 해야 합니다.
 - 서비스 계정으로 유효성을 검사 합니다. 자세한 내용은 [서비스 계정으로 유효성 검사 설정](azure-stack-vaas-set-up-account.md)합니다.  
- 시스템에 설치 하는 로컬 에이전트입니다. 자세한 내용은 [로컬 에이전트 및 테스트 가상 머신 배포](azure-stack-vaas-test-vm.md)합니다.

## <a name="add-a-new-solution"></a>새 솔루션 추가

1. 에 로그인 합니다 [유효성 검사 포털](https://azurestackvalidation.com)합니다.

    ![유효성 검사 포털에 로그인](media/vaas_portalsignin.png)  

2. 선택 **새 솔루션**합니다.
3. 솔루션에 대 한 이름을 입력 하 고 선택 **저장할**합니다.

## <a name="create-a-solution-validation-workflow"></a>유효성 검사 워크플로 솔루션 만들기

1. 솔루션 이름을 선택 합니다.
2. 선택 **관리** 에 **솔루션 유효성 검사** 바둑판식으로 배열 합니다.

    ![솔루션의 유효성 검사](media/image2.png)

## <a name="create-a-solution-workflow"></a>솔루션 워크플로 만들기

1. 선택 **새 솔루션 유효성 검사**합니다.
2. 유효성 검사의 이름을 입력 합니다.
3. 선택 **최소** 하거나 **최대**합니다.  
    - **최소**  
    솔루션은 지원 되는 최소 노드 수를 사용 하 여 구성 됩니다.  
    - **최대값**  
    솔루션은 지원 되는 최대 노드 수를 사용 하 여 구성 됩니다.
4. 환경 매개 변수를 추가 합니다. 자세한 내용은 [환경 매개 변수 추가](#add-environmental-parameters)합니다.
5. 일반적인 테스트 매개 변수를 추가 합니다. 자세한 내용은 [일반적인 테스트 매개 변수 추가](#add-common-test-parameters)합니다.

    테스트 정의 따라 테스트 공통 매개 변수를 독립적으로 값을 입력 해야 할 수 있습니다 또는 공통 매개 변수 값을 재정의할 수 있습니다.
6. 클릭 **제출** 테스트를 예약 합니다.

## <a name="add-environmental-parameters"></a>환경 매개 변수 추가

다음 환경 매개 변수를 추가 합니다.

| 테스트 단계 정보 | 필수 | 설명 |
| --- | --- | --- | --- |
| Azure Stack 빌드 | 필수 | Azure Stack 빌드를 유효한 Azure Stack 빌드 번호 또는 예를 들어 1.0.170330.9 버전 (예: 20170501.1) 숫자 값 이어야 합니다 |
| 테넌트 ID | 필수 | Active Directory 테 넌 트 id입니다. GUID (예를 들어 ECA23256-6BA0-4F27-8E4D-AFB02F088363) 이어야 합니다. |
| 지역 | 필수 | Azure stack 배포 |
| 테 넌 트 Resource Manager 끝점 | 필수 | (예: 테 넌 트 Azure Resource Manager 작업에 대 한 끝점 https://management.loc-ext.domain.com) |
| 관리자 Resource Manager 끝점 | 필요하지 않음 | (예: 테 넌 트 Azure Resource Manager 작업에 대 한 끝점 https://management.loc-ext.domain.com) |
| 외부 FQDN | 필요하지 않음 | 외부 정규화 된 도메인 이름 끝점에 대 한 접미사로 사용 합니다. (예: local.azurestack.external 또는 redmond.contoso.com) |
| 노드 수 | 필수 | 솔루션의 노드 수입니다. |

## <a name="add-common-test-parameters"></a>일반 테스트 매개 변수를 추가 합니다.

다음 일반적인 테스트 매개 변수를 추가 합니다.

| 테스트 단계 정보 | 필수 | 설명 |
| --- | --- | --- |
| 테 넌 트 사용자 이름 | 필수 | 사용자를 테 넌 트 (예를 들어 tenant@contoso.onmicrosoft.com) |
| 테 넌 트 암호 | 필수 | 테 넌 트에 대 한 암호입니다. |
| 서비스 관리자 사용자 이름 | 필요하지 않음 | 사용자를 테 넌 트 (예를 들어 tenant@contoso.onmicrosoft.com) |
| 서비스 관리자 암호 | 필요하지 않음 | 서비스 관리자 사용자 이름 (예를 들어 serviceadmin@contoso.onmicrosoft.com) |
| 클라우드 관리자 사용자 이름 | 필요하지 않음 | Azure Stack 도메인 관리자 계정 (예를 들어 contoso\cloudadmin) |
| 클라우드 관리자 암호 | 필요하지 않음 | |
|  진단 연결 문자열 | 필요하지 않음 | SAS URI는 진단 테스트 실행 하는 동안 로그 복사 될 Azure Storage 계정입니다. 참조 [로그를 저장할 Azure 저장소 blob 만들기](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)합니다. <br><br>값을 **진단 연결 문자열이** 일반 매개 변수를 서비스에서 저장 하 고이 매개 변수를 사용 하는 워크플로의 모든 테스트를 예약 된 시간에 제공 됩니다. SAS URL의 만료 30 일 이내 이면 공용 매개 변수 페이지에서 새 SAS URL에 대해 묻는 메시지가 나타납니다. |
| 태그-이름 | 필요하지 않음 |  워크플로 레이블을 설명 태그를 입력할 수 있습니다. 태그의 이름입니다. |
| 태그-값 | 필요하지 않음 | 워크플로 레이블을 설명 태그를 입력할 수 있습니다. 태그의 값입니다. |

## <a name="next-steps"></a>다음 단계

- [새 Azure Stack 솔루션의 유효성을 검사합니다](azure-stack-vaas-validate-solution-new.md)  
- 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.
