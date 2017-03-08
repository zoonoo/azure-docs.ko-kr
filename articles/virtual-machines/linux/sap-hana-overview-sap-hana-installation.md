---
title: "Azure(큰 인스턴스)에서 SAP HANA 설치 | Microsoft Docs"
description: "Azure(큰 인스턴스)에서 SAP HANA를 설치하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 0d998b5347a9da6dd209b0ba106792ab5c34164d
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Azure(큰 인스턴스)에서 SAP HANA를 설치하고 구성하는 방법

SAP HANA 설치를 사용자가 진행하며, Azure(큰 인스턴스) 서버에 새 SAP HANA가 제공된 직후에 이 작업을 수행할 수 있습니다. SAP 정책에 따라 SAP HANA의 설치는 SAP HANA 설치 인증 시험인 Certified SAP Technology Associate를 통과한 인증된 SAP HANA 설치 관리자 또는 SAP 인증 SI(시스템 통합업체)가 수행해야 합니다.

SAP HANA(서버 쪽) 및 SAP HANA(클라이언트 쪽)와 관련해서 중요한 특정 연결 고려 사항이 있습니다. 대부분의 경우 SAP HANA 서버는 클라이언트에 IP 주소를 보냅니다. 그러면 클라이언트에서 캐시된 후 후속 연결 시도에 사용됩니다. Azure(큰 인스턴스)의 SAP HANA는 테넌트 네트워크에서 사용되는 내부 서버 IP 주소를 지정된 Azure Vnet에 대해 제공된 IP 주소로 NAT하므로 기본적으로 SAP HANA 데이터베이스 서버는 &quot;내부&quot; IP 주소 범위를 전송합니다. 예를 들어 호스트 이름 확인을 위해, SAP HANA에서 NAT된 IP 주소를 제공하지 않고, 캐시된 내부 IP 주소가 사용됩니다. 따라서 SAP HANA 클라이언트를 사용하는 응용 프로그램(ODBC, JDBC 등)은 이 IP 주소로 연결할 수 없습니다. SAP HANA 서버에 NAT된 IT 주소를 클라이언트로 전파하도록 지시하려면 SAP HANA 글로벌 시스템 구성 파일(global.ini)을 편집해야 합니다.

