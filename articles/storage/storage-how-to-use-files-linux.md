<properties
	pageTitle="Linux에서 Azure Files 사용 방법 | Microsoft Azure"
        description="이 단계별 자습서를 사용하여 클라우드에 Azure 파일 공유를 만듭니다. 파일 공유 콘텐츠를 관리하고 Linux 또는 SMB 3.0을 지원하는 온-프레미스 응용 프로그램을 실행하는 Azure VM(가상 컴퓨터)에서 파일 공유를 탑재합니다."
        services="storage"
        documentationCenter="na"
        authors="mine-msft"
        manager="aungoo"
        editor="tysonn" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="02/29/2016"
      ms.author="minet" />


# Linux에서 Azure 파일 저장소 사용 방법

## 개요

Azure 파일 저장소는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다. Azure Files을 사용하여 파일 서버를 사용하는 엔터프라이즈 응용 프로그램을 Azure로 마이그레이션할 수 있습니다. Azure에서 실행 중인 응용 프로그램은 Linux를 실행 중인 Azure 가상 컴퓨터에서 파일 공유를 쉽게 탑재할 수 있습니다. 최신 릴리스 파일 저장소를 사용하면 SMB 3.0을 지원하는 온-프레미스 응용 프로그램에서 파일 공유를 탑재할 수도 있습니다.

[Azure 포털](https://portal.azure.com), Azure 저장소 PowerShell cmdlet, Azure 저장소 클라이언트 라이브러리 또는 Azure 저장소 REST API를 사용하여 Azure 파일 공유를 만들 수 있습니다. 또한 파일 공유는 SMB 공유이므로 표준 파일 시스템 API를 통해 파일 공유에 액세스할 수 있습니다.

파일 저장소는 Blob, 테이블 및 큐 저장소와 동일한 기술을 토대로 만들어졌으므로 파일 저장소는 Azure 저장소 플랫폼에 기본 제공되는 기존 가용성, 내구성, 확장성 및 지리적 중복을 활용할 수 있습니다. 파일 저장소 성능 목표 및 제한에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](storage-scalability-targets.md)를 참조하세요.

