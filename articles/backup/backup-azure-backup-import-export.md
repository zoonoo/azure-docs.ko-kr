---
title: "Azure 백업 - Azure 가져오기/내보내기 서비스를 사용한 오프라인 백업 또는 초기 시드 작업 | Microsoft Docs"
description: "Azure 백업이 Azure 가져오기/내보내기 서비스를 사용하여 네트워크를 통해 데이터를 보내는 방법에 대해 알아봅니다. 이 문서에서는 Azure 가져오기 내보내기 서비스를 사용한 초기 백업 데이터의 오프라인 시드 작업을 설명합니다."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2016
ms.author: jimpark;saurabhsensharma;nkolli;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 2876f3a7e8e83dc05801d914c7582a4f1fd92e98


---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure 백업의 오프라인 백업 워크플로
Azure 백업은 데이터를 Azure에 처음 전체 백업하는 동안 네트워크 및 저장소 비용을 절약하는 여러 가지 기본 제공 효율성 향상 기능이 있습니다. 초기 "전체" 백업은 일반적으로 많은 양의 데이터를 전송하며 델타/증분만 전송하는 후속 백업에 비해 네트워크 대역폭을 더 많이 요구합니다. Azure 백업은 초기 백업을 압축합니다. 오프라인 시드의 프로세스를 통해 Azure 백업은 디스크를 사용하여 오프라인으로 압축된 초기 백업 데이터를 Azure에 업로드할 수 있습니다.  

Azure 백업의 오프라인 시드 프로세스는 디스크를 사용하여 데이터를 Azure에 전송할 수 있는 [Azure 가져오기/내보내기 서비스](../storage/storage-import-export-service.md) 와 밀접하게 통합됩니다. 긴 대기 시간 및 낮은 대역폭 네트워크를 통해 전송해야 하는 TB(테라바이트)의 초기 백업 데이터가 있다면 오프라인 시드 워크플로를 사용하여 하나 이상의 하드 드라이브에 있는 초기 백업 복사본을 Azure 데이터 센터로 배송할 수 있습니다. 이 문서는 이 워크플로를 완료하는 단계에 대한 개요를 제공합니다.

## <a name="overview"></a>개요
Azure 백업의 오프라인 시드 기능 및 Azure 가져오기/내보내기를 사용하면 디스크를 사용하여 오프라인으로 간단하게 Azure에 데이터를 업로드할 수 있습니다. 네트워크를 통해 초기 전체 복사본을 전송하는 대신 백업 데이터가 *준비 위치*에 기록됩니다. Azure 가져오기/내보내기 도구를 사용하여 스테이징 위치에 복사를 완료한 후 데이터의 양에 따라 이 데이터를 하나 이상의 SATA 드라이브에 기록합니다. 결국 이러한 드라이브를 가장 가까운 Azure 데이터 센터에 배송합니다.

