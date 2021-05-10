---
title: Azure Security Center에서 파일 무결성 모니터링 | Microsoft Docs
description: 이 연습을 사용하여 Azure Security Center에서 FIM(파일 무결성 모니터링)을 구성하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 834df2387d037c19672e3287970300f4e84ca34b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047986"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure Security Center에서 파일 무결성 모니터링
이 연습을 사용하여 Azure Security Center에서 FIM(파일 무결성 모니터링)을 구성하는 방법을 알아봅니다.


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Azure Defender](defender-for-servers-introduction.md)가 필요합니다.<br>FIM은 Log Analytics 작업 영역에 데이터를 업로드합니다. 업로드하는 데이터의 양에 따라 데이터 요금이 부과됩니다. 자세한 내용은 [Log Analytics 가격](https://azure.microsoft.com/pricing/details/log-analytics/)을 참조하세요.|
|필요한 역할 및 권한:|**작업 영역 소유자** 는 FIM을 사용하거나 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [Log Analytics에 대한 Azure 역할](/services-hub/health/azure-roles#azure-roles)을 참조하세요.<br>**읽기 권한자** 는 결과를 볼 수 있습니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)<br>Azure Automation의 변경 내용 추적 솔루션을 사용할 수 있는 지역에서만 지원됩니다.<br>![예 ](./media/icons/yes-icon.png) [Azure Arc](../azure-arc/servers/overview.md) 지원 디바이스입니다.<br>[연결된 Log Analytics 작업 영역에 대해 지원되는 지역](../automation/how-to/region-mappings.md)을 참조하세요.<br>[변경 내용 추적에 대해 자세히 알아보세요](../automation/change-tracking/overview.md).|
|||

## <a name="what-is-fim-in-security-center"></a>Security Center의 FIM이란?
변경 모니터링이라고도 하는 FIM(파일 무결성 모니터링)은 운영 체제 파일, Windows 레지스트리, 애플리케이션 소프트웨어, Linux 시스템 파일 등을 검토하여 공격을 나타낼 수 있는 변경 사항을 찾습니다. 

Security Center는 FIM을 사용하여 모니터링할 엔터티를 권장하며 사용자 고유의 FIM 정책 또는 모니터링할 엔터티를 정의할 수도 있습니다. FIM은 다음과 같은 의심스러운 활동을 경고합니다.

- 파일 및 레지스트리 키 만들기 또는 제거
- 파일 수정(파일 크기, 액세스 제어 목록 및 콘텐츠 해시의 변경)
- 레지스트리 수정(크기, 액세스 제어 목록, 형식 및 콘텐츠의 변경)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * FIM에서 모니터링할 제안된 엔터티 목록을 검토
> * 사용자 고유의 사용자 지정 FIM 규칙 정의
> * 모니터링되는 엔터티에 대한 변경 감사
> * 와일드카드를 사용하여 디렉터리에 대한 추적 간소화


## <a name="how-does-fim-work"></a>FIM 작동 방식

FIM은 이러한 항목의 현재 상태와 이전 검색 중의 상태를 비교하여 의심스러운 수정을 수행되었을 때 경고를 표시합니다.

FIM은 Azure 변경 내용 추적 솔루션을 사용하여 환경의 변경 내용을 추적하고 식별합니다. 파일 무결성 모니터링을 사용하도록 설정하면 **솔루션** 형식의 **변경 내용 추적** 리소스 종류가 있습니다. 데이터 수집 빈도에 대한 자세한 내용은 [변경 내용 추적 데이터 수집 세부 정보](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection)를 참조하세요.

> [!NOTE]
> **변경 내용 추적** 리소스를 제거하면 Security Center에서 파일 무결성 모니터링 기능을 사용하지 않도록 설정합니다.

## <a name="which-files-should-i-monitor"></a>어떤 파일을 모니터링해야 할까요?
모니터링할 파일을 선택하는 경우 시스템 및 애플리케이션에 중요한 파일을 고려합니다. 계획 없이 변경할 것으로 예상되지 않는 파일을 모니터링합니다. 애플리케이션이나 운영 체제(예: 로그 파일 및 텍스트 파일)에서 자주 변경되는 파일을 선택하면 공격을 식별하기 어렵게 만드는 노이즈가 많이 발생합니다.

Security Center는 알려진 공격 패턴을 기준으로 모니터링할 다음과 같은 권장 항목 목록을 제공합니다.

|Linux 파일|Windows 파일|Windows 레지스트리 키(HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*.conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>파일 무결성 모니터링 사용 

FIM은 Azure Portal Security Center 페이지에서만 사용할 수 있습니다. 현재 FIM을 사용하기 위한 REST API는 없습니다.

1. **Azure Defender** 대시보드의 **고급 보호** 영역에서 **파일 무결성 모니터링** 을 선택합니다.

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="FIM 시작" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    **파일 무결성 모니터링** 구성 페이지가 열립니다.

    각 작업 영역에 대해 제공되는 정보는 다음과 같습니다.

    - 지난 주에 발생한 총 변경 횟수(작업 영역에서 FIM을 사용하지 않는 경우 대시(-)가 표시될 수 있음)
    - 작업 영역에 보고하는 총 컴퓨터 및 VM 수
    - 작업 영역의 지리적 위치
    - 작업 영역이 속한 Azure 구독

1. 이 페이지에서 다음을 수행합니다.

    - 각 작업 영역의 상태 및 설정에 액세스 및 보기

    - ![업그레이드 계획 아이콘][4] Azure Defender를 사용하도록 작업 영역을 업그레이드합니다. 이 아이콘은 작업 영역 또는 구독이 Azure Defender에서 보호되지 않음을 나타냅니다. FIM 기능을 사용하려면 Azure Defender에서 구독을 보호해야 합니다. 자세한 내용은 [Azure Security Center 무료 및 Azure Defender 사용 비교](security-center-pricing.md)를 참조하세요.

    - ![사용 아이콘:][3] 작업 영역 아래의 모든 머신에서 FIM을 사용하도록 설정하고 FIM 옵션을 구성합니다. 이 아이콘은 작업 영역에 대해 FIM을 사용할 수 없음을 나타냅니다.

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="특정 작업 영역에 대해 FIM 사용":::


    > [!TIP]
    > 설정 또는 업그레이드 단추가 없고 공간이 비어 있으면 작업 영역에서 FIM이 이미 사용하도록 설정된 것입니다.


1. **사용** 을 선택합니다. 작업 영역 아래에 Windows 및 Linux 머신 수를 포함하여 작업 영역의 세부 정보가 표시됩니다.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="FIM 작업 영역 세부 정보 페이지":::

   또한 Windows 및 Linux에 권장되는 설정도 나열됩니다.  권장 항목의 전체 목록을 보려면 **Windows 파일**, **레지스트리** 및 **Linux 파일** 을 펼칩니다.

1. FIM에서 모니터링하지 않으려는 권장 엔터티에 대한 확인란을 선택 취소합니다.

1. **파일 무결성 모니터링 적용** 을 선택하여 FIM을 사용하도록 설정합니다.

> [!NOTE]
> 언제든지 설정을 변경할 수 있습니다. 자세한 내용은 아래의 [모니터링된 엔터티 수정](#edit-monitored-entities)을 참조하세요.



## <a name="audit-monitored-workspaces"></a>모니터링된 작업 영역 감사 

FIM을 사용하는 작업 영역에 대한 **파일 무결성 모니터링** 대시보드가 표시됩니다. 작업 영역에서 FIM을 사용하도록 설정한 후이거나 FIM이 이미 사용되는 **파일 무결성 모니터링** 창에서 작업 영역을 선택하면 FIM 대시보드가 열립니다.

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="FIM 대시보드 및 다양한 정보 패널":::

작업 영역에 대한 FIM 대시보드에서 표시하는 세부 항목은 다음과 같습니다.

- 작업 영역에 연결된 총 컴퓨터 수
- 선택한 기간 동안 발생한 총 변경 횟수
- 변경 유형에 대한 분석(파일, 레지스트리)
- 변경 범주에 대한 분석(수정됨, 추가됨, 제거됨)

대시보드 맨 위에서 **필터** 를 선택하여 변경 내용이 표시되는 기간을 변경합니다.

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="FIM 대시보드의 기간 필터":::

**서버** 탭에는 이 작업 영역에 보고하는 모든 머신이 나열됩니다. 대시보드에서 각 컴퓨터에 대해 나열하는 항목은 다음과 같습니다.

- 선택한 기간 동안 발생한 총 변경 횟수
- 파일 변경 또는 레지스트리 변경으로 인한 총 변경 횟수에 대한 분석

**로그 검색** 은 검색 필드에 머신 이름을 입력하거나 컴퓨터 탭에 나열된 머신을 선택하면 열립니다. 로그 검색은 머신에 대해 선택한 기간 동안 수행된 모든 변경 내용을 표시합니다. 자세한 내용을 확인하려면 변경 내용을 펼칠 수 있습니다.

![로그 검색][8]

**변경** 탭(아래 그림 참조)에는 선택한 기간 동안 작업 영역에 대한 모든 변경 내용이 나열됩니다. 대시보드에서 변경된 각 엔터티에 대해 나열하는 항목은 다음과 같습니다.

- 변경이 발생한 컴퓨터
- 변경 유형(레지스트리 또는 파일)
- 변경 범주(수정됨, 추가됨, 제거됨)
- 변경 날짜 및 시간

![작업 영역에 대한 변경 내용][9]

검색 필드에서 변경 내용을 입력하거나 **변경** 탭 아래에 나열된 항목을 선택하면 **변경 세부 정보** 가 열립니다.

![변경 세부 정보][10]

## <a name="edit-monitored-entities"></a>모니터링된 엔터티 수정

1. **파일 무결성 모니터링 대시보드** 로 돌아가서 **설정** 을 선택합니다.

   ![설정][11]

   **Windows 레지스트리**, **Windows 파일** 및 **Linux 파일** 의 세 가지 탭을 표시하는 **작업 영역 구성** 이 열립니다. 각 탭에는 해당 범주에서 편집할 수 있는 엔터티가 나열됩니다. Security Center에서는 나열된 각 엔터티에 대해 FIM을 사용하거나(true) 사용하지 않도록(false) 설정되었는지를 식별합니다.  엔터티를 편집하면 FIM을 사용하거나 사용하지 않도록 설정할 수 있습니다.

   ![작업 영역 구성][12]

2. ID 보호를 선택합니다. 이 예에서는 Windows 레지스트리에 속한 항목을 선택했습니다. **변경 내용 추적 편집** 에 대한 편집이 열립니다.

   ![변경 내용 추적 편집][13]

**변경 내용 추적 편집** 에서 수행할 수 있는 작업은 다음과 같습니다.

- 파일 무결성 모니터링 사용 설정(True) 또는 해제(False)
- 엔터티 이름 제공 또는 변경
- 값 또는 경로 제공 또는 변경
- 엔터티 삭제, 변경 내용 삭제 또는 변경 내용 저장

## <a name="add-a-new-entity-to-monitor"></a>모니터링할 새 엔터티 추가
1. **파일 무결성 모니터링 대시보드** 로 돌아가서 위쪽에 있는 **설정** 을 선택합니다. **작업 영역 구성** 이 열립니다.
2. **작업 영역 구성** 아래에서 추가하려는 엔터티의 형식에 대한 탭(Windows 레지스트리, Windows 파일 또는 Linux 파일)을 선택합니다. 이 예에서는 **Linux 파일** 을 선택했습니다.

   ![모니터링할 새 항목 추가][14]

3. **추가** 를 선택합니다. **변경 내용 추적에 추가** 가 열립니다.

   ![요청된 정보 입력][15]

4. **추가** 페이지에서 요청된 정보를 입력하고, **저장** 을 선택합니다.

## <a name="disable-monitored-entities"></a>모니터링된 엔터티 사용 안 함
1. **파일 무결성 모니터링** 대시보드로 돌아갑니다.
2. FIM이 현재 사용되는 작업 영역을 선택합니다. [사용] 단추 또는 [업그레이드 계획] 단추가 없으면 작업 영역을 FIM에 사용할 수 있습니다.

   ![FIM이 사용되는 작업 영역 선택][16]

3. 파일 무결성 모니터링 아래에서 **설정** 을 선택합니다.

   ![설정 선택][17]

4. **작업 영역 구성** 아래에서 **사용** 이 [참]으로 설정된 그룹을 선택합니다.

   ![작업 영역 구성][18]

5. **변경 내용 추적 편집** 창 아래에서 **사용** 을 [거짓]으로 설정합니다.

   ![[거짓]으로 설정된 [사용]][19]

6. **저장** 을 선택합니다.

## <a name="folder-and-path-monitoring-using-wildcards"></a>와일드 카드를 사용하여 폴더 및 경로 모니터링

와일드 카드를 사용하여 디렉터리에 대한 추적을 간소화합니다. 와일드 카드를 사용하여 폴더 모니터링을 구성한 경우 다음 규칙이 적용됩니다.
-   와일드 카드는 여러 파일을 추적하는 데 필요합니다.
-   C:\folder\file or /etc/*.conf와 같은 경로의 마지막 세그먼트에만 와일드 카드를 사용할 수 있습니다.
-   환경 변수에 유효하지 않은 경로가 포함된 경우 인벤토리를 실행하면 유효성 검사는 성공하지만 경로는 실패합니다.
-   경로를 설정할 때 c:\*.*와 같은 일반 경로를 사용하면 너무 많은 폴더가 트래버스되므로 사용하지 않는 것이 좋습니다.

## <a name="disable-fim"></a>FIM 사용 안 함
FIM을 사용하지 않도록 설정할 수 있습니다. FIM은 Azure 변경 내용 추적 솔루션을 사용하여 환경의 변경 내용을 추적하고 식별합니다. FIM을 사용하지 않도록 설정하면 선택한 작업 영역에서 변경 내용 추적 솔루션이 제거됩니다.

1. FIM을 사용하지 않도록 설정하려면 **파일 무결성 모니터링** 대시보드로 돌아갑니다.
2. 작업 영역을 선택합니다.
3. **파일 무결성 모니터링** 아래에서 **사용 안 함** 을 선택합니다.

   ![FIM 사용 안 함][20]

4. **제거** 를 선택하여 사용하지 않도록 설정합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 FIM(파일 무결성 모니터링)을 사용하는 방법에 대해 알아보았습니다. Security Center에 대한 자세한 내용은 다음 페이지를 참조하세요.

* [보안 정책 설정](tutorial-security-policy.md) - Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
* [보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항을 통해 Azure 리소스를 보호하는 방법을 알아봅니다.
* [Azure 보안 블로그](/archive/blogs/azuresecurity/)-- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png