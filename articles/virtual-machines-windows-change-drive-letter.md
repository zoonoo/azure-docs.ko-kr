<properties title="How To Change the Drive Letter of the Windows Temporary Disk" pageTitle="How To Change the Drive Letter of the Windows Temporary Disk" description="Describes how to remap the temporary disk on a Windows VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Windows 임시 디스크의 드라이브 문자를 변경하는 방법

D 드라이브를 다른 용도로 사용해야 하는 경우 임시 디스크의 드라이브 문자를 변경할 수 있습니다. D 드라이브를 영구 저장소 위치로 사용하는 응용 프로그램이나 서비스를 지원하기 위해 이렇게 하는 경우가 많습니다.

시작하기 전에 다음이 있어야 합니다.

-   이 절차 중에 Windows 페이지 파일(pagefile.sys)을 저장하는 데 사용할 수 있는 연결된 하나의 데이터 디스크. 지침은 [Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법][Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법]을 참조하세요.
-   D 드라이브에 있는 기존 데이터 디스크 VHD를 사용하려는 경우 저장소 계정에 업로드된 VHD. 지침은 [Windows Server VHD를 만들어 Azure에 업로드][Windows Server VHD를 만들어 Azure에 업로드]의 3, 4단계를 참조하세요.

## 드라이브 문자 변경

1.  가상 컴퓨터에 로그인합니다.

2.  D 드라이브의 pagefile.sys를 다른 드라이브로 옮깁니다.

3.  가상 컴퓨터를 다시 시작합니다.

4.  다시 로그인하고 드라이브 문자를 D에서 E로 변경합니다.

5.  [Azure 관리 포털][Azure 관리 포털]에서 기존 데이터 디스크 또는 빈 데이터 디스크를 연결합니다.

6.  가상 컴퓨터에 다시 로그인하고 디스크를 초기화한 후 방금 연결한 디스크의 드라이브 문자로 D를 할당합니다.

7.  E가 임시 저장소 디스크에 매핑되어 있는지 확인합니다.

8.  다른 드라이브에 옮겨 놓았던 pagefile.sys를 E 드라이브로 옮깁니다.

## 추가 리소스

[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]

[가상 컴퓨터에서 데이터 디스크를 분리하는 방법][가상 컴퓨터에서 데이터 디스크를 분리하는 방법]

[저장소 계정 정의][저장소 계정 정의]

<!--Link references-->

  [Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법]: ../storage-windows-attach-disk
  [Windows Server VHD를 만들어 Azure에 업로드]: ../virtual-machines-create-upload-vhd-windows-server/
  [Azure 관리 포털]: http://manage.windowsazure.com
  [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]: ../virtual-machines-log-on-windows-server/
  [가상 컴퓨터에서 데이터 디스크를 분리하는 방법]: ../storage-windows-detach-disk/
  [저장소 계정 정의]: ../storage-whatis-account/
