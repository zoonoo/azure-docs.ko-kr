<properties
   pageTitle="StorSimple 가상 배열 배포 3 – 가상 장치를 파일 서버로 설정"
   description="StorSimple 가상 배열 배포의 세 번째 자습서는 가상 장치를 파일 서버로 설정하는 방법을 설명합니다."
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
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# StorSimple 가상 배열 배포 - 파일 서버로 설정

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## 소개 

이 문서는 2016년 3월 GA(일반 공급) 버전을 실행하는 Microsoft Azure StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치 또는 StorSimple 가상 장치라고도 함)에 적용됩니다. 초기 설정을 수행하고, StorSimple 파일 서버를 등록하고, 장치 설정을 완료하고, SMB 공유를 만들고 연결하는 방법을 설명합니다. 가상 배열을 파일 서버 또는 iSCSI 서버로 완전히 배포하는 데 필요한 배포 자습서 시리즈의 마지막 문서입니다.

설정 및 구성 프로세스를 완료하는 데 10분 정도가 소요됩니다.


## 설정 필수 조건

StorSimple 가상 장치를 구성하고 설정하기 전에 다음 사항을 확인합니다.

-   [Hyper-V에서 StorSimple 가상 배열 프로비전](storsimple-ova-deploy2-provision-hyperv.md) 또는 [VMware에서 StorSimple 가상 배열 프로비전](storsimple-ova-deploy2-provision-vmware.md)에 설명된 대로 가상 장치를 프로비전하고 연결했습니다.

-   StorSimple 가상 장치를 관리하려고 만든 StorSimple Manager 서비스의 서비스 등록 키가 있습니다. 자세한 내용은 StorSimple 가상 배열의 [2단계: 서비스 등록 키 받기](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)를 참조하세요.

-   기존 StorSimple Manager 서비스에 가상 장치를 두 번째 또는 후속으로 등록하는 경우에는 서비스 데이터 암호화 키가 있어야 합니다. 이 키는 서비스에 첫 번째 장치가 등록될 때 생성되었습니다. 이 키를 잃어버린 경우에는 StorSimple 가상 배열의 [서비스 데이터 암호화 키 가져오기](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)를 참조하세요.

## 단계별 설정

다음 단계별 지침을 사용하여 StorSimple 가상 장치를 설정 및 구성합니다.

## 1단계: 로컬 웹 UI 설정을 완료하고 장치를 등록 


#### 설정을 완료하고 장치를 등록하려면

