<properties
   pageTitle="Azure 백업 - Azure 가져오기/내보내기 서비스를 사용한 오프라인 백업 또는 초기 시드 작업ㅣMicrosoft Azure"
   description="Azure 백업이 Azure 가져오기/내보내기 서비스를 사용하여 네트워크를 통해 데이터를 보내는 방법에 대해 알아봅니다. 이 문서에서는 Azure 가져오기 내보내기 서비스를 사용한 초기 백업 데이터의 오프라인 시드 작업을 설명합니다."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="shreeshd"
   editor=""/>
<tags  ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="08/28/2015" ms.author="aashishr"; "jimpark"/>

# Azure 백업의 오프라인 백업 워크플로

Azure 백업은 초기 백업 데이터를 신속하게 전송할 수 있도록 하는 Azure 가져오기/내보내기 서비스와 긴밀하게 통합됩니다. 긴 대기 시간 및 낮은 대역폭 네트워크를 통해 전송해야 하는 수 TB의 초기 백업 데이터가 있다면 Azure 가져오기/내보내기 서비스를 사용하여 하나 이상의 하드 드라이브에 있는 초기 백업 복사본을 Azure 데이터 센터로 배송할 수 있습니다. 이 문서는 이 워크플로를 완료하는 데 필요한 단계에 대한 개요를 제공합니다.

## 개요

Azure 백업 및 Azure 가져오기/내보내기를 사용하면 디스크를 통해 오프라인으로 간단하고 직관적으로 Azure에 데이터를 업로드할 수 있습니다. 네트워크를 통해 초기 전체 복사본을 전송하는 대신 백업 데이터가 *준비 위치*에 기록됩니다. 준비 위치는 직접 연결된 저장소 또는 네트워크 공유일 수 있습니다. 초기 복사가 완료되면 이 데이터가 *Azure 가져오기/내보내기 도구*를 사용하여 SATA 드라이브에 기록되고, 마지막으로 Azure 데이터 센터에 배송됩니다. 초기 백업의 크기에 따라 이 작업을 완료하는 데 하나 이상의 SATA 드라이브가 필요할 수 있습니다. Azure 가져오기/내보내기 도구는 이러한 시나리오를 고려합니다. 백업이 디스크에 기록된 후에는 Azure에 업로드하기 위해 가장 가까운 데이터 센터 위치로 배송될 수 있습니다. 그런 다음 Azure 백업이 백업 자격 증명 모음에 백업 데이터를 복사하고 증분 백업이 예약됩니다.

## 필수 조건

