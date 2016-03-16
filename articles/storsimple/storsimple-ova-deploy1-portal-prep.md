<properties
   pageTitle="StorSimple 가상 배열 배포 1 – 포털 준비"
   description="StorSimple 가상 배열을 배포하는 첫 번째 자습서에는 포털 준비가 포함됩니다."
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
   ms.workload="NA"
   ms.date="03/01/2016"
   ms.author="alkohli"/>

# StorSimple 가상 배열 배포 – 포털 준비

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## 개요 

이 문서는 2016년 3월 GA(일반 공급) 버전을 실행하는 Microsoft Azure StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치 또는 StorSimple 가상 장치라고도 함)에 적용됩니다. 가상 배열을 파일 서버 또는 iSCSI 서버로 완전히 배포하는 데 필요한 배포 자습서 시리즈의 첫 번째 문서입니다. 이 문서는 가상 배열을 프로비전하기 전에 StorSimple Manager 서비스를 만들고 구성하는 데 필요한 준비 사항을 설명합니다. 이 문서는 배포 구성 검사 목록 뿐만 아니라 필수 조건과도 연관됩니다.

설치 및 구성 프로세스를 완료하려면 관리자 권한이 필요합니다. 시작하기 전에 배포 구성 검사 목록을 검토하는 것이 좋습니다. 포털 준비에는 10분 미만이 소요됩니다.

이 문서에 게시된 StorSimple 배포 정보는 StorSimple 가상 배열에만 적용됩니다.

### 시작

배포 워크플로는 포털 준비, 가상화된 환경에 가상 배열 프로비전, 설치 완료로 구성됩니다. 파일 서버 또는 iSCSI 서버로 StorSimple 가상 배열 배포를 시작하려면 다음 표에 준비된 리소스(문서 및 동영상)를 참고해야 합니다.

#### 배포 문서

StorSimple 가상 배열을 배포하려면 다음 문서를 지정된 순서대로 참조하세요.

