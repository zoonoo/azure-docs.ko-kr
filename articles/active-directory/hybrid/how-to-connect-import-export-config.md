---
title: Azure AD Connect 구성 설정을 가져오고 내보내는 방법
description: 이 문서에서는 클라우드 프로 비전에 대 한 질문과 대답을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da80af9fe598186fa25d59601c9fa4faccb4286a
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447042"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings-public-preview"></a>Azure AD Connect 구성 설정 가져오기 및 내보내기 (공개 미리 보기)

Azure Active Directory (Azure AD) Connect 배포는 사용자 지정 동기화 규칙을 사용 하 여 여러 포리스트 간에 동기화 되는 복잡 한 배포와 단일 포리스트 빠른 모드 설치에 따라 다릅니다. 구성 옵션과 메커니즘의 수가 많기 때문에 어떤 설정이 적용 되 고 있는지 이해 하 고 동일한 구성으로 서버를 신속 하 게 배포할 수 있는 것이 중요 합니다. 이 기능에는 지정 된 동기화 서버의 구성을 카탈로그로 만들고 새 배포로 설정을 가져올 수 있는 기능이 도입 되었습니다. 서로 다른 동기화 설정 스냅숏을 비교 하 여 두 서버 간 차이 또는 시간이 지남에 따라 동일한 서버를 쉽게 시각화할 수 있습니다.

Azure AD Connect 마법사에서 구성이 변경 될 때마다 새 시간 스탬프 JSON 설정 파일이 자동으로 **%ProgramData%\AADConnect**로 내보내집니다. 설정 파일 이름은 **적용 됨-SynchronizationPolicy-* 형식입니다. JSON**-파일 이름의 마지막 부분이 타임 스탬프입니다.

> [!IMPORTANT]
> Azure AD Connect에서 변경한 내용만 자동으로 내보내집니다. PowerShell, Synchronization Service Manager 또는 동기화 규칙 편집기를 사용 하 여 변경한 내용은 최신 복사본을 유지 하기 위해 필요에 따라 필요에 따라 내보내야 합니다. 요청 시 내보내기를 사용 하 여 재해 복구를 위해 보안 위치에 설정 복사본을 저장할 수도 있습니다.

## <a name="export-azure-ad-connect-settings"></a>Azure AD Connect 설정 내보내기 

구성 설정에 대 한 요약을 보려면 Azure AD Connect 도구를 열고 **현재 구성 보기 또는 내보내기**라는 추가 작업을 선택 합니다. 설정에 대 한 빠른 요약이 서버 전체 구성 내보내기 기능과 함께 표시 됩니다.

기본적으로 설정은 **%ProgramData%\AADConnect**로 내보내집니다. 또한 재해가 발생 하는 경우 가용성을 보장 하기 위해 설정을 보호 된 위치에 저장 하도록 선택할 수 있습니다. 설정은 JSON 파일 형식을 사용 하 여 내보내집니다. 논리적 일관성을 유지 하기 위해 직접 만들거나 편집 하면 안 됩니다. 손으로 만들었거나 편집한 파일을 가져오는 것은 지원 되지 않으며 예기치 않은 결과가 발생할 수 있습니다.

## <a name="import-azure-ad-connect-settings"></a>Azure AD Connect 설정 가져오기

이전에 내보낸 설정을 가져오려면:
 
