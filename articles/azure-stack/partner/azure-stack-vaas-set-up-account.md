---
title: 서비스 계정으로 Azure Stack 유효성 검사 설정 | Microsoft Docs
description: 서비스 계정으로 유효성 검사를 설정 하는 방법에 알아봅니다.
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
ms.openlocfilehash: 3f87721dcf7485e15a473e82597f6bf4baeca659
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42139634"
---
# <a name="set-up-your-validation-as-a-service-account"></a>서비스 계정으로 유효성 검사 설정

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

유효성 검사 (VaaS) 서비스로 설계, 개발, 유효성 검사, 판매, 배포 및 시장에서 Azure Stack 솔루션을 지원 하려면 Microsoft와 공동 엔지니어링 계약을가지고 있는 Microsoft Azure Stack 파트너에 게 제공 되는 Azure 서비스입니다.

서비스와 유효성 검사에 대 한 시스템을 준비 하는 방법에 알아봅니다. Azure Active Directory 인스턴스를 설정 하 고 VaaS 사용 준비에 대 한 기타 필수 작업을 수행 합니다. 

작업은 다음과 같습니다.

- 로그를 저장할 Azure Storage blob 만들기
- 로컬 에이전트를 배포 합니다.
- Azure Stack 인스턴스를 테스트할 수의 테스트 이미지 가상 컴퓨터 다운로드

## <a name="create-an-azure-active-directory-tenant-id"></a>Azure Active Directory 테 넌 트 ID 만들기

