---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 HANA 설치 방법 | Microsoft Docs
description: Azure(대규모 인스턴스)의 SAP HANA에 대한 HANA를 설치하는 방법입니다.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167650"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>HANA 큰 인스턴스에 SAP HANA 설치의 예

이 섹션에서는 HANA 큰 인스턴스 유닛에 SAP HANA를 설치하는 방법을 설명합니다. 시작 상태는 다음과 같습니다.

- SAP HANA 큰 인스턴스에 배포하기 위해 Microsoft에 모든 데이터를 제공했습니다.
- Microsoft로부터 SAP HANA 큰 인스턴스를 받았습니다.
- 온-프레미스 네트워크에 연결된 Azure VNet을 만들었습니다.
- HANA 큰 인스턴스용 ExpressRoute 회로를 동일한 Azure VNet에 연결했습니다.
- HANA 큰 인스턴스의 점프 박스로 사용하는 Azure VM을 설치했습니다.
- 점프 박스에서 HANA 큰 인스턴스 유닛으로 연결할 수 있고 그 반대로도 연결할 수 있는 것을 확인했습니다.
- 필요한 패키지와 패치가 모두 설치되어 있는지 확인했습니다.
- 사용 중인 OS에 HANA 설치에 관한 SAP 메모 및 문서를 읽었고 선택한 HANA 릴리스가 OS 릴리스에서 지원되는지 확인했습니다.

다음 순서로 표시되는 내용은 점프 박스 VM(이 경우 Windows OS에서 실행 중인)에 HANA 설치 패키지를 다운로드하고, HANA 큰 인스턴스 유닛에 패키지를 복사하고 설치하는 시퀀스입니다.

## <a name="download-of-the-sap-hana-installation-bits"></a>SAP HANA 설치 비트 다운로드
HANA 큰 인스턴스 장치는 인터넷에 직접 연결되어 있지 않기 때문에 설치 패키지를 SAP에서 HANA 큰 인스턴스 VM으로 직접 다운로드할 수 없습니다. 인터넷에 직접 연결되지 않는 상황을 해결하려면 점프 박스가 필요합니다. 패키지를 점프 박스 VM에 다운로드합니다.

HANA 설치 패키지를 다운로드하려면 SAP Marketplace에 액세스할 수 있는 SAP S-user 또는 다른 사용자가 필요합니다. 로그인한 후 화면의 순서에 따라 이동합니다.

