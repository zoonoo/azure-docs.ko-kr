---
title: "StorSimple 가상 배열 iSCSI 서버 설정 | Microsoft Docs"
description: "초기 설정을 수행하고, StorSimple iSCSI 서버를 등록하고, 장치 설정을 완료하는 방법을 설명합니다."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 1a854bb3-3068-4db9-87b7-9f3692ab64e4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a5d3d1c9afd8023b201ff37318a3905b5fe791a2


---
# <a name="deploy-storsimple-virtual-array-set-up-your-virtual-device-as-an-iscsi-server"></a>StorSimple 가상 배열 배포 – 가상 장치를 iSCSI 서버로 설정
![iscsi 설정 프로세스 흐름](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>개요
이 배포 자습서는 2016년 3월 GA(일반 공급) 버전을 실행하는 Microsoft Azure StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치 또는 StorSimple 가상 장치라고도 함)에 적용됩니다. 이 자습서는 초기 설정을 수행하고, StorSimple iSCSI 서버를 등록하고, 장치 설정을 완료하고, StorSimple 가상 장치 iSCSI 서버에서 볼륨을 만들고, 탑재하고, 초기화하고, 포맷하는 방법을 설명합니다. 이 문서의 StorSimple 설정 정보는 StorSimple 가상 배열에만 적용됩니다. 

여기서 설명된 프로시저를 완료하려면 30분에서 1시간 정도가 소요됩니다. 이 문서에 게시된 정보는 StorSimple 가상 배열에만 적용됩니다.

## <a name="setup-prerequisites"></a>설정 필수 조건
StorSimple 가상 장치를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [StorSimple 가상 배열 배포 - Hyper-V에서 가상 배열 프로비전](storsimple-ova-deploy2-provision-hyperv.md) 또는 [StorSimple 가상 배열 배포 - VMware에서 가상 배열 프로비전](storsimple-ova-deploy2-provision-vmware.md)에 설명된 대로 가상 장치를 프로비전하고 연결했습니다.
* StorSimple 가상 장치를 관리하려고 만든 StorSimple Manager 서비스의 서비스 등록 키가 있습니다. 자세한 내용은 **StorSimple 가상 배열 배포 – 포털 준비** 의 [2단계: 서비스 등록 키 받기](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)를 참조하세요.
* 기존 StorSimple Manager 서비스에 가상 장치를 두 번째 또는 후속으로 등록하는 경우에는 서비스 데이터 암호화 키가 있어야 합니다. 이 키는 서비스에 첫 번째 장치가 등록될 때 생성되었습니다. 이 키를 잃어버린 경우에는 **웹 UI를 사용하여 StorSimple 가상 배열 관리** 의 [서비스 데이터 암호화 키 가져오기](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)를 참조하세요.

## <a name="step-by-step-setup"></a>단계별 설정
다음 단계별 지침을 사용하여 StorSimple 가상 장치를 설정 및 구성합니다.

