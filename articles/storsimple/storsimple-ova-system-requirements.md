---
title: StorSimple 가상 배열 시스템 요구 사항
description: StorSimple 가상 배열의 소프트웨어 및 네트워킹 요구 사항에 대해 알아봅니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/14/2016
ms.author: alkohli

---
# StorSimple 가상 배열 시스템 요구 사항
## 개요
이 문서는 Microsoft Azure StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치 또는 StorSimple 가상 장치라고도 함) 및 배열에 액세스하는 저장소 클라이언트에 대한 주요 시스템 요구 사항을 설명합니다. StorSimple 시스템을 배포하기 전에 정보를 신중하게 검토하고 배포 및 후속 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

시스템 요구 사항에는 다음 내용이 포함됩니다.

* **저장소 클라이언트에 대한 소프트웨어 요구 사항** - 지원되는 가상화 플랫폼, 웹 브라우저, iSCSI 초기자, SMB 클라이언트, 최소 가상 장치 요구 사항, 운영 체제에 대한 추가적인 요구 사항을 설명합니다.
* **StorSimple 장치에 대한 네트워킹 요구 사항** - iSCSI, 클라우드 또는 관리 트래픽을 허용하도록 방화벽에서 열려야 하는 포트에 대한 정보를 제공합니다.

이 문서에 게시된 StorSimple 시스템 요구 사항 정보는 StorSimple 가상 배열에만 적용됩니다.

* 8000 시리즈 장치는 [System requirements for your StorSimple 8000 series device](storsimple-system-requirements.md)(StorSimple 8000 시리즈 장치에 대한 시스템 요구 사항)을 참조하세요.
* 7000 시리즈 장치는 [System requirements for your StorSimple 5000-7000 series device](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)(StorSimple 5000-7000 시리즈 장치에 대한 시스템 요구 사항)을 참조하세요.

## 소프트웨어 요구 사항
소프트웨어 요구 사항에는 지원되는 웹 브라우저, SMB 버전, 가상화 플랫폼, 최소 가상 장치 요구 사항에 대한 정보가 포함됩니다.

### 지원되는 가상화 플랫폼
| **하이퍼바이저** | **버전** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 이상 |
| VMware ESXi |5\.5 이상 |

### 가상 장치 요구 사항
| **구성 요소** | **요구 사항** |
| --- | --- |
| 최소 가상 프로세서(코어) 수 |4 |
| 최소 메모리(RAM) |8GB |
| 디스크 공간<sup>1</sup> |OS 디스크 - 80GB <br></br>데이터 디스크 - 500GB~8TB |
| 최소 네트워크 인터페이스 수 |1 |
| 최소 인터넷 대역폭<sup>2</sup> |5Mbps |

<sup>1</sup> - 씬 프로비전됨

<sup>2</sup> - 네트워크 요구 사항은 일일 데이터 변경률에 따라 달라질 수 있습니다. 예를 들어, 장치에서 하루 동안 10GB 이상을 백업해야 한다면, 5Mbps 연결을 통한 매일 백업에는 최대 4.25 시간이 소요될 수 있습니다(데이터를 압축하거나 중복 제거할 수 없는 경우).

### 지원되는 웹 브라우저
| **구성 요소** | **버전** | **추가 요구 사항/메모** |
| --- | --- | --- |
| Microsoft Edge |최신 버전 | |
| Internet Explorer |최신 버전 |Internet Explorer 11로 테스트함 |
| Google Chrome |최신 버전 |Chrome 46으로 테스트함 |

### 지원되는 저장소 클라이언트
다음 소프트웨어 요구 사항은 StorSimple 가상 배열(iSCSI 서버로 구성됨)에 액세스하는 iSCSI 초기자에 대한 것입니다.

| **지원되는 운영 체제** | **필요한 버전** | **추가 요구 사항/메모** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple은 씬 프로비전된 볼륨과 완전히 프로비전된 볼륨을 만들 수 있습니다. 부분적으로 프로비전된 볼륨은 만들 수 없습니다. StorSimple iSCSI 볼륨은 다음에 대해서만 지원합니다: <ul><li>Windows 기본 디스크의 단순 볼륨.</li><li>볼륨 포맷을 위한 Windows NTFS.</li> |

다음 소프트웨어 요구 사항은 StorSimple 가상 배열(파일 서버로 구성됨)에 액세스하는 SMB 클리이언트에 대한 것입니다.

| **SMB 버전** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

## 네트워킹 요구 사항
다음 테이블에는 iSCSI, SMB, 클라우드 또는 관리 트래픽을 고려하여 방화벽에서 열려야 하는 포트가 나열되어 있습니다. 이 테이블에서 *인* 또는 *인바운드*는 장치에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. *아웃* 또는 *아웃바운드*는 배포 후 데이터를 외부로 보내는 StorSimple 장치에서 방향을 참조합니다.

