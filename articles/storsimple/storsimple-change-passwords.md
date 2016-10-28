<properties 
   pageTitle="StorSimple 암호 변경 | Microsoft Azure" 
   description="StorSimple 관리자 서비스를 사용하여 StorSimple 스냅숏 관리자 및 장치 관리자 암호를 변경하는 방법에 대해 설명합니다." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/17/2016"
   ms.author="alkohli"/>

# StorSimple 관리자 서비스를 사용하여 StorSimple 암호 변경

## 개요 

Azure 클래식 포털 **구성** 페이지에는 StorSimple 관리자 서비스로 관리되는 StorSimple 장치에서 다시 구성할 수 있는 모든 장치 매개 변수가 포함되어 있습니다. 이 자습서에서는 **구성** 페이지를 사용하여 장치 관리자 또는 StorSimple Snapshot Manager 암호를 변경하는 방법에 대해 설명합니다.

## 장치 관리자 암호 구성

Windows PowerShell 인터페이스를 사용하여 StorSimple 장치에 액세스할 때 장치 관리자 암호를 입력해야 합니다. StorSimple 장치를 서비스에 처음으로 등록할 때 이 인터페이스의 기본 암호는 *Password1*입니다. 데이터 보안을 위해 등록 과정 마지막에 이 암호를 변경해야 합니다. 이 암호를 변경하지 않고 등록 과정을 종료할 수 없습니다. 자세한 내용은 [3단계: StorSimple용 Windows PowerShell을 통해 장치 구성 및 등록](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)을 참조하세요.

등록하는 동안 먼저 Windows PowerShell 인터페이스를 통해 설정된 암호는 Azure 클래식 포털을 통해 변경할 수 있습니다. 장치 관리자 암호를 변경하려면 다음 단계를 수행합니다.

#### 장치 관리자 암호 변경 방법

1. 클래식 포털에서 사용자의 장치에 대해 **장치** > **구성**을 클릭합니다.

2. **장치 관리자 암호** 섹션으로 스크롤합니다. 8자에서 15자를 포함하는 관리자 암호를 입력합니다. 암호는 대문자, 소문자, 숫자 및 특수 문자 중 3가지 이상의 조합이어야 합니다.

3. 암호를 확인합니다.

4. 페이지 맨 아래에서 **저장**을 클릭합니다.

이제 장치 관리자 암호를 업데이트해야 합니다. 이 수정된 암호를 사용하여 Windows PowerShell 인터페이스에 액세스할 수 있습니다.

## StorSimple 스냅숏 관리자 암호 변경

StorSimple 스냅숏 관리자 소프트웨어는 Windows 호스트에 상주하며 관리자가 로컬 및 클라우드 스냅숏의 형태로 StorSimple 장치의 백업을 관리할 수 있습니다.

StorSimple 스냅숏 관리자에서 장치를 구성하면, 장치 IP 주소 및 암호를 입력하여 저장소 장치를 인증하라는 메시지가 표시됩니다. 이 암호는 Windows PowerShell 인터페이스를 통해 처음 구성됩니다. 자세한 내용은 [3단계: StorSimple용 Windows PowerShell을 통해 장치 구성 및 등록](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)을 참조하세요.

등록하는 동안 먼저 Windows PowerShell 인터페이스를 통해 설정된 암호는 클래식 포털을 통해 변경할 수 있습니다. 다음 단계에서 StorSimple 스냅숏 관리자 암호를 변경합니다.

#### StorSimple 스냅숏 관리자 암호 변경 방법

1. 클래식 포털에서 사용자의 장치에 대해 **장치** > **구성**을 클릭합니다.

2. **StorSimple 스냅숏 관리자** 섹션으로 스크롤합니다. 14자 또는 15자인 암호를 입력합니다. 암호에 대문자, 소문자, 숫자 및 특수 문자 중 3가지 이상의 조합이 포함되어 있는지 확인합니다.

3. 암호를 확인합니다.

4. 페이지 맨 아래에서 **저장**을 클릭합니다.

이제 StorSimple 스냅숏 관리자 암호가 업데이트됩니다.
 

## 다음 단계

- [StorSimple 보안](storsimple-security.md)에 대해 자세히 알아봅니다.

- [장치 구성을 수정](storsimple-modify-device-config.md)하는 방법을 자세히 알아봅니다.

- [StorSimple Manager 서비스를 사용하여 StorSimple 장치를 관리](storsimple-manager-service-administration.md)하는 방법을 자세히 알아봅니다.

<!---HONumber=AcomDC_0817_2016-->