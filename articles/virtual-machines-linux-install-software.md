<properties linkid="manage-linux-commontasks-install-software" urlDisplayName="Install software on VM" pageTitle="Install software on a Linux virtual machine - Azure" metaKeywords="" description="Learn how to install software on your Linux virtual machine in Azure by using CentOS/Red Hat or Ubuntu." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install software on your Linux virtual machine in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Azure에서 Linux 가상 컴퓨터에 소프트웨어 설치

Linux 분산은 소프트웨어를 설치하는 데 소프트웨어 "패키지"를 사용하는 경향이 있습니다. 이 패키지는 대체로 `apt` 또는 `yum`과 같은 명령 집합을 사용하여 관리합니다. 패키지 없이 소스 코드의 *tarball*을 사용하는 등의 방법으로 프로그램을 설치할 수도 있습니다.

일반적인 Linux 분산의 일부에 패키지 관리자를 사용하는 방법을 다루겠습니다. 그 단계는 사용 중인 Linux 분산에 따라 다릅니다.

**참고:** 환경이 설정된 방식에 따라, 루트 권한을 사용하여(`sudo`를 통해) 이 명령을 실행해야 할 수도 있습니다.

## CentOS/Red Hat

CentOS에는 패키지 관리용 `yum`이 제공됩니다. 이 도구를 사용하면 패키지를 설치, 제거, 업데이트하고 설치된 패키지를 나열할 수 있습니다. 이 명령에 사용되는 구문은 아래를 참조하십시오.

### 설치

다음은 패키지뿐만 아니라 패키지 작동에 필요한 다른 패키지도 설치합니다. 종속성으로 인해, 두 개 이상의 패키지가 설치될 수 있습니다.

    yum install [package name]

### 제거

다음은 컴퓨터에서 패키지를 제거합니다. 종속성을 제거하지는 않습니다.

    yum remove [package name]

### 업데이트

다음은 패키지를 최신 버전으로 업데이트합니다. 패키지를 업데이트하려면 먼저 설치해야 합니다.

    yum update [package name]

### 설치된 패키지 나열

다음은 컴퓨터에 설치된 모든 패키지의 목록을 표시합니다.

    yum list installed

## Ubuntu

Ubuntu에는 패키지 관리용으로 `apt`(Advanced Packaging Tool)가 제공됩니다. 이 도구를 사용하면 패키지를 설치, 제거, 업데이트하고 설치된 패키지를 나열할 수 있습니다. 이 명령에 사용되는 구문은 아래를 참조하십시오.

### 설치

다음은 패키지뿐만 아니라 패키지 작동에 필요한 다른 패키지도 설치합니다. 종속성으로 인해, 두 개 이상의 패키지가 설치될 수 있습니다.

    apt-get install [package name]

### 제거

다음은 컴퓨터에서 패키지를 제거합니다. 종속성을 제거하지는 않습니다.

    apt-get remove [package name]

### 업데이트/업그레이드

새 버전으로 업그레이드하려면 두 가지 명령, 즉 패키지 인덱스를 업데이트하는 명령과 패키지를 업그레이드하는 명령을 사용해야 합니다. 다음 명령을 실행하여 패키지 인덱스를 업데이트합니다.

    apt-get update

패키지 인덱스가 업데이트되고 나면 다음 명령을 실행하여 패키지를 업데이트합니다.

    apt-get upgrade
