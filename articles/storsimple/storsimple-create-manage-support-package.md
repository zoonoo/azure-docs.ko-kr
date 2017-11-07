---
title: "StorSimple 지원 패키지 만들기 | Microsoft Docs"
description: "StorSimple 장치용 지원 패키지를 만들고, 암호 해독 및 편집하는 방법을 알아봅니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: eac76f5f-5db1-4c92-af8c-54053b91e66c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 32d20e7a8adcfc646c592213fe7395b87a93c985
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-a-storsimple-support-package"></a>StorSimple 지원 패키지 만들기 및 관리
## <a name="overview"></a>개요
StorSimple 지원 패키지는 StorSimple 장치 문제를 해결하는 데 있어서 Microsoft 지원을 지원하기 위해 모든 관련 로그를 수집하는 사용하기 쉬운 메커니즘입니다. 수집된 로그는 암호화되고 압축됩니다.

이 자습서는 지원 패키지를 만들고 관리하도록 단계별 지침을 제공합니다.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Azure 클래식 포털에서 지원 패키지 만들기 및 업로드
Azure 클래식 포털에서 서비스의 **유지 관리** 페이지를 통해 Microsoft 지원 사이트에 지원 패키지를 만들고 업로드할 수 있습니다.

> [!NOTE]
> 업로드하려면 지원 암호가 필요합니다. 지원 엔지니어는 암호를 전자 메일로 제공해야 합니다.
> 
> 

암호화되고 압축된 지원 패키지(.cab 파일)를 만들고 지원 사이트에 업로드합니다. 그런 다음 지원 엔지니어는 문제를 해결하기 위한 지원 사이트에서 이 패키지를 검색할 수 있습니다.

지원 패키지를 만들려면 클래식 포털에서 다음 단계를 수행합니다.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Azure 클래식 포털에서 지원 패키지를 만드는 방법
1. **장치** > **유지 관리**를 선택합니다.
2. **지원 패키지** 섹션에서 **지원 패키지 만들기 및 업로드**를 선택합니다.
3. **지원 패키지 만들기 및 업로드** 대화 상자에서 다음을 수행합니다.
   
    ![지원 패키지 만들기](./media/storsimple-create-manage-support-package/IC740923.png)
   
   * **지원 암호** 텍스트 상자에 암호를 입력합니다. Microsoft 지원 엔지니어가 전자 메일로 이 암호를 전송해야 합니다.
   * 동의를 제공하는 확인란을 선택하여 Microsoft 지원 사이트에 지원 패키지를 자동으로 업로드합니다.
   * 확인 아이콘 ![확인 아이콘](./media/storsimple-create-manage-support-package/IC740895.png)를 선택합니다.

## <a name="manually-create-a-support-package"></a>수동으로 지원 패키지 만들기
경우에 따라 StorSimple 용 Windows PowerShell을 통해 지원 패키지를 수동으로 만들어야 합니다. 예:

* Microsoft 지원과 공유하기 전에 로그 파일에서 중요한 정보를 제거해야 하는 경우
* 연결 문제로 인해 패키지를 업로드하는 데 문제가 발생하는 경우

전자 메일을 통해 Microsoft 지원에 수동으로 생성된 지원 패키지를 공유할 수 있습니다. StorSimple용 Windows PowerShell에서 지원 패키지를 만들려면 다음 단계를 수행합니다.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell에서 지원 패키지를 만들려면
1. StorSimple 장치에 연결하는데 사용된 원격 컴퓨터에서 관리자 권한으로 Windows PowerShell 세션을 시작하려면 다음 명령을 입력합니다.
   
    `Start PowerShell`
2. Windows PowerShell 세션에서 장치의 SSAdmin 콘솔에 연결합니다.
   
   * 명령 프롬프트에 다음을 입력합니다.
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   * 열린 대화 상자에서 장치 관리자 암호를 입력합니다. 기본 암호는 다음과 같습니다.
     
      `Password1`
     
      ![PowerShell 자격 증명 대화 상자](./media/storsimple-create-manage-support-package/IC740962.png)
   * **확인**을 선택합니다.
   * 명령 프롬프트에 다음을 입력합니다.
     
      `Enter-PSSession $MS`
3. 열린 세션에서 적절한 명령을 입력합니다.
   
   * 암호로 보호된 네트워크 공유에 다음을 입력합니다.
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       암호, 네트워크 공유 폴더의 경로 및 암호화 암호에 대해 묻는 메시지가 나타납니다.(지원 패키지가 암호화 되었기 때문) 그런 다음 지원 패키지는 지정된 폴더에 생성됩니다.
   * 암호로 보호되지 않은 공유의 경우 `-Credential` 매개 변수가 필요하지 않습니다. 다음을 입력합니다.
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       지정된 네트워크 공유 폴더에 두 컨트롤러를 위한 지원 패키지를 만들 수 있습니다. 문제해결을 위한 Microsoft 기술 지원 서비스에 보낼 수 있는 암호화되고 압축된 파일입니다. 자세한 내용은 [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md)를 참조하세요.

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Export-HcsSupportPackage cmdlet 매개 변수
Export-HcsSupportPackage cmdlet으로 다음 매개 변수를 사용할 수 있습니다.