[Azure 백업의 2016년 8월 업데이트(및 그 이상)](http://go.microsoft.com/fwlink/?LinkID=229525) 는 다음을 수행하는 AzureOfflineBackupDiskPrep이라는 *Azure 디스크 준비 도구*를 포함합니다.

* Azure 가져오기/내보내기 도구를 사용하여 드라이브를 Azure 가져오기에 적합하도록 준비하는 데 도움이 됩니다.
* Azure 백업의 이전 버전을 사용하여 수동으로 Azure 가져오기 작업을 만들지 않고 [Azure 클래식 포털](https://manage.windowsazure.com) 의 Azure 가져오기/내보내기 서비스에 적합하도록 자동으로 작업을 생성합니다.

Azure에 백업 데이터 업로드를 완료한 후 Azure 백업이 백업 데이터를 백업 자격 증명 모음에 복사하고 증분 백업이 예약됩니다.

> [!NOTE]
> Azure 디스크 준비 도구를 사용하려면 Azure 백업의 2016년 8월 업데이트(또는 그 이상)를 설치하고 해당 업데이트를 사용하여 워크플로의 모든 단계를 수행해야 합니다. Azure 백업의 이전 버전을 사용하는 경우 이 문서의 뒷부분에 나오는 섹션에서 설명한 대로 Azure 가져오기/내보내기 도구를 사용하여 SATA 드라이브를 준비할 수 있습니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
* [Azure 가져오기/내보내기 워크플로를 숙지합니다](../storage/storage-import-export-service.md).
* 워크플로를 시작하기 전에 다음을 확인합니다.
  * Azure 백업 자격 증명 모음이 만들어졌습니다.
  * 자격 증명 모음 자격 증명이 다운로드되었습니다.
  * Azure 백업 에이전트가 Windows Server/Windows 클라이언트 또는 System Center Data Protection Manager 서버에 설치되었고 컴퓨터가 Azure 백업 자격 증명 모음으로 등록됩니다.
* [Azure 게시 설정 파일을 다운로드](https://manage.windowsazure.com/publishsettings) 합니다.
* 준비 위치를 준비합니다. 네트워크 공유 또는 컴퓨터의 추가 드라이브가 될 수 있습니다. 스테이징 위치는 임시 저장소이며 이 워크플로 중에 일시적으로 사용됩니다. 준비 위치에 초기 복사본을 저장할 디스크 공간이 충분한지 확인합니다. 예를 들어 500GB 파일 서버를 백업하려는 경우 준비 영역이 500GB인지 확인합니다. (압축으로 인해 더 작은 양이 사용됩니다.)
* 지원되는 드라이브를 사용하고 있는지 확인합니다. 3.5인치 SATA II/III 하드 드라이브만 가져오기/내보내기 서비스에서 사용하도록 지원됩니다. 8TB보다 큰 하드 드라이브는 지원되지 않습니다. SATA II/III USB 어댑터를 사용하여 외부에서 대부분의 컴퓨터에 SATA II/III 디스크를 연결할 수 있습니다. Azure 가져오기/내보내기 설명서에서 서비스가 지원하는 최신 드라이브를 확인하세요.
* SATA 드라이브 기록기가 연결된 컴퓨터에서 BitLocker를 활성화합니다.
* [Azure 가져오기/내보내기 도구를 다운로드](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) 합니다. Azure 백업의 2016년 8월 업데이트(또는 그 이상)를 다운로드하여 설치한 경우 이 단계가 필요하지 않습니다.

## <a name="workflow"></a>워크플로
이 섹션에 제공된 정보는 데이터를 Azure 데이터 센터에 배송하고 Azure 저장소에 업로드할 수 있도록 오프라인 백업 워크플로를 완료하는 데 도움이 됩니다. 가져오기 서비스 또는 프로세스의 모든 측면에 대한 질문이 있으면 앞에서 언급한 [가져오기 서비스 개요](../storage/storage-import-export-service.md)를 참조하세요.

### <a name="initiate-offline-backup"></a>오프라인 백업 시작
1. 백업을 예약할 때 다음 화면이 표시됩니다(Windows Server, Windows 클라이언트 또는 System Center Data Protection Manager에서).
   
    ![가져오기 화면](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)
   
    다음은 System Center Data Protection Manager의 해당 화면입니다. <br/>
    ![DPM 가져오기 화면](./media/backup-azure-backup-import-export/dpmoffline.png)
   
    입력에 대한 설명은 다음과 같습니다.
   
    * **스테이징 위치**: 초기 백업 복사본을 쓸 임시 저장소 위치입니다. 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 서로 다르면 스테이징 위치의 전체 네트워크 경로를 지정하는 것이 좋습니다.
    * **Azure 가져오기 작업 이름**: Azure 가져오기 서비스 및 Azure 백업이 디스크에서 Azure에 보내지는 데이터의 전송을 추적하는 고유한 이름입니다.
    * **Azure 게시 설정**: 구독 프로필에 대한 정보를 포함하는 XML 파일입니다. 또한 구독에 연결된 보안 자격 증명도 포함합니다. [파일을 다운로드](https://manage.windowsazure.com/publishsettings)할 수 있습니다. 게시 설정 파일에 대한 로컬 경로를 제공합니다.
    * **Azure 구독 ID**: Azure 가져오기 작업을 시작하려는 구독에 대한 Azure 구독 ID입니다. 여러 Azure 구독이 있는 경우 가져오기 작업과 연결하려는 구독의 ID를 사용합니다.
    * **Azure Storage 계정**: Azure 가져오기 작업과 연결될 제공된 Azure 구독의 클래식 유형 저장소 계정입니다.
    * **Azure Storage 컨테이너**: 이 작업의 데이터를 가져올 Azure 저장소 계정의 대상 저장소 Blob 이름입니다.
     
    > [!NOTE]
    > 백업을 위해 서버를 [Azure 포털](https://portal.azure.com) 에서 Azure 복구 서비스 자격 증명에 등록했는데 클라우드 솔루션 공급자(CSP) 구독에 없는 경우, 여전히 Azure 포털에서 클래식 유형 저장소 계정을 만들고 오프라인 백업 워크플로에 해당 계정을 사용할 수 있습니다.
    > 
    > 
     
     다음 단계에서 다시 입력해야 하기 때문에 이러한 모든 정보를 저장합니다. Azure 디스크 준비 도구를 사용하여 디스크를 준비하는 경우 *스테이징 위치* 만 필요합니다.    
2. 워크플로를 완료한 다음 Azure 백업 관리 콘솔에서 **지금 백업** 을 선택하여 오프라인 백업 복사를 시작합니다. 초기 백업은 이 단계의 일환으로 준비 영역에 기록됩니다.
   
    ![지금 백업](./media/backup-azure-backup-import-export/backupnow.png)
   
    System Center Data Protection Manager에서 해당 워크플로를 완료하려면 **보호 그룹**을 마우스 오른쪽 단추로 클릭한 다음 **복구 지점 만들기** 옵션을 선택합니다. 그런 다음 **온라인 보호** 옵션을 선택합니다.
   
    ![DPM 지금 백업](./media/backup-azure-backup-import-export/dpmbackupnow.png)
   
    작업이 완료되면 스테이징 위치를 디스크 준비에 사용할 수 있습니다.
   
    ![백업 진행](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>Azure 디스크 준비 도구를 사용하여 SATA 드라이브 준비 및 Azure 가져오기 작업 만들기
Azure 디스크 준비 도구는 다음 경로에 있는 복구 서비스 에이전트(2016년 8월 업데이트 및 그 이상)의 설치 디렉터리에서 사용할 수 있습니다.

   *\Microsoft* *Azure* *Recovery* *Services* *Agent\Utils\*

1. 디렉터리로 이동하고 준비할 드라이브가 탑재될 복사 컴퓨터에 **AzureOfflineBackupDiskPrep** 디렉터리를 복사합니다. 복사 컴퓨터를 기준으로 다음 사항을 확인합니다.
   
    * 복사 컴퓨터는 **오프라인 백업 시작** 워크플로에 제공된 것과 같은 네트워크 경로를 사용하여 오프라인 시드 워크플로에 대해 제공된 스테이징 위치에 액세스할 수 있습니다.
    * BitLocker가 컴퓨터에서 활성화됩니다.
    * 컴퓨터는 Azure 포털에 액세스할 수 있습니다.
     
    필요한 경우 복사 컴퓨터는 원본 컴퓨터와 같을 수 있습니다.
2. Azure 디스크 준비 도구 디렉터리를 현재 디렉터리로 하여 승격된 명령 프롬프트를 복사 컴퓨터에서 열고 다음 명령을 실행합니다.
   
    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | 매개 변수 | 설명 |
    | --- | --- |
    | s:&lt;*스테이징 위치 경로*&gt; |**오프라인 백업 시작** 워크플로에 입력한 스테이징 위치에 대한 경로를 제공하는 데 사용되는 필수 입력입니다. |
    | p:&lt;*PublishSettingsFile에 대한 경로*&gt; |**오프라인 백업 시작** 워크플로에 입력한 **Azure 게시 설정** 파일에 대한 경로를 제공하는 데 사용되는 옵션 입력입니다. |

    > [!NOTE]
    > 복사 컴퓨터 및 원본 컴퓨터가 서로 다른 경우 &lt;PublishSettingFile에 대한 경로&gt; 값은 필수입니다.
    > 
    > 

    명령을 실행할 때 도구에서 준비해야 할 드라이브에 해당하는 Azure 가져오기 작업의 선택을 요청합니다. 제공된 스테이징 위치와 연결된 가져오기 작업이 하나만 있는 경우 다음과 같은 화면이 나타납니다.

    ![Azure 디스크 준비 도구 입력](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Azure에 전송하도록 준비하려는 탑재된 디스크에 대한 드라이브 문자를 후행 콜론 없이 입력합니다. 메시지가 표시될 때 드라이브의 서식에 대한 확인을 제공합니다.

    그런 다음 도구가 백업 데이터를 사용하여 디스크를 준비하기 시작합니다. 제공된 디스크에 백업 데이터에 대한 충분한 공간이 없는 경우 도구에서 메시지를 표시할 때 추가 디스크에 연결해야 할 수 있습니다. <br/>

    도구의 성공적인 실행의 끝에 제공한 하나 이상의 디스크가 Azure에 전달하도록 준비됩니다. 또한 **오프라인 백업 시작** 워크플로 중에 제공한 이름의 가져오기 작업이 Azure 클래식 포털에서 생성됩니다. 마지막으로 도구는 디스크를 배송해야 하는 Azure 데이터 센터에 대한 배송 주소 및 Azure 클래식 포털에서 가져오기 작업을 찾을 링크를 표시합니다.

    ![Azure 디스크 준비 완료](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. 디스크를 도구가 제공한 주소로 배송하고 나중에 참조할 수 있도록 추적 번호를 유지합니다.<br/>

5. 도구가 표시한 링크로 이동하면 **오프라인 백업 시작** 워크플로에서 지정한 Azure 저장소 계정이 표시됩니다. 여기서 저장소 계정의 **가져오기/내보내기** 탭에서 새로 생성된 가져오기 작업을 볼 수 있습니다.
   
    ![생성된 가져오기 작업](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. 페이지 아래쪽의 **배송 정보**를 클릭하여 다음 화면에 표시된 것처럼 사용자의 연락처 세부 정보를 업데이트합니다. Microsoft는 가져오기 작업이 완료된 후 이 정보를 사용하여 사용자의 디스크를 배송합니다.

    ![연락처 정보](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. 다음 화면에서 배송 세부 정보를 입력합니다. Azure 데이터 센터에 배송한 디스크에 해당하는 **배달 배송업체** 및 **추적 번호** 세부 정보를 제공합니다.
   
    ![배송 정보](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>워크플로 완료
가져오기 작업이 완료된 후 사용자의 저장소 계정에서 초기 백업 데이터를 사용할 수 있습니다. 이때 복구 서비스 에이전트가 데이터의 내용을 이 계정에서 백업 자격 증명 모음 또는 복구 서비스 자격 증명 모음 중 해당하는 것에 복사합니다. 다음 일정 백업 시 Azure 백업 에이전트가 초기 백업 복사본을 통해 증분 백업을 수행합니다.

> [!NOTE]
> 다음 섹션은 Azure 디스크 준비 도구에 대한 액세스 권한이 없는 Azure 백업의 이전 버전의 사용자에게 적용됩니다.
> 
> 

### <a name="prepare-a-sata-drive"></a>SATA 드라이브 준비
1. [Microsoft Azure Import/Export 도구](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409)를 복사 컴퓨터에 다운로드합니다. 다음 명령 집합을 실행하려는 컴퓨터에서 준비 위치에 액세스할 수 있는지 확인합니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 같을 수 있습니다.

2. WAImportExport.zip 파일의 압축을 풉니다. SATA 드라이브를 포맷하는 WAImportExport 도구를 실행하고, SATA 드라이브에 백업 데이터를 기록하고 암호화합니다. 다음 명령을 실행하기 전에 컴퓨터에 BitLocker가 활성화되었는지 확인합니다. <br/>
   
    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`
   
    > [!NOTE]
    > Azure 백업의 2016년 8월 업데이트(또는 그 이상)를 설치한 경우 입력한 스테이징 위치가 **지금 백업** 화면에 표시된 것과 같으며 AIB 및 기본 Blob 파일을 포함하는지 확인합니다.
    > 
    > 

| 매개 변수 | 설명 |
| --- | --- |
| /j:<*JournalFile*> |저널 파일에 대한 경로입니다. 각 드라이브에는 정확히 하나의 저널 파일이 있어야 합니다. 저널 파일은 대상 드라이브에 있지 않아야 합니다. 저널 파일 확장명은 .jrn이고 이 명령 실행의 일부로 만들어집니다. |
| /id:<*SessionId*> |세션 ID는 복사 세션을 식별합니다. 중단된 복사 세션의 정확한 복구를 위해 사용됩니다. 복사 세션에서 복사된 파일은 대상 드라이브의 세션 ID의 이름을 딴 디렉터리에 저장됩니다. |
| /sk:<*StorageAccountKey*> |데이터를 가져올 저장소 계정의 계정 키입니다. 백업 정책/보호 그룹을 만들 때 입력한 키와 같아야 합니다. |
| /BlobType |Blob의 형식입니다. 이 워크플로는 **PageBlob** 이 지정된 경우에만 성공합니다. 이는 기본 옵션이 아니며 이 명령에서 언급해야 합니다. |
| /t:<*TargetDriveLetter*> |현재 복사 세션에 대한 대상 하드 드라이브의 후행 콜론 없는 드라이브 문자입니다. |
| /format |드라이브를 포맷하는 옵션입니다. 이 매개 변수는 드라이브를 포맷해야 할 때 지정합니다. 그렇지 않은 경우 생략합니다. 이 도구는 드라이브를 포맷하기 전에 콘솔에서 확인 메시지를 표시합니다. 확인을 하지 않으려면 /silentmode 매개 변수를 지정합니다. |
| /encrypt |드라이브를 암호화하는 옵션입니다. 드라이브가 아직 BitLocker로 암호화되지 않았고 이 도구를 통해 암호화해야 하는 경우 이 매개 변수를 지정합니다. BitLocker로 드라이브를 이미 암호화한 경우 이 매개 변수를 생략하고 /bk 매개 변수를 지정하고 기존 BitLocker 키를 제공합니다. /format 매개 변수를 지정하는 경우 /encrypt 매개 변수도 지정해야 합니다. |
| /srcdir:<*SourceDirectory*> |대상 드라이브에 복사할 파일이 들어 있는 원본 디렉터리입니다. 지정된 디렉터리 이름이 상대 경로가 아닌 전체를 포함하는지 확인합니다. |
| /dstdir:<*DestinationBlobVirtualDirectory*> |Azure 저장소 계정의 대상 가상 디렉터리에 대한 경로입니다. 대상 가상 디렉터리 또는 Blob을 지정할 때는 유효한 컨테이너 이름을 사용해야 합니다. 컨테이너 이름은 소문자여야 합니다.  이 컨테이너 이름은 백업 정책/보호 그룹을 만들 때 입력한 이름이어야 합니다. |

> [!NOTE]
> 저널 파일은 워크플로의 전체 정보를 캡처하는 WAImportExport 폴더에 생성됩니다. Azure 포털에서 가져오기 작업을 만들 때 이 파일이 필요합니다.
> 
> 

  ![PowerShell 출력](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>Azure 포털에서 가져오기 작업 만들기
1. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 저장소 계정으로 이동하고 **가져오기/내보내기**를 클릭한 후 작업 창에서 **가져오기 작업 만들기**를 클릭합니다.
   
    ![Azure 포털에서 가져오기/내보내기 탭](./media/backup-azure-backup-import-export/azureportal.png)

2. 마법사의 1단계에서 드라이브를 준비했으며 사용 가능한 드라이브 저널 파일이 있음을 지정합니다.

3. 마법사의 2단계에서는 해당 가져오기 작업을 담당하는 사용자의 연락처 정보를 제공합니다.

4. 3단계에서는 이전 섹션에서 얻은 드라이브 저널 파일을 업로드합니다.

5. 4단계에서는 백업 정책/보호 그룹을 만들 때 입력한 가져오기 작업에 대한 구체적인 이름을 입력합니다. 입력하는 이름에는 소문자, 숫자, 하이픈 및 밑줄만 포함될 수 있으며 문자로 시작해야 하고 공백을 포함할 수 없습니다. 작업이 진행 중인 동안 그리고 작업이 완료되면 선택한 이름을 사용하여 작업을 추적합니다.

6. 다음으로 목록에서 데이터 센터 지역을 선택합니다. 데이터 센터 지역은 패키지를 발송해야 하는 데이터 센터 및 주소를 나타냅니다.
   
    ![데이터 센터 지역 선택](./media/backup-azure-backup-import-export/dc.png)

7. 5단계에서는 목록에서 반품 택배사를 선택하고 택배사 계정 번호를 입력합니다. 가져오기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다.

8. 디스크를 배송하고 배송 상태를 추적하려면 추적 번호를 입력합니다. 디스크가 데이터 센터에 도착하면 저장소 계정에 복사되고 상태가 업데이트됩니다.
   
    ![완료 상태](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>워크플로 완료
저장소 계정에서 초기 백업 데이터를 사용할 수 있게 된 후 Microsoft Azure Recovery Services 에이전트가 데이터의 내용을 이 계정에서 백업 자격 증명 모음 또는 복구 서비스 자격 증명 모음 중 해당하는 것에 복사합니다. 다음 일정 백업 시 Azure 백업 에이전트가 초기 백업 복사본을 통해 증분 백업을 수행합니다.

## <a name="next-steps"></a>다음 단계
* Azure 가져오기/내보내기 워크플로에 대한 질문은 [Microsoft Azure Import/Export 서비스를 사용하여 Blob 저장소에 데이터 전송](../storage/storage-import-export-service.md)을 참조하세요.
* 워크플로에 대한 질문이 있으면 Azure 백업 [FAQ](backup-azure-backup-faq.md) 의 오프라인 백업 섹션을 참조하세요.




<!--HONumber=Nov16_HO3-->


