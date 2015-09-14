<properties 
   pageTitle="StorSimple 장치의 섀시 교체 | Microsoft Azure"
	description="StorSimple 기본 장치 또는 EBOD 엔클로저의 섀시를 꺼내고 교체하는 방법을 설명합니다."
	services="storsimple"
	documentationCenter=""
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="08/31/2015"
	ms.author="alkohli"/>

# StorSimple 장치의 섀시 교체

## 개요

이 자습서에서는 StorSimple 장치의 섀시를 꺼내고 교체하는 방법을 설명합니다. StorSimple 8100 모델은 단일 엔클로저 장치(섀시 1개)인 반면 8600은 이중 엔클로저 장치(섀시 2개)입니다. 8600 모델의 경우 장치에 오류가 발생할 수 있는 두 개의 섀시(기본 엔클로저용 섀시 또는 EBOD 엔클로저용 섀시)가 있습니다.

두 경우 모두 Microsoft에서 제공한 교체 섀시가 비어 있게 됩니다. PCM(전원 및 냉각 모듈), 컨트롤러 모듈, SSD(반도체 디스크 드라이브), HDD(하드 디스크 드라이브) 또는 EBOD 모듈은 포함되지 않습니다.

>[AZURE.IMPORTANT]섀시를 꺼내고 교체하기 전에 [StorSimple 하드웨어 구성 요소 교체](storsimple-hardware-component-replacement.md)에서 안전 정보를 검토하세요.

## 섀시 꺼내기

StorSimple 장치의 섀시를 꺼내려면 다음 단계를 따르세요.

#### 섀시를 꺼내려면

1. StorSimple 장치가 종료되고 모든 전원에서 연결이 끊어졌는지 확인합니다.

2. 해당하는 경우 모든 네트워크 및 SAS 케이블을 뺍니다.

3. 랙에서 장치를 꺼냅니다.

4. 각 드라이브를 꺼내고 들어 있던 슬롯을 적어둡니다. 자세한 내용은 [디스크 드라이브 꺼내기](storsimple-disk-drive-replacement.md#remove-the-disk-drive)를 참조하세요.

5. EBOD 엔클로저에서(이 섀시에서 오류가 발생한 경우) EBOD 컨트롤러 모듈을 꺼냅니다. 자세한 내용은 [EBOD 컨트롤러 꺼내기](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller)를 참조하세요.

    기본 엔클로저에서(이 섀시에서 오류가 발생한 경우) 컨트롤러를 꺼내고 들어 있던 슬롯을 적어둡니다. 자세한 내용은 [컨트롤러 꺼내기](storsimple-controller-replacement.md#remove-a-controller)를 참조하세요.

## 섀시 설치

Microsoft Azure StorSimple 장치에 섀시를 설치하려면 다음 단계를 따르세요.

#### 섀시를 설치하려면

1. 섀시를 랙에 탑재합니다. 자세한 내용은 [StorSimple 8100 장치 랙 탑재](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) 또는 [StorSimple 8600 장치 랙 탑재](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device)를 참조하세요.

2. 섀시를 랙에 탑재한 후 이전에 설치된 곳과 동일한 위치에 컨트롤러 모듈을 설치합니다.

3. 이전에 설치된 곳과 동일한 위치 및 슬롯에 드라이브를 설치합니다.

    >[AZURE.NOTE]일반적으로 SSD를 먼저 슬롯에 넣은 후 HDD를 설치하는 것이 좋습니다.

2. 장치를 랙에 탑재하고 구성 요소를 설치한 후 장치를 해당 전원에 연결하고 장치를 켭니다. 자세한 내용은 [StorSimple 8100 장치 케이블 연결](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) 또는 [StorSimple 8600 장치 케이블 연결](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)을 참조하세요.

## 다음 단계

[StorSimple 하드웨어 구성 요소 교체](storsimple-hardware-component-replacement.md)에 대해 자세히 알아봅니다.

<!---HONumber=September15_HO1-->