| **#** | **단계** | **수행할 사항** | **이러한 문서를 사용합니다.**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1\. | **Azure 클래식 포털 설정** | StorSimple 가상 장치를 프로비전하기 전에 StorSimple Manager 서비스를 만들고 구성합니다. |[포털 준비](storsimple-ova-deploy1-portal-prep.md)| 
|2\. | **가상 배열 프로비전** | Hyper-V의 경우, Hyper-V 2008 R2, Hyper-V 2012 또는 Hyper-V 2012 R2를 실행하는 호스트 시스템에서 StorSimple 가상 장치에 프로비전하고 연결합니다. <br></br> <br></br> VMware의 경우, VMware ESXi 5.5 이상을 실행하는 호스트 시스템에서 StorSimple 온-프레미스 가상 장치에 프로비전하고 연결합니다.<br></br>| [Hyper-V에서 가상 배열 프로비전](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [VMware에서 가상 배열 프로비전](storsimple-ova-deploy2-provision-vmware.md)|
|3\. | **가상 배열 설정** | 파일 서버에 대해 초기 설정을 수행하고 StorSimple 파일 서버를 등록하고 장치 설정을 완료합니다. 그런 다음 SMB 공유를 프로비전할 수 있습니다. <br></br> <br></br> iSCSI 서버에 대해 초기 설정을 수행하고 StorSimple iSCSI 서버를 등록하고 장치 설정을 완료합니다. 그런 다음 iSCSI 볼륨을 프로비전할 수 있습니다.| [파일 서버로 가상 배열 설정](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[iSCSI 서버로 가상 배열 설정](storsimple-ova-deploy3-iscsi-setup.md)|

#### 배포 동영상

| **이 단계를 수행하려면...** | **이 동영상을 보세요.**|
|----------------|-------------|
| StorSimple 가상 배열을 시작하는 단계별 지침 | [StorSimple 가상 배열 시작](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| Hyper-V에서 StorSimple 가상 배열을 프로비전하는 단계별 지침|[StorSimple 가상 배열 만들기](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|StorSimple 가상 배열을 구성하고 등록하는 단계별 지침|[StorSimple 가상 배열 구성](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|파일 서버로 구성된 StorSimple 가상 배열에서 공유를 만들고, 공유를 백업하고, 데이터를 복원하는 단계별 지침|[StorSimple 가상 배열 사용](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|StorSimple 가상 배열의 장애 조치(failover) 및 재해 복구에 대한 단계별 지침|[StorSimple 가상 배열 재해 복구](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

이제 Azure 클래식 포털 설치를 시작할 수 있습니다.

## 구성 검사 목록

구성 검사 목록에서는 StorSimple 장치에 소프트웨어를 구성하기 전에 수집해야 하는 정보를 설명합니다. 이 정보를 미리 준비하면 사용자 환경에서 StorSimple 장치를 배포하는 과정을 간소화 하는 데 도움이 됩니다. StorSimple 가상 장치를 파일 서버로 배포할지 iSCSI 서버로 배포할지에 따라서 다음 검사 목록 중 하나가 필요합니다.

-   [StorSimple 가상 배열 파일 서버 구성 검사 목록](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)을 다운로드합니다.

-   [StorSimple 가상 배열 iSCSI 서버 구성 검사 목록](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)을 다운로드합니다.

## 필수 조건

여기에는 StorSimple Manager 서비스, StorSimple 가상 장치, 데이터 센터 네트워크에 대한 필수 조건이 제공됩니다.

### StorSimple 관리자 서비스의 경우

시작하기 전에 다음 사항을 확인합니다.

-   액세스 자격 증명이 있는 Microsoft 계정이 있습니다.

-   액세스 자격 증명이 있는 Microsoft Azure 저장소 계정이 있습니다.

-   사용자의 Microsoft Azure 구독을 StorSimple Manager 서비스에 사용할 수 있습니다.

### StorSimple 가상 장치의 경우 

가상 장치를 배포하기 전에 다음 사항을 확인해야 합니다.

-   장치 프로비전에 사용될 수 있는 Hyper-V(2008 R2 이상) 또는 VMware(ESXi 5.5 이상)를 실행하는 호스트 시스템에 액세스할 수 있습니다.

-   가상 디스크 프로비전을 위해 호스트 시스템에서 다음 리소스를 전용할 수 있습니다.
	
	-   코어 4개 이상
	
	-   RAM 8GB 이상
	
	-   네트워크 인터페이스 하나
	
	-   시스템 데이터용 가상 디스크 500GB

### 데이터 센터 네트워크의 경우 

시작하기 전에 다음 사항을 확인합니다.

-   데이터 센터의 네트워크가 StorSimple 장치에 대한 네트워킹 요구 사항에 따라 구성되어 있습니다. 자세한 내용은 [StorSimple 가상 배열 시스템 요구 사항](storsimple-ova-system-requirements.md)을 참조하세요.

-   StorSimple 가상 장치에 전용 5Mbps 인터넷 대역폭(또는 그 이상)을 항상 사용할 수 있습니다. 이 대역폭은 다른 응용 프로그램과 공유하면 안됩니다.

## 단계별 준비

다음 단계별 지침을 사용하여 StorSimple Manager 서비스용 포털을 준비합니다.

## 1단계: 새 서비스 만들기

StorSimple Manager 서비스 단일 인스턴스는 여러 StorSimple 1200 장치를 관리할 수 있습니다. StorSimple 관리자 서비스의 새 인스턴스를 만들려면 다음 단계를 수행합니다. 1200 장치를 관리하는 기존 StorSimple Manager 서비스가 있는 경우에는 이 단계를 건너뛰고 [2단계: 서비스 등록 키 받기](#step-2-get-the-service-registration-key)로 이동합니다.

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
> 
> 서비스와 함께 저장소 계정을 자동으로 만들도록 설정하지 않은 경우, 서비스를 성공적으로 만든 후 하나 이상의 저장소 계정을 만들어야 합니다.
> 

> - 저장소 계정을 자동으로 만들지 않은 경우 자세한 지침은 [서비스에 대한 새 저장소 계정 구성](#optional-step-configure-a-new-storage-account-for-the-service)을 참조하세요.
> 

> - 저장소 계정을 자동으로 생성하도록 설정한 경우, [2단계: 서비스 등록 키 받기](#step-2-get-the-service-registration-key)로 이동합니다.


## 2단계: 서비스 등록 키 받기


StorSimple 관리자 서비스를 실행한 후에는 서비스 등록 키를 받아야 합니다. 이 키는 StorSimple 장치를 서비스에 등록 및 연결하는 데 사용됩니다.

[Azure 클래식 포털](https://manage.windowsazure.com/)에서 다음 단계를 수행합니다.


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
> 
> 서비스 등록 키는 StorSimple Manager 서비스에 등록해야 하는 모든 StorSimple Manager 장치를 등록하는 데 사용됩니다.

## 3단계: 가상 장치 이미지 다운로드

서비스 등록 키를 확보한 후에는 호스트 시스템에 가상 장치를 프로비전할 적절한 가상 장치 이미지를 다운로드해야 합니다. 가상 장치 이미지는 운영 체제 별로 구분되며 Azure 클래식 포털의 빠른 시작 페이지에서 다운로드할 수 있습니다.

> [AZURE.IMPORTANT] StorSimple 가상 배열에서 실행되는 소프트웨어는 Storsimple Manager 서비스와 함께 사용해야 합니다.


[Azure 클래식 포털](https://manage.windowsazure.com/)에서 다음 단계를 수행합니다.

#### 가상 장치 이미지를 가져오려면

1.  **StorSimple 관리자 서비스** 페이지에서 작성한 서비스를 클릭합니다. 그러면 **빠른 시작** 페이지로 이동합니다. 언제든지 빠른 시작 아이콘 ![](./media/storsimple-ova-deploy1-portal-prep/image8.png)을 클릭하여 **빠른 시작** 페이지에 액세스할 수 있습니다.


1.  데이터 센터의 네트워크 공유에 적절한 VHD 또는 VHDX를 다운로드합니다. 별도 이미지를 사용할 수 있는 버전은 다음과 같습니다.

	-   Hyper-V 2012 이상
	
	-   Hyper-V 2008 R2 이상

	-   VMWare ESXi 5.5 이상

	> [AZURE.IMPORTANT] StorSimple 가상 배열에서 실행되는 소프트웨어는 Storsimple Manager 서비스와 함께 사용해야 합니다.


1.  가상 장치를 프로비전하는 데 사용할 호스트 운영 체제의 이미지를 클릭합니다. 이렇게 하면 Microsoft 다운로드 센터로 이동합니다.

1.  Hyper-V를 사용하는 경우 Hyper-V 2012용 VHDX 또는 Hyper-V 2008 R2용 VHD를 다운로드합니다. VMware를 사용하는 경우 VMDK를 다운로드합니다. VHDX는 4.77GB의 압축된 파일이며 VHD는 4.77GB 파일이고 VMDK는 4.75GB 파일입니다. 파일을 다운로드하는 시간은 인터넷 연결에 따라 다릅니다.

2.  파일 압축을 풀고 로컬 드라이브에 압축을 푼 위치를 기록합니다.

![동영상 아이콘](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **동영상 사용 가능**

StorSimple 가상 배열을 시작하는 단계별 지침에 대한 동영상을 보세요.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## 선택적 단계: 서비스에 대한 새 저장소 계정 구성

서비스와 저장소 계정을 자동으로 생성하도록 설정하지 않은 경우에만 수행해야 하는 선택적 단계입니다.

다른 지역에 Azure 저장소 계정을 만들어야 하는 경우 단계별 지침은 [저장소 계정을 만드는 방법](storage-create-storage-account.md#create-a-storage-account)을 참조하세요.

기존 Microsoft Azure 저장소 계정을 추가하려면 StorSimple Manager 서비스 페이지의 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 다음 단계를 수행합니다.

#### 저장소 계정을 추가하려면

1.  StorSimple 관리자 서비스 방문 페이지에서 서비스를 선택하고 두번 클릭합니다. 이렇게 하면 **퀵 스타트** 페이지로 이동됩니다. **구성** 페이지를 선택합니다.

2.  **저장소 계정 추가/편집**을 클릭합니다. **저장소 계정 추가/편집** 대화 상자에서 다음을 수행합니다.

	1.  **새로 추가**를 클릭합니다.

	1.  저장소 계정의 이름을 제공합니다.

	1.  Microsoft Azure 저장소 계정의 기본 **액세스 키**를 지정합니다.

	1.  **SSL 모드 사용**을 선택하여 장치와 클라우드 간의 네트워크 통신을 위한 보안 채널을 만듭니다. 사설 클라우드 내에서 작업 중인 경우에만 **SSL 모드 사용** 확인란을 지웁니다.

	1.  확인 아이콘![](./media/storsimple-ova-deploy1-portal-prep/image7.png)을 클릭합니다. 저장소 계정이 성공적으로 만들어진 후 알림이 표시됩니다.

		![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  새로 만들어진 저장소 계정이 **저장소 계정**의 **구성** 페이지에 표시됩니다. **저장**을 클릭하여 새로 만들어진 저장소 계정을 저장합니다. 확인하라는 메시지가 표시되면 **확인**을 클릭합니다.


## 다음 단계

다음 단계는 StorSimple 가상 장치에 대한 가상 컴퓨터를 프로비전하는 것입니다. 호스트 운영 체제에 따라서 다음의 자세한 지침을 참조하세요.

-   [Hyper-V에서 StorSimple 가상 배열 프로비전](storsimple-ova-deploy2-provision-hyperv.md)

-   [VMware에서 StorSimple 가상 배열 프로비전](storsimple-ova-deploy2-provision-vmware.md)

<!---HONumber=AcomDC_0302_2016-->