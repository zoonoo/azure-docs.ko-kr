<properties 
	pageTitle="Puppet 및 Azure 가상 컴퓨터 정보" 
	description="Azure에서 VM에 Puppet을 설치 및 구성하는 방법에 대해 설명합니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="kathydav"/>

#Puppet 및 Azure 가상 컴퓨터 정보

<p>Puppet 엔터프라이즈는 인프라 빌드, 배포 및 관리에 대한 자동화 소프트웨어입니다. 이 프로그램을 사용하여 검색, 프로비전, OS 및 응용 프로그램의 구성 관리, 오케스트레이션, 보고를 비롯한 IT 인프라 수명 주기를 관리할 수 있습니다.

Puppet은 클라이언트-서버 시스템입니다. Puppet Master와 Puppet Enterprise Agent는 모두 Azure를 통해 설치할 수 있습니다.

- Puppet Master는 Ubuntu 서버에 설치된 미리 구성된 이미지로 사용할 수 있습니다. 기존 서버에 Puppet Enterprise를 설치할 수도 있지만 이미지를 사용하는 것이 설치를 시작하는 가장 간단한 방법입니다. 에이전트를 설치하려면 서버에 대한 정보가 필요합니다. 
- Puppet Enterprise Agent는 가상 컴퓨터를 만들 때 설치하거나 기존 가상 컴퓨터에 설치할 수 있는 가상 컴퓨터 확장으로 사용할 수 있습니다.

지침을 보려면 [Microsoft Windows 및 Azure](http://puppetlabs.com/solutions/microsoft)(영문) 페이지의 "시작 가이드"를 다운로드하세요.


##추가 리소스
[Microsoft Azure 및 Visual Studio와의 새로운 통합]

[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]

[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]

[확장 관리]

<!--Link references-->
[Microsoft Azure 및 Visual Studio와의 새로운 통합]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]: virtual-machines-log-on-windows-server.md
[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]: virtual-machines-linux-how-to-log-on.md
[Azure VM Extensions and Features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=58--> 