* [1단계: 로컬 웹 UI 설정을 완료하고 장치를 등록](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [2단계: 필요한 장치 설정 완료](#step-2-complete-the-required-device-setup)
* [3단계: 볼륨 추가](#step-3-add-a-volume)
* [4단계: 볼륨 탑재, 초기화 및 포맷](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1단계: 로컬 웹 UI 설정을 완료하고 장치를 등록
#### <a name="to-complete-the-setup-and-register-the-device"></a>설정을 완료하고 장치를 등록하려면
1. 브라우저 창을 열고 다음을 입력하여 웹 UI에 연결합니다.
   
    `https://<ip-address of network interface>`
   
    이전 단계에서 언급한 연결 URL을 사용합니다. 웹 사이트의 보안 인증서에 문제가 있음을 알려주는 오류가 표시됩니다. **이 웹 페이지에서 계속 진행**을 클릭합니다.
   
    ![보안 인증서 오류](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)
2. 가상 장치의 웹 UI에 **StorSimpleAdmin**으로 로그인합니다. [StorSimple 가상 배열 배포 - Hyper-V에서 가상 장치 프로비전](storsimple-ova-deploy2-provision-hyperv.md) 또는 [StorSimple 가상 배열 배포 - VMware에서 가상 장치 프로비전](storsimple-ova-deploy2-provision-vmware.md)의 3단계: 가상 장치 시작에서 변경한 장치 관리자 암호를 입력합니다.
   
    ![로그인 페이지](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)
3. **홈** 페이지로 이동합니다. 이 페이지는 StorSimple Manager 서비스에 가상 장치를 구성하고 등록하는 데 필요한 다양한 설정을 설명합니다. **네트워크 설정**, **웹 프록시 설정**, **시간 설정**은 선택 사항입니다. 필요한 설정은 **장치 설정** 및 **클라우드 설정**입니다.
   
    ![홈 페이지](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)
4. **네트워크 설정** 페이지의 **네트워크 인터페이스**에서 DATA 0이 자동으로 구성됩니다. 각 네트워크 인터페이스는 IP 주소를 자동으로 가져오도록(DHCP) 기본 설정됩니다. 따라서 IP 주소, 서브넷 및 게이트웨이가 자동으로 할당됩니다(IPv4 및 IPv6 모두에 대해).
   
    장치를 iSCSI 서버(블록 저장소를 프로비전하기 위해)로 배포할 계획이므로, **자동으로 IP 주소 받기** 옵션을 사용하지 않도록 설정하고 고정 IP 주소를 구성하는 것이 좋습니다.
   
    ![네트워크 설정 페이지](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)
   
    장치를 프로비전하는 동안 네트워크 인터페이스를 둘 이상 추가한 경우에는 여기에서 구성할 수 있습니다. 네트워크 인터페이스를 IPv4로만 구성하거나 IPv4와 IPv6 둘 다로 구성할 수 있습니다. IPv6 전용 구성은 지원되지 않습니다.
5. 장치가 클라우드 저장소 서비스 공급자와 통신하려고 시도하거나 파일 서버로 구성된 경우 이름으로 장치를 확인하려고 시도하는 경우에 DNS 서버가 사용되기 때문에 DNS 서버가 필요합니다. **네트워크 설정** 페이지의 **DNS 서버** 아래에서:
   
   1. 기본 및 보조 DNS 서버는 자동으로 구성됩니다. 고정 IP 주소를 구성하도록 선택하면 DNS 서버를 지정할 수 있습니다. 고가용성을 위해 기본 및 보조 DNS 서버를 구성하는 것이 좋습니다.
   2. **Apply**를 클릭합니다. 네트워크 설정이 적용되고 유효성 검사가 수행됩니다.
6. **장치 설정** 페이지에서:
   
   1. 장치에 고유한 **이름** 을 할당합니다. 이름에는 1-15자를 사용할 수 있으며 문자, 숫자 및 하이픈을 포함할 수 있습니다.
   2. 만드는 장치의 **유형**에 대해 **iSCSI 서버** 아이콘 ![iSCSI server icon](./media/storsimple-ova-deploy3-iscsi-setup/image7.png)을 클릭합니다. iSCSI 서버에서 블록 저장소를 프로비전할 수 있습니다.
   3. 이 장치를 도메인에 가입할지 지정합니다. 장치가 iSCSI 서버인 경우, 도메인 가입은 선택 사항입니다. iSCSI 서버를 도메인에 가입하지 않으려면, **적용**을 클릭하고 설정이 적용될 때까지 기다린 후에 다음 단계로 건너뜁니다.
      
       장치를 도메인에 가입하려면 **도메인 이름**을 입력하고 **적용**을 클릭합니다.
      
      > [!NOTE]
      > iSCSI 서버를 도메인에 연결하는 경우 가상 배열이 Microsoft Azure Active Directory용 자체 OU(조직 구성 단위)에 있으며 GPO(그룹 정책 개체)가 적용되지 않았는지 확인합니다.
      > 
      > 
   4. 대화 상자가 표시됩니다. 지정된 형식으로 도메인 자격 증명을 입력합니다. 확인 아이콘 ![확인 아이콘](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)에 설명된 대로 가상 장치를 프로비전하고 연결했습니다. 도메인 자격 증명이 확인됩니다. 자격 증명이 올바르지 않으면 오류 메시지가 표시됩니다.
      
       ![자격 증명](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)
   5. **Apply**를 클릭합니다. 장치 설정이 적용되고 유효성 검사가 수행됩니다.
7. 선택적으로 웹 프록시 서버를 구성합니다. 웹 프록시 구성은 선택 사항이지만 웹 프록시를 사용하면 여기서만 구성할 수 있습니다.
   
    ![웹 프록시 구성](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)
   
    **웹 프록시** 페이지에서:
   
   1. *http://호스트 IP 주소* 또는 *FDQN:포트 번호* 형식으로 **웹 프록시 URL**을 공급합니다. HTTPS URL은 지원되지 않습니다.
   2. **인증**은 **기본** 또는 **없음**으로 지정합니다.
   3. 인증을 사용하는 경우에는 **사용자 이름** 및 **암호**도 제공해야 합니다.
   4. **Apply**를 클릭합니다. 구성된 웹 프록시 설정의 유효성을 검사하고 적용합니다.
8. 선택적으로 장치에 대한 시간 설정(예: 표준 시간대 및 기본 및 보조 NTP 서버)을 구성합니다. 클라우드 서비스 공급자와 인증할 수 있도록 장치 시간을 동기화해야 하기 때문에 NTP 서버가 필요합니다.
   
    ![시간 설정](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)
   
    **시간 설정** 페이지에서:
   
   1. 드롭다운 목록에서 해당 장치가 배포되는 지리적 위치를 기반으로 **표준 시간대** 를 선택합니다. 장치의 기본 표준 시간대는 PST입니다. 장치는 모든 예약된 작업에 대해 이 표준 시간대를 사용합니다.
   2. 장치에 **기본 NTP 서버** 를 지정하거나 time.windows.com의 기본값을 적용합니다. 네트워크에서 NTP 트래픽이 데이터 센터에서 인터넷으로 전달되도록 허용하는지 확인합니다.
   3. 선택적으로 장치에 대한 **보조 NTP 서버** 를 지정합니다.
   4. **Apply**를 클릭합니다. 구성된 시간 설정의 유효성을 검사하고 적용합니다.
9. 장치에 대한 클라우드 설정을 구성합니다. 이 단계에서는 로컬 장치 구성을 완료한 후 StorSimple Manager 서비스에 장치를 등록합니다.
   
   1. [StorSimple 가상 배열 배포 - 포털 준비](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)에서 **2단계:서비스 등록 키 얻기**에서 얻은 **서비스 등록 키**를 입력합니다.
   2. 서비스에 장치를 처음으로 등록하는 경우가 아니라면 **서비스 데이터 암호화 키**를 제공해야 합니다. 이 키는 StorSimple Manager 서비스에 추가 장치를 등록하기 위한 서비스 등록 키에 필요합니다. 자세한 내용은 로컬 웹 UI의 [서비스 데이터 암호화 키 받기](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) 를 참조하세요.
   3. **Register**를 클릭합니다. 장치가 다시 시작됩니다. 장치 등록이 완료되기까지 2-3분 정도 기다려야 할 수 있습니다. 장치가 다시 시작된 후 로그인 페이지가 열립니다.
      
      ![장치 등록](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)
10. Azure 클래식 포털로 돌아갑니다. **장치** 페이지에서 상태를 조회하여 장치가 서비스에 성공적으로 연결되었는지 확인합니다. 장치 상태는 **활성**이어야 합니다.
    
    ![장치 페이지](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>2단계: 필요한 장치 설정 완료
StorSimple 장치의 구성을 완료하려면 다음 사항이 필요합니다.

* 장치에 연결할 저장소 계정을 선택합니다.
* 클라우드에 전송되는 데이터에 대한 암호화 설정을 선택합니다.

필요한 장치 설정을 완료하려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

#### <a name="to-complete-the-minimum-device-setup"></a>최소 장치 설정을 완료하려면
1. **장치** 페이지에서, 방금 만든 장치를 선택합니다. 이 장치는 **활성**으로 표시됩니다. 장치 이름 옆의 화살표를 클릭한 후 **빠른 시작**을 클릭합니다.
   
    ![장치 페이지](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)
2. **장치 설치 완료** 를 클릭하여 장치 구성 마법사를 시작합니다.
   
    ![장치 구성 마법사](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)
3. 장치 구성 마법사의 **기본 설정** 페이지에서 다음을 수행합니다.
   
   1. 장치에 사용할 저장소 계정을 지정합니다. 구독의 드롭다운 목록에서 기존 저장소 계정을 선택하거나, 다른 구독에서 계정을 선택하려면 **더 추가** 를 지정할 수 있습니다.
   2. 클라우드에 전송될 모든 미사용 데이터에 대한 암호화 설정을 정의합니다. (StorSimple에는 AES-256 암호화가 사용됩니다.) 데이터를 암호화하려면 **클라우드 저장소 암호화 사용** 확인란을 선택합니다. 32자를 포함하는 클라우드 저장소 암호화 키를 입력합니다. 확인을 위해 키를 다시 입력합니다.
   3. 확인 아이콘 ![확인 아이콘](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)에 설명된 대로 가상 장치를 프로비전하고 연결했습니다.
      
      ![기본 설정](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)
      
      설정이 업데이트됩니다. 설정이 업데이트된 후에는 장치 설치 완료 단추를 사용할 수 없게 됩니다. 장치 **퀵 스타트** 페이지로 돌아갑니다.                                                        

> [!NOTE]
> **구성** 페이지에 액세스하여 언제든지 다른 모든 장치 설정을 수정할 수 있습니다.
> 
> 

## <a name="step-3-add-a-volume"></a>3단계: 볼륨 추가
볼륨을 만들려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

#### <a name="to-create-a-volume"></a>볼륨을 만들려면
1. 장치 **빠른 시작** 페이지에서 **볼륨 추가**를 클릭합니다. 추가 볼륨 마법사가 시작됩니다.
2. 볼륨 추가 마법사의 **기본 설정**에서 다음을 수행합니다.
   
   1. 볼륨에 고유한 이름을 지정합니다. 이름은 3~127개의 문자를 포함하는 문자열이어야 합니다.
   2. 볼륨에 대한 설명을 제공합니다. 설명은 볼륨 소유자를 식별하는 데 도움이 됩니다.
   3. 볼륨에 대한 사용 유형을 선택합니다. 사용 유형에는 **계층화된 볼륨** 또는 **로컬로 고정된 볼륨**이 있습니다. (**계층화된 볼륨**이 기본값입니다.) 로컬 보증, 낮은 대기 시간 및 높은 성능을 필요로 하는 워크로드의 경우 **로컬로 고정된** **볼륨**을 선택합니다. 다른 모든 데이터에 대해서는 **계층화된** **볼륨**을 선택합니다.
      
       로컬로 고정된 볼륨은 씩 프로비전되며, 볼륨의 기본 데이터가 장치에 유지되고 클라우드에 유출되지 않도록 합니다. 로컬로 고정된 볼륨을 만드는 경우 장치에서는 요청된 크기의 볼륨을 프로비전하기 위해 로컬 계층에서 사용 가능한 공간을 확인합니다. 로컬로 고정된 볼륨을 만드는 작업은 장치의 기존 데이터를 클라우드로 유출하는 것이 필요할 수 있으며, 볼륨을 만드는 데 시간이 오래 걸릴 수 있습니다. 전체 시간은 프로비전되는 볼륨의 크기, 사용 가능한 네트워크 대역폭 및 장치의 데이터에 따라 달라집니다.
      
       반면에 계층화된 볼륨은 씬 프로비전되며 매우 신속하게 만들 수 있습니다. 계층화된 볼륨을 만들 때 공간의 약 10%는 로컬 계층에 프로비전되고 공간의 90%는 클라우드에 프로비전됩니다. 예를 들어, 1TB 볼륨을 프로비전하는 경우 100GB는 로컬 공간에 상주하고 900GB는 데이터가 계층화될 때 클라우드에서 사용됩니다. 이것은 장치의 로컬 공간이 부족하면 계층화된 공유를 프로비전할 수 없다는 것을(10%를 사용할 수 없기 때문에) 의미합니다.
   4. 볼륨의 프로비전된 용량을 지정합니다. 지정된 용량은 사용 가능한 용량보다 작아야 합니다. 계층화된 볼륨을 만드는 경우, 그 크기는 500GB~5TB 사이여야 합니다. 로컬로 고정된 볼륨의 경우, 볼륨 크기를 50GB~500 GB 사이로 지정합니다. 사용 가능한 용량을 볼륨을 프로비전하는 가이드로 사용합니다. 사용 가능한 로컬 용량이 0GB이면, 로컬로 고정된 볼륨 또는 계층화된 볼륨을 프로비전할 수 없습니다.
      
       ![기본 설정](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)
   5. 화살표 아이콘 ![화살표 아이콘](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) 을 클릭하여 다음 페이지로 이동합니다.
3. **추가 설정** 페이지에서 새 ACR(액세스 제어 레코드)을 추가합니다.
   
   1. ACR의 **이름** 을 지정합니다.
   2. **iSCSI 초기자 이름**에서 Windows 호스트의 IQN(정규화된 iSCSI 이름)을 제공합니다. IQN이 없는 경우 [부록 A: Windows Server 호스트의 IQN 가져오기](#appendix-a-get-the-iqn-of-a-windows-server-host)로 이동합니다.
   3. **이 볼륨에 대해 기본 백업 사용** 확인란을 선택하여 기본 백업을 사용하는 것이 좋습니다. 기본 백업은 매일 22:30(장치 시간)에 실행되며 이 볼륨의 클라우드 스냅숏을 만드는 정책을 만듭니다.
      
       ![추가 설정](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)
   4. 확인 아이콘 ![확인 아이콘](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)에 설명된 대로 가상 장치를 프로비전하고 연결했습니다. 볼륨 만들기 작업이 시작됩니다. 다음과 유사한 진행 메시지가 표시됩니다.
      
       ![진행 메시지](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)
      
       지정한 설정으로 볼륨이 만들어집니다. 기본적으로 볼륨에 대한 모니터링 및 백업을 사용하도록 설정됩니다.
   5. 볼륨이 성공적으로 만들어졌는지 확인하려면 **볼륨** 페이지로 이동합니다. 볼륨이 목록으로 표시되어야 합니다.
      
       ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>4단계: 볼륨 탑재, 초기화 및 포맷
다음 단계를 수행하여 Windows Server 호스트에서 StorSimple 볼륨을 탑재, 초기화 및 포맷합니다.

#### <a name="to-mount-initialize-and-format-a-volume"></a>볼륨을 탑재, 초기화 및 포맷하려면
1. Microsoft iSCSI 초기자를 시작합니다.
2. **iSCSI 초기자 속성** 창의 **검색** 탭에서 **포털 검색**을 클릭합니다.
   
    ![포털 검색](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)
3. **대상 포털 검색** 대화 상자에서 iSCSI 사용 네트워크 인터페이스의 IP 주소를 입력하고 **확인**을 클릭합니다.
   
    ![IP 주소](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)
4. **iSCSI 초기자 속성** 창의 **대상** 탭에서 **검색된 대상**을 찾습니다. (각 볼륨은 검색된 대상이 됩니다.) 장치 상태가 **비활성**으로 표시되어야 합니다.
   
    ![검색된 대상](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)
5. 대상 장치를 선택하고 **연결**을 클릭합니다. 장치가 연결되면 상태가 **연결됨**으로 변경됩니다. Microsoft iSCSI 초기자 사용에 대한 자세한 내용은 [Microsoft iSCSI 초기자 설치 및 구성][1]을 참조하세요.
   
    ![대상 장치 선택](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)
6. Windows 호스트에서 Windows 로고 키 + X를 누르고 **실행**을 클릭합니다.
7. **실행** 대화 상자에 **Diskmgmt.msc**를 입력합니다. **확인**을 클릭하면 **디스크 관리** 대화 상자가 나타납니다. 오른쪽 창에 호스트의 볼륨이 표시됩니다.
8. **디스크 관리** 창에 탑재된 볼륨이 다음 그림과 같이 표시됩니다. 검색된 볼륨을 마우스 오른쪽 단추로 클릭(디스크 이름 클릭)한 다음 **온라인**을 클릭합니다.
   
    ![디스크 관리](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)
9. 마우스 오른쪽 단추를 클릭한 다음 **디스크 초기화**를 선택합니다.
   
    ![디스크 1 초기화](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)
10. 대화 상자에서 초기화하려는 디스크를 선택한 다음 **확인**을 클릭합니다.
    
    ![디스크 2 초기화](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)
11. 단순 볼륨 만들기 마법사가 시작됩니다. 디스크 크기를 선택한 후 **다음**을 클릭합니다.
    
    ![새 볼륨 마법사 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)
12. 볼륨에 드라이브 문자를 할당한 후 **다음**을 클릭합니다.
    
    ![새 볼륨 마법사 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)
13. 볼륨을 포맷하는 매개 변수를 입력합니다. **Windows Server에는 NTFS만 지원됩니다.**  AUS를 64K로 설정합니다. 볼륨의 레이블을 지정합니다. 이 이름은 StorSimple 가상 장치에 지정한 볼륨 이름과 동일하게 지정하는 것이 좋습니다. **다음**을 클릭합니다.
    
    ![새 볼륨 마법사 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)
14. 볼륨에 대한 값을 확인한 후 **마침**을 클릭합니다.
    
    ![새 볼륨 마법사 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)
    
    이 볼륨은 **온라인** on the **디스크 관리** 페이지로 이동합니다.
    
    ![온라인 볼륨](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>다음 단계
[로컬 웹 UI를 사용하여 StorSimple 가상 배열을 관리하는 방법을 알아봅니다](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>부록 A: Windows Server 호스트의 IQN 가져오기
Windows Server 2012를 실행하는 Windows 호스트의 iSCSI 정규화된 이름(IQN)을 가져오려면 다음 단계를 수행합니다.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Windows Server 호스트의 IQN을 가져오려면
1. Windows 호스트에서 Microsoft iSCSI 초기자를 시작합니다.
2. **iSCSI 초기자 속성** 창의 **구성** 탭에서 **초기자 이름** 필드의 문자열을 선택하고 복사합니다.
   
    ![iSCSI 초기자 속성](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)
3. 이 문자열을 저장합니다.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx






<!--HONumber=Nov16_HO3-->


