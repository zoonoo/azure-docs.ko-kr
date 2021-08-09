---
title: Microsoft Endpoint Configuration Manager 구성 - Azure
description: Azure Virtual Desktop에서 Windows 10 Enterprise 다중 세션에 소프트웨어 업데이트를 배포하도록 Microsoft Endpoint Configuration Manager를 구성하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: femila
ms.openlocfilehash: 8266473553288584a534e8833d8c50dcb2ec40cf
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754550"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager 구성

이 문서에서는 Windows 10 Enterprise 다중 세션을 실행하는 Azure Virtual Desktop 호스트에 업데이트를 자동으로 적용하도록 Microsoft Endpoint Configuration Manager를 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 설정을 구성하려면 다음 항목이 필요합니다.

   - 가상 머신에 Microsoft Endpoint Configuration Manager 에이전트를 설치했는지 확인합니다.
   - Microsoft Endpoint Configuration Manager의 버전이 최소한 분기 수준 1906에 있는지 확인합니다. 최상의 결과를 얻으려면 분기 수준 1910 이상을 사용하세요.

## <a name="configure-the-software-update-point"></a>소프트웨어 업데이트 지점 구성

Windows 10 Enterprise 다중 세션에 대한 업데이트를 받으려면 Microsoft Endpoint Configuration Manager 내에서 Windows Server 버전 1903 이상을 제품으로 사용하도록 설정해야 합니다. 이 제품 설정은 Windows Server Update Service를 사용하여 시스템에 업데이트를 배포하는 경우에도 적용됩니다.

업데이트를 받으려면 다음을 수행합니다.

1. Microsoft Endpoint Configuration Manager 열고 **사이트** 를 선택합니다.
2. **사이트 구성 요소 구성** 을 선택합니다.
3. 드롭다운 메뉴에서 **소프트웨어 업데이트 지점** 을 선택합니다.
4. **제품** 탭을 선택합니다.
5. **Windows Server 버전 1903 이상** 이라고 표시된 확인란을 선택합니다.
6. **소프트웨어 라이브러리** > **개요** > **소프트웨어 업데이트** > **모든 소프트웨어 업데이트** 로 이동하여 **소프트웨어 업데이트 동기화** 를 선택합니다.
7. **프로그램 파일** > **Microsoft Configuration Manager** > **로그** 의 wsyncmgr.log 파일을 확인하여 변경 내용이 저장되었는지 확인합니다. 업데이트를 동기화하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-a-query-based-collection"></a>쿼리 기반 컬렉션 만들기

Windows 10 Enterprise 다중 세션 가상 머신의 컬렉션을 만들기 위해 쿼리 기반 컬렉션을 사용하여 특정 운영 체제 SKU를 식별할 수 있습니다.

컬렉션을 만들려면

1. **자산 및 규정 준수** 를 선택합니다.
2. **개요** > **디바이스 컬렉션** 으로 이동하여 **디바이스 컬렉션** 을 마우스 오른쪽 단추로 클릭하고 드롭다운 메뉴에서 **디바이스 컬렉션 만들기** 를 선택합니다.
3. 열리는 메뉴의 **일반** 탭에서 **이름** 필드에 컬렉션을 설명하는 이름을 입력합니다. **설명** 필드에서 컬렉션이 무엇인지 설명하는 추가 정보를 제공할 수 있습니다. **컬렉션 제한** 에서 컬렉션 쿼리에 포함할 머신을 정의합니다.
4. **멤버 자격 규칙** 탭에서 **규칙 추가** 를 선택한 다음, **쿼리 규칙** 을 선택하여 쿼리에 대한 규칙을 추가합니다.
5. **쿼리 규칙 속성** 에서 규칙 이름을 입력한 다음, **쿼리 문 편집** 을 선택하여 규칙의 매개 변수를 정의합니다.
6. **쿼리 문 표시** 를 선택합니다.
7. 명령문에 다음 문자열을 입력합니다.

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. **확인** 을 선택하여 컬렉션을 만듭니다.
9. 컬렉션을 성공적으로 만들었는지 확인하려면 **자산 및 규정 준수** > **개요** > **디바이스 컬렉션** 으로 이동합니다.