1.  브라우저 창을 열고 로컬 웹 UI에 연결합니다. 다음을 입력합니다.	

    `https://<ip-address of network interface>`

	이전 단계에서 언급한 연결 URL을 사용합니다. 웹 사이트의 보안 인증서에 문제가 있음을 나타내는 오류가 표시됩니다. **이 웹 페이지에서 계속 진행**을 클릭합니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  가상 장치의 웹 UI에 **StorSimpleAdmin**으로 로그인합니다. [Hyper-V에서 StorSimple 가상 배열 프로비전](storsimple-ova-deploy2-provision-hyperv.md) 또는 [VMware에서 StorSimple 가상 배열 프로비전](storsimple-ova-deploy2-provision-vmware.md)의 3단계: 가상 장치 시작에서 변경한 장치 관리자 암호를 입력합니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  **홈** 페이지로 이동합니다. 이 페이지는 StorSimple Manager 서비스에 가상 장치를 구성하고 등록하는 데 필요한 다양한 설정을 설명합니다. **네트워크 설정**, **웹 프록시 설정**, **시간 설정**은 선택 사항입니다. 필요한 설정은 **장치 설정** 및 **클라우드 설정**입니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  **네트워크 설정** 페이지의 **네트워크 인터페이스**에서 DATA 0이 자동으로 구성됩니다. 각 네트워크 인터페이스는 IP 주소를 자동으로 가져오도록(DHCP) 기본 설정됩니다. 따라서 IP 주소, 서브넷 및 게이트웨이가 자동으로 할당됩니다(IPv4 및 IPv6 모두에 대해).

	![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

	장치를 프로비전하는 동안 네트워크 인터페이스를 둘 이상 추가한 경우에는 여기에서 구성할 수 있습니다. 네트워크 인터페이스를 IPv4로만 구성하거나 IPv4와 IPv6 둘 다로 구성할 수 있습니다. IPv6 전용 구성은 지원되지 않습니다.

1.  장치가 클라우드 저장소 서비스 공급자와 통신하려고 시도하거나 파일 서버로 구성된 경우 이름으로 장치를 확인하려고 시도하는 경우에 DNS 서버가 사용되기 때문에 DNS 서버가 필요합니다. **네트워크 설정** 페이지의 **DNS 서버** 아래에:

    1.  기본 및 보조 DNS 서버는 자동으로 구성됩니다. 고정 IP 주소를 구성하도록 선택하면 DNS 서버를 지정할 수 있습니다. 고가용성을 위해 기본 및 보조 DNS 서버를 구성하는 것이 좋습니다.

    2.  **Apply**를 클릭합니다. 네트워크 설정이 적용되고 유효성 검사가 수행됩니다.

2.  **장치 설정** 페이지에서:

    1.  장치에 고유한 **이름**을 할당합니다. 이름에는 1-15자를 사용할 수 있으며 문자, 숫자 및 하이픈을 포함할 수 있습니다.

    2.  만드는 장치의 **유형**에 대해 **파일 서버** 아이콘 ![](./media/storsimple-ova-deploy3-fs-setup/image6.png)을 클릭합니다. 파일 서버를 사용하여 공유 폴더를 만들 수 있습니다.

    3.  장치가 파일 서버이므로 장치를 도메인에 가입해야 합니다. **도메인 이름**을 입력합니다.

	1.  **Apply**를 클릭합니다.

2.  대화 상자가 표시됩니다. 지정된 형식으로 도메인 자격 증명을 입력합니다. 확인 아이콘을 클릭합니다. 도메인 자격 증명이 확인됩니다. 자격 증명이 올바르지 않으면 오류 메시지가 표시됩니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  **Apply**를 클릭합니다. 장치 설정이 적용되고 유효성 검사가 수행됩니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

	> [AZURE.NOTE]
	> 
	> 가상 배열이 Active Directory용 자체 OU(조직 구성 단위)에 있으며 GPO(그룹 정책 개체)가 적용되었거나 상속되지 않았는지 확인합니다. 그룹 정책은 StorSimple 가상 배열에 바이러스 백신 소프트웨어와 같은 응용 프로그램을 설치할 수 있습니다. 추가 소프트웨어를 설치하는 것은 지원되지 않으며 데이터 손상을 초래할 수 있습니다.

1.  선택적으로 웹 프록시 서버를 구성합니다. 웹 프록시 구성은 선택 사항이지만 웹 프록시를 사용하면 여기서만 구성할 수 있습니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

	**웹 프록시** 페이지에서:

	1.  *http://&lt;host-IP 주소 또는 FDQN&gt;:포트 번호* 형식으로 **웹 프록시 URL**을 입력합니다. HTTPS URL은 지원되지 않습니다.

	2.  **인증**은 **기본** 또는 **없음**으로 지정합니다.

	3.  인증을 사용하는 경우 **사용자 이름** 및 **암호**를 입력해야 합니다.

	4.  **Apply**를 클릭합니다. 구성된 웹 프록시 설정의 유효성을 검사하고 적용합니다.

1.  선택적으로 장치에 대한 시간 설정(예: 표준 시간대 및 기본 및 보조 NTP 서버)을 구성합니다. 클라우드 서비스 공급자와 인증할 수 있도록 장치 시간을 동기화해야 하기 때문에 NTP 서버가 필요합니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

	**시간 설정** 페이지에서:

	1.  드롭다운 목록에서 장치가 배포되는 지리적 위치를 기반으로 **표준 시간대**를 설정합니다. 장치의 기본 표준 시간대는 PST입니다. 장치는 모든 예약된 작업에 대해 이 표준 시간대를 사용합니다.

	2.  장치에 **기본 NTP 서버**를 지정하거나 time.windows.com의 기본값을 적용합니다. 네트워크에서 NTP 트래픽이 데이터 센터에서 인터넷으로 전달되도록 허용하는지 확인합니다.

	3.  선택적으로 장치에 대한 **보조 NTP 서버**를 지정합니다.

	4.  **Apply**를 클릭합니다. 구성된 시간 설정의 유효성을 검사하고 적용합니다.

1.  장치에 대한 클라우드 설정을 구성합니다. 이 단계에서는 로컬 장치 구성을 완료한 후 StorSimple Manager 서비스에 장치를 등록합니다.

    1.  StorSimple 가상 배열의 [2단계: 서비스 등록 키 받기](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)에서 확보한 **서비스 등록 키**를 입력합니다.

    2.  이 서비스에 등록된 첫 번째 장치인 경우 이 단계를 건너뛰고 다음 단계로 이동합니다. 서비스에 장치를 처음으로 등록하는 경우가 아니라면 **서비스 데이터 암호화 키**를 제공해야 합니다. 이 키는 StorSimple Manager 서비스에 추가 장치를 등록하기 위한 서비스 등록 키에 필요합니다. 자세한 내용은 로컬 웹 UI의 [서비스 데이터 암호화 키 받기](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)를 참조하세요.

    3.  **Register**를 클릭합니다. 장치가 다시 시작됩니다. 장치 등록이 완료되기까지 2-3분 정도 기다려야 할 수 있습니다. 장치가 다시 시작된 후 로그인 페이지가 열립니다.

		![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
	

1.  Azure 클래식 포털로 돌아갑니다. **장치** 페이지에서 상태를 조회하여 장치가 서비스에 성공적으로 연결되었는지 확인합니다. 장치 상태는 **활성**이어야 합니다.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## 2단계: 필요한 장치 설정 완료

StorSimple 장치의 구성을 완료하려면 다음 사항이 필요합니다.

-   장치에 연결할 저장소 계정을 선택합니다.

-   클라우드에 전송되는 데이터에 대한 암호화 설정을 선택합니다.

필요한 장치 설정을 완료하려면 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 다음 단계를 수행합니다.

#### 최소 장치 설정을 완료하려면

1.  **장치** 페이지에서 방금 만든 장치를 선택합니다. 이 장치는 **활성**으로 표시됩니다. 장치 이름을 가리키는 화살표를 클릭한 후 **빠른 시작**을 클릭합니다.

2.  **장치 설치 완료**를 클릭하여 장치 구성 마법사를 시작합니다.

3.  장치 구성 마법사의 **기본 설정** 페이지에서 다음을 수행합니다.

	1.  장치에 사용할 저장소 계정을 지정합니다. 드롭다운 목록에서 이 구독의 기존 저장소 계정을 선택하거나, 다른 구독에서 계정을 선택하려면 **더 추가**를 지정할 수 있습니다.

	2.  클라우드에 전송될 모든 미사용 데이터에 대한 암호화 설정(AES 암호화)을 정의합니다. 데이터를 암호화하려면 **enable cloud storage encryption key(클라우드 저장소 암호화 키 사용)** 콤보 상자를 클릭합니다. 32자를 포함하는 클라우드 저장소 암호화 키를 입력합니다. 확인을 위해 키를 다시 입력합니다. 암호화를 위한 사용자 정의 키에 256비트 AES 키가 사용됩니다.

	3.  확인 아이콘![](./media/storsimple-ova-deploy3-fs-setup/image15.png)을 클릭합니다.

		![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

설정이 업데이트됩니다. 설정이 업데이트된 후에는 장치 설치 완료 단추가 회색으로 표시됩니다. 장치 **퀵 스타트** 페이지로 돌아갑니다.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> **구성** 페이지에 액세스하여 언제든지 다른 모든 장치 설정을 수정할 수 있습니다.

## 3단계: 공유 추가

공유를 만들려면 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 다음 단계를 수행합니다.

#### 공유를 만들려면

1.  장치 **빠른 시작** 페이지에서 **공유 추가**를 클릭합니다. 공유 추가 마법사가 시작됩니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  **기본 설정** 페이지에서 다음을 수행합니다.

    1.  공유에 대한 고유 이름을 지정합니다. 이름은 3~127개의 문자를 포함하는 문자열이어야 합니다.

    2.  (선택 사항) 공유에 대한 설명을 제공합니다. 설명은 공유 소유자를 식별하는 데 도움이 됩니다.

    3.  공유에 대한 사용 유형을 선택합니다. 사용 유형에는 **계층화됨** 또는 **로컬로 고정**을 지정할 수 있으며 계층화됨이 기본값입니다. 로컬 보증, 낮은 대기 시간 및 높은 성능이 필요한 워크로드의 경우 **로컬로 고정** 공유를 선택합니다. 다른 모든 데이터에 대해서는 **계층화됨** 공유를 선택합니다.

	로컬로 고정된 공유는 씩 프로비전되며, 공유의 기본 데이터가 장치에 로컬로 유지되고 클라우드로 유출되지 않습니다. 반면에 계층화된 공유는 씬 프로비전됩니다. 계층화된 공유를 만들 때 공간의 10%는 로컬 계층에 프로비전되고 공간의 90%는 클라우드에 프로비전됩니다. 예를 들어, 1TB 볼륨을 프로비전하는 경우 100GB는 로컬 공간에 상주하고 900GB는 데이터가 계층화될 때 클라우드에서 사용됩니다. 이것은 장치의 로컬 공간이 부족하면 계층화된 공유를 프로비전할 수 없다는 것을 의미합니다.

1.  공유의 프로비전된 용량을 지정합니다. 지정된 용량은 사용 가능한 용량보다 작아야 합니다. 계층화된 공유를 사용하는 경우, 공유 크기는 500GB~20TB 사이여야 합니다. 로컬로 고정된 공유의 경우, 공유 크기를 50GB~2TB 사이로 지정합니다. 사용 가능한 용량을 공유를 프로비전하기 위한 가이드로 사용합니다. 사용 가능한 로컬 용량이 0GB이면, 로컬 공유 또는 계층화된 공유를 프로비전할 수 없습니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  화살표 아이콘![](./media/storsimple-ova-deploy3-fs-setup/image19.png)을 클릭하여 다음 페이지로 이동합니다.

1.  **추가 설정** 페이지에서 공유에 액세스할 사용자 또는 그룹에 권한을 할당합니다. 사용자 또는 사용자 그룹의 이름을 *<john@contoso.com>* 형식으로 지정합니다. 이 공유에 액세스하는 관리자 권한은 사용자 그룹(단일 사용자 대신)을 사용하여 허용하는 것이 좋습니다. 여기에서 권한을 할당한 후에 Windows 탐색기를 사용하여 해당 권한을 수정할 수 있습니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  확인 아이콘![](./media/storsimple-ova-deploy3-fs-setup/image21.png)을 클릭합니다. 지정한 설정으로 공유가 만들어집니다. 기본적으로 공유에 대한 모니터링 및 백업을 사용하도록 설정됩니다.

## 4단계: 공유에 연결

이전 단계에서 만든 공유에 연결해야 합니다. Windows Server 호스트에서 이러한 단계를 수행합니다.

#### 공유에 연결하려면

1.  ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + R 키를 누릅니다. 실행 창에서 *\<file server name>*로 경로를 지정하고, *파일 서버 이름*을 파일 서버에 할당한 장치 이름으로 변경합니다. **확인**을 클릭합니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  탐색기가 열립니다. 폴더로 만든 공유가 표시됩니다. 공유(폴더)를 선택하고 두 번 클릭하여 콘텐츠를 봅니다.

	![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  이제 공유에 파일을 추가하고 백업을 수행할 수 있습니다.

![동영상 아이콘](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **동영상 사용 가능**

StorSimple 가상 배열을 파일 서버로 구성하고 등록하는 방법을 보려면 동영상을 시청하세요.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## 다음 단계

로컬 웹 UI를 사용하여 [StorSimple 가상 배열을 관리](storsimple-ova-web-ui-admin.md)하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0601_2016-->