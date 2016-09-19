<properties
   pageTitle="Azure의 FreeBSD 소개 | Microsoft Azure"
   description="Azure에서 FreeBSD 가상 컴퓨터를 사용하는 방법을 알아봅니다."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

#Azure의 FreeBSD 소개
이 항목에서는 Azure에서 FreeBSD 가상 컴퓨터를 실행하는 방법의 개요를 제공합니다.

##개요
Microsoft Azure용 FreeBSD는 Microsoft Corporation에서 제공합니다. FreeBSD는 최신 서버, 데스크톱 및 포함된 플랫폼을 작동하는 데 사용되는 고급 컴퓨터 운영 체제입니다. 현재, FreeBSD 10.3 이미지를 Azure에서 사용할 수 있습니다. 이 이미지는 FreeBSD 10.3 릴리스를 기준으로 합니다. 또한 Azure VM 게스트 에이전트 [2\.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4)가 설치됩니다. 이 에이전트는 처음 사용 시 VM을 프로비전(사용자 이름, 암호, 호스트 이름, 등)하고 선택적 VM 확장 기능을 사용하도록 설정하는 것과 같은 작업을 위해 FreeBSD VM과 Azure 패브릭 간의 통신을 담당합니다. FreeBSD 후속 버전에서는 제품을 최신 상태로 유지하고, FreeBSD 릴리스 엔지니어링 팀에서 릴리스한 후에 바로 해당 최신 릴리스를 사용할 수 있도록 하는 전략을 따릅니다. 곧 출시될 버전은 [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html)입니다.

##FreeBSD 가상 컴퓨터를 배포하는 방법
FreeBSD 가상 컴퓨터 배포 작업은 [Azure 마켓플레이스](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)의 이미지를 사용하는 간단한 프로세스입니다.

##FreeBSD에 대한 VM 확장
FreeBSD VM에서 지원되는 VM 확장은 다음과 같습니다.

• [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

VMAccess 확장으로 다음을 수행할 수 있습니다.

- 원래 sudo 사용자의 암호를 다시 설정
- 지정된 암호를 사용하여 새 sudo 사용자 만들기
- 지정된 키로 공개 호스트 키 설정
- 호스트 키가 제공되지 않은 경우 VM 프로비전 중에 제공된 공개 호스트 키를 다시 설정
- SSH 포트(22)를 열고, reset\_ssh가 true로 설정된 경우 복원
- 기존 사용자 제거
- 디스크 확인
- 추가된 디스크 복구

• [CustomSript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)

CustomScript 확장으로 다음을 수행할 수 있습니다.

- 제공될 경우 Azure Storage 또는 외부 공용 저장소(예: Github)에서 사용자 지정된 스크립트 다운로드
- 입력 지점 스크립트 실행
- 인라인 명령 지원
- 셸 및 Python 스크립트에서 Windows 스타일 줄 바꿈 문자를 자동으로 변환
- 셸 및 Python 스크립트의 BOM을 자동으로 제거
- commandToExecute의 중요 데이터 보호

##인증: 사용자 이름, 암호 및 SSH 키
Azure 클래식 포털을 사용하여 FreeBSD 가상 컴퓨터를 만들 때 사용자 이름, 암호 또는 SSH 공개 키를 제공하라는 메시지가 표시됩니다. Azure에서 FreeBSD 가상 컴퓨터를 배포하기 위해 사용자 이름을 선택할 경우 가상 컴퓨터에 이미 있는 시스템 계정 이름(UID <100)(예: 'root')은 허용되지 않는다는 제한이 적용됩니다. 지금까지 RSA SSH 키만 지원됩니다. 여러 줄로 된 SSH 키는 ---- BEGIN SSH2 PUBLIC KEY ----로 시작하고 ---- END SSH2 PUBLIC KEY ----로 끝나야 합니다.

##슈퍼 사용자 권한을 구하는 방법
Azure에서 가상 컴퓨터 인스턴스를 배포하는 동안 지정한 사용자 계정이 권한 있는 계정입니다. "sudo"의 패키지는 게시된 FreeBSD 이미지에 설치되어 있습니다. 이 사용자 계정을 사용하여 로그인하면 다음 명령 구문을 사용하여 루트 권한으로 명령을 실행할 수 있습니다.
  
    # sudo <COMMAND>

선택적으로 sudo -s를 사용하여 루트 셸을 얻을 수 있습니다.

##다음 단계
- [Azure 마켓플레이스](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)로 가서 FreeBSD VM을 만듭니다.
- 자체 FreeBSD를 Azure로 가져오려면 [FreeBSD VHD 만들기 및 Azure로 업로드](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md)를 참조하세요.

<!---HONumber=AcomDC_0907_2016-->