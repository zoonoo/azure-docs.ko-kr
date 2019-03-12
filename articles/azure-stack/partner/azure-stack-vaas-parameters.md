---
title: 서비스로 Azure Stack 유효성 검사 워크플로 일반 매개 변수 | Microsoft Docs
description: 서비스로 Azure Stack 유효성 검사에 대 한 워크플로 일반 매개 변수
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f66f57799e1e6b6d0e27624e3dc08b4de5d09cac
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766203"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>서비스로 Azure Stack 유효성 검사에 대 한 워크플로 일반 매개 변수

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

환경 변수 및 사용자와 같은 값을 포함 하는 일반 매개 변수 유효성 검사 (VaaS) 서비스로에서 모든 테스트에 필요한 자격 증명입니다. 이러한 값은 만들거나 워크플로 변경할 때 워크플로 수준에서 정의 됩니다. 테스트를 예약 하는 경우 이러한 값은 워크플로 아래에서 각 테스트에 매개 변수로 전달 됩니다.

> [!NOTE]
> 각 테스트 자체 매개 변수 집합을 정의합니다. 예약 된 시간에 테스트를 공통 매개 변수를 독립적으로 값을 입력 해야 할 수 있습니다 또는 공통 매개 변수 값을 재정의할 수 있습니다.

## <a name="environment-parameters"></a>환경 매개 변수

환경 매개 변수 테스트 대상 Azure Stack 환경에 설명 합니다. 이러한 값을 생성 하 고 테스트 하는 특정 인스턴스에 대 한 Azure Stack 스탬프 정보 파일을 업로드 하 여 제공 되어야 합니다.

> [!NOTE]
> 공식 유효성 검사 워크플로 워크플로 만든 후 환경 매개 변수를 수정할 수 없습니다.

### <a name="generate-the-stamp-information-file"></a>스탬프 정보 파일을 생성 합니다.

1. dvm이 또는 Azure Stack 환경에 대 한 액세스 권한이 있는 모든 컴퓨터에 로그인 합니다.
2. 관리자 권한 PowerShell 창에서 다음 명령을 실행 합니다.

    ```PowerShell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>ECE 구성 파일에서 값을 찾습니다.

환경 매개 변수 값도 수동으로에 있을 수 있습니다 합니다 **ECE 구성 파일** 위치한 `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` 는 dvm이에서.

## <a name="test-parameters"></a>테스트 매개 변수

일반 테스트 매개 변수는 구성 파일에 저장할 수 없는 중요 한 정보를 포함 합니다. 이러한 수동으로 제공 해야 합니다.

매개 변수    | 설명
-------------|-----------------
테 넌 트 관리자 사용자                            | AAD 디렉터리에서 서비스 관리자가 프로 비전 되었습니다 azure Active Directory 테 넌 트 관리자. 이 사용자는 리소스 (Vm, 저장소 계정 등)을 설정 하는 템플릿을 배포 하 고 워크 로드를 실행 하는 등의 테 넌 트 수준 작업을 수행 합니다. 테 넌 트 계정의 프로 비전에 대 한 내용은 참조 하세요 [새 Azure Stack 테 넌 트 추가](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad)합니다.
서비스 관리자             | Azure Stack 배포 중에 지정한 AAD 디렉터리 테 넌 트의 azure Active Directory 관리자입니다. 검색할 `AADTenant` ECE 구성에서 파일과의 값을 선택 합니다 `UniqueName` 요소입니다.
클라우드 관리자               | Azure Stack 도메인 관리자 계정 (예를 들어 `contoso\cloudadmin`). 검색할 `User Role="CloudAdmin"` ECE 구성에서 파일과의 값을 선택 합니다 `UserName` 요소입니다.
진단 연결 문자열          | 진단 테스트 실행 하는 동안 로그 복사 될 Azure 저장소 계정을 SAS URL입니다. SAS URL 생성에 대 한 지침을 참조 하세요 [진단 연결 문자열을 생성](#generate-the-diagnostics-connection-string)합니다. |

> [!IMPORTANT]
> 합니다 **진단 연결 문자열이** 계속 하기 전에 유효 해야 합니다.

### <a name="generate-the-diagnostics-connection-string"></a>진단 연결 문자열 생성

진단 연결 문자열은 테스트 실행 하는 동안 진단 로그를 저장 하는 데 필요 합니다. 설치 중에 만든 Azure Storage 계정을 사용 하 여 (참조 [유효성 검사 서비스 리소스로 설정](azure-stack-vaas-set-up-resources.md)) 저장소 계정에 로그를 업로드 하려면 VaaS 액세스 권한을 부여 하려면 공유 액세스 서명 (SAS) URL을 만들려면.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. 선택 **Blob** 에서 **허용 된 서비스 옵션**합니다. 나머지 옵션을 선택 취소 합니다.

1. 선택 **서비스**하십시오 **컨테이너**, 및 **개체** 에서 **허용 되는 리소스 유형**.

1. 선택 **읽기**를 **작성**를 **목록**를 **추가**를 **만들기** 에서 **허용 사용 권한**합니다. 나머지 옵션을 선택 취소 합니다.

1. 설정 **시작 시간** 은 현재 시간 및 **종료 시간** 현재 시점부터 3 개월입니다.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> SAS URL의 URL을 생성할 때 지정한 종료 시간에 만료 됩니다.  
테스트를 예약 하는 경우에 적어도 30 일에 대 한 URL이 올바른지와 (3 개월 제안 됩니다.) 테스트 실행에 필요한 시간을 확인 합니다.

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [서비스 핵심 개념으로 유효성 검사](azure-stack-vaas-key-concepts.md)