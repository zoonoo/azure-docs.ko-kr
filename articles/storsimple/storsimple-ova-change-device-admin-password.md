<properties 
   pageTitle="StorSimple 가상 장치 관리자 암호 변경 | Microsoft Azure"
   description="Azure 클래식 포털 또는 StorSimple 가상 배열 웹 UI를 사용하여 장치 관리자 암호를 변경하는 방법을 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="03/02/2016"
   ms.author="alkohli" />

# StorSimple 가상 배열 장치 관리자 암호 변경

## 개요

Windows PowerShell 인터페이스를 사용하여 StorSimple 가상 장치에 액세스할 때 장치 관리자 암호를 입력해야 합니다. StorSimple 장치를 처음으로 프로비저닝하고 시작할 때 기본 암호는 *Password1*입니다. 데이터 보안을 위해 처음 로그인하면 기본 암호가 만료되며, 암호를 변경해야 합니다.

프로덕션 환경에 장치가 배포된 후에는 로컬 웹 UI 또는 Azure 클래식 포털을 사용하여 장치 관리자 암호를 언제든 변경할 수 있습니다. 이 문서에서는 각 절차에 대해 설명합니다.

## Azure 클래식 포털을 사용하여 암호 변경

Azure 클래식 포털을 통해 장치 관리자 암호를 변경하려면 다음 단계를 수행합니다.

#### Azure 클래식 포털을 통해 장치 관리자 암호를 변경하려면

1. 포털에서 사용자의 장치에 대해 **장치** > **구성**을 클릭합니다.

2. **장치 관리자 암호** 섹션으로 스크롤합니다. 8자에서 15자를 포함하는 관리자 암호를 입력합니다. 암호는 대문자, 소문자, 숫자 및 특수 문자의 조합이어야 합니다.

3. 암호를 확인합니다.

4. 페이지 맨 아래에서 **저장**을 클릭합니다.

이제 장치 관리자 암호를 업데이트해야 합니다. 수정된 암호를 사용하여 로컬에서 장치에 액세스할 수 있습니다.

## StorSimple 가상 배열 웹 UI를 사용하여 암호를 변경하려면

로컬 웹 UI를 통해 장치 관리자 암호를 변경하려면 다음 단계를 수행합니다.

#### 로컬 웹 UI를 통해 장치 관리자 암호를 변경하려면

1. 로컬 웹 UI에서 장치에 대한 **유지 관리** > **암호 변경**을 클릭합니다.

    ![password1 변경](./media/storsimple-ova-change-device-admin-password/image40.png)

2. **현재 암호**를 입력합니다.

3. **새 암호**를 제공합니다. 암호의 길이는 8자 이상이어야 합니다. 대문자, 소문자, 숫자 및 특수 문자 이렇게 4가지 중 3가지가 포함되어야 합니다.

    암호는 마지막 24개의 암호와 동일할 수 없으니 유의하세요.

3. 확인을 위해 암호를 다시 입력합니다.

    ![password2 변경](./media/storsimple-ova-change-device-admin-password/image41.png)

4. 페이지 아래쪽에서 **적용**을 클릭합니다. 새 암호가 적용됩니다. 암호 변경이 실패하면 다음 오류가 표시됩니다.

    ![암호 오류](./media/storsimple-ova-change-device-admin-password/image42.png)

    암호가 성공적으로 업데이트되면 알림이 표시됩니다. 그런 다음 수정된 암호를 사용하여 로컬에서 장치에 액세스할 수 있습니다.

## 다음 단계

[StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0309_2016-->