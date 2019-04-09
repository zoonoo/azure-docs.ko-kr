---
title: Azure Stack 개요에 대 한 업데이트 관리 | Microsoft Docs
description: Azure Stack 통합 시스템에 대 한 업데이트 관리에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: bf797404b173d6febe133eacbb9d36310160aff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281576"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Azure Stack 개요에 대 한 업데이트 관리

*적용 대상 Azure Stack 통합 시스템*

Microsoft update 패키지 일반적으로 Azure Stack 통합 시스템에 대 한 각 월의 네 번째 화요일 주위를 놓습니다. 업데이트 알림 조직에 도달 하는 특정 알림 프로세스에 대 한 원래 장비 제조업체 (OEM)을 요청 합니다. 이 문서 라이브러리에서 확인할 수 있습니다 **개요** > **릴리스** 현재 지원 되는 릴리스에 대 한 정보에 대 한 합니다. 

각 릴리스의 Microsoft 소프트웨어 업데이트는 단일 업데이트 패키지로 제공 됩니다. Azure Stack 운영자로 가져올 수 있습니다, 설치 및 이러한의 설치 진행률을 모니터링 관리자 포털에서 패키지를 업데이트 합니다. 

OEM 공급 업체는 또한 드라이버 및 펌웨어 업데이트 등의 업데이트를 릴리스 했습니다. 이러한 업데이트, 공급 업체에서 별도 패키지로 전달 되는 동안 가져온 하 고, 설치 하 고, Microsoft에서 업데이트 패키지와 동일한 방식으로 관리 하는 있습니다.

지원 시스템을 유지 하려면 특정 버전 수준으로 업데이트 하는 Azure Stack을 유지 해야 합니다. 검토 하 고 있는지 확인 합니다 [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)합니다.

> [!NOTE]
> Azure Stack 개발 키트 Azure Stack 업데이트 패키지에 적용할 수 없습니다. 업데이트 패키지는 통합된 시스템을 위한 설계 되었습니다. 정보를 참조 하세요 [재배포는 ASDK](https://docs.microsoft.com/azure/azure-stack/asdk)합니다.

## <a name="the-update-resource-provider"></a>업데이트 리소스 공급자

Azure Stack에는 Microsoft 소프트웨어 업데이트의 응용 프로그램을 처리 하는 업데이트 리소스 공급자를 포함 합니다. 이 공급자는 모든 물리적 호스트, Service Fabric 응용 프로그램 및 런타임 및 모든 인프라 가상 컴퓨터 및 연결 된 서비스에서 업데이트에 적용 되었는지 확인 합니다.

업데이트 설치에 업데이트 프로세스 대상 Azure Stack (예를 들어, 물리적 호스트 및 인프라 virtual machines)에서 다양 한 하위 시스템으로 상위 수준 상태를 볼 수 있습니다.

## <a name="plan-for-updates"></a>업데이트에 대 한 계획

유지 관리 작업을 사용자에 게 알림 및 예약 하는 일반적인 유지 관리 기간 업무 외 시간 동안 가능한 경우 것이 좋습니다. 유지 관리 작업 테 넌 트 워크 로드와 포털 작업에 영향을 줄 수 있습니다.

- 이 업데이트의 설치를 시작 하기 전에 실행할 [테스트 AzureStack](azure-stack-diagnostic-test.md) 에 Azure Stack의 상태를 확인 하 고 발견 된 작동 문제를 해결 하려면 다음 매개 변수를 사용 하 여 모든 경고 및 오류를 포함 합니다. 또한 활성 경고를 검토 하 고 작업을 필요로 하는 해결 합니다.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>타일 업데이트를 사용 하 여 업데이트를 관리 하려면

관리자 포털에서 업데이트를 관리합니다. Azure Stack 운영자로 서는 대시보드에서 타일 업데이트를 사용할 수 있습니다.

- 현재 버전 같은 중요 한 정보를 봅니다.
- 업데이트를 설치 하 고 진행률을 모니터링 합니다.
- 이전에 설치 된 업데이트에 대 한 업데이트 기록을 검토 합니다.
- 클라우드의 현재 OEM 패키지 버전을 보려면
 
## <a name="determine-the-current-version"></a>현재 버전 확인

타일 업데이트에서에서 Azure Stack의 현재 버전을 볼 수 있습니다. 타일을 열려면:

1. Azure Stack 관리 포털을 엽니다.
2. 선택 **대시보드**합니다. 에 **업데이트** 타일, 현재 버전이 나열 됩니다. 

    ![기본 대시보드에서 타일을 업데이트](./media/azure-stack-updates/image1.png)

    예를 들어 화면에서 버전이 1.1903.0.35 합니다.

## <a name="install-updates-and-monitor-progress"></a>업데이트를 설치 하 고 진행률을 모니터링


1. Azure Stack 관리 포털을 엽니다.
2. 선택 **대시보드**합니다. 타일 업데이트를 선택 합니다.
3. 선택 **지금 업데이트**합니다.

    ![Azure Stack 업데이트 실행 세부 정보](media/azure-stack-updates/azure-stack-update-button.png)

4.  Azure Stack에서 여러 하위 시스템을 업데이트 프로세스를 반복 하는 대로 상위 수준 상태를 볼 수 있습니다. 예제 하위 시스템 실제 호스트, Service Fabric 인프라 virtual machines를 포함 하 고 서비스는 관리자와 사용자 포털을 제공 합니다. 업데이트 과정에서, 업데이트 리소스 공급자를 만들기가 성공 하는 단계의 수 뿐만 아니라 진행에서 수 같은 업데이트에 대 한 추가 세부 정보를 보고 합니다.

5. 선택 된 **전체 로그를 다운로드** 업데이트에서 전체 로그를 다운로드 하려면 세부 정보 블레이드를 실행 합니다.

    ![Azure Stack 업데이트 실행 세부 정보](media/azure-stack-updates/update-run-details.png)

6. 업데이트 리소스 공급자를 제공 하는 완료 되 면을 **Succeeded** 업데이트 프로세스가 완료 되었다는 것을 알 수 있도록 확인 및 소요 된 시간입니다. 여기에서 모든 업데이트, 사용 가능한 업데이트를 또는 필터를 사용 하 여 설치 된 업데이트에 대 한 정보를 볼 수 있습니다.

    ![Azure Stack 업데이트 성공 세부 정보를 실행 합니다.](media/azure-stack-updates/update-success.png)

   업데이트 보고서 타일 업데이트 못하면 **주의가 필요한**합니다. 사용 하 여는 **전체 로그를 다운로드** 업데이트 못한 수의 개략적인 상태를 가져옵니다. Azure Stack 로그 컬렉션에 진단 및 문제 해결을 용이 하 게 하는 데 도움이 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack 서비스 정책](azure-stack-servicing-policy.md) 
- [Azure Stack의 지역 관리](azure-stack-region-management.md)