| 매개 변수 | 필수/선택 | 설명 |
| --- | --- | --- |
| `-Path` |필수 |지원 패키지가 배치된 네트워크 공유 폴더의 위치를 제공하는 데 사용합니다. |
| `-EncryptionPassphrase` |필수 |지원 패키지를 암호화하기 위해 암호를 제공하는 데 사용합니다. |
| `-Credential` |옵션 |네트워크 공유 폴더에 대한 액세스 자격 증명을 제공하는 데 사용합니다. |
| `-Force` |옵션 |암호화 암호 확인 단계를 건너뛰는 데 사용합니다. |
| `-PackageTag` |옵션 |지원 패키지가 배치된 *경로* 의 디렉터리를 지정하는 데 사용합니다. 기본값은 [장치 이름]-[현재 날짜 및 시간: yyyy-MM-dd-HH-mm-ss]입니다. |
| `-Scope` |옵션 |**클러스터** (기본값)로 지정하여 두 컨트롤러에 대한 지원 패키지를 만듭니다. 현재 컨트롤러에 대해서만 패키지를 만들려면 **컨트롤러**를 지정합니다. |

## <a name="edit-a-support-package"></a>지원 패키지 편집
지원 패키지를 생성한 후에 패키지를 편집하여 중요한 정보를 제거해야 합니다. 볼륨 이름, 장치 IP 주소 및 로그 파일에서 백업 이름을 포함할 수 있습니다.

> [!IMPORTANT]
> StorSimple용 Windows PowerShell을 통해 생성된 지원 패키지를 편집할 수 있습니다. StorSimple Manager 서비스로 Azure 클래식 포털에서 생성한 패키지를 편집할 수 없습니다.
> 
> 

Microsoft 지원 사이트에 업로드하기 전에 지원 패키지를 편집하려면 먼저 지원 패키지의 암호를 해독하고 파일을 편집한 다음 다시 암호화합니다. 다음 단계를 수행합니다.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell에서 지원 패키지를 편집하려면
1. 이전에 [StorSimple용 Windows PowerShell에서 지원 패키지 만들기](#to-create-a-support-package-in-windows-powershell-for-storsimple)에서 설명한 대로 지원 패키지를 생성합니다.
2. [스크립트](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) 를 로컬로 클라이언트에 다운로드합니다.
3. Windows PowerShell 모듈을 가져옵니다. 스크립트를 다운로드한 로컬 폴더로 경로를 지정합니다. 모듈을 가져오려면 다음을 입력합니다.
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. 모든 파일은 압축 및 암호화된 *.aes* 파일입니다. 압축을 해제하고 파일의 암호를 해독하려면 다음을 입력합니다.
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    실제 파일 확장명은 모든 파일에서 표시됩니다.
   
    ![지원 패키지 편집](./media/storsimple-create-manage-support-package/IC750706.png)
5. 암호화 암호에 대한 메시지가 표시 되는 경우 지원 패키지를 만들 때 사용한 암호를 입력합니다.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. 로그 파일이 들어 있는 폴더로 이동합니다. 이제 로그 파일의 압축을 해제하고 암호를 해독했기 때문에 원본 파일 확장명을 가질 수 있습니다. 이러한 파일을 수정하여 볼륨 이름 및 장치 IP 주소와 같은 고객 관련 정보를 제거하고 파일을 저장합니다.
7. 파일을 닫아서 Gzip으로 압축한 다음 AES-256으로 암호화합니다. 네트워크를 통해 지원 패키지를 전송하는 경우 보안 및 속도를 위해서 이렇게 합니다. 파일을 압축하고 암호화하려면 다음을 입력합니다.
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![지원 패키지 편집](./media/storsimple-create-manage-support-package/IC750707.png)
8. 메시지가 표시되면 수정된 지원 패키지에 암호화 암호를 제공합니다.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Microsoft 지원 요청 시 공유할 수 있도록 새 암호를 적어둡니다.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>예: 암호로 보호된 공유에 대한 지원 패키지에서 파일 편집
다음 예제에서는 지원 패키지의 암호를 해독하고 편집 및 다시 암호화하는 방법을 보여 줍니다.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>다음 단계
* [지원 패키지 및 장치 로그를 사용하여 장치 배포 문제를 해결](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)하는 방법을 알아봅니다.
* [StorSimple Manager 서비스를 사용하여 StorSimple 장치를 관리](storsimple-manager-service-administration.md)하는 방법을 알아봅니다.