1. Azure Active Directory 테 넌 트 만들기 합니다 [Azure portal](https://portal.azure.com) 하거나 기존 테 넌 트를 사용 하 여 합니다.

    특별히 사용 하기 위해 테 넌 트를 사용 하 여 만들려는 VaaS 설명이 포함 된 이름으로 같은 것이 좋습니다 ContosoVaaS@onmicrosoft.com합니다. 테 넌 트의 역할 기반 액세스 제어 (RBAC) 기능 VaaS를 사용할 수 있는 파트너 조직의 사용자를 관리 하는 파트너에 의해 사용 됩니다.  
    
    자세한 내용은 참조 하십시오 [Azure AD 디렉터리 관리](https://docs.microsoft.com/azure/active-directory/active-directory-administer)합니다.

    > [!Note]  
    > 새 Azure Active Directory 테 넌 트를 만드는 방법에 대 한 자세한 내용은 참조 하세요. [Azure AD 시작](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad)합니다.

2. 테 넌 트 서비스를 사용 하 여 지불 해야 하는 조직의 구성원을 추가 합니다. 각 사용자 VaaS 해당 액세스 수준을 제어 하려면 다음 역할 중 하나는 테 넌 트에 할당 합니다.

    | 역할 이름 | 설명 |
    |---------------------|------------------------------------------|
    | 소유자 | 모든 리소스에 전체 액세스할 수 있습니다. |
    | 읽기 권한자 | 모든 리소스를 볼 수 있지만 편집할 수는 없습니다. |
    | 테스트 참가자 | 테스트 리소스를 관리할 수 있습니다. |
    | 카탈로그 참가자 | 솔루션 게시 리소스를 관리할 수 있습니다. |

## <a name="set-up-your-tenant"></a>테 넌 트 설정

테 넌 트를 설정 합니다 **Azure Stack 유효성 검사 서비스** 응용 프로그램입니다. 

1. 테 넌 트에 대 한 다음 정보를 Microsoft에 보내기 vaashelp@microsoft.com합니다.

    | Data | 설명 |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | 조직 이름 | 공식 조직 이름입니다. |
    | Azure AD 테 넌 트 디렉터리 이름 | 등록할 azure AD 테 넌 트 디렉터리 이름입니다. |
    | Azure AD 테 넌 트 디렉터리 Id | Azure AD 테 넌 트 디렉터리의 디렉터리와 연결 된 GUID입니다.<br> Azure AD 테 넌 트 디렉터리 ID를 찾는 방법에 대 한 정보를 찾을 수 있습니다, 참조 "[테 넌 트 ID 가져오기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)." |

    

2. Azure Stack 팀 테 넌 트 VaaS 포털을 사용할 수 없다는 확인을 제공 합니다.

3. 테 넌 트의 전역 관리자 자격 증명을 사용 하 여 로그인 합니다 [VaaS 포털](https://azurestackvalidation.com/
)합니다. 선택 **내 계정**합니다.

    ![VaaS 포털에 로그인](media/vaas_portalsignin.png)

3. 사이트는 VaaS에 대 한 액세스 권한을 부여 하는 메시지가 표시 됩니다. 계속 하려면 약관에 동의 합니다.

## <a name="assign-user-roles"></a>사용자 역할 할당

사용자 역할을 할당 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 선택 **모든 서비스** > **Azure Active Directory** 에 **Identity** 그룹입니다.
3. 선택 **엔터프라이즈 응용 프로그램** > **Azure Stack 유효성 검사 서비스** 응용 프로그램입니다.
4. **사용자 및 그룹**을 선택합니다. 합니다 **Azure Stack 유효성 검사 서비스-사용자 및 그룹** 블레이드에서 응용 프로그램을 사용할 수 있는 권한 가진 사용자를 나열 합니다.
5. 선택 **+ 사용자 추가** 할당을 추가 합니다.

## <a name="create-an-azure-storage-blob-to-store-logs"></a>로그를 저장할 Azure 저장소 blob 만들기

VaaS는 유효성 검사 테스트를 실행 하는 경우 진단 로그를 만듭니다. 위치는 Azure blob 서비스 SAS URL에 로그를 저장 해야 합니다. 저장소 계정의 저장소 OEM 사용자 지정 패키지에 사용할 수도 있습니다.

다음이 단계를 설정 하 고 Azure storage 계정에 저장소-as a service (SAS) URI를 생성 하는 방법 및 포털에서 테스트를 시작할 때 VaaS 포털에서 저장소 계정을 지정할 수 있는 위치를 안내 합니다.

### <a name="create-an-azure-storage-account"></a>Azure 저장소 계정 만들기

1. 저장소 계정 만들기, 지침을 따릅니다 [저장소 계정 만들기](../../storage/common/storage-quickstart-create-account.md)합니다.

2. 저장소 계정의 형식을 선택할 때 선택 합니다 **Blob storage** 계정 유형입니다.

### <a name="generate-a-sas-url-for-the-storage-account"></a>저장소 계정의 SAS URL 생성

1. 위에서 만든 저장소 계정으로 이동 합니다.

2. 아래 블레이드에서 **설정을**를 선택 **공유 액세스 서명**합니다.

3. 검사만 **Blob** 에서 **허용 된 서비스 옵션** (나머지 선택 취소) 합니다.

4. 확인할 **서비스**, * * 컨테이너 및 **개체** 에서 **허용 되는 리소스 유형**합니다.

5. 확인 **읽기**, **작성**를 **목록**, **추가**를 **만들기** 에서 **권한 허용**  (나머지 선택 취소) 합니다.

6. 설정 **시작 시간** 은 현재 시간 및 **종료 시간** 현재 시점부터 3 개월입니다.

7. 선택 **SAS 생성 및 연결 문자열** 하 고 저장 합니다 **Blob service SAS URL** 문자열입니다.

> [!Note]  
> SAS URL의 URL을 생성할 때 설정 종료 시간에 만료 됩니다. 디버깅을 위해 제품 팀과 공유 하기 전에 URL이 유효한 충분히 테스트를 예약 하는 경우 URL 30 일 이상에 대 한 유효한 지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- VaaS 로컬 에이전트를 사용 하 여 하드웨어를 확인 합니다. 명령 참조 [로컬 에이전트 및 테스트 가상 머신 배포](azure-stack-vaas-test-vm.md)합니다.
- 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.