global.ini에 다음을 추가합니다(직접 또는 SAP HANA Studio를 통해).
```
[public\_hostname\_resolution]
use\_default\_route = no
map\_<hostname of SAP HANA on Azure (Large Instances) tenant> = <NAT IP Address (IP address that can be accessed in Azure)>
```
자세한 내용 및 예제에 대해서는 [SAP HANA 관리 가이드](http://help.sap.com/hana/sap_hana_administration_guide_en.pdf)의 _데이터베이스 클라이언트 액세스에 대한 호스트 이름 매핑_ 섹션을 참조하세요.

클라이언트 쪽(Azure의 SAP 응용 프로그램 서버)에서 /etc/hosts 파일(Linux) 또는 /system32/drivers/etc/hosts(Windows Server)를 편집하고 Azure(큰 인스턴스) 테넌트 및 NAT된 해당 IP 주소에 SAP HANA의 호스트 이름에 대한 항목을 포함합니다.

>[!NOTE] 
>SAP HANA Database Client를 설치하는 동안 Azure(큰 인스턴스)에서 SAP HANA에 연결할 때 오류가 발생하면 호스트 이름 대신, 암시적으로 ANA 큰 인스턴스 테넌트의 NAT IP 주소를 사용합니다.

## <a name="storage"></a>저장소

Azure(큰 인스턴스)에서 SAP HANA에 대한 저장소 레이아웃은 SAP 권장 모범 사례를 통해 Azure Service Management의 SAP HANA에 의해 구성됩니다. [SAP HANA 저장소 요구 사항](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 백서를 참조하세요.

여러 HANA 큰 인스턴스용 저장소의 정확한 구성은 다음과 같습니다.

| 메모리 크기 | HANA/데이터 | HANA/로그 | HANA/공유 | HANA/로그/백업 |
| --- | --- | --- | --- | --- |
| 768GB | 1280GB | 512GB | 768GB | 512GB |
| 1536GB | 3456GB | 768GB | 1024GB | 768GB |
| 3072GB | 7680GB | 1536GB | 1024GB | 1536GB |

또한 고객은 1TB 단위로 추가 저장소 용량을 구입할 수 있습니다. 이 추가 저장소는 HANA 큰 인스턴스에 새 볼륨으로 추가될 수 있습니다.

큰 인스턴스 스탬프의 저장소 컨트롤러와 노드는 NTP 서버와 동기화됩니다. Azure(큰 인스턴스) 장치의 SAP HANA와 Azure VM을 NTP 서버에 대해 동기화할 경우 Azure 또는 큰 인스턴스 스탬프에서 인프라 및 계산 장치 간에 중대한 시간 변동이 발생하지 않게 됩니다.

## <a name="operating-system"></a>운영 체제

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana)는 Azure(큰 인스턴스)에서 SAP HANA에 대해 설치되는 Linux 배포판입니다. 이 특정 배포판은 SAP 관련 기능을 &quot;바로 사용할 수 있게&quot; 제공합니다(SLES에서 SAP을 효과적으로 실행하기 위해 미리 설정된 매개 변수 포함).

SLES의 SAP HANA 배포와 관련된 몇 가지 유용한 리소스(고가용성 설정, SAP 작업과 관련된 보안 강화 등)에 대해서는 SUSE 웹 사이트의 [리소스 라이브러리/백서](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) 및 SCN(SAP Community Network)의 [SUSE의 SAP](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)을 참조하세요.

유용한 추가 SLES 관련 링크:

- [SUSE Linux의 SAP HANA 사이트](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP 모범 사례: 복제 대기 – SUSE Linux Enterprise 12의 SAP NetWeaver](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP - SAP에 대한 SLES 바이러스 보호](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)(SLES 12 for SAP Applications 포함)

SLES 12 SP1의 SAP HANA 구현에 적용할 수 있는 SAP Support Notes:

- [SAP Support Note #1944799 – SLES 운영 체제 설치에 대한 SAP HANA 지침](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP Support Note #2205917 – SLES 12 for SAP Applications를 위한 SAP HANA DB 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12: 설치 참고](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP Support Note #171356 – Linux의 SAP 소프트웨어: 일반 정보](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP Support Note #1391070 – Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)

## <a name="time-synchronization"></a>시간 동기화

SAP은 SAP 시스템을 구성하는 다양한 구성 요소의 시간 차이에 매우 민감합니다. 오랫 동안 SAP(Basis)으로 작업해왔다면 오류 제목 ZDATE\_LARGE\_TIME\_DIFF를 갖는 SAP ABAP 짧은 덤프가 친숙할 것입니다. 이러한 짧은 덤프는 다른 서버 또는 VM의 시스템 시간이 너무 큰 차이를 보일 때 나타나기 때문입니다.

Azure(큰 인스턴스)에 있는 SAP HANA의 경우 Azure에서 수행되는 시간 동기화가 큰 인스턴스 스탬프의 계산 장치에 적용되지 않습니다. Azure는 시스템 시간이 제대로 동기화되도록 하므로 Azure(VM)에서 SAP 응용 프로그램을 실행하는 경우는 해당되지 않습니다. 결과적으로 Azure VM에서 실행되는 SAP 응용 프로그램 서버와 HANA 큰 인스턴스에서 실행되는 SAP HANA 데이터베이스 인스턴스에서 사용할 수 있는 별도의 시간 서버를 설정해야 합니다. 큰 인스턴스 스탬프의 저장소 인프라는 NTP 서버와 시간 동기화됩니다.