1. 새 서버에 **Azure AD Connect** 를 설치 합니다.
1. **시작** 페이지에서 **사용자 지정** 옵션을 선택 합니다.
1. **동기화 설정 가져오기**를 선택 합니다. 이전에 내보낸 JSON 설정 파일을 찾습니다.
1. **설치**를 선택합니다.

   ![필수 구성 요소 설치 화면을 보여 주는 스크린샷](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> LocalDB 대신 SQL Server를 사용 하거나 기본 VSA 대신 기존 서비스 계정을 사용 하는 것과 같이이 페이지의 설정을 재정의 합니다. 이러한 설정은 구성 설정 파일에서 가져오지 않습니다. 정보 및 비교 목적으로 제공 됩니다.

### <a name="import-installation-experience"></a>설치 환경 가져오기 

가져오기 설치 환경은 기존 서버에 대 한 재현 가능성을 쉽게 제공 하기 위해 사용자의 입력을 최소화 하면서 의도적으로 간단 하 게 유지 됩니다.

설치 환경에서 수행할 수 있는 유일한 변경 내용은 다음과 같습니다. Azure AD Connect 마법사에서 설치 후 다른 모든 변경 작업을 수행할 수 있습니다.
- **Azure Active Directory 자격 증명**: 원래 서버를 구성 하는 데 사용 되는 Azure 전역 관리자의 계정 이름이 기본적으로 제안 됩니다. *must*   새 디렉터리에 정보를 동기화 하려는 경우 변경 해야 합니다.
- **사용자 로그인**: 원래 서버에 대해 구성 된 로그온 옵션이 기본적으로 선택 되며, 구성 중에 필요한 자격 증명 또는 기타 정보를 자동으로 확인 합니다. 드문 경우 지만 활성 서버의 동작을 변경 하지 않도록 다른 옵션을 사용 하 여 서버를 설정 해야 할 수 있습니다. 그렇지 않은 경우 **다음** 을 선택 하 여 동일한 설정을 사용 합니다.
- **온-프레미스 디렉터리 자격 증명**: 동기화 설정에 포함 된 각 온-프레미스 디렉터리에 대해 동기화 계정을 만들거나 미리 만든 사용자 지정 동기화 계정을 제공 하기 위한 자격 증명을 제공 해야 합니다. 이 절차는 디렉터리를 추가 하거나 제거할 수 없다는 예외를 제외 하 고는 새로 설치 환경과 동일 합니다.
- **구성 옵션**: 새로 설치 하는 경우와 마찬가지로 자동 동기화를 시작할지 아니면 준비 모드를 사용할지에 대 한 초기 설정을 구성 하도록 선택할 수 있습니다. 주요 차이점은 준비 모드는 기본적으로 의도적으로 사용 하도록 설정 되어 결과를 Azure로 적극적으로 내보내기 전에 구성과 동기화 결과를 비교할 수 있다는 것입니다.

![디렉터리 연결 화면을 보여 주는 스크린샷](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> 하나의 동기화 서버만 주 역할에 있을 수 있으며 구성 변경 내용을 Azure로 적극적으로 내보낼 수 있습니다. 다른 모든 서버는 준비 모드에 배치 해야 합니다.

## <a name="migrate-settings-from-an-existing-server"></a>기존 서버에서 설정 마이그레이션 

기존 서버에서 설정 관리를 지원 하지 않는 경우 서버를 현재 위치로 업그레이드 하거나 새 준비 서버에서 사용할 설정을 마이그레이션하여 선택할 수 있습니다.

마이그레이션을 수행 하려면 새 설치에 사용할 기존 설정을 추출 하는 PowerShell 스크립트를 실행 해야 합니다.이 메서드를 사용 하 여 기존 서버의 설정을 카탈로그로 만든 후 새로 설치 된 준비 서버에 적용할 수 있습니다.원래 서버의 설정을 새로 만든 서버와 비교 하면 서버 간의 변경 내용을 신속 하 게 시각화할 수 있습니다.항상 조직의 인증 프로세스에 따라 추가 구성이 필요 하지 않도록 합니다.

### <a name="migration-process"></a>마이그레이션 프로세스 
설정을 마이그레이션하려면:

1. 새 준비 서버에서 **AzureADConnect.msi** 를 시작 하 고 Azure AD Connect의 **시작** 페이지에서 중지 합니다.

1. **MigrateSettings.ps1** 를 Microsoft Azure AD Connect\Tools 디렉터리에서 기존 서버의 위치로 복사 합니다. 예를 들어, 설치 프로그램은 기존 서버에서 만든 디렉터리인 C:\setup입니다.

   ![Azure AD Connect 디렉터리를 보여 주는 스크린샷](media/how-to-connect-import-export-config/migrate1.png)

1. 여기에 표시 된 대로 스크립트를 실행 하 고 하위 수준 서버 구성 디렉터리 전체를 저장 합니다. 이 디렉터리를 새 준비 서버에 복사 합니다. **ServerConfiguration-*** 폴더 전체를 새 서버에 복사 해야 합니다.

   ![Windows PowerShell ](media/how-to-connect-import-export-config/migrate2.png)
    ![ 의 스크립트를 보여 주는 스크린샷 ServerConfiguration-* 폴더를 복사 하는 방법을 보여 주는 스크린샷](media/how-to-connect-import-export-config/migrate3.png)

1. 바탕 화면에서 아이콘을 두 번 클릭 하 여 **Azure AD Connect** 를 시작 합니다. Microsoft 소프트웨어 사용 조건에 동의 하 고 다음 페이지에서 **사용자 지정**을 선택 합니다.
1. **동기화 설정 가져오기** 확인란을 선택 합니다. **찾아보기** 를 선택 하 여 복사 된 ServerConfiguration-* 폴더를 검색 합니다. 에서 MigratedPolicy.js를 선택 하 여 마이그레이션된 설정을 가져옵니다.

   ![동기화 설정 가져오기 옵션을 보여 주는 스크린샷](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>설치 후 확인 

원래 가져온 설정 파일과 새로 배포 된 서버의 내보낸 설정 파일을 비교 하는 것은 의도 된 배포와 그에 따른 배포 간의 차이점을 이해 하는 데 반드시 필요한 단계입니다. 선호 하는 side-by-side 텍스트 비교 응용 프로그램을 사용 하면 원하는 대로 또는 실수로 변경 된 내용을 빠르게 강조 표시 하는 인스턴트 시각화가 생성 됩니다.

이전의 수동 구성 단계는 대부분 제거 되었지만 조직의 인증 프로세스에 따라 추가 구성이 필요 하지 않은지 확인 해야 합니다. 이 구성은 현재 설정 관리의 공개 미리 보기 릴리스에서 캡처되지 않은 고급 설정을 사용 하는 경우 발생할 수 있습니다.

알려진 제한 사항은 다음과 같습니다.
- **동기화 규칙**: 사용자 지정 규칙에 대 한 우선 순위는 Microsoft 표준 규칙과의 충돌을 피하기 위해 예약 된 범위 0에서 99 사이 여야 합니다. 사용자 지정 규칙을 예약 된 범위 밖에 두면 표준 규칙이 구성에 추가 되 면 사용자 지정 규칙이 이동 될 수 있습니다. 구성이 수정 된 표준 규칙을 포함 하는 경우 유사한 문제가 발생 합니다. 표준 규칙을 수정 하지 않는 것이 좋습니다. 규칙 배치가 잘못 된 것일 수 있습니다.
- **장치 쓰기 저장 (writeback**): 이러한 설정은 카탈로그로 저장 됩니다. 구성 하는 동안 현재 적용 되지 않습니다. 원본 서버에 대해 장치 쓰기 저장을 사용 하도록 설정한 경우 새로 배포 된 서버에서이 기능을 수동으로 구성 해야 합니다.
- **동기화 된 개체 형식**: Synchronization Service Manager을 사용 하 여 동기화 된 개체 형식 (예: 사용자, 연락처 및 그룹)의 목록을 제한할 수 있지만이 기능은 현재 동기화 설정을 통해 지원 되지 않습니다. 설치를 완료 한 후에는 고급 구성을 수동으로 다시 적용 해야 합니다.
- **사용자 지정 실행 프로필**: Synchronization Service Manager을 사용 하 여 기본 실행 프로필 집합을 수정할 수 있지만이 기능은 현재 동기화 설정을 통해 지원 되지 않습니다. 설치를 완료 한 후에는 고급 구성을 수동으로 다시 적용 해야 합니다.
- **프로 비전 계층 구성**: Synchronization Service Manager의이 고급 기능은 동기화 설정을 통해 지원 되지 않습니다. 초기 배포를 완료 한 후 수동으로 다시 구성 해야 합니다.
- **Active Directory Federation Services (AD FS) 인증**: 이러한 인증 기능과 연결 된 로그온 방법이 자동으로 미리 선택 됩니다. 다른 모든 필수 구성 매개 변수를 대화형으로 제공 해야 합니다.
- 사용 **하지 않도록 설정 된 사용자 지정 동기화 규칙**을 사용으로 가져옵니다. 사용 하지 않도록 설정 된 사용자 지정 동기화 규칙을 사용 하도록 설정 합니다. 새 서버 에서도 사용 하지 않도록 설정 해야 합니다.

 ## <a name="next-steps"></a>다음 단계

- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)
- [Azure AD Connect Health 에이전트 설치](how-to-connect-health-agent-install.md) 
