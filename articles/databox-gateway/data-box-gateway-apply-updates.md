---
title: Azure Data Box Gateway 시리즈 디바이스에 업데이트 설치 | Microsoft Docs
description: Azure Portal 및 Azure Data Box Gateway 시리즈 디바이스에 대한 로컬 웹 UI를 사용하여 업데이트를 적용하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 7fda10310a4bac085b248248d80d708dfa7f3ff7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582768"
---
# <a name="update-your-azure-data-box-gateway"></a>Azure Data Box Gateway 업데이트

이 문서에서는 로컬 웹 UI 및 Azure Portal을 통해 Azure Data Box Gateway에 업데이트를 설치하는 데 필요한 단계를 설명합니다. Data Box Gateway 디바이스를 최신 상태로 유지하려면 소프트웨어 업데이트 또는 핫픽스를 적용합니다.

> [!IMPORTANT]
>
> - 업데이트 **1911** 은 디바이스에 있는 **1.6.1049.786** 소프트웨어 버전에 해당합니다. 이 업데이트에 대한 내용은 [업데이트 1.1에 대한 릴리스 정보](data-box-gateway-1911-release-notes.md)로 이동합니다.
>
> - 업데이트 또는 핫픽스를 설치하면 디바이스가 다시 시작됩니다. Data Box Gateway가 단일 노드 디바이스인 경우 진행 중인 모든 I/O가 중단되고 디바이스 소프트웨어 업데이트에 대해 최대 30분의 가동 중지 시간이 발생합니다.

이러한 각 단계는 다음 섹션에 설명되어 있습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal을 통해 업데이트를 설치하는 것이 좋습니다. 디바이스는 하루에 한 번 업데이트를 자동으로 검색합니다. 업데이트를 사용할 수 있으면 포털에 알림이 표시됩니다. 그런 다음 업데이트를 다운로드하여 설치할 수 있습니다.

> [!NOTE]
> 업데이트 설치를 계속하기 전에 디바이스가 정상 상태이고 상태가 **온라인** 으로 표시되는지 확인합니다.

