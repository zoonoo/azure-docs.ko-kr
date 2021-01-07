---
title: 지역 전체 오류에서 복구
description: Azure App Service에서 BCDR (비즈니스 연속성 및 재해 복구) 기능을 유지 하는 데 도움이 되는 방법에 대해 알아봅니다. Azure에서 지역 전체 오류 로부터 앱을 복구 합니다.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073734"
---
# <a name="move-an-app-service-app-to-another-region"></a>App Service 앱을 다른 지역으로 이동

이 문서에서는 전체 Azure 지역에 영향을 주는 재해 발생 시 다른 Azure 지역에서 App Service 리소스를 다시 온라인 상태로 전환 하는 방법을 설명 합니다. 재해가 발생 하면 전체 Azure 지역이 오프 라인 상태가 되 면 해당 지역에서 호스트 되는 모든 App Service 앱은 재해 복구 모드에 배치 됩니다. 앱을 다른 지역으로 복원 하거나 영향을 받는 앱에서 파일을 복구 하는 데 도움이 되는 기능을 사용할 수 있습니다.

App Service 리소스는 지역에 따라 달라 지 며 지역 간에 이동할 수 없습니다. 앱을 다른 지역의 새 앱으로 복원한 다음 새 앱에 대 한 미러링 구성 또는 리소스를 만들어야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 없음 [스냅숏에서 복원은](app-service-web-restore-snapshots.md) 일반적으로 **프리미엄** 계층이 필요 하지만 재해 복구 모드에서 영향 받는 앱이 있는 계층에 관계 없이 영향을 받는 앱에 대해 자동으로 사용 하도록 설정 됩니다.

## <a name="prepare"></a>준비

영향을 받는 앱에서 현재 사용 하는 모든 App Service 리소스를 식별 합니다. 예를 들면 다음과 같습니다.

- App Service 앱
- [App Service 계획](overview-hosting-plans.md)
- [배포 슬롯](deploy-staging-slots.md)
- [Azure에서 구매한 사용자 지정 도메인](manage-custom-dns-buy-domain.md)
- [SSL 인증서](configure-ssl-certificate.md)
- [Azure Virtual Network 통합](web-sites-integrate-with-vnet.md)
- [하이브리드 연결](app-service-hybrid-connections.md).
- [관리 ID](overview-managed-identity.md)
- [백업 설정](manage-backup.md)

가져온 인증서 또는 하이브리드 연결과 같은 특정 리소스에는 다른 Azure 서비스와의 통합이 포함 되어 있습니다. 이러한 리소스를 지역 간에 이동 하는 방법에 대 한 자세한 내용은 각 서비스에 대 한 설명서를 참조 하세요.

## <a name="restore-app-to-a-different-region"></a>앱을 다른 지역으로 복원

1. 영향을 받는 앱과 *다른* Azure 지역에 새 App Service 앱을 만듭니다. 재해 복구 시나리오의 대상 앱입니다.

1. [Azure Portal](https://portal.azure.com)에서 영향을 받는 앱의 관리 페이지로 이동 합니다. 실패 한 Azure 지역에서 영향을 받는 앱은 경고 텍스트를 표시 합니다. 경고 텍스트를 클릭 합니다.

    ![영향을 받는 앱 페이지의 스크린샷 상태를 설명 하 고 앱 복원 링크를 제공 하는 경고 알림이 표시 됩니다.](media/manage-disaster-recovery/restore-start.png)

1. **백업 복원** 페이지에서 다음 표에 따라 복원 작업을 구성 합니다. 완료되었으면 **확인**을 클릭합니다.

   | 설정 | 값 | 설명 |
   |-|-|-|
   | **스냅숏 (미리 보기)** | 스냅숏을 선택 합니다. | 가장 최근의 두 스냅숏이 제공 됩니다. |
   | **복원 대상** | **기존 앱** | 다음을 클릭 하 여 **복원 대상 앱을 변경** 하 고 대상 앱을 선택 합니다. 재해 시나리오에서는 다른 Azure 지역에 있는 앱에만 스냅숏을 복원할 수 있습니다. |
   | **사이트 구성 복원** | **예** | |

    ![백업 복원 페이지의 스크린샷 특정 스냅숏, 앞의 표에 나열 된 옵션 및 확인 단추가 강조 표시 됩니다.](media/manage-disaster-recovery/restore-configure.png)

3. 대상 앱의 [다른 모든 항목](#prepare) 을 구성 하 여 영향을 받는 앱을 미러링 하 고 구성을 확인 합니다.

4. 사용자 지정 도메인이 대상 앱을 가리키도록 준비 되 면 [도메인 이름을 다시 매핑합니다](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>앱 콘텐츠만 복구

복원 하지 않고 영향 받는 앱에서 파일을 복구 하려는 경우 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에서 영향을 받는 앱의 관리 페이지로 이동 하 고 **게시 프로필 가져오기**를 클릭 합니다.

    ![영향을 받는 앱 페이지의 스크린샷 경고 알림이 표시 되지만 강조 표시 되지 않습니다. 대신 게시 프로필 가져오기 항목이 강조 표시 됩니다.](media/manage-disaster-recovery/get-publish-profile.png)

1. 다운로드 한 파일을 열고 이름에가 포함 된 게시 프로필을 찾습니다 `ReadOnly - FTP` . 이는 재해 복구 프로필입니다. 예를 들면 다음과 같습니다.

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    3 개의 특성 값을 복사 합니다. 
        
    - `publishUrl`: FTP 호스트 이름
    - `userName` 및 `userPWD` : FTP 자격 증명

1. 원하는 FTP 클라이언트를 사용 하 여 호스트 이름 및 자격 증명을 사용 하 여 영향을 받는 앱의 FTP 호스트에 연결 합니다.

1. 연결 된 후에는 전체 */>/* > 폴더를 다운로드 합니다. 다음 스크린샷에서는 [FileZilla](https://filezilla-project.org/)에서를 다운로드 하는 방법을 보여 줍니다.

    ![FileZilla 파일 계층의 스크린샷 Wwwroot 폴더가 강조 표시 되 고 바로 가기 메뉴가 표시 됩니다. 해당 메뉴에서 다운로드가 강조 표시 됩니다.](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>다음 단계
[스냅샷에서 Azure의 앱 복원](app-service-web-restore-snapshots.md)
