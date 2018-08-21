---
title: 테스트 유효성 검사 서비스로 Azure Stack에 대 한 매개 변수 | Microsoft Docs
description: 구성 파일 및 테스트 하는 방법에 대 한 참조 항목을 서비스로 Azure Stack 유효성 검사에 대 한 매개 변수를 전달 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 65cae4d10a4683207474008a18bac39751ce8e96
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235355"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>테스트 유효성 검사 서비스로 Azure Stack에 대 한 매개 변수

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

모든 테스트 도구 모음에서 유효성 검사 (VaaS) 서비스를 실행 하기 전에 솔루션을 선택 하 고 테스트 패스를 만듭니다.

- 로그인에 등록 된 VaaS 테 넌 트 자격 증명.
- 새 솔루션 만들기 (선택 하 여 **솔루션 추가**) 또는 모든 기존 항목 선택 합니다.
- 선택 된 **시작** 에서 단추를 **테스트 통과** 워크플로 타일입니다.

> [!Note]  
> 유효성을 검사 하는 해당 컴퓨터 집합에 대 한 모든 빌드 배포에 대 한 새 테스트를 전달 하는 각 고유 컴퓨터 집합/하드웨어 구성에 대 한 새 솔루션 항목을 만듭니다.

테스트를 실행 하는 데 필요한 매개 변수를 입력 해야 합니다 **정보를 전달 하는 테스트** 페이지입니다. 새 테스트 통과 또는 유효성 검사 실행을 시작할 때 이러한 매개 변수를 제공 합니다. 매개 변수는 대부분 Azure Stack을 배포할 때 제공한 동일한 값을 갖습니다.

수동으로에 나열 된 값을 입력할 수는 [테스트 매개 변수 테이블](#test-parameters), 또는 전체 Azure Stack 스탬프 정보를 포함 하는 배포 구성 파일을 업로드 합니다. 업로드 되 면 포털 파일에서 값을 로드 합니다.

> [!Note]  
> 찾기 및 포털에 구성 파일을 로드 하 여 테스트 매개 변수 값을 입력 및 검색할 수 있습니다.

## <a name="export-the-test-parameters-using-powershell"></a>PowerShell을 사용 하 여 테스트 매개 변수를 내보내기

1. Dvm이 컴퓨터 또는 Azure Stack 환경에 대 한 액세스 권한이 있는 모든 컴퓨터에 로그인 합니다.
2. 관리자 권한 PowerShell 창을 열고 실행 합니다.

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. 업로드할 **stampinfoproperties.json** VaaS 포털에 있습니다.

## <a name="find-the-test-parameters-in-the-configuration-file"></a>구성 파일에서 테스트 매개 변수를 찾으려면

찾을 수도 있습니다 테스트 매개 변수 값을 ECE를 열어 **구성 파일**합니다. Dvm이 컴퓨터에서 다음 경로에 파일을 찾을 수 있습니다.  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>테스트 매개 변수 

| 매개 변수    | 설명 |
|-------------|-----------------|
| Azure Stack 빌드                      | Azure Stack 빌드 또는 버전 번호는 배포 예를 들어 1.0.170330.0 | 
| 테넌트 ID                              | Azure Active Directory 테 넌 트 Azure Stack 배포 중에 지정 합니다. 검색할 **AADTenant** 선택한 구성 파일을 **GUID** 값을 `Id` 태그. | 
| 지역                                 | Azure Stack 배포 지역입니다. 검색할 **지역** 구성 파일에 있습니다. | 
| 테 넌 트 리소스 관리자                | 예를 들어, Azure Resource Manager 끝점을 테 넌 트 `https://management.<ExternalFqdn>` | 
| 리소스 관리자 관리                 | 관리자 Azure Resource Manager 끝점입니다. 예: `https://adminmanagement.<ExternalFqdn>` | 
| 외부 FQDN                          | 환경의 외부 FQDN입니다. 검색할 **ExternalFqdn** 구성 파일에 있습니다. | 
| 테 넌 트 사용자                            | Azure 이미 프로 비전 하거나 되는 Active Directory 테 넌 트 관리자 또는 서비스 관리자 Azure AD 디렉터리에 의해 프로 비전 해야 합니다. 테 넌 트 계정을 프로 비전에 대 한 자세한 내용은 참조 하십시오 [Azure Active Directory에서 새 Azure Stack 테 넌 트 계정 추가](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad)합니다. 이 값은 리소스를 프로 비전 하는 템플릿 배포와 같은 테 넌 트 수준 작업을 수행 하려면 테스트에서 사용 됩니다 (VM, 저장소 계정 등) 및 워크 로드를 실행 합니다. | 
| 서비스 관리자             | Azure Stack 배포 중에 지정 된 Azure AD 디렉터리 테 넌 트의 azure Active Directory 관리자를 선택 합니다. 검색할 **AADTenant** 구성에서 파일과의 값을 선택 합니다 `UniqueName` 구성 파일에서 태그. | 

## <a name="checks-before-starting-the-tests"></a>테스트를 시작 하기 전에 확인

테스트를 원격 작업을 수행 합니다. 테스트를 실행 하는 컴퓨터의 Azure Stack 끝점 권한이 있어야 합니다. 그렇지 않으면 테스트가 작동 하지 않습니다. VaaS 프레미스 에이전트를 사용 하는 경우 사용 하 여 컴퓨터 에이전트가 실행 될 위치입니다. 컴퓨터에는 다음 검사를 실행 하 여 Azure Stack 지점에 대 한 액세스를 확인할 수 있습니다.

1. 기본 URI에 연결할 수 있는지 확인 합니다. CMD 프롬프트를 열고 또는 bash 셸 및 다음 명령을 실행 합니다.

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. 웹 브라우저를 열고 이동할 `https://adminportal.<EXTERNALFQDN>` MAS 포털에 연결할 수 있는지 확인 하기 위해.

3. Azure AD를 사용 하 여 로그인 테스트 패스를 만들 때 제공한 관리자 이름 및 암호 값을 서비스 합니다.

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.