1. 디바이스에 업데이트를 사용할 수 있으면 알림이 표시됩니다. 알림을 선택하거나 상단의 명령 모음에서 **디바이스 업데이트** 를 선택합니다. 이렇게 하면 디바이스 소프트웨어 업데이트를 적용할 수 있습니다.

    ![개요 및 디바이스 옵션 업데이트가 호출된 Azure Data Box Gateway 홈페이지 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. **디바이스 업데이트** 블레이드에서 릴리스 정보의 새 기능과 관련된 사용 조건을 검토했는지 확인합니다.

    업데이트를 **다운로드하여 설치** 하거나 업데이트를 **다운로드** 하도록 선택할 수 있습니다. 그런 다음 나중에 이러한 업데이트를 설치하도록 선택할 수 있습니다.

    ![동의 및 이해 옵션과 다운로드 및 설치 옵션이 호출된 디바이스 업데이트 대화 상자 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    업데이트를 다운로드하여 설치하려면 다운로드를 완료한 후 업데이트가 자동으로 설치되도록 하는 옵션을 선택합니다.

    ![동의 옵션과 다운로드 옵션이 호출된 디바이스 업데이트 대화 상자 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. 업데이트 다운로드가 시작됩니다. 다운로드가 진행 중이라는 알림이 표시됩니다.

    !["업데이트 다운로드 및 설치 진행 중"을 표시하는 알림 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    알림 배너도 Azure Portal에 표시됩니다. 이 배너는 다운로드 진행률을 나타냅니다.

    ![개요 옵션 및 업데이트 다운로드 중 알림 배너가 호출된 Azure Data Box Gateway 홈페이지 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    이 알림을 선택하거나 **디바이스 업데이트** 를 선택하여 업데이트의 자세한 상태를 확인할 수 있습니다.

    ![상태: 진행 중 메시지와 업데이트 다운로드 메시지가 호출된 업데이트 다운로드 및 설치 대화 상자 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. 다운로드가 완료되면 완료되었음을 알리도록 알림 배너가 업데이트됩니다. 업데이트를 다운로드하여 설치하도록 선택한 경우 설치가 자동으로 시작됩니다.

    ![개요 옵션 및 업데이트 다운로드 완료 알림 배너가 호출된 Azure Data Box Gateway 홈페이지 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    업데이트를 다운로드만 하도록 선택한 경우에는 알림을 선택하여 **디바이스 업데이트** 블레이드를 엽니다. **설치** 를 선택합니다.
  
    ![설치 옵션이 호출된 디바이스 업데이트 대화 상자 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. 설치가 진행 중이라는 알림이 표시됩니다.

    ![개요 옵션과 다운로드 및 설치 진행률 메시지가 호출된 Azure Data Box Gateway 홈페이지 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    또한 포털에는 설치가 진행 중임을 나타내는 정보 알림도 표시됩니다. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. 이는 1노드 디바이스이므로 업데이트가 설치된 후 디바이스가 다시 시작됩니다. 다시 시작하는 동안 중요한 알림이 디바이스 하트비트가 끊겼음을 나타냅니다.

    ![개요 옵션 및 중요한 알림 배너가 호출된 Azure Data Box Gateway 홈페이지 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    경고를 선택하여 해당하는 디바이스 이벤트를 확인합니다.

    ![디바이스에서 하트비트 끊김 이벤트가 호출된 디바이스 이벤트 섹션 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. 업데이트를 설치한 후 디바이스 상태가 **온라인** 으로 업데이트됩니다.

    ![개요 옵션과 온라인 상태가 호출된 Azure Data Box Gateway 홈페이지 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    위쪽 명령 모음에서 **디바이스 업데이트** 를 선택합니다. 업데이트가 성공적으로 설치되었고 디바이스 소프트웨어 버전에 해당 업데이트가 반영되었는지 확인합니다.

    ![설치된 소프트웨어 버전 섹션이 호출된 디바이스 업데이트 대화 상자 스크린샷](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>로컬 웹 UI 사용

로컬 웹 UI를 사용하는 경우 다음 두 단계로 구성됩니다.

- 업데이트 또는 핫픽스 다운로드
- 업데이트 또는 핫픽스 설치

이러한 각 단계는 다음 섹션에서 자세히 설명합니다.

### <a name="download-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 다운로드

업데이트를 다운로드하려면 다음 단계를 수행합니다. Microsoft에서 제공하는 위치 또는 Microsoft 업데이트 카탈로그에서 업데이트를 다운로드할 수 있습니다.

Microsoft 업데이트 카탈로그에서 업데이트를 다운로드하려면 다음 단계를 수행합니다.

1. 브라우저를 시작하고 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)으로 이동합니다.

   ![검색 텍스트 상자에 Data Box Gateway가 입력된 브라우저 창의 Microsoft 업데이트 카탈로그 스크린샷 브라우저의 주소 표시줄과 검색 텍스트 상자가 모두 호출됩니다.](./media/data-box-gateway-apply-updates/download-update-1.png)

2. Microsoft 업데이트 카탈로그의 검색 상자에 다운로드하려는 핫픽스의 KB(기술 자료) 번호 또는 업데이트의 단어를 입력합니다. 예를 들어 **Azure Data Box Gateway** 를 입력한 다음, **검색** 을 클릭합니다.

   업데이트 목록은 **Azure Data Box Gateway 1911** 로 표시됩니다.

   ![Data Box Gateway 검색 결과가 호출된 브라우저 창의 Microsoft 업데이트 카탈로그 스크린샷](./media/data-box-gateway-apply-updates/download-update-2.png)

3. **다운로드** 를 선택합니다. 디바이스 소프트웨어 업데이트에 해당하는 *SoftwareUpdatePackage.exe* 라는 다운로드할 파일이 하나 있습니다. 로컬 시스템의 폴더에 파일을 다운로드합니다. 디바이스에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.

   ![SoftwareUpdatePackage.exe 파일에 대한 링크가 호출된 다운로드 대화 상자 스크린샷](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 설치

업데이트 또는 핫픽스를 설치하기 전에 다음을 확인합니다.

- 업데이트 또는 핫픽스를 호스트에 로컬로 다운로드하거나 네트워크 공유를 통해 액세스할 수 있는지 확인합니다.
- 로컬 웹 UI의 **개요** 페이지에 표시된 대로 디바이스 상태가 정상입니다.

   ![대시보드 옵션과 소프트웨어 상태: 정상 메시지가 호출된 Data Box Gateway 로컬 웹 UI 스크린샷](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

이 절차를 완료하는 데 약 20분 정도 걸립니다. 다음 단계를 수행하여 업데이트 또는 핫픽스를 설치합니다.

1. 로컬 웹 UI에서 **유지 관리** > **소프트웨어 업데이트** 로 이동합니다. 실행 중인 소프트웨어 버전을 기록해 둡니다.

   ![소프트웨어 업데이트 옵션 및 현재 소프트웨어 버전 메시지가 표시된 Data Box Gateway 로컬 웹 UI 스크린샷](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. 업데이트 파일의 경로를 제공합니다. 네트워크 공유에 있는 경우 업데이트 설치 파일로 이동할 수 있습니다. *SoftwareUpdatePackage.exe* 접미사가 포함된 소프트웨어 업데이트 파일을 선택합니다.

   ![SoftwareUpdatePackage.exe 파일이 표시된 파일 탐색기 스크린샷](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. **적용** 을 선택합니다.

   ![소프트웨어 업데이트 옵션, 업데이트 파일 경로 텍스트 상자, 업데이트 적용 옵션이 표시된 Data Box Gateway 로컬 웹 UI 스크린샷](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. 확인하라는 메시지가 표시되면 **예** 를 선택하여 계속합니다. 단일 노드 디바이스인 경우 업데이트가 적용된 후 디바이스를 다시 시작하고 가동 중지 시간이 발생합니다.

   ![예 옵션이 강조 표시된 소프트웨어 업데이트 대화 상자 스크린샷](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. 업데이트가 시작됩니다. 디바이스가 성공적으로 업데이트된 후 다시 시작됩니다. 이 시간 동안 로컬 UI에 액세스할 수 없습니다.

6. 다시 시작이 완료된 후 **로그인** 페이지가 열립니다. 로컬 웹 UI에서 디바이스 소프트웨어가 업데이트되었는지 확인하려면 **유지 관리** > **소프트웨어 업데이트** 로 이동합니다. 이 예제에 표시된 소프트웨어 버전은 **1.6.1049.786** 입니다.

   ![소프트웨어 업데이트 옵션 및 업데이트된 현재 소프트웨어 버전 메시지가 표시된 Data Box Gateway 로컬 웹 UI 스크린샷](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>다음 단계

[Azure Data Box Gateway 관리](data-box-gateway-manage-users.md)에 대해 자세히 알아봅니다.
