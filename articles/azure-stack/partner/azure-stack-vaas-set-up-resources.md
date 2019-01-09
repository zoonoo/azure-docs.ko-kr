---
title: 자습서-서비스로 유효성 검사에 대 한 리소스를 설정 합니다. | Microsoft Docs
description: 이 자습서에서는 서비스와 유효성 검사에 대 한 리소스를 설정 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 58c69840e7b8ebdc2bd367859184ab54460c9426
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103481"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>자습서: 서비스 유효성 검사에 대 한 리소스 설정

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

솔루션을 만드는 해야 합니다. 서비스 (VaaS) 솔루션으로 유효성 검사를 특정 하드웨어 자재 명세서 자료를 사용 하 여 Azure Stack 솔루션을 나타냅니다. 하드웨어가 Azure Stack을 실행된을 지원할 수 있는지 확인 하려면 솔루션을 사용 합니다. 이 자습서에 따라 솔루션을 사용 하 여 서비스를 사용 하기 위해 준비 합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VaaS를 사용 하 여 Azure AD를 설정 하 여 준비 인스턴스 (Azure AD).
> * 저장소 계정을 만듭니다.

## <a name="configure-an-azure-ad-tenant"></a>Azure AD 테 넌 트를 구성 합니다.

Azure AD 테 넌 트를 인증 하 고 VaaS 등록 필요 합니다. 테 넌 트의 역할 기반 액세스 제어 (RBAC) 기능 VaaS를 사용할 수 있는 파트너 조직의 사용자를 관리 하는 파트너에 의해 사용 됩니다.