| **포트 번호<sup>1</sup>** | **인 또는 아웃** | **포트 범위** | **필수** | **참고 사항** |
| --- | --- | --- | --- | --- |
| TCP 80(HTTP) |아웃 |WAN |아니요 |업데이트 복구를 위한 인터넷 액세스에는 아웃바운드 포트가 사용됩니다. <br></br>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다. |
| TCP 443(HTTPS) |아웃 |WAN |예 |아웃바운드 포트는 클라우드의 데이터에 액세스하는 데 사용됩니다. <br></br>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다. |
| UDP 53(DNS) |아웃 |WAN |일부 경우에는 메모를 참조하십시오. |이 포트는 인터넷 기반 DNS 서버로 사용하는 경우에만 필요합니다. <br></br> **참고**: 파일 서버를 배포하는 경우에는 로컬 DNS 서버를 사용하는 것이 좋습니다. |
| UDP 123(NTP) |아웃 |WAN |일부 경우에는 메모를 참조하십시오. |이 포트는 인터넷 기반 NTP 서버로 사용하는 경우에만 필요합니다.<br></br> **참고:** 파일 서버를 배포하는 경우에는 Active Directory 도메인 컨트롤러와 시간을 동기화하는 것이 좋습니다. |
| TCP 80(HTTP) |그런 다음 |LAN |예 |로컬 관리용 StorSimple 장치의 로컬 UI에 대한 인바운드 포트입니다. <br></br> **참고**: HTTP를 통해 로컬 UI에 액세스할 경우 HTTPS로 자동으로 리디렉션됩니다. |
| TCP 443(HTTPS) |그런 다음 |LAN |예 |로컬 관리용 StorSimple 장치의 로컬 UI에 대한 인바운드 포트입니다. |
| TCP 3260(iSCSI) |그런 다음 |LAN |아니요 |이 포트는 iSCSI를 통해 데이터에 액세스하는 데 사용됩니다. |

<sup>1</sup> 인바운드 포트는 공용 인터넷에서 열릴 필요가 없습니다.

> [!IMPORTANT]
> 방화벽이 StorSimple 장치 및 Azure 사이의 모든 SSL 트래픽을 수정하거나 암호를 해독하지 않도록 해야 합니다.
> 
> 

### 방화벽 규칙에 대한 URL 패턴
네트워크 관리자는 URL 패턴을 기준으로 하는 고급 방화벽 규칙이 인바운드 및 아웃바운드 트래픽을 필터링하도록 구성할 수 있습니다. 가상 배열 및 StorSimple Manager 서비스는 Azure 서비스 버스, Azure Active Directory 액세스 제어, 저장소 계정, Microsoft 업데이트 서버 등의 다른 Microsoft 응용 프로그램에 의존합니다. 이러한 응용 프로그램과 연결된 URL 패턴을 사용하여 방화벽 규칙을 구성할 수 있습니다. 이러한 응용 프로그램과 연결된 URL 패턴은 달라질 수 있습니다. 따라서 네트워크 관리자는 StorSimple에 대한 방화벽 규칙을 모니터링하고 필요에 따라 업데이트해야 합니다.

StorSimple 고정 IP 주소에 따라 대부분의 경우에서 자유롭게 아웃바운드 트래픽에 대한 방화벽 규칙을 설정하는 것이 좋습니다. 그러나 보안 환경을 만드는 데 필요한 고급 방화벽 규칙을 설정하려면 아래 정보를 사용할 수 있습니다.

> [!NOTE]
> * 장치(원본) IP는 항상 클라우드를 사용하도록 설정된 네트워크 인터페이스로 설정해야 합니다.
> * 대상 IP는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/ko-KR/download/confirmation.aspx?id=41653)로 설정해야 합니다.
> 
> 

| URL 패턴 | 구성 요소/기능 |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` |StorSimple Manager 서비스<br>액세스 제어 서비스<br>Azure 서비스 버스 |
| `http://*.backup.windowsazure.com` |장치 등록 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |인증서 해지 |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure 저장소 계정 및 모니터링 |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft 업데이트 서버<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |지원 패키지 |
| `http://*.data.microsoft.com ` |Windows의 원격 분석 서비스는 [사용자 환경 및 진단 원격 분석 업데이트](https://support.microsoft.com/ko-KR/kb/3068708)를 참조하세요. |

## 다음 단계
* [StorSimple 가상 배열 배포를 위한 포털 준비](storsimple-ova-deploy1-portal-prep.md)

<!---HONumber=AcomDC_0720_2016-->