[SAP Service Marketplace](https://support.sap.com/en/index.html)로 이동하여 > Download Software(소프트웨어 다운로드) > Installations and Upgrade(설치 및 업그레이드) >By Alphabetical Index(알파벳 순서에 따라)를 클릭하고 H – SAP HANA Platform Edition > SAP HANA Platform Edition 2.0 > Installation(설치) 아래에서 다음 파일을 다운로드합니다.

![HANA 설치 다운로드](./media/hana-installation/image16_download_hana.PNG)

데모에서는 SAP HANA 2.0 설치 패키지를 다운로드했습니다. Azure 점프 박스 VM에서 자동 압축 해제 아카이브를 디렉터리에 추출합니다(아래 그림 참조).

![HANA 설치 추출](./media/hana-installation/image17_extract_hana.PNG)

아카이브가 추출되면 추출을 통해 생성된 디렉터리(위의 경우 51052030)를 HANA 큰 인스턴스 유닛에서 /hana/shared 볼륨으로 작성한 디렉터리로 복사합니다.

> [!Important]
> 공간에 제한되어 있고 다른 프로세스에서도 사용해야 하므로 설치 패키지를 루트 또는 부팅 LUN에 복사하지 마세요.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>HANA 큰 인스턴스 유닛에 SAP HANA 설치
SAP HANA를 설치하려면 root 사용자로 로그인해야 합니다. root만 SAP HANA를 설치할 수 있는 권한이 있습니다.
먼저 해야 할 일은 /hana/shared에 복사한 디렉터리에 대한 권한을 설정하는 것입니다. 권한은 다음과 같이 설정해야 합니다.

```
chmod –R 744 <Installation bits folder>
```

그래픽 설치를 사용하여 SAP HANA를 설치하려면 gtk2 패키지가 HANA 큰 인스턴스에 설치되어 있어야 합니다. 명령을 사용하여 설치되었는지 확인합니다.

```
rpm –qa | grep gtk2
```

이후 단계에서는 그래픽 사용자 인터페이스를 사용하여 SAP HANA 설치를 보여줍니다. 다음 단계로 설치 디렉터리로 이동하여 하위 디렉터리인 HDB_LCM_LINUX_X86_64로 이동합니다. 시작

```
./hdblcmgui 
```
해당 디렉터리에서. 이제 설치를 위해 데이터를 제공해야 하는 일련의 화면이 설명되어 있습니다. 데모에서는 SAP HANA 데이터베이스 서버 및 SAP HANA 클라이언트 구성 요소를 설치합니다. 따라서 아래와 같이 'SAP HANA Database'(SAP HANA 데이터베이스)가 선택됩니다.

![설치 시 HANA 선택](./media/hana-installation/image18_hana_selection.PNG)

다음 화면에서는 'Install New System'(새 시스템 설치) 옵션을 선택합니다.

![HANA 신규 설치 선택](./media/hana-installation/image19_select_new.PNG)

이 단계 후에는 SAP HANA 데이터베이스 서버에 추가로 설치할 수 있는 몇 가지 추가 구성 중 하나를 선택해야 합니다.

![추가 HANA 구성 요소 선택](./media/hana-installation/image20_select_components.PNG)

이 문서에서는 SAP HANA Client와 SAP HANA Studio를 선택했습니다. 강화 인스턴스도 설치했습니다. 따라서 다음 화면에서 'Single-Host System'(단일 호스트 시스템)을 선택해야 합니다. 

![강화 설치 선택](./media/hana-installation/image21_single_host.PNG)

다음 화면에서 몇 가지 데이터를 제공해야 합니다.

![SAP HANA SID 제공](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> HANA SID(System ID)로 HANA 큰 인스턴스 배포를 주문할 때 Microsoft에 제공한 것과 동일한 SID를 제공해야 합니다. 다른 SID를 선택하면 다른 볼륨의 액세스 권한 문제로 인해 설치가 실패합니다.

설치 디렉터리로 /hana/shared 디렉터리를 사용합니다. 다음 단계에서는 HANA 데이터 파일과 HANA 로그 파일의 위치를 제공해야 합니다.


![HANA 로그 위치 제공](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> 데이터 및 로그 파일로 이 화면의 이전 화면에서 선택한 SID가 포함된 탑재 지점과 함께 제공된 볼륨을 정의해야 합니다. SID가 앞의 화면에서 입력한 내용과 일치하지 않으면 뒤로 돌아가서 탑재 지점에 있는 값으로 SID를 조정합니다.

다음 단계에서 호스트 이름을 검토하고 수정합니다. 

![호스트 이름 검토](./media/hana-installation/image24_review_host_name.PNG)

다음 단계에서는 HANA 큰 인스턴스 배포를 주문할 때 Microsoft에 제공한 데이터를 검색해야 합니다. 

![시스템 사용자 UID 및 GID 제공](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> 유닛 배포를 주문할 때 Microsoft에 제공한 것과 동일한 사용자 ID 및 사용자 그룹 ID를 제공해야 합니다. 동일한 ID를 제공하지 못하면 HANA 큰 인스턴스 유닛에 SAP HANA를 설치할 수 없습니다.

다음 두 화면에서는 이 문서에는 없지만 SAP HANA 데이터베이스 인스턴스의 일부로 설치되는 SAP Host Agent에 사용되는 SAP HANA 데이터베이스의 SYSTEM 사용자에 대한 암호와 sapadm 사용자에 대한 암호를 제공해야 합니다.

암호를 정의한 후 확인 화면이 표시됩니다. 나열된 모든 데이터를 확인하고 설치를 계속합니다. 아래와 같이 설치 진행률을 보여주는 진행률 화면이 표시됩니다.

![설치 진행률 확인](./media/hana-installation/image27_show_progress.PNG)

설치를 마치면 다음과 같은 그림이 표시됩니다.

![설치를 마쳤습니다.](./media/hana-installation/image28_install_finished.PNG)

이 시점에서 SAP HANA 인스턴스가 실행되어 사용할 준비가 되어야 합니다. SAP HANA Studio에서 연결할 수 있어야 합니다. 또한 SAP HANA의 최신 패치를 확인하고 해당 패치를 적용해야 합니다.


**다음 단계**

- [Azure의 SAP HANA 대규모 인스턴스 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조하세요.