등록 조직 Azure AD directory (Azure Stack에 사용 되는 Azure AD 테 넌 트 디렉터리 아님)를 테 넌 트 및 사용자 계정에 관리에 대 한 정책을 설정 합니다. 자세한 내용은 [Azure AD 디렉터리 관리](https://docs.microsoft.com/azure/active-directory/active-directory-administer)를 참조하세요.

### <a name="create-a-tenant"></a>테넌트 만들기

특별히 사용 하기 위해 테 넌 트를 사용 하 여 만들기 VaaS 설명이 포함 된 이름으로 예를 들어 `ContosoVaaS@onmicrosoft.com`합니다.

1. Azure AD 테 넌 트 만들기 합니다 [Azure portal](https://portal.azure.com), 또는 기존 테 넌 트를 사용 하 여 합니다. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. 테 넌 트에 조직의 구성원을 추가 합니다. 이러한 사용자는 보거나 테스트를 예약 하려면 서비스를 사용 해야 합니다. 등록을 완료 하면 사용자의 액세스 수준을 정의 합니다.
 
    다음 역할 중 하나를 할당 하 여 VaaS에서 작업을 실행 하려면 테 넌 트의 사용자 권한을 부여 합니다.

    | 역할 이름 | 설명 |
    |---------------------|------------------------------------------|
    | 소유자 | 모든 리소스에 전체 액세스할 수 있습니다. |
    | 판독기 | 모든 리소스 보기 하지만 하지 만들 수도 있고 관리. |
    | 테스트 참가자 | 만들 하 고 테스트 리소스를 관리할 수 있습니다. |

    역할을 할당 합니다 **Azure Stack 유효성 검사 서비스** 응용 프로그램:

    1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
    2. 선택 **모든 서비스** > **Azure Active Directory** 아래 합니다 **Identity** 섹션입니다.
    3. 선택 **엔터프라이즈 응용 프로그램** > **Azure Stack 유효성 검사 서비스** 응용 프로그램입니다.
    4. **사용자 및 그룹**을 선택합니다. 합니다 **Azure Stack 유효성 검사 서비스-사용자 및 그룹** 블레이드에서 응용 프로그램을 사용할 수 있는 권한 가진 사용자를 나열 합니다.
    5. 선택 **+ 사용자 추가** 테 넌 트에서 사용자를 추가 하 고 역할을 할당 합니다.
   
    VaaS 리소스 및 조직 내의 여러 그룹 간에 작업을 격리 하려는 경우에 여러 Azure AD 테 넌 트 디렉터리를 만들 수 있습니다.

### <a name="register-your-tenant"></a>테 넌 트를 등록 합니다.

이 프로세스에 사용 하 여 테 넌 트 권한을 부여 합니다 **Azure Stack 유효성 검사 서비스** Azure AD 응용 프로그램입니다.

1. Microsoft에서 테 넌 트에 대 한 다음 정보를 보낼 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)합니다.

    | Data | 설명 |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | 조직 이름 | 공식 조직 이름입니다. |
    | Azure AD 테 넌 트 디렉터리 이름 | 등록할 Azure AD 테 넌 트 디렉터리 이름입니다. |
    | Azure AD 테 넌 트 디렉터리 ID | Azure AD 테 넌 트 디렉터리의 디렉터리와 연결 된 GUID입니다. Azure AD 테 넌 트 디렉터리 ID를 찾는 방법에 대 한 내용은 참조 하세요 [테 넌 트 ID 가져오기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)합니다. |

2. 테 넌 트 VaaS 포털을 사용할 수 있는지 확인 하려면 Azure Stack 유효성 검사 팀에서 확인 될 때까지 기다립니다.

### <a name="consent-to-the-vaas-application"></a>VaaS 응용 프로그램에 동의

Azure AD 관리자로 VaaS Azure AD 응용 프로그램을 테 넌 트를 대신 하 여 필요한 권한을 제공 합니다.

1. 테 넌 트의 전역 관리자 자격 증명을 사용 하 여 로그인 합니다 [VaaS 포털](https://azurestackvalidation.com/)합니다. 

2. 선택 **내 계정**합니다.

3은 VaaS 나열 된 Azure AD 사용 권한을 부여 하 라는 메시지가 표시 되는 경우 계속 하려면 약관에 동의 합니다.

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

테스트 실행 도중 VaaS Azure Storage 계정에 진단 로그를 출력합니다. 테스트 로그 외에도 저장소 계정을 사용할 수도 있습니다 업로드 OEM 확장 패키지를 솔루션 유효성 검사 워크플로에 대 한 합니다.

Azure Storage 계정에 Azure Stack 환경에 없는 Azure 공용 클라우드에서 호스팅됩니다.

1. Azure portal에서 선택 **모든 서비스** > **Storage** > **저장소 계정은**합니다. 에 **Storage 계정** 블레이드에서 **추가**합니다.

2. 저장소 계정을 만들 구독을 선택합니다.

3. 아래 **리소스 그룹**를 선택 **새로 만들기**합니다. 새 리소스 그룹의 이름을 입력 합니다.

4. 저장소 계정의 이름을 입력합니다. 선택한 이름 이어야 합니다.
    - Azure에서 고유
    - 3 ~ 24 자 사이
    - 숫자 및 소문자만 포함

5. 선택 된 **미국 서 부** 저장소 계정의 지역입니다.

    로그를 저장 하기 위한 네트워킹 요금이 발생 하지는 보장 하기 위해 Azure Storage 계정을 구성할 수 있습니다만 사용 하 여 **미국 서 부** 지역입니다. 데이터 복제 및 핫 저장소 계층 기능이이 데이터에 대 한 필요 하지 않습니다. 두 기능을 사용 하면 비용을 크게 증가 합니다.

6. 설정을 제외 하 고 기본값으로 그대로 두고 **계정 종류**:

    - 합니다 **배포 모델** 필드 설정 됩니다 **Resource Manager** 기본적으로 합니다.
    - **성능** 필드는 기본적으로 **표준**으로 설정됩니다.
    - 선택 **계정 종류** 필드를 **Blob 저장소**합니다.
    - 합니다 **복제 필드** 로 설정 된 **로컬 중복 저장소 (LRS)** 기본적으로 합니다.
    - **액세스 계층**은 기본적으로 **핫**으로 설정됩니다.

7. **검토 + 만들기**를 클릭하여 저장소 계정 설정을 검토하고 계정을 만듭니다.

## <a name="next-steps"></a>다음 단계

사용자 환경에서 바인딩된 연결을 허용 하지 않으면 하는 경우에 하드웨어에서 테스트를 실행 하려면 로컬 에이전트를 배포 하는 방법에 자습서를 따르세요.

> [!div class="nextstepaction"]
> [로컬 에이전트를 배포 합니다.](azure-stack-vaas-local-agent.md)
