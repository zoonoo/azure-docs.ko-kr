<properties 
   pageTitle="StorSimple 가상 배열 업데이트 적용| Microsoft Azure"
   description="StorSimple 가상 배열 웹 UI를 사용하여 업데이트 및 핫픽스를 적용하는 방법을 설명합니다."
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
   ms.date="01/13/2016"
   ms.author="alkohli" />

# StorSimple 가상 배열에 업데이트 및 핫픽스 적용(미리 보기)

## 개요

StorSimple 가상 배열을 최신 상태로 유지하려면 소프트웨어 업데이트 또는 핫픽스를 적용해야 합니다. 일반적으로 Azure 클래식 포털을 통해 업데이트를 설치하는 것이 좋습니다. 하지만 포털을 사용할 수 없는 경우에는 로컬 웹 UI를 사용하여 핫픽스 또는 업데이트를 적용할 수 있습니다. 이 자습서는 로컬 웹 UI를 사용하여 업데이트 또는 핫픽스를 설치하는 방법을 설명합니다.(클래식 포털을 통해 업데이트를 설치하는 절차는 [Azure 클래식 포털을 통해 일반 업데이트 설치](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal)를 참조하세요.)

업데이트 또는 핫픽스를 설치하면 장치가 다시 시작될 수 있습니다. StorSimple 가상 배열이 단일 노드 장치라는 점을 고려하면, 진행 중인 모든 IO가 중단되고 장치가 한동안 중단됩니다. 업데이트를 적용하기 전에 호스트에서 볼륨 또는 공유를 오프라인으로 전환한 후 장치를 오프라인으로 전환하는 것이 좋습니다. 이렇게 하면 데이터 손상 가능성이 최소화됩니다.

## 로컬 웹 UI를 사용하여 업데이트 또는 핫픽스 적용

업데이트 또는 핫픽스를 설치하기 전에, 업데이트 또는 핫픽스를 호스트의 로컬에 다운로드하거나 네트워크 공유를 통해 액세스할 수 있는지 확인합니다.

#### 업데이트 또는 핫픽스를 설치하려면

1. 로컬 웹 UI에서 **유지 관리** > **소프트웨어 업데이트**로 이동합니다.

2. **Update file path**(업데이트 파일 경로)에 업데이트 또는 핫픽스의 파일 이름을 입력합니다. 네트워크 공유에 있는 경우 업데이트 또는 핫픽스 설치 파일로 이동할 수 있습니다. 업데이트가 시작됩니다. 완료된 후에 알림이 표시됩니다.

    ![장치 업데이트](./media/storsimple-ova-update/image43.png)

## 다음 단계

[StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0224_2016-->