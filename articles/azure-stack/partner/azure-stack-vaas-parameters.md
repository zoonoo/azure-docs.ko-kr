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
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 81a7be973739cfd6eb3f8fb8dc7a0723623c2b8e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235316"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>서비스로 Azure Stack 유효성 검사에 대 한 워크플로 일반 매개 변수

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

환경 변수 및 사용자와 같은 값을 포함 하는 일반 매개 변수 유효성 검사 (VaaS) 서비스로에서 모든 테스트에 필요한 자격 증명입니다. 워크플로 수준에서 이러한 값을 정의 합니다. 생성 또는 워크플로 수정 하는 경우 값을 저장 합니다. 예약 된 시간에 워크플로 테스트에 대 한 값을 로드합니다. 

## <a name="environment-parameters"></a>환경 매개 변수

환경 매개 변수 테스트 대상 Azure Stack 환경에 설명 합니다. 생성 및 스탬프 구성 파일을 업로드 하 여 이러한 값을 제공 해야 합니다. `&lt;link&gt;. [How to get the stamp info link].`

| 매개 변수 이름 | 필수 | 형식 | 설명 |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Azure Stack 빌드 | 필수 |  | Azure Stack 배포 (예를 들어 1.0.170330.9)의 빌드 번호 |
| OEM 버전 | 예 |  | Azure Stack 배포 하는 동안 사용 하는 OEM 패키지의 버전 번호입니다. |
| OEM 서명 | 예 |  | Azure Stack 배포 하는 동안 사용 하는 OEM 패키지의 서명입니다. |
| AAD 테 넌 트 ID | 필수 |  | Azure Stack 배포 하는 동안 azure Active Directory 테 넌 트 GUID를 지정 합니다.|
| 지역 | 필수 |  | Azure Stack 배포 지역입니다. |
| 테 넌 트 Resource Manager 끝점 | 필수 |  | 테 넌 트 Azure Resource Manager 작업에 대 한 끝점 (예를 들어 https://management.<ExternalFqdn>) |
| 관리자 Resource Manager 끝점 | 예 |  | 테 넌 트 Azure Resource Manager 작업에 대 한 끝점 (예를 들어 https://adminmanagement.<ExternalFqdn>) |
| 외부 FQDN | 예 |  | 외부 정규화 된 도메인 이름 끝점에 대 한 접미사로 사용 합니다. (예: local.azurestack.external 또는 redmond.contoso.com)입니다. |
| 노드 수 | 예 |  | 배포에서 노드 수입니다. |

## <a name="test-parameters"></a>테스트 매개 변수

일반 테스트 매개 변수 구성 파일에 저장 된 중요 한 없는 정보를 포함 하 고 수동으로 제공 해야 합니다.

| 매개 변수 이름 | 필수 | 형식 | 설명 |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 테 넌 트 사용자 이름 | 필수 |  | Azure 이미 프로 비전 하거나 되는 Active Directory 테 넌 트 관리자 또는 AAD 디렉터리에서 서비스 관리자에 의해 프로 비전 해야 합니다. 테 넌 트 계정을 프로 비전에 대 한 자세한 내용은 참조 하세요. [Azure AD 시작](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad)합니다. 이 값은 리소스를 프로 비전 하는 템플릿 배포와 같은 테 넌 트 수준 작업을 수행 하려면 테스트에서 사용 됩니다 (Vm, 저장소 계정 등) 및 워크 로드를 실행 합니다. 이 값은 리소스를 프로 비전 하는 템플릿 배포와 같은 테 넌 트 수준 작업을 수행 하려면 테스트에서 사용 됩니다 (Vm, 저장소 계정 등) 및 워크 로드를 실행 합니다. |
| 테 넌 트 암호 | 필수 |  | 테 넌 트 사용자 암호입니다. |
| 서비스 관리자 사용자 이름 | 필수: 솔루션 유효성 검사 패키지 유효성 검사<br>필요 하지 않습니다: 테스트 통과 |  | Azure Stack 배포 중에 지정한 AAD 디렉터리 테 넌 트의 azure Active Directory 관리자를 선택 합니다. |
| 서비스 관리자 암호 | 필수: 솔루션 유효성 검사 패키지 유효성 검사<br>필요 하지 않습니다: 테스트 통과 |  | 서비스 관리자 사용자 암호입니다. |
| 클라우드 관리자 사용자 이름 | 필수 |  | Azure Stack 도메인 관리자 계정 (예를 들어 contoso\cloudadmin). 사용자 역할에 대 한 검색 구성 파일에서 "CloudAdmin" = 및 구성 파일에서 사용자 이름 태그에서 값을 선택 합니다. |
| 클라우드 관리자 암호 | 필수 |  | 클라우드 관리자 사용자 암호입니다. |
| 진단 연결 문자열 | 필수 |  | 진단 테스트 실행 하는 동안 로그 복사 될 Azure 저장소 계정을 사용 하는 SAS URI. SAS URI를 생성 하기 위한 지침 위치한 [blob storage 계정 설정](azure-stack-vaas-set-up-account.md)합니다. |


## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.
