<properties title="About Puppet and Azure Virtual Machines" pageTitle="About Puppet and Azure Virtual Machines" description="Describes installing and configuring Puppet on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Puppet 및 Azure 가상 컴퓨터 정보

Puppet Enterprise는 인프라를 구축, 배포 및 관리하기 위한 자동화 소프트웨어입니다. 이 프로그램을 사용하여 검색, 프로비전, OS 및 응용 프로그램의 구성 관리, 오케스트레이션, 보고를 비롯한 IT 인프라 수명 주기를 관리할 수 있습니다.

Puppet은 클라이언트-서버 시스템입니다. Puppet Master와 Puppet Enterprise Agent는 모두 Azure를 통해 설치할 수 있습니다.

-   Puppet Master는 Ubuntu 서버에 설치된 미리 구성된 이미지로 사용할 수 있습니다. 기존 서버에 Puppet Enterprise를 설치할 수도 있지만 이미지를 사용하는 것이 설치를 시작하는 가장 간단한 방법입니다. 에이전트를 설치하려면 서버에 대한 정보가 필요합니다.
-   Puppet Enterprise Agent는 가상 컴퓨터를 만들 때 설치하거나 기존 가상 컴퓨터에 설치할 수 있는 가상 컴퓨터 확장으로 사용할 수 있습니다.

지침을 보려면 [Microsoft Windows 및 Azure][Microsoft Windows 및 Azure](영문) 페이지의 "시작 가이드"를 다운로드하세요.

## 추가 리소스

[Microsoft Azure 및 Visual Studio와의 새로운 통합(영문)][Microsoft Azure 및 Visual Studio와의 새로운 통합(영문)]

[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]

[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법][Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]

[확장 관리][확장 관리]

<!--Link references-->

  [Microsoft Windows 및 Azure]: http://puppetlabs.com/solutions/microsoft
  [Microsoft Azure 및 Visual Studio와의 새로운 통합(영문)]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
  [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]: ../virtual-machines-log-on-windows-server/
  [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]: ../virtual-machines-linux-how-to-log-on
  [확장 관리]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