1. [여기](../storage-import-export-service.md)에 나와 있는 Azure 가져오기 내보내기 워크플로를 익히는 것이 중요합니다.
2. 워크플로를 시작하기 전에 Azure 백업 자격 증명 모음이 생성되었고, 자격 증명 모음 자격 증명이 다운로드되었고, Windows Server/Windows 클라이언트 또는 System Center Data Protection Manager(SCDPM) 서버에 Azure 백업 에이전트가 설치되었고, Azure 백업 자격 증명 모음에 컴퓨터가 등록되었는지 확인합니다.
3. [여기](https://manage.windowsazure.com/publishsettings)에서 데이터를 백업하려는 컴퓨터의 Azure 게시 설정 파일을 다운로드합니다.
4. *준비 위치*를 준비합니다. 네트워크 공유 나 컴퓨터의 추가 드라이브가 될 수 있습니다. 준비 위치에 초기 복사본을 저장할 디스크 공간이 충분한지 확인합니다. 예를 들어 500GB 파일 서버를 백업하려는 경우 준비 영역이 500GB인지 확인합니다(실제로는 이보다 작은 크기가 사용됨). 준비 영역은 '임시 저장소'이며 이 워크플로 중에 일시적으로 사용됩니다.
5. 외부 SATA 드라이브 기록기 및 외부 3.5인치 SATA 드라이브입니다. 3.5인치 SATA II/III 하드 드라이브만 가져오기/내보내기 서비스에서 사용하도록 지원됩니다. 6TB보다 큰 하드 드라이브는 지원되지 않습니다. SATA II/III USB 어댑터를 사용하여 외부에서 대부분의 컴퓨터에 SATA II/III 디스크를 연결할 수 있습니다. Azure 가져오기/내보내기 설명서에서 이 서비스에서 지원되는 최신 드라이브를 확인하세요.
6. SATA 드라이브 기록기가 연결된 컴퓨터에서 BitLocker를 활성화합니다.
7. [여기](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)에 있는 Azure 가져오기/내보내기 도구를 SATA 드라이브 기록기가 연결된 컴퓨터에 다운로드합니다.

## 워크플로
이 섹션에 제공된 정보는 데이터를 Azure 데이터 센터에 배송하고 Azure 저장소에 업로드할 수 있도록 **오프라인 백업** 워크플로를 완료하기 위한 것입니다. 가져오기 서비스 또는 프로세스의 특정 측면에 대 한 질문이 있으면 [위에](../storage-import-export-service.md) 언급된 가져오기 서비스 개요를 참조하세요.

### 오프라인 백업 시작

1. Windows Server, Windows 클라이언트 또는 SCDPM에서 백업 예약 중에는 다음 화면이 표시됩니다.

    ![가져오기 화면](./media/backup-azure-backup-import-export/importscreen.png)

    SCDPM의 해당 화면은 다음과 같습니다. <br/> ![DPM 가져오기 화면](./media/backup-azure-backup-import-export/dpmoffline.png)

    설명:

    - **준비 위치** - 초기 백업 복사본을 쓸 임시 저장소 위치를 나타냅니다. 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다.
    - **Azure 가져오기 작업 이름** - 이 워크플로를 완료하기 위해 Azure 포털에서 *가져오기 작업*을 만들어야 합니다(문서의 뒷부분에 설명됨). Azure 포털에서 나중에 사용하려는 입력도 제공합니다.
    - **Azure 게시 설정** -구독 프로필에 대한 정보를 포함하는 XML 파일입니다. 또한 구독에 연결된 보안 자격 증명도 포함합니다. 파일은 [여기](https://manage.windowsazure.com/publishsettings)에서 다운로드할 수 있습니다. 게시 설정 파일에 대한 로컬 경로를 제공합니다.
    - **Azure 구독 ID** - Azure 가져오기 작업을 시작 하려는 Azure 구독 ID를 제공합니다. 여러 Azure 구독이 있는 경우 가져오기 작업과 연결된 ID를 사용합니다.
    - **Azure 저장소 계정** - 이 가져오기 작업과 연결할 Azure 저장소 계정의 이름을 입력합니다.
    - **Azure 저장소 컨테이너** - 이 작업의 데이터를 가져올 대상 저장소 BLOB의 이름을 입력합니다.

2. 워크플로를 완료하고 Azure 백업 mmc에서 **지금 백업**을 선택하여 오프라인 백업 복사본을 시작합니다. 초기 백업은 이 단계의 일환으로 준비 영역에 기록됩니다.

    ![지금 백업](./media/backup-azure-backup-import-export/backupnow.png)

    SCDPM의 해당 워크플로는 **보호 그룹**을 클릭하고 **복구 지점 만들기** 옵션을 선택하면 활성화됩니다. 그런 다음 **온라인 보호** 옵션을 선택합니다.

    ![DPM 지금 백업](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    작업이 완료되면 준비 위치에 *. AIBBlob* 및 *.BaseBlob* 파일이 만들어집니다.

    ![출력](./media/backup-azure-backup-import-export/opbackupnow.png)

### SATA 드라이브 준비

1. [Microsoft Azure 가져오기/내보내기 도구](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409)를 *복사 컴퓨터*에 다운로드합니다. 다음 명령 집합을 실행하려는 컴퓨터에서 (이전 단계의) 준비 위치에 액세스할 수 있는지 확인합니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 같을 수 있습니다.

2. *WAImportExport.zip* 파일의 압축을 풉니다. SATA 드라이브를 포맷하는 *WAImportExport* 도구를 실행하고, SATA 드라이브에 백업 데이터를 기록하고 암호화합니다. 컴퓨터에 BitLocker가 활성화되었는지 확인한 후 다음 명령을 실행합니다. <br/>

    *.\\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*


| 매개 변수 | 설명
|-------------|-------------|
| /j:<*JournalFile*>| 저널 파일에 대한 경로입니다. 각 드라이브에는 정확히 하나의 저널 파일이 있어야 합니다. 저널 파일은 대상 드라이브에 있지 않아야 합니다. 저널 파일 확장명은 .jrn이고 이 명령 실행의 일부로 만들어집니다.|
|/id:<*SessionId*> | 세션 ID는 *복사 세션*을 식별합니다. 중단된 복사 세션의 정확한 복구를 위해 사용됩니다. 복사 세션에서 복사된 파일은 대상 드라이브의 세션 ID의 이름을 딴 디렉터리에 저장됩니다.|
| /sk:<*StorageAccountKey*> | 데이터를 가져올 저장소 계정의 계정 키입니다. |
| /BlobType | **PageBlob**를 지정합니다. 이 워크플로는 PageBlob 옵션을 지정하는 경우에만 성공합니다. 이는 기본 옵션이 아니며 이 명령에서 언급해야 합니다. |
|/t:<*TargetDriveLetter*> | 현재 복사 세션에 대한 대상 하드 드라이브의 드라이브 문자입니다(후행 콜론 없음).|
|/format | 이 매개 변수는 드라이브를 포맷해야 할 때 지정합니다. 그렇지 않은 경우 생략합니다. 이 도구는 드라이브를 포맷하기 전에 콘솔에서 확인 메시지를 표시합니다. 확인을 하지 않으려면 /silentmode 매개 변수를 지정합니다.|
|/encrypt | 드라이브가 아직 BitLocker로 암호화되지 않았고 이 도구를 통해 암호화해야 하는 경우 이 매개 변수를 지정합니다. BitLocker로 드라이브를 이미 암호화한 경우 이 매개 변수를 생략하고/bk 매개 변수를 지정하여 기존 BitLocker 키를 제공합니다. /format 매개 변수를 지정하는 경우 /encrypt 매개 변수도 지정 해야 합니다. |
|/srcdir:<*SourceDirectory*> | 대상 드라이브에 복사할 파일이 들어 있는 원본 디렉터리입니다. 디렉터리 경로는 절대 경로(상대 경로 아님)이어야 합니다.|
|/dstdir:<*DestinationBlobVirtualDirectory*> | Microsoft Azure 저장소 계정의 대상 가상 디렉터리에 대한 경로입니다. 대상 가상 디렉터리 또는 BLOB를 지정할 때는 유효한 컨테이너 이름을 사용해야 합니다. 컨테이너 이름은 소문자여야 합니다.|

  >[AZURE.NOTE]저널 파일은 워크플로의 전체 정보를 캡처하는 WAImportExport 폴더에 생성됩니다. Azure 포털에서 가져오기 작업을 만들 때 이 파일이 필요합니다.

  ![PowerShell 출력](./media/backup-azure-backup-import-export/psoutput.png)

### Azure 포털에서 가져오기 작업 만들기
1. [관리 포털](https://manage.windowsazure.com/)에서 저장소 계정으로 이동하고 **가져오기/내보내기**를 클릭한 후 작업 창에서 **가져오기 작업 만들기**를 클릭합니다.

    ![포털](./media/backup-azure-backup-import-export/azureportal.png)

2. 마법사의 1단계에서 드라이브를 준비했으며 사용 가능한 드라이브 저널 파일이 있음을 지정합니다. 마법사의 2단계에서는 해당 가져오기 작업을 담당하는 사용자의 연락처 정보를 제공합니다.
3. 3단계에서는 이전 섹션에서 얻은 드라이브 저널 파일을 업로드합니다.
4. 4단계에서는 가져오기 작업의 설명 이름을 입력합니다. 입력하는 이름에는 소문자, 숫자, 하이픈 및 밑줄만 포함될 수 있으며 문자로 시작해야 하고 공백이 포함될 수 없습니다. 작업이 진행 중인 동안 그리고 작업이 완료되면 선택한 이름을 사용하여 작업을 추적합니다.
5. 이제 목록에서 데이터 센터 지역을 선택합니다. 데이터 센터 지역은 패키지를 발송해야 하는 데이터 센터 및 주소를 나타냅니다.

    ![DC](./media/backup-azure-backup-import-export/dc.png)

6. 5단계에서는 목록에서 반품 택배사를 선택하고 택배사 계정 번호를 입력합니다. 가져오기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다.

7. 디스크를 배송하고 배송 상태를 추적하려면 추적 번호를 입력합니다. 디스크가 데이터 센터에 도착하면 저장소 계정에 복사되고 상태가 업데이트됩니다.

    ![완료 상태](./media/backup-azure-backup-import-export/complete.png)

### 워크플로 완료
초기 백업 데이터를 저장소 계정에서 사용할 수 있게 되면 Azure 백업 에이전트가 데이터의 내용을 이 계정에서 다중 비분할된 백업 저장소 계정에 복사합니다. 다음 일정 백업 시 Azure 백업 에이전트가 초기 백업 복사본을 통해 증분 백업을 수행합니다.

## 다음 단계
- Azure 가져오기/내보내기 워크플로에 대한 질문이 있으면 이 [문서](../storage-import-export-service.md)를 참조하세요.
- 워크플로에 대한 질문이 있으면 Azure 백업 [FAQ](backup-azure-backup-faq.md)의 오프라인 백업 섹션을 참조하세요.

<!---HONumber=Oct15_HO3-->