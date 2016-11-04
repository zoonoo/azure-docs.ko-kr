---
title: Azure에서 앱 복원
description: 백업에서 앱을 복원하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: wpickett
editor: jimbe

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin

---
# Azure에서 앱 복원
이 문서에서는 이전에 백업한 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에서 앱을 복원하는 방법을 보여 줍니다([Azure에서 앱 백업](web-sites-backup.md) 참조). 요청 시 연결된 데이터베이스(SQL 데이터베이스 또는 MySQL)와 함께 앱을 이전 상태로 복원하거나, 원래 앱의 백업 중 하나를 기반으로 새 앱을 만들 수 있습니다. 최신 버전과 병행하여 실행되는 앱을 새로 만드는 것이 A/B를 테스트하는 데 유용할 수 있습니다.

백업에서 복원하는 방식은 **표준** 및 **프리미엄** 계층에서 실행되는 앱에 사용할 수 있습니다. 앱 확장에 대한 자세한 내용은 [Azure에서 앱 확장](web-sites-scale.md)을 참조하세요. **프리미엄** 계층을 사용하면 **표준** 계층보다 더 많은 매일 백업을 수행할 수 있습니다.

<a name="PreviousBackup"></a>

## 기존 백업에서 앱 복원
1. Azure 포털에서 앱의 **설정** 블레이드에서 **백업**을 클릭하여 **백업** 블레이드를 표시합니다. 그런 다음 명령 모음에서 **지금 복원**을 클릭합니다.
   
    ![지금 복원 선택][ChooseRestoreNow]
2. **복원** 블레이드에서 먼저 백업 소스를 선택합니다.
   
    ![](./media/web-sites-restore/021ChooseSource.png)
   
    **앱 백업** 옵션에는 현재 앱의 모든 기존 백업을 표시하며 백업 중 하나를 쉽게 선택할 수 있습니다. **저장소** 옵션을 사용하면 구독의 기존 Azure 저장소 계정 및 컨테이너에서 백업 ZIP 파일을 선택할 수 있습니다. 다른 앱의 백업을 복원하려는 경우 **저장소** 옵션을 사용합니다.
3. 그런 다음 **복원 대상**에서 앱 복원 대상을 지정합니다.
   
    ![](./media/web-sites-restore/022ChooseDestination.png)
   
   > [!WARNING]
   > **덮어쓰기**를 선택하면 현재 앱의 모든 기존 데이터가 지워집니다. **확인**을 클릭하기 전에 수행하려는 작업이 정확히 맞는지 확인하세요.
   > 
   > 
   
    **기존 앱**을 선택하여 같은 리소스 그룹에 있는 다른 앱으로 앱 백업을 복원할 수 있습니다. 이 옵션을 사용하려면 리소스 그룹에 미러링 데이터베이스가 앱 백업에 정의된 것으로 구성되어 있는 다른 앱이 이미 만들어져 있어야 합니다.
4. **확인**을 클릭합니다.

<a name="StorageAccount"></a>

## 저장소 계정에서 백업 다운로드 또는 삭제
1. Azure 포털의 기본 **찾아보기** 블레이드에서 **저장소 계정**을 선택합니다.
   
    기존 저장소 계정 목록이 표시됩니다.
2. 다운로드 또는 삭제하려는 백업을 포함하는 저장소 계정을 선택합니다.
   
    저장소 계정에 대한 블레이드가 표시됩니다.
3. 저장소 계정 블레이드에서 원하는 컨테이너를 선택합니다.
   
    ![컨테이너 보기][ViewContainers]
4. 다운로드 또는 삭제하려는 백업 파일을 선택합니다.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. 수행하려는 작업에 따라 **다운로드** 또는 **삭제**를 클릭합니다.

<a name="OperationLogs"></a>

## 복원 작업 모니터링
1. 앱 복원 작업의 성공 또는 실패에 대한 자세한 내용을 보려면 Azure 포털의 **감사 로그** 블레이드로 이동합니다.
   
    **감사 로그** 블레이드는 단계, 상태, 리소스 및 시간 세부 정보와 함께 작업의 모든 것을 표시합니다.
2. 아래로 스크롤하여 원하는 복원 작업을 찾은 후 클릭하여 선택합니다.

세부 정보 블레이드가 복원 작업과 관련된 사용 가능한 정보를 표시합니다.

## 다음 단계
또한 REST API를 사용하여 앱 서비스 앱을 백업 및 복원할 수 있습니다([REST를 사용하여 앱 서비스 앱 백업 및 복원](websites-csm-backup.md) 참조).

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 시작 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png


<!---HONumber=AcomDC_0713_2016-->