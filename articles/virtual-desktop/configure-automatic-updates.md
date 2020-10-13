---
title: Microsoft Endpoint Configuration Manager 구성-Azure
description: Windows 가상 데스크톱에서 Windows 10 Enterprise 다중 세션에 소프트웨어 업데이트를 배포 하도록 Microsoft Endpoint Configuration Manager를 구성 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010126"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager 구성

이 문서에서는 Windows 10 Enterprise 다중 세션을 실행 하는 Windows 가상 데스크톱 호스트에 업데이트를 자동으로 적용 하도록 Microsoft Endpoint Configuration Manager를 구성 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 설정을 구성 하려면 다음 항목이 필요 합니다.

   - 가상 컴퓨터에 Microsoft 끝점 Configuration Manager 에이전트를 설치 했는지 확인 합니다.
   - Microsoft 끝점 Configuration Manager 버전이 최소한 분기 수준 1906 인지 확인 합니다. 최상의 결과를 위해서는 분기 수준 1910 이상을 사용 합니다.

## <a name="configure-the-software-update-point"></a>소프트웨어 업데이트 지점 구성

Windows 10 Enterprise 다중 세션의 업데이트를 받으려면 Microsoft Endpoint Configuration Manager 내에서 Windows Server 버전 1903 이상을 제품으로 사용 하도록 설정 해야 합니다. 이 제품 설정은 Windows Server 업데이트 서비스를 사용 하 여 시스템에 업데이트를 배포 하는 경우에도 적용 됩니다.

업데이트를 수신 하려면:

1. Microsoft 끝점 Configuration Manager 열고 **사이트**를 선택 합니다.
2. **사이트 구성 요소 구성**을 선택 합니다.
3. 드롭다운 메뉴에서 **소프트웨어 업데이트 지점** 을 선택 합니다.
4. **제품** 탭을 선택합니다.
5. **Windows Server, 버전 1903 이상**이라는 확인란을 선택 합니다.
6. **소프트웨어 라이브러리**  >  **개요**  >  **소프트웨어 업데이트**  >  **모든 소프트웨어 업데이트** 로 이동 하 여 **소프트웨어 업데이트 동기화**를 선택 합니다.
7. **프로그램 파일**Microsoft Configuration Manager 로그의 wsyncmgr .log 파일  >  **Microsoft Configuration Manager**  >  **Logs** 을 확인 하 여 변경 내용이 저장 되었는지 확인 합니다. 업데이트를 동기화 하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-a-query-based-collection"></a>쿼리 기반 컬렉션 만들기

Windows 10 Enterprise 다중 세션 가상 컴퓨터의 컬렉션을 만들기 위해 쿼리 기반 컬렉션을 사용 하 여 특정 운영 체제 SKU를 식별할 수 있습니다.

컬렉션을 만들려면

1. **자산 및 준수**를 선택 합니다.
2. **개요**  >  **장치 컬렉션** 으로 이동 하 여 **장치 컬렉션** 을 마우스 오른쪽 단추로 클릭 하 고 드롭다운 메뉴에서 **장치 컬렉션 만들기** 를 선택 합니다.
3. 열리는 메뉴의 **일반** 탭에서 **이름** 필드에 컬렉션을 설명 하는 이름을 입력 합니다. **설명** 필드에서 컬렉션이 무엇 인지 설명 하는 추가 정보를 제공할 수 있습니다. **제한 컬렉션**에서 컬렉션 쿼리에 포함 하는 컴퓨터를 정의 합니다.
4. **멤버 자격 규칙** 탭에서 **규칙 추가**를 선택한 다음 **쿼리 규칙**을 선택 하 여 쿼리에 대 한 규칙을 추가 합니다.
5. **쿼리 규칙 속성**에서 규칙의 이름을 입력 한 다음 **쿼리 문 편집**을 선택 하 여 규칙의 매개 변수를 정의 합니다.
6. **쿼리 문 표시**를 선택 합니다.
7. 문에서 다음 문자열을 입력 합니다.

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. **확인** 을 선택 하 여 컬렉션을 만듭니다.
9. 컬렉션이 성공적으로 만들어졌는지 확인 하려면 **자산 및 준수**  >  **개요**  >  **장치 컬렉션**으로 이동 합니다.