파일 저장소는 이제 일반적으로 사용 가능하며 SMB 2.1과 SMB 3.0을 모두 지원합니다. 파일 저장소에 대한 자세한 내용은 [파일 서비스 REST API](https://msdn.microsoft.com/library/azure/dn167006.aspx)를 참조하세요.

>[AZURE.NOTE] Linux SMB 클라이언트는 아직 암호화를 지원하지 않으므로 Linux에서 파일 공유를 탑재하려면 여전히 클라이언트가 파일 공유와 동일한 Azure 지역에 있어야 합니다. 그러나 Linux에 대한 암호화 지원은 SMB 기능을 담당하는 Linux 개발자의 로드맵상에 있습니다. 향후 암호화를 지원하는 Linux 배포판은 어디에서나 Azure 파일 공유를 탑재할 수 있게 됩니다.

## 비디오: Linux에서 Azure 파일 저장소 사용

Linux에서 Azure 파일 공유를 만들고 사용하는 방법을 보여주는 비디오는 다음과 같습니다.

> [AZURE.VIDEO azure-file-storage-with-linux]

## 사용할 Linux 배포판 ##

Azure에서 Linux 가상 컴퓨터를 만들 때 SMB 2.1 이상을 지원하는 Linux 이미지를 Azure 이미지 갤러리에서 지정할 수 있습니다. 다음은 권장되는 Linux 이미지의 목록입니다.

- Ubuntu Server 14.04
- Ubuntu Server 15.04
- CentOS 7.1
- Open SUSE 13.2
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12(Premium 이미지)

## 파일 공유 탑재 ##

Linux를 실행하는 가상 컴퓨터에서 파일 공유를 탑재하려면 사용 중인 배포판에 기본 제공 클라이언트가 없는 경우 SMB/CIFS 클라이언트를 설치해야 할 수 있습니다. 다음은 선택한 한 가지 cifs-utils를 설치하는 Ubuntu의 명령입니다.

    sudo apt-get install cifs-utils

그런 다음 탑재 지점(mkdir mymountpoint)을 만들고 다음과 비슷한 탑재 명령을 실행해야 합니다.

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

또한 /etc/fstab에서 공유를 탑재하는 설정을 추가할 수도 있습니다.

여기서 0777은 모든 사용자에게 실행/읽기/쓰기 권한을 부여하는 디렉터리/파일 사용 권한 코드를 나타냅니다. Linux 파일 사용 권한 문서 다음에 이 코드를 다른 파일 사용 권한 코드와 바꿀 수 있습니다.

또한 다시 부팅 후에 탑재된 파일 공유를 유지하기 위해 /etc/fstab에서 아래와 같은 설정을 추가할 수 있습니다.

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

예를 들어 Azure 이미지 갤러리에서 사용할 수 있는 Linux 이미지 Ubuntu Server 15.04를 사용하여 Azure VM을 만든 경우에는 다음과 같이 파일을 탑재할 수 있습니다.

    azureuser@azureconubuntu:~$ sudo apt-get install cifs-utils
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

CentOS 7.1을 사용하는 경우 다음과 같이 파일을 탑재할 수 있습니다.

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Open SUSE 13.2를 사용하는 경우 다음과 같이 파일을 탑재할 수 있습니다.

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

## 파일 공유 관리 ##

[Azure 포털](https://portal.azure.com)에서 Azure 파일 저장소를 관리하기 위한 사용자 인터페이스를 제공합니다. 웹 브라우저에서 다음 작업을 수행할 수 있습니다.

- 파일 공유에 대해 파일 업로드 및 다운로드
- 각 파일 공유의 실제 사용량 모니터링
- 파일 공유 크기 할당량 조정
- Windows 클라이언트에서 파일 공유를 탑재하기 위해 사용할 `net use` 명령을 복사합니다.

또한 Linux에서 Azure CLI(Azure 플랫폼 간 명령줄 인터페이스)를 사용하여 파일 공유를 관리할 수 있습니다. Azure CLI는 파일 저장소를 비롯한 Azure 저장소 작업을 실행하기 위한 공개 소스, 플랫폼 간 명령 집합을 제공합니다. 다양한 데이터 액세스 기능뿐만 아니라 Azure 포털에 있는 동일한 기능을 대부분 제공합니다. 예제는 [Azure 저장소와 함께 Azure CLI 사용](storage-azure-cli.md)을 참조하세요.

## 파일 저장소를 사용하여 개발 ##

개발자는 [Java용 Azure 저장소 클라이언트 라이브러리](https://github.com/azure/azure-storage-java)를 사용하여 파일 저장소와 함께 응용 프로그램을 빌드할 수 있습니다. 코드 예제를 보려면 [Java에서 파일 저장소를 사용하는 방법](storage-java-how-to-use-file-storage.md)을 참조하세요.

또한 파일 저장소에 대해 개발하기 위해 [Node.js용 Azure 저장소 클라이언트 라이브러리](https://github.com/Azure/azure-storage-node)를 사용할 수도 있습니다.

## 피드백 및 추가 정보 ##

Linux 사용자 여러분의 의견을 듣고 싶습니다!

Linux 사용자 그룹용 Azure 파일 저장소는 Linux에서 파일 저장소를 평가하고 채택할 때 피드백을 공유할 수 있도록 포럼을 제공합니다. 사용자 그룹에 참가하려면 [Azure 파일 저장소 Linux 사용자](mailto:azurefileslinuxusers@microsoft.com)에게 메일을 보내세요.

## 다음 단계

Azure 파일 저장소에 대한 자세한 내용은 다음 링크를 참조합니다.

### 개념 문서 및 비디오

- [Azure 파일 저장소: Windows 및 Linux을 위한 원활한 클라우드 SMB 파일 시스템 ](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Windows에서 Azure 파일 저장소 시작](storage-dotnet-how-to-use-files.md)

### 파일 저장소용 도구 지원

- [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)
- Azure CLI를 사용하여 [파일 공유 만들기 및 관리](storage-azure-cli.md#create-and-manage-file-shares)

### 참조

- [파일 서비스 REST API 참조](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### 블로그 게시물

- [Azure 파일 저장소 일반적으로 사용 가능(영문)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
- [Azure 파일 저장소의 내면(영문)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
- [Microsoft Azure 파일 서비스 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Microsoft Azure 파일에 대한 연결 유지](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=AcomDC_0302_2016-->