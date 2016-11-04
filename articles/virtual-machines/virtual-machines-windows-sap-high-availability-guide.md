---
title: Windows VM(가상 컴퓨터)의 SAP NetWeaver - 고가용성 가이드 | Microsoft Docs
description: Windows VM(가상 컴퓨터)의 SAP NetWeaver에 대한 고가용성 가이드
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''

ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/18/2016
ms.author: goraco

---
# <a name="sap-netweaver-on-windows-virtual-machines-vms-highavailability-guide"></a>Windows VM(가상 컴퓨터)의 SAP NetWeaver - 고가용성 가이드
[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md(Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드)
[dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f(VM 및 VHD용 캐싱)
[dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91(소프트웨어 RAID)
[dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152(Microsoft Azure Storage)
[dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64(RDBMS 배포의 구조)
[dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3(Azure VM에서 고가용성 및 재해 복구)
[dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268(SQL Server 2012 SP1 CU4 이상)
[dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b(SQL Server 2012 SP1 CU3 및 이전 버전)
[dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8(Microsoft Azure Marketplace에서 SQL Server 이미지 사용)
[dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30(Azure의 SAP용 일반 SQL Server 요약)
[dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737(SQL Server RDBMS에 대한 고유 정보)
[dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573(Storage 구성)
[dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d(백업 및 복원)
[dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c(백업 및 복원에 대한 성능 고려 사항)

[dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818(기타)
[dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드)
[deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94(SAP 리소스)
[deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab(사용자 지정 이미지를 사용하여 VM 배포)
[deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281(시나리오 1: SAP용 Azure 마켓플레이스에서 VM 배포)
[deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2(시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포)
[deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1(시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동)
[deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e(Microsoft Azure에서의 SAP용 VM 배포 시나리오)
[deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7(Azure PowerShell cmdlet 배포)
[deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e(SAP 관련 PowerShell cmdlet 다운로드 및 가져오기)
[deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc(온-프레미스 도메인에 VM 가입 - Windows에만 해당)

[deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542(Linux)
[deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d(Azure VM 에이전트 다운로드, 설치 및 사용)
[deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4(Azure PowerShell)
[deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f(Azure CLI)
[deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca(SAP용 Azure 고급 모니터링 확장 구성)
[deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2(SAP용 Azure 고급 모니터링에 대한 준비 검사)
[deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1(Azure 모니터링 인프라 구성에 대한 상태 검사)
[deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8(SAP용 Azure 모니터링 인프라의 추가 문제 해결)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md(Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드)

[planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff(리소스)
[planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058(Azure 가상 컴퓨터에서 실행되는 SAP NetWeaver의 HA(고가용성) 및 DR(재해 복구))
[planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77(SAP 응용 프로그램 서버에 대한 고가용성)
[planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f(SAP 인스턴스에 대해 자동 시작 사용)
[planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803(클라우드 전용 - 온-프레미스 고객 네트워크에 의존하지 않고 Azure에 가상 컴퓨터 배포)
[planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10(프레미스 간 - 온-프레미스 네트워크에 요구 사항을 완전히 통합하고 Azure에 단일 또는 다중 SAP VM 배포)
[planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a(Azure 지역)
[planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358(장애 도메인)
[planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560(업그레이드 도메인)
[planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665(Azure 가용성 집합)
[planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77(Microsoft Azure Virtual Machines 개념)
[planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92(Azure 프리미엄 Storage)
[planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53(일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동)
[planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c(고객별 이미지를 사용하여 VM 배포)
[planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef(일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로의 VM 이동 준비)
[planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3(SAP용 고객별 이미지를 사용하여 VM 배포 준비)
[planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7(Azure용 SAP로 VM 준비)
[planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79(Azure 디스크와 Azure 이미지 간 차이점)
[planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a(온-프레미스에서 Azure로 VHD 업로드)
[planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1(Azure Storage 계정 간 디스크 복사)
[planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646(SAP 배포를 위한 VM/VHD 구조)
[planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d(연결된 디스크에 대한 자동 탑재 설정)
[planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30(SAP NetWeaver 데모/학습 시나리오가 있는 단일 VM)
[planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14(SAP 인스턴스의 클라우드 전용 배포 개념)
[planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3(SAP용 Azure 모니터링 솔루션)
[planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92(Azure 프리미엄 Storage)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[sap-ha-guide]:virtual-machines-windows-sap-high-availability-guide.md (High-availability SAP NetWeaver on Windows virtual machines)
[sap-ha-guide-1]:virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c (Prerequisites)
[sap-ha-guide-2]:virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 (Resources)
[sap-ha-guide-3]:virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 (High-availability SAP with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-3.1]:virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 (Resource groups)
[sap-ha-guide-3.2]:virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 (Clustering with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-4]:virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 (Windows Server Failover Clustering)
[sap-ha-guide-4.1]:virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 (Quorum modes)
[sap-ha-guide-5]:virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 (Windows Server Failover Clustering on-premises)
[sap-ha-guide-5.1]:virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 (Shared storage)
[sap-ha-guide-5.2]:virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd (Networking and name resolution)
[sap-ha-guide-6]:virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a (Windows Server Failover Clustering in Azure)
[sap-ha-guide-6.1]:virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 (Shared disk in Azure with SIOS DataKeeper)
[sap-ha-guide-6.2]:virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb (Name resolution in Azure)
[sap-ha-guide-7]:virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa(Azure IaaS의 SAP NetWeaver 고가용성)
[sap-ha-guide-7.1]:virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e(고가용성 SAP 응용 프로그램 서버)
[sap-ha-guide-7.2]:virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db(고가용성 SAP ASCS/SCS 인스턴스)
[sap-ha-guide-7.2.1]:virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51(Azure에서 Windows Server 장애 조치 클러스터링을 사용한 고가용성 SAP ASCS/SCS 인스턴스)
[sap-ha-guide-7.3]:virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027(고가용성 DBMS 인스턴스)
[sap-ha-guide-7.4]:virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816(종단간 고가용성 배포 시나리오)
[sap-ha-guide-8]:virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf(인프라 준비)
[sap-ha-guide-8.1]:virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489(프로덕션에서 사용하기 위해 회사 네트워크 연결(크로스-프로미스)을 사용하여 가상 시스템 배포)
[sap-ha-guide-8.2]:virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310(테스트/데모용 SAP 인스턴스의 클라우드 전용 배포)

[sap-ha-guide-8.3]:virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a (Azure 가상 네트워크)
[sap-ha-guide-8.4]:virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e(DNS IP 주소)
[sap-ha-guide-8.5]:virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f(SAP ASCS/SCS 클러스터형 인스턴스 및 DBMS 클러스터형 인스턴스의 호스트 이름 및 고정 IP 주소)
[sap-ha-guide-8.6]:virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30(SAP 가상 컴퓨터에 대한 고정 IP 주소 설정)
[sap-ha-guide-8.7]:virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e(내부 부하 분산 장치의 정적 IP 주소 만들기)
[sap-ha-guide-8.8]:virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c(Azure 내부 부하 분산 장치에 대한 기본 ASCS/SCS 부하 분산 규칙)
[sap-ha-guide-8.9]:virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716(Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경)
[sap-ha-guide-8.10]:virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c(도메인에 Windows 컴퓨터 추가)
[sap-ha-guide-8.11]:virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158(SAP ASCS/SCS 인스턴스의 클러스터 노드 둘 다에 대한 레지스트리 항목 추가)
[sap-ha-guide-8.12]:virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab(SAP ASCS/SCS 인스턴스에 대한 Windows Server 장애 조치 클러스터 설정)
[sap-ha-guide-8.12.1]:virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79(클러스터 구성에서 클러스터 노드 수집)
[sap-ha-guide-8.12.2]:virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca(클러스터 파일 공유 감시 구성)
[sap-ha-guide-8.12.2.1]:virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855(파일 공유 만들기)
[sap-ha-guide-8.12.2.2]:virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d(장애 조치 클러스터 관리자에서 파일 공유 감시 쿼럼 설정)
[sap-ha-guide-8.12.3]:virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868(SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치)
[sap-ha-guide-8.12.3.1]:virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3(.NET Framework 3.5 추가)
[sap-ha-guide-8.12.3.2]:virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102(SIOS DataKeeper 설치)
[sap-ha-guide-8.12.3.3]:virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006(SIOS DataKeeper 설정)
[sap-ha-guide-9]:virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b(SAP NetWeaver 시스템 설정)
[sap-ha-guide-9.1]:virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170(고가용성 ASCS/SCS 인스턴스를 포함하는 SAP 설치)
[sap-ha-guide-9.1.1]:virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097(클러스터형 SAP ASCS/SCS에 대한 가상 호스트 이름 만들기)
[sap-ha-guide-9.1.2]:virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0(SAP 첫 번째 클러스터 노드 설치)
[sap-ha-guide-9.1.3]:virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556(ASCS/SCS 인스턴스의 SAP 프로필 수정)
[sap-ha-guide-9.1.4]:virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761(프로브 포트 추가)
[sap-ha-guide-9.2]:virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7(데이터베이스 인스턴스 설치)
[sap-ha-guide-9.3]:virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748(두 번째 클러스터 노드 설치)
[sap-ha-guide-9.4]:virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a(SAP ERS 인스턴스의 Windows 서비스 시작 유형 변경)
[sap-ha-guide-9.5]:virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5(SAP 기본 응용 프로그램 서버 설치)
[sap-ha-guide-9.6]:virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772(SAP 추가 응용 프로그램 서버 설치)
[sap-ha-guide-10]:virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9(SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 복제 테스트)
[sap-ha-guide-10.1]:virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445(SAP ASCS/SCS 인스턴스가 클러스터 노드 A에서 실행되고 있음)
[sap-ha-guide-10.2]:virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916(노드 A에서 노드 B로의 장애 조치)
[sap-ha-guide-10.3]:virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5(SAP ASCS/SCS 인스턴스가 클러스터 노드 B에서 실행되고 있음))

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png


[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md


Azure Virtual Machines는 긴 조달 주기 없이 최소한의 시간 안에 계산, Storage 및 네트워크 리소스를 필요로 하는 조직을 위한 솔루션입니다. Azure Virtual Machines를 사용하여 SAP NetWeaver 기반 ABAP, Java 및 ABAP+Java 스택과 같은 기존 응용 프로그램을 배포할 수 있습니다. 추가 온-프레미스 리소스 없이도 안정성과 가용성을 확장할 수 있습니다. 또한 Azure Virtual Machines는 크로스-프레미스 연결을 지원하므로 조직에서는 Azure Virtual Machines를 온-프레미스 도메인, 사설 클라우드 및 SAP 시스템 지형에 통합할 수 있습니다.

이 문서에서는 새로운 Azure Resource Manager 배포 모델을 사용하여 Azure에서 고가용성 SAP 시스템을 배포하기 위한 단계를 설명합니다. 다음과 같은 주요 작업을 진행하게 됩니다.

* [리소스][sap-ha-guide-2] 섹션에서 적절한 SAP 설치 가이드 및 Note를 찾아보세요. 이 문서는 특정 플랫폼에 SAP 소프트웨어를 설치 및 배포하는 데 도움이 되는 기본 리소스인 SAP 설치 설명서 및 SAP Note를 보완합니다.
* Azure 클래식 배포 모델 및 Azure Resource Manager 배포 모델 간 차이를 알아보세요.
* Windows Server 장애 조치 클러스터링 쿼럼 모드에 대해 자세히 알아보고 사용 중인 Azure 배포에 적합한 모델을 선택할 수 있습니다.
* Azure 서비스의 Windows Server 장애 조치 클러스터링 공유 Storage에 대해 자세히 알아보세요.
* Azure의 단일 실패 지점 구성 요소(예: ASCS(ABAP SAP 중앙 서비스)/SCS(SAP 중앙 서비스) 및 DBMS(데이터베이스 관리 시스템)) 및 중복 구성 요소(예: SAP 응용 프로그램 서버)를 보호하는 방법을 알아보세요.
* Azure Resource Manager에서 Azure를 플랫폼으로 사용하여 Windows Server 장애 조치 클러스터링 클러스터에서 고가용성 SAP 시스템을 설치하고 구성하는 단계별 작업 예제를 따르세요.
* Azure에서 Windows Server 장애 조치 클러스터링을 사용하는 데 필요한 추가 단계도 알아봅니다. 이러한 단계는 온-프레미스 배포에는 필요하지 않습니다.

이 문서에서는 배포 및 구성을 단순화하기 위해 새로운 SAP 3계층 고가용성 Resource Manager 템플릿을 사용합니다. 이 템플릿은 고가용성 SAP 시스템에 필요한 전체 인프라의 배포를 자동화합니다. 또한 이러한 인프라는 SAP 시스템의 SAPS 크기 조정도 지원합니다.

[!INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="a-name217c547955954cd8870d15ab00d4f84ca-prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> 필수 조건
시작하기 전에 다음 섹션에 설명된 필수 조건을 충족하는지 확인하세요. 또한 [리소스][sap-ha-guide-2] 섹션에 나열된 모든 리소스를 확인해야 합니다.

이 문서에서는 [3계층 SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/)에 대한 Azure Resource Manager 템플릿을 사용합니다. 유용한 템플릿 개요를 보려면 [SAP Azure Resource Manager 템플릿](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)을 참조하세요.

## <a name="a-name42b8f6007ba34606b8a553c4f026da08a-resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> 리소스
또한 이 가이드에서는 Azure의 SAP 배포에 대해 설명합니다.

* [Windows VM(가상 컴퓨터)의 SAP NetWeaver - 계획 및 구현 가이드][planning-guide]
* [Windows VM(가상 컴퓨터)의 SAP NetWeaver - 배포 가이드][deployment-guide]
* [Windows VM(가상 컴퓨터)의 SAP NetWeaver - DBMS 배포 가이드][dbms-guide]
* [Windows VM(가상 컴퓨터)의 SAP NetWeaver - 고가용성 가이드[이 가이드]][sap-ha-guide]

> [!NOTE]
> 가능한 경우 SAP 설치 가이드에 대한 링크가 제공됩니다([SAP 설치 가이드][sap-installation-guides] 참조). 설치 프로세스의 필수 조건 및 자세한 내용을 보려면 SAP NetWeaver 설치 가이드를 자세히 읽어 보는 것이 좋습니다. 이 문서에서는 Azure Virtual Machines와 함께 사용할 수 있는 SAP NetWeaver 기반 시스템에 대한 특정 작업만 다룹니다.
> 
> 

이러한 SAP Note는 Azure의 SAP 항목과 관련이 있습니다.

| Note 번호 | 제목 |
| --- | --- |
| [1928533] |Azure의 SAP 응용 프로그램: 지원 제품 및 크기 조정 |
| [2015553] |Microsoft Azure의 SAP: 지원 필수 조건 |
| [1999351] |향상된 SAP용 Azure 모니터링 |
| [2178632] |Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [1999351] |Windows에서의 가상화: 향상된 모니터링 |

일반적인 기본 제한 및 최대 제한 사항을 포함하여 [Azure 구독 제한][azure-subscription-service-limits-subscription]에 대해 자세히 알아보세요.

## <a name="a-name42156640c601cf45a9b2254baa678b24f1ahighavailability-sap-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Azure Resource Manager 및 클래식 배포 모델의 고가용성 SAP
Azure Resource Manager 및 클래식 배포 모델은 다음과 같은 두 가지 차원에서 차이가 있습니다.

* 리소스 그룹
* 클러스터링 요구 사항

### <a name="a-namef76af27319934d83b12d65deeae23686a-resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> 리소스 그룹
Azure Resource Manager에서 리소스 그룹을 사용하여 Azure 구독에서 모든 응용 프로그램 리소스를 관리할 수 있습니다. 통합 접근 방식의 리소스 그룹에서는 모든 리소스가 동일한 수명 주기를 갖습니다. 예를 들어 모든 리소스가 동시에 생성되고 동시에 삭제됩니다. [리소스 그룹](../resource-group-overview.md#resource-groups)에 대해 자세히 알아볼 수 있습니다.

### <a name="a-name3e85fbe084b1489287afd9b65ff91860a-clustering-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Azure Resource Manager 및 클래식 배포 모델의 클러스터링
Azure Resource Manager 모델에서는 Azure 내부 부하 분산 기능을 사용하여 고가용성을 구현하기 위해 클라우드 서비스가 필요하지 않습니다.

Azure 클래식 모델을 사용하려면 [SAP NetWeaver in Azure: Clustering SAP ASCS/SCS instances by using Windows Server Failover Clustering in Azure with SIOS DataKeeper(Azure의 SAP NetWeaver - SIOS Datakeeper를 통해 Azure에서 Windows Server 장애 조치 클러스터를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링)](http://go.microsoft.com/fwlink/?LinkId=613056)에 설명된 절차를 따라야 합니다.

> [!NOTE]
> SAP 설치를 위해서는 Azure Resource Manager 배포 모델을 사용하는 것이 좋습니다. 이 모델은 클래식 배포 모델에서 사용할 수 없는 다양한 이점을 제공합니다. [배포 모델][virtual-machines-azure-resource-manager-architecture-benefits-arm]에서 Azure에 대한 자세한 내용을 볼 수 있습니다.   
> 
> 

## <a name="a-name8ecf3ba067c044959c14feec1a2255b7a-windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server 장애 조치 클러스터링
Windows Server 장애 조치 클러스터링은 Windows에서 고가용성 SAP ASCS/SCS를 설치하고 DBMS를 사용하기 위한 기반이 됩니다.

장애 조치 클러스터는 함께 작동하여 응용 프로그램 및 서비스의 가용성을 높이는 1+n개 독립 서버(노드) 그룹입니다. 노드에 장애가 발생하는 경우 Windows Server 장애 조치 클러스터링은 발생할 수 있는 장애 횟수를 계산하고 정의된 응용 프로그램 및 서비스를 제공하기 위해 정상 클러스터를 유지 관리합니다. 이를 위해 여러 다른 쿼럼 모드 중에서 선택할 수 있습니다.

### <a name="a-name1a3c5408b16846d699f54219ad1b1ff2a-quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> 쿼럼 모드
Windows Server 장애 조치 클러스터링을 사용하는 경우 다음 4개의 쿼럼 모드 중에서 선택할 수 있습니다.

* **노드 과반수**. 각 클러스터 노드에서 투표할 수 있습니다. 클러스터는 투표 수의 과반수, 즉 절반 이상일 때만 작동합니다. 노드 수가 균일하지 않은 클러스터에 대해 이 옵션을 사용하는 것이 좋습니다. 예를 들어 7 노드 클러스터 중에서 3개의 노드에서 장애가 발생해도 클러스터는 과반수 조건이 충족되므로 계속 실행됩니다.  
* **노드 및 디스크 과반수**. 사용 가능하고 통신이 설정되어 있을 때마다 각 노드와 클러스터 Storage의 지정된 디스크(디스크 감시)가 투표할 수 있습니다. 클러스터는 투표 수의 과반수, 즉 절반 이상일 때만 작동합니다. 이 모드는 노드 수가 짝수인 클러스터 환경에 적합합니다. 노드 및 디스크 절반이 온라인 상태이면 클러스터는 정상 상태를 유지합니다.
* **노드 및 파일 공유 과반수**. 사용 가능하고 통신이 설정되어 있는지에 관계 없이 각 노드와 관리자가 만든 지정된 파일 공유를 더한 크기(파일 공유 감시)가 투표할 수 있습니다. 클러스터는 투표 수의 과반수, 즉 절반 이상일 때만 작동합니다. 이 모드는 노드 수가 짝수인 클러스터 환경에 적합합니다. 이 모드는 노드 및 디스크 과반수 모드와 유사하지만 감시 디스크 대신 감시 파일 공유를 사용합니다. 이 모드는 구현하기는 쉽지만 파일 공유 자체의 가용성이 낮을 경우 단일 실패 지점이 될 수 있습니다.
* **과반수 없음: 디스크만**. 하나의 노드를 사용할 수 있고 클러스터 Storage의 특정 디스크와 통신이 설정된 경우 클러스터에는 쿼럼이 하나 있습니다. 해당 디스크와도 통신하는 노드만 클러스터에 참여할 수 있습니다. 이 모드는 사용하지 않는 것이 좋습니다.
   
  
  ## <a name="a-namefdfee8756e66483aa34314bbaee33275a-windows-server-failover-clustering-onpremises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server 장애 조치 클러스터링 온-프레미스
  그림 1의 예제에서는 두 노드 클러스터를 보여 줍니다. 노드 간 네트워크 연결이 끊어지고 두 노드는 계속 작동될 경우 쿼럼 디스크 또는 파일 공유는 클러스터의 응용 프로그램 및 서비스를 계속 제공할 노드를 확인합니다. 쿼럼 디스크 또는 파일 공유에 액세스할 수 있는 노드는 서비스가 계속되도록 하는 노드입니다.

이 예제에서는 두 노드 클러스터를 사용하므로 노드 및 파일 공유 과반수 쿼럼 모드를 사용합니다. 노드 및 디스크 과반수도 사용 가능한 옵션입니다. 프로덕션 환경에서는 쿼럼 디스크를 사용하는 것이 좋습니다. 네트워크 및 Storage 시스템 기술을 사용하여 항상 고가용성을 유지할 수 있습니다.

![그림 1: Azure에서 SAP ASCS/SCS에 대한 Windows Server 장애 조치 클러스터링 구성 예제][sap-ha-guide-figure-1000]

***그림 1:** Azure에서 SAP ASCS/SCS에 대한 Windows Server 장애 조치 클러스터링 구성 예제*

### <a name="a-namebe21cf3efb01402b995554fbecf66592a-shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> 공유 Storage
그림 1에는 2 노드 공유 Storage 클러스터도 나와 있습니다. 온-프레미스 공유 Storage 클러스터에서 클러스터의 모든 노드는 공유 Storage를 검색합니다. 잠금 메커니즘을 통해 데이터가 손상으로부터 보호됩니다. 또한 다른 노드에 장애가 발생하면 모든 노드에서 이 사실을 감지할 수 있습니다. 한 노드가 실패하면 나머지 하나가 Storage 리소스의 소유권을 가지며, 서비스의 가용성을 보장합니다.

> [!NOTE]
> SQL Server와 같은 일부 DBMS 응용 프로그램에서는 가용성을 높이기 위해 공유 디스크를 사용할 필요가 없습니다. SQL Server AlwaysOn은 한 클러스터 노드의 로컬 디스크에서 다른 클러스터 노드의 로컬 디스크로 DBMS 데이터 및 로그 파일을 복제합니다. 이 경우 Windows 클러스터 구성에는 공유 디스크가 필요하지 않습니다.
> 
> 

### <a name="a-nameff7a9a062bc54b20860a46cdb44669cda-networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> 네트워킹 및 이름 확인
클라이언트 컴퓨터는 DNS 서버에서 제공하는 가상 IP 주소 및 가상 호스트 이름을 통해 클러스터에 도달합니다. 온-프레미스 노드와 DNS 서버는 여러 개의 IP 주소를 처리할 수 있습니다.

표준 설치에서는 다음 두 가지 이상의 네트워크 연결을 사용합니다.

* Storage에 대한 전용 연결
* 하트비트에 대한 클러스터 내부 네트워크 연결
* 클라이언트에서 클러스터에 연결하는 데 사용하는 공용 네트워크

## <a name="a-name2ddba413a7f54e4e9a5187908879c10aa-windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Azure의 Windows Server 장애 조치 클러스터링
운영 체제 미설치 또는 사설 클라우드 배포에 비해, Azure Virtual Machines는 Windows Server 장애 조치 클러스터링을 구성하기 위한 추가 단계가 필요합니다. 공유 클러스터 디스크를 만들려면 SAP ASCS/SCS 인스턴스에 대해 여러 개의 IP 주소 및 가상 호스트 이름을 설정해야 합니다.

이 문서에서는 Azure에서 SAP 고가용성 중앙 서비스 클러스터를 구축할 때 필요한 핵심 개념 및 추가 단계에 대해 설명합니다. 타사 도구 SIOS DataKeeper를 설정하고 Azure 내부 부하 분산 장치를 구성하는 방법을 보여 줍니다. 이러한 도구와 Azure에서 파일 공유 감시를 사용하여 Windows 장애 조치 클러스터를 만들 수 있습니다.

![그림 2: 공유 디스크를 사용하지 않는 Azure의 Windows Server 장애 조치 클러스터링 구성][sap-ha-guide-figure-1001]

***그림 2:** 공유 디스크를 사용하지 않는 Azure의 Windows Server 장애 조치 클러스터링 구성*

### <a name="a-name1a464091922b48d79d087cecf757f341a-shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> SIOS DataKeeper를 사용한 Azure의 공유 디스크
고가용성 SAP ASCS/SCS 인스턴스를 위해서는 클러스터 공유 Storage가 필요합니다. 2016년 9월을 기준으로 Azure는 공유 Storage 클러스터를 만드는 데 사용할 수 있는 공유 Storage를 제공하지 않습니다. 타사 소프트웨어 SIOS DataKeeper Cluster Edition을 사용하여 클러스터 공유 Storage를 시뮬레이션하는 미러링된 Storage를 만들 수 있습니다. SIOS 솔루션은 실시간 동기 데이터 복제 기능을 제공합니다. 다음은 클러스터에 대한 공유 디스크 리소스를 만드는 방법입니다.

1. Windows 클러스터 구성에 있는 각 가상 컴퓨터에 추가 Azure VHD(가상 하드 디스크)를 추가합니다.
2. 두 가상 컴퓨터 노드에서 SIOS DataKeeper Cluster Edition을 실행합니다.
3. 원본 가상 컴퓨터의 추가 VHD 연결 볼륨의 콘텐츠를 대상 가상 컴퓨터의 추가 VHD 연결 볼륨에 미러링하는 방식으로 SIOS DataKeeper Cluster Edition을 구성합니다. SIOS DataKeeper는 원본 및 대상 로컬 볼륨을 추상화한 다음 Windows Server 장애 조치 클러스터링에 단일 공유 디스크로 제공합니다.

[SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/)에 대한 자세한 정보를 참조하세요.

 ![그림 3: SIOS DataKeeper를 사용하는 Azure의 Windows Server 장애 조치 클러스터링 구성][sap-ha-guide-figure-1002]

***그림 3:** SIOS DataKeeper를 사용하는 Azure의 Windows Server 장애 조치 클러스터링 구성*

> [!NOTE]
> SQL Server와 같은 일부 DBMS 제품에서는 가용성을 높이기 위해 공유 디스크를 사용할 필요가 없습니다. SQL Server AlwaysOn은 한 클러스터 노드의 로컬 디스크에서 다른 클러스터 노드의 로컬 디스크로 DBMS 데이터 및 로그 파일을 복제합니다. 이 경우 Windows 클러스터 구성에는 공유 디스크가 필요하지 않습니다.
> 
> 

### <a name="a-name44641e18a94e431f95ff303ab65e0bcba-name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Azure의 이름 확인
 Azure 클라우드 플랫폼은 부동 IP와 같은 가상 IP 주소를 구성하는 옵션을 제공하지 않습니다. 이러한 이유로 클라우드의 클러스터 리소스에 연결하도록 가상 IP 주소를 설정하기 위한 대체 솔루션이 필요합니다.
Azure의 Azure Load Balancer 서비스에는 내부 부하 분산 장치가 있습니다. 내부 부하 분산 장치를 사용하면 클라이언트는 클러스터 가상 IP 주소를 통해 클러스터에 도달합니다.
클러스터 노드를 포함하는 리소스 그룹에 부하 분산 장치를 배포해야 합니다. 그런 후 내부 부하 분산 장치의 프로브 포트를 사용하여 필요한 모든 포트 전달 규칙을 구성합니다.
클라이언트는 가상 호스트 이름을 통해 연결할 수 있습니다. DNS 서버는 클러스터 IP 주소를 확인하고 내부 부하 분산 장치는 클러스터의 활성 노드에 대한 포트 전달을 처리합니다.

## <a name="a-name2e3fec50241e441b87080b1864f66dfaa-highavailability-sap-netweaver-in-azure-infrastructureasaservice-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Azure IaaS(Infrastructure-as-a-Service)의 고가용성 SAP NetWeaver
SAP 응용 프로그램 고가용성(예: SAP 소프트웨어 구성 요소)을 달성하려면 다음 구성 요소를 보호해야 합니다. 이 내용은 [Windows VM(가상 컴퓨터)의 SAP NetWeaver - 계획 및 구현 가이드][planning-guide-11]에 자세히 설명되어 있습니다.

* SAP 응용 프로그램 서버
* SAP ASCS/SCS 인스턴스
* DBMS 서버

### <a name="a-name93faa747907e440ab00a1ae0a89b1c0ea-highavailability-sap-application-servers"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> 고가용성 SAP 응용 프로그램 서버
일반적으로 SAP 응용 프로그램 서버 및 대화 상자 인스턴스의 경우 특정 고가용성 솔루션이 필요하지 않습니다. 중복성으로 고가용성을 달성하고 다른 Azure Virtual Machines 인스턴스에서 여러 대화 상자 인스턴스를 구성합니다. 두 개의 Azure Virtual Machines 인스턴스에 2개 이상의 SAP 응용 프로그램 인스턴스가 설치되어 있어야 합니다.

![그림 4: 고가용성 SAP 응용 프로그램 서버][sap-ha-guide-figure-2000]

***그림 4:** 고가용성 SAP 응용 프로그램 서버*

SAP 응용 프로그램 서버를 호스트하는 모든 가상 컴퓨터를 동일한 Azure 가용성 집합에 배치해야 합니다. Azure 가용성 집합은 다음을 확인합니다.

* 모든 가상 컴퓨터가 동일한 업그레이드 도메인에 속하는지 여부. 예를 들어 업그레이드 도메인은 가상 컴퓨터가 계획된 유지 관리 가동 중지 시간 동안 동시에 업데이트되지 않는지 확인합니다.
* 모든 가상 컴퓨터가 동일한 장애 도메인에 속하는지 여부. 예를 들어 장애 도메인은 어떤 단일 장애 지점도 모든 가상 컴퓨터의 가용성에 영향을 주지 않도록 가상 컴퓨터가 배포되어 있는지 확인합니다.

[가상 컴퓨터의 가용성 관리][virtual-machines-manage-availability] 방법을 알아보세요.

Azure Storage 계정은 잠재적인 단일 실패 지점일 수 있으므로 두 개 이상의 가상 컴퓨터가 배포될 2개 이상의 Azure Storage 계정이 있어야 합니다. 이상적인 설치에서는 SAP 대화 상자 인스턴스를 실행하는 각 가상 컴퓨터가 다른 Storage 계정에 배포됩니다.

### <a name="a-namef559c285ee684eecadd1f60fe7b978dba-highavailability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> 고가용성 SAP ASCS/SCS 인스턴스
![그림 5: 고가용성 SAP ASCS/SCS 인스턴스][sap-ha-guide-figure-2001]

***그림 5:** 고가용성 SAP ASCS/SCS 인스턴스*

#### <a name="a-nameb5b1fd0b1db44d499162de07a0132a51a-highavailability-sap-ascsscs-instance-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Azure에서 Windows 장애 조치 클러스터링을 사용한 고가용성 SAP ASCS/SCS 인스턴스
운영 체제 미설치 또는 사설 클라우드 배포에 비해, Azure Virtual Machines는 Windows Server 장애 조치 클러스터링을 구성하기 위한 추가 단계가 필요합니다. Windows 장애 조치 클러스터를 구축하려면 SAP ASCS/SCS 인스턴스를 클러스터링하기 위한 공유 클러스터 디스크, 일부 IP 주소, 가상 호스트 이름 및 Azure 내부 부하 분산 장치가 필요합니다.

이 내용은 문서 뒷부분에서 좀 더 자세히 설명합니다.

![그림 6: SIOS DataKeeper를 사용하는 Azure의 SAP ASCS/SCS용 Windows Server 장애 조치 클러스터링 구성][sap-ha-guide-figure-1002]

***그림 6:** SIOS DataKeeper를 사용하는 Azure의 SAP ASCS/SCS용 Windows Server 장애 조치 클러스터링 구성*

### <a name="a-nameddd878a09c2f4b8e896826ce60be1027a-highavailability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> 고가용성 DBMS 인스턴스
DBMS는 SAP 시스템의 단일 연락 지점이기도 합니다. 따라서 고가용성 솔루션을 사용하여 보호해야 합니다. 그림 7에서는 Windows Server 장애 조치 클러스터링 및 Azure 내부 부하 분산 장치를 사용하여 Azure의 SQL Server Always On 고가용성 솔루션 예제를 보여 줍니다. SQL Server Always On은 자체 DBMS 복제를 사용하여 DBMS 데이터 및 로그 파일을 복제합니다. 이 경우 전체 설정을 간소화하는 클러스터 공유 디스크가 필요하지 않습니다.

![그림 7: 고가용성 SAP DBMS: SQL Server Always On 예제][sap-ha-guide-figure-2003]

***그림 7:** 고가용성 SAP DBMS: SQL Server Always On 예제*

Azure Resource Manager 배포 모델을 사용하는 Azure의 SQL Server 클러스터링에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Resource Manager를 사용하여 Azure Virtual Machines에서 수동으로 Always On 가용성 그룹 구성][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Azure에서 Always On 가용성 그룹에 대한 내부 부하 분산 장치 구성][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="a-name045252ed02774fc88f46c5a29694a816a-endtoend-highavailability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> 종단간 고가용성 배포 시나리오
그림 8에서는 Azure의 SAP NetWeaver 고가용성 아키텍처 예제를 보여 줍니다. 이 시나리오에서는 SAP ASCS/SCS 인스턴스용 전용 클러스터와 DBMS용 전용 클러스터를 각각 하나씩 사용합니다.

![그림 8: SAP HA 아키텍처 템플릿 1(ASCS/SCS용 전용 클러스터 및 DBMS 인스턴스용 전용 클러스터)][sap-ha-guide-figure-2004]

***그림 8:** SAP HA 아키텍처 템플릿 1: ASCS/SCS용 전용 클러스터 및 DBMS용 전용 클러스터*

## <a name="a-name78092dbe165b454c92f54972bdbef9bfa-prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> 인프라 준비
SAP용 Azure Resource Manager 템플릿은 필요한 리소스의 배포를 간소화하도록 도와줍니다.

또한 3계층 템플릿은 2개의 클러스터가 있는 아키텍처 템플릿 1과 같은 고가용성 시나리오도 지원합니다. 각 클러스터는 SAP ASCS/SCS 및 DBMS에 대한 SAP 단일 실패 지점입니다.

다음은 시나리오 1에 대한 Azure Resource Manager 템플릿을 다운로드할 수 있는 위치입니다.

* [Azure Marketplace 이미지](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [사용자 지정 이미지](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

SAP 3계층 마켓플레이스 이미지를 선택하면 Azure Portal에 이 화면이 표시됩니다.

![그림 9: SAP 고가용성 Azure Resource Manager 매개 변수 지정][sap-ha-guide-figure-3000]

***그림 9:** SAP 고가용성 Azure Resource Manager 매개 변수 지정*

**SYSTEMAVAILABILITY**에서 **HA**를 선택합니다.

템플릿은 다음을 만듭니다.

* **가상 컴퓨터**:
  * SAP 응용 프로그램 서버 가상 컴퓨터: <*SAPSystemSID*>-di-<*Number*>
  * ASCS/SCS 클러스터 가상 컴퓨터: <*SAPSystemSID*>-ascs-<*Number*>
  * DBMS 클러스터: <*SAPSystemSID*>-db-<*Number*>
* **연결된 IP 주소를 갖는 모든 가상 컴퓨터에 대한 네트워크 카드**:
  * <*SAPSystemSID*>-nic-di-<*Number*>
  * <*SAPSystemSID*>-nic-ascs-<*Number*>
  * <*SAPSystemSID*>-nic-db-<*Number*>
* **Azure Storage 계정**
* 다음에 대한 **가용성 그룹**:
  * SAP 응용 프로그램 서버 가상 컴퓨터: <*SAPSystemSID*>-avset-di
  * SAP ASCS/SCS 클러스터 가상 컴퓨터: <*SAPSystemSID*>-avset-ascs
  * DBMS 클러스터 가상 컴퓨터: <*SAPSystemSID*>-avset-db
* **Azure 내부 부하 분산 장치**:
  * ASCS/SCS 인스턴스 및 IP 주소 <*SAPSystemSID*>-lb-ascs에 대한 모든 포트
  * SQL Server DBMS 및 IP 주소 <*SAPSystemSID*>-lb-db에 대한 모든 포트
* **네트워크 보안 그룹**: <*SAPSystemSID*>-nsg-ascs-0  
  * <*SAPSystemSID*>-ascs-0 가상 컴퓨터에 대해 열려 있는 외부 RDP(원격 데스크톱 프로토콜) 포트

> [!NOTE]
> 네트워크 카드 및 Azure 내부 부하 분산 장치의 모든 IP 주소는 기본적으로 **동적**입니다. 이 주소를 **고정** IP 주소와 비교해보세요. 이 내용은 문서 뒷부분에 설명되어 있습니다.
> 
> 

### <a name="a-namec87a8d3fb1dc4d2fb23cda4b72977489a-deploy-virtual-machines-with-corporate-network-connectivity-crosspremises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> 프로덕션 환경에서 사용하기 위해 회사 네트워크 연결(크로스-프레미스)을 사용하여 가상 컴퓨터 배포
프로덕션 SAP 시스템의 경우 Azure 사이트 간 VPN 또는 Azure Express 경로를 사용하여 [회사 네트워크 연결(크로스-프레미스)][planning-guide-2.2]을 통해 Azure Virtual Machines를 배포합니다.

> [!NOTE]
> Azure 가상 네트워크 인스턴스를 사용할 수 있습니다. 가상 네트워크 및 서브넷은 이미 생성되고 준비되어 있습니다.
> 
> 

**NEWOREXISTINGSUBNET**에서 **기존**을 선택합니다.

Azure Virtual Machines를 배포하려는 경우 **SUBNETID**에서 준비된 Azure 네트워크 서브넷 ID의 전체 문자열을 추가합니다.

다음 PowerShell 명령을 실행하여 모든 Azure 네트워크 서브넷 목록을 가져옵니다.

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```

**ID** 필드에 **SUBNETID**가 표시됩니다.

다음 PowerShell 명령을 사용하여 모든 **SUBNETID** 값 목록을 검색할 수 있습니다.

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

**SUBNETID**는 다음과 유사합니다.

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="a-name7fe9af0e3cce495ba5ecdcb4d8e0a310a-cloudonly-deployment-of-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> 테스트 및 데모용 SAP 인스턴스의 클라우드 전용 배포
또한 클라우드 전용 배포 모델에서 고가용성 SAP 시스템을 배포할 수도 있습니다.

기본적으로 이러한 종류의 배포는 데모 또는 테스트 사용 사례에 사용할 수 있습니다. 프로덕션 사용 사례에는 적합하지 않습니다.

**NEWOREXISTINGSUBNET**에서 **새로 만들기**를 선택합니다. **SUBNETID** 필드는 비워둡니다.

SAP Azure Resource Manager 템플릿은 Azure 가상 네트워크 및 서브넷을 자동으로 만듭니다.

> [!NOTE]
> 또한 동일한 Azure 가상 네트워크 인스턴스에 Active Directory 및 DNS에 대한 하나 이상의 전용 가상 컴퓨터를 배포해야 합니다. 이 템플릿이 이러한 가상 컴퓨터를 만들지는 않습니다.
> 
> 

### <a name="a-name47d5300aa83041d483dd1a0d1ffdbe6aa-azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure 가상 네트워크
이 예제에서 Azure 가상 네트워크의 주소 공간은 10.0.0.0/16입니다. **Subnet**이라는 서브넷이 하나 있으며 주소 범위는 10.0.0.0/24입니다. 모든 가상 컴퓨터와 내부 부하 분산 장치는 이 가상 네트워크에 배포됩니다.

> [!NOTE]
> 게스트 운영 체제 내의 네트워크 설정은 변경하지 않도록 합니다. 여기에는 IP 주소, DNS 서버 및 서브넷이 포함됩니다. Azure에서 모든 네트워크 설정을 구성합니다. DHCP(동적 호스트 구성 프로토콜) 서비스가 사용자 설정을 전파합니다.
> 
> 

### <a name="a-nameb22d7b3b434340ffa319097e13f62f9ea-dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP 주소
가상 네트워크 **DNS 서버** 옵션이 **사용자 지정 DNS**로 설정되어 있는지 확인합니다.
그런 다음 사용 중인 네트워크의 종류에 따라 설정을 선택합니다.

* [회사 네트워크 연결(크로스-프레미스)][planning-guide-2.2]: 온-프레미스 DNS 서버의 IP 주소를 추가합니다.  
  
    Azure에서 실행되는 가상 컴퓨터로 온-프레미스 DNS 서버를 확장할 수 있습니다. 이 시나리오에서는 DNS 서비스를 실행하는 Azure Virtual Machines의 IP 주소를 추가할 수 있습니다.
* [클라우드 전용 배포][planning-guide-2.1]: DNS 서버 역할을 하는 추가 가상 컴퓨터를 동일한 가상 네트워크 인스턴스에 배포합니다. DNS 서비스를 실행하도록 설정한 Azure Virtual Machines의 IP 주소를 추가합니다.

![그림 10: Azure 가상 네트워크에 대해 DNS 서버 구성][sap-ha-guide-figure-3001]

***그림 10:** Azure 가상 네트워크에 대해 DNS 서버 구성*

> [!NOTE]
> DNS 서버의 IP 주소를 변경하는 경우 변경 내용을 적용하고 새 DNS 서버를 전파하기 위해 Azure Virtual Machines를 다시 시작해야 합니다.
> 
> 

이 예제에서는 다음 Windows 가상 컴퓨터에서 DNS 서비스가 설치되고 구성됩니다.

| 가상 컴퓨터 역할 | 가상 컴퓨터 호스트 이름 | 네트워크 카드 이름 | 고정 IP 주소 |
| --- | --- | --- | --- |
| 첫 번째 DNS 서버 |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| 두 번째 DNS 서버 |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="a-name9fbd43c05850496597262a921d85d73fa-host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> SAP ASCS/SCS 클러스터형 인스턴스 및 DBMS 클러스터형 인스턴스의 호스트 이름 및 고정 IP 주소
온-프레미스 배포에 대해 다음의 예약된 호스트 이름 및 IP 주소가 필요합니다.

| 가상 호스트 이름 역할 | 가상 호스트 이름 | 가상 고정 IP 주소 |
| --- | --- | --- |
| SAP ASCS/SCS 첫 번째 클러스터 가상 호스트 이름(클러스터 관리용) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS 인스턴스 가상 호스트 이름 |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS 두 번째 클러스터 가상 호스트 이름(클러스터 관리용) |pr1-dbms-vir |10.0.0.32 |

클러스터를 만들 때 만들 가상 호스트 이름 **pr1-ascs-vir** 및 **pr1-dbms-vir**클러스터 자체를 관리하는 연결된 IP 주소를 만듭니다. 이 프로세스는 [클러스터 구성에서 클러스터 노드 수집][sap-ha-guide-8.12.1]에 설명됩니다.

DNS 서버에서 다른 두 가상 호스트 이름 **pr1 ascs sap** 및 **pr1 dbms sap**와 연결된 IP 주소는 수동으로 만들 수 있습니다. 클러스터형 SAP ASCS/SCS 인스턴스 및 클러스터형 DBMS 인스턴스는 이러한 리소스를 사용합니다. 이 내용은 [클러스터형 SAP ASCS/SCS에 대한 가상 호스트 이름 만들기][sap-ha-guide-9.1.1]에 설명됩니다.

### <a name="a-name84c019fe8c584dac9e54173efd4b2c30a-set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> SAP 가상 컴퓨터에 대한 고정 IP 주소 설정
클러스터에서 사용할 가상 컴퓨터를 배포한 후 모든 가상 컴퓨터에 대해 고정 IP 주소를 설정해야 합니다. 이 작업은 게스트 운영 체제가 아니라 Azure 가상 네트워크 구성에서 수행합니다.

고정 IP 주소를 설정하는 유일한 방법은 Azure Portal을 사용하는 것입니다. Azure Portal에서 **리소스 그룹** > **네트워크 카드** > **설정** > **IP 주소**로 이동합니다.

**할당**에서 **고정**을 선택합니다. **IP 주소** 필드에 사용할 IP 주소를 입력합니다.

> [!NOTE]
> 네트워크 카드의 IP 주소를 변경하는 경우 Azure Virtual Machines를 다시 시작하여 변경 내용을 적용해야 합니다.  
> 
> 

![그림 11: 각 가상 컴퓨터의 네트워크 카드에 대해 고정 IP 주소 설정][sap-ha-guide-figure-3002]

***그림 11:** 각 가상 컴퓨터의 네트워크 카드에 대해 고정 IP 주소 설정*

모든 네트워크 인터페이스, 즉 Active Directory/DNS 서비스에 사용하려는 가상 컴퓨터를 비롯한 모든 가상 컴퓨터에 대해 이 단계를 반복합니다.

예제에서는 다음 가상 컴퓨터 및 고정 IP 주소를 사용합니다.

| 가상 컴퓨터 역할 | 가상 컴퓨터 호스트 이름 | 네트워크 카드 이름 | 고정 IP 주소 |
| --- | --- | --- | --- |
| 첫 번째 SAP 응용 프로그램 서버 |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| 두 번째 SAP 응용 프로그램 서버 |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| 마지막 SAP 응용 프로그램 서버 |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| ASCS/SCS 인스턴스의 첫 번째 클러스터 노드 |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| ASCS/SCS 인스턴스의 두 번째 클러스터 노드 |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| DBMS 인스턴스의 첫 번째 클러스터 노드 |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| DBMS 인스턴스의 두 번째 클러스터 노드 |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="a-name7a8f3e9b062440519e41b73fff816a9ea-set-a-static-ip-address-for-the-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> 내부 부하 분산 장치에 대한 고정 IP 주소 설정
SAP Azure Resource Manager 템플릿은 SAP ASCS/SCS 인스턴스 클러스터 및 DBMS 클러스터에 사용되는 Azure 내부 부하 분산 장치를 만듭니다.

초기 배포는 내부 부하 분산 장치 IP 주소를 **동적**으로 설정합니다. IP 주소를 **고정**으로 변경해야 합니다.

예제에서는 다음과 같은 고정 IP 주소를 가진 두 개의 Azure 내부 부하 분산 장치가 사용됩니다.

| Azure 내부 부하 분산 장치 역할 | Azure 내부 부하 분산 장치 이름 | 고정 IP 주소 |
| --- | --- | --- |
| SAP ASCS/SCS 인스턴스 내부 부하 분산 장치 |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS 내부 부하 분산 장치 |pr1-lb-dbms |10.0.0.33 |

> [!NOTE]
> SAP ASCS/SCS 인스턴스의 가상 호스트 이름 IP 주소는 SAP ASCS/SCS 내부 부하 분산 장치 pr1-lb-ascs의 IP 주소와 같습니다.
> DBMS의 가상 호스트 이름 IP 주소는 DBMS 내부 부하 분산 장치 pr1-lb-dbms의 IP 주소와 같습니다.
> 
> 

이 예제에서는 내부 부하 분산 장치 **pr1-lb-ascs**의 IP 주소를 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 IP 주소(예제의 **10.0.0.43**)로 설정합니다.

![그림 12: SAP ASCS/SCS 인스턴스에 대한 내부 부하 분산 장치에 대한 고정 IP 주소 설정][sap-ha-guide-figure-3003]

***그림 12:** SAP ASCS/SCS 인스턴스에 대한 내부 부하 분산 장치에 대한 고정 IP 주소 설정*

내부 부하 분산 장치 **pr1-lb-dbms**의 IP 주소를 DBMS 인스턴스의 가상 호스트 이름 IP 주소(예제의 **10.0.0.33**)로 설정합니다.

### <a name="a-namef19bd997154d4583a46e7f5a69d0153ca-default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Azure 내부 부하 분산 장치에 대한 기본 ASCS/SCS 부하 분산 규칙
SAP Azure Resource Manager 템플릿은 다음에 대해 필요한 포트를 만듭니다.

* 기본 인스턴스 번호가 **00**인 ABAP ASCS 인스턴스
* 기본 인스턴스 번호가 **01**인 Java SCS 인스턴스

SAP ASCS/SCS 인스턴스를 설치하면 ABAP ASCS 인스턴스에 대해 기본 인스턴스 번호 00을, Java SCS 인스턴스에 대해 기본 인스턴스 번호 01을 사용해야 합니다.

다음으로 SAP NetWeaver ABAP ASCS 포트에 대한 다음과 같은 필수 내부 부하 분산 끝점을 만듭니다.

| 서비스/부하 분산 규칙 이름 | 기본 포트 번호 | (인스턴스 번호가 00인 ASCS 인스턴스)(ERS가 10)에 대한 구체적인 포트 |
| --- | --- | --- |
| 서버를 큐에 넣기/ *lbrule3200* |32<*InstanceNumber*> |3200 |
| ABAP 메시지 서버/ *lbrule3600* |36<*InstanceNumber*> |3600 |
| 내부 ABAP 메시지/ *lbrule3900* |39<*InstanceNumber*> |3900 |
| 메시지 서버 HTTP/ *Lbrule8100* |81<*InstanceNumber*> |8100 |
| SAP 시작 서비스 ASCS HTTP/ *Lbrule50013* |5<*InstanceNumber*>13 |50013 |
| SAP 시작 서비스 ASCS HTTP/ *Lbrule50014* |5<*InstanceNumber*>14 |50014 |
| 복제를 큐에 넣기/ *Lbrule50016* |5<*InstanceNumber*>16 |50016 |
| SAP 시작 서비스 ERS HTTP/ *Lbrule51013* |5<*InstanceNumber*>13 |51013 |
| SAP 시작 서비스 ERS HTTP *Lbrule51014* |5<*InstanceNumber*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| 파일 공유 *Lbrule445* | |445 |

***표 1:** SAP NetWeaver ABAP ASCS 인스턴스의 포트 번호*

그런 후 SAP NetWeaver Java SCS 포트에 대한 다음과 같은 필수 내부 부하 분산 끝점을 만듭니다.

| 서비스/부하 분산 규칙 이름 | 기본 포트 번호 | (인스턴스 번호가 01인 SCS 인스턴스)(ERS가 11)에 대한 구체적인 포트 |
| --- | --- | --- |
| 서버를 큐에 넣기/ *lbrule3201* |32<*InstanceNumber*> |3201 |
| 게이트웨이 서버/ *lbrule3301* |33<*InstanceNumber*> |3301 |
| Java 메시지 서버/ *lbrule3900* |39<*InstanceNumber*> |3901 |
| 메시지 서버 HTTP/ *Lbrule8101* |81<*InstanceNumber*> |8101 |
| SAP 시작 서비스 SCS HTTP/ *Lbrule50113* |5<*InstanceNumber*>13 |50113 |
| SAP 시작 서비스 SCS HTTP/ *Lbrule50114* |5<*InstanceNumber*>14 |50114 |
| 복제를 큐에 넣기/ *Lbrule50116* |5<*InstanceNumber*>16 |50116 |
| SAP 시작 서비스 ERS HTTP *Lbrule51113* |5<*InstanceNumber*>13 |51113 |
| SAP 시작 서비스 ERS HTTP *Lbrule51114* |5<*InstanceNumber*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| 파일 공유 *Lbrule445* | |445 |

***표 2:** SAP NetWeaver Java SCS 인스턴스의 포트 번호*

![그림 13: Azure 내부 부하 분산 장치에 대한 기본 ASCS/SCS 부하 분산 규칙][sap-ha-guide-figure-3004]

***그림 13:** Azure 내부 부하 분산 장치에 대한 기본 ASCS/SCS 부하 분산 규칙*

부하 분산 장치 **pr1-lb-dbms**의 IP 주소를 DBMS 인스턴스의 가상 호스트 이름 IP 주소(예제의 **10.0.0.33**)로 설정합니다.

### <a name="a-namefe0bd8b52b4345e3829580bee5415716a-change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경
SAP ASCS 또는 SCS 인스턴스에 대해 다른 인스턴스 번호를 사용하려는 경우 해당 포트의 이름 및 값을 업데이트해야 합니다.

인스턴스 번호를 업데이트하는 한 가지 방법은 Azure Portal을 사용하는 것입니다.

**<*SID*>-lb-ascs 부하 분산 장치** > **부하 부산 장치 규칙**으로 이동합니다.

SAP ASCS 또는 SCS 인스턴스에 속하는 모든 부하 분산 규칙에 대해 다음 값을 변경합니다.

* 이름
* 포트
* 백 엔드 포트

예를 들어 기본 ASCS 인스턴스 번호를 00에서 31로 변경하려는 경우 표 1에 나열된 모든 포트에 대해 이러한 변경을 수행해야 합니다.

다음은 포트 _lbrule3200_에 대한 업데이트 예제입니다.

![그림 14: Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경][sap-ha-guide-figure-3005]

***그림 14:** Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경*

### <a name="a-namee69e9a34460147a3a41cd2e11c626c0ca-add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> 도메인에 Windows 가상 컴퓨터 추가
가상 컴퓨터에 고정 IP 주소를 할당한 후 가상 컴퓨터를 도메인에 추가합니다.

![그림 15: 도메인에 가상 컴퓨터 추가][sap-ha-guide-figure-3006]

***그림 15:** 도메인에 가상 컴퓨터 추가*

### <a name="a-name661035b24d0f4d3186f8dc0a50d78158a-add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> SAP ASCS/SCS 인스턴스의 클러스터 노드 둘 다에 대한 레지스트리 항목 추가
Azure Load Balancer에는 설정된 시간(유휴 제한 시간) 동안 연결이 유휴 상태일 때 연결을 닫는 내부 부하 분산 장치가 있습니다. 대화 상자 인스턴스의 SAP 작업 프로세스는 첫 번째 큐에 넣지/큐에서 제거 요청이 전송되는 즉시 SAP 큐에 넣기 프로세스에 대한 연결을 엽니다. 이러한 연결은 일반적으로 작업 프로세스 또는 큐에 넣기 프로세스가 다시 시작될 때까지 설정 상태를 유지합니다. 그러나 연결이 특정 시간 동안 유휴 상태이면 Azure 내부 부하 분산 장치는 연결을 닫습니다. 그렇지만 연결이 더 이상 없는 경우 SAP 작업 프로세스는 큐에 넣기 프로세스에 대한 연결을 다시 설정하기 때문에 문제가 되지 않습니다. 이러한 활동은 SAP 프로세스의 개발자 추적에 설명되어 있지만 해당 추적에 많은 양의 추가 콘텐츠가 생성됩니다. 따라서 두 클러스터 노드에서 TCP/IP `KeepAliveTime` 및 `KeepAliveInterval`을 변경하는 것이 좋습니다. 이 문서 뒷부분에 설명된 것처럼 TCP/IP 매개 변수의 이러한 변경 내용을 SAP 프로필 매개 변수와 결합합니다.

SAP ASCS/SCS에 대한 두 Windows 클러스터 노드에 대해 다음 Windows 레지스트리 항목을 추가합니다.

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 변수 이름 |`KeepAliveTime` |
| 변수 유형 |REG_DWORD(10진수) |
| 값 |120000 |
| 설명서 링크 |[https://technet.microsoft.com/en-us/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx) |

***표 3:** 첫 번째 TCP/IP 매개 변수 변경*

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 변수 이름 |`KeepAliveInterval` |
| 변수 유형 |REG_DWORD(10진수) |
| 값 |120000 |
| 설명서 링크 |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

***표 4:** 두 번째 TCP/IP 매개 변수 변경*

변경 내용을 적용하려면 두 클러스터 노드를 모두 다시 시작합니다.

### <a name="a-name0d67f090792843e087725ccbf8f59aaba-set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> SAP ASCS/SCS 인스턴스에 대한 Windows Server 장애 조치 클러스터링 클러스터 설정
#### <a name="a-name5eecb071c7034cccba6dfe9c6ded9d79a-collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> 클러스터 구성에서 클러스터 노드 수집
첫 번째 단계는 두 클러스터 노드에 장애 조치 클러스터링을 추가하는 것입니다. 역할 및 기능 추가 마법사를 사용합니다.

두 번째 단계는 장애 조치 클러스터 관리자를 사용하여 장애 조치 클러스터를 설정하는 것입니다.

장애 조치 클러스터 관리자에서 **클러스터 만들기**를 선택하고 첫 번째 클러스터 노드 A의 이름만 추가합니다. 예를 들어 **pr1-ascs-0** 두 번째 노드는 아직 추가하지 마세요. 이후 단계에서 두 번째 노드를 추가합니다.

![그림 16: 첫 번째 클러스터 노드의 서버 또는 가상 컴퓨터 이름 추가][sap-ha-guide-figure-3007]

***그림 16:** 첫 번째 클러스터 노드의 서버 또는 가상 컴퓨터 이름 추가*

다음으로, 클러스터의 네트워크 이름(가상 호스트 이름)을 묻는 메시지가 표시됩니다.

![그림 17: 클러스터 이름 정의][sap-ha-guide-figure-3008]

***그림 17:** 클러스터 이름 정의*

클러스터를 만든 후에 클러스터 유효성 검사 테스트를 실행합니다.

![그림 18: 클러스터 유효성 검사 실행][sap-ha-guide-figure-3009]

***그림 18:** 클러스터 유효성 검사 실행*

![그림 19: 쿼럼 디스크가 없음][sap-ha-guide-figure-3010]

***그림 19:** 쿼럼 디스크가 없음*

프로세스의 이 시점에 표시되는 디스크에 대한 경고는 무시해도 됩니다. 파일 공유 감시 및 SIOS 공유 디스크는 나중에 추가합니다. 이 단계에서는 쿼럼이 없어도 걱정할 필요가 없습니다.

![그림 20: 핵심 클러스터 리소스에 새 IP 주소 필요][sap-ha-guide-figure-3011]

***그림 20:** 핵심 클러스터 리소스에 새 IP 주소 필요*

서버의 IP 주소가 가상 컴퓨터 노드 중 하나를 가리키므로 클러스터를 시작할 수 없습니다. 핵심 클러스터 서비스의 IP 주소를 변경해야 합니다.

예를 들어 클러스터 가상 호스트 이름 **pr1-ascs-vir**에 대한 IP 주소(이 예제에서 **10.0.0.42**)를 할당해야 합니다. 그림 21과 같이 핵심 클러스터 서비스 IP 리소스의 속성 페이지에서 이 작업을 수행합니다.

![그림 21: **속성** 대화 상자에서 IP 주소 변경][sap-ha-guide-figure-3012]

***그림 21:** **속성** 대화 상자에서 IP 주소 변경*

![그림 22: 클러스터에 대해 예약된 IP 주소 할당][sap-ha-guide-figure-3013]

***그림 22:** 클러스터에 대해 예약된 IP 주소 할당*

IP 주소를 변경한 후 클러스터 가상 호스트 이름을 온라인 상태로 만듭니다.

![그림 23: 올바른 IP 주소를 사용하여 클러스터 핵심 서비스 실행][sap-ha-guide-figure-3014]

***그림 23:** 올바른 IP 주소를 사용하여 클러스터 핵심 서비스 실행*

핵심 클러스터 서비스가 작동 및 실행되고 있으므로 두 번째 클러스터 노드를 추가할 수 있습니다.

![그림 24: 두 번째 클러스터 노드 추가][sap-ha-guide-figure-3015]

***그림 24:** 두 번째 클러스터 노드 추가*

![그림 25: 두 번째 클러스터 노드의 호스트 이름(예: pr1-ascs-1) 추가][sap-ha-guide-figure-3016]

***그림 25:** 두 번째 클러스터 노드의 호스트 이름(예: **pr1-ascs-1**) 추가*

![그림 26: 이 확인란을 선택하지 말 것][sap-ha-guide-figure-3017]

***그림 26:** 이 확인란을 선택하지 *말 것**

> [!IMPORTANT]
> **클러스터에 사용할 수 있는 모든 Storage를 추가하세요.** 확인란을 선택하지 *않았는지* 확인합니다.  
> 
> 

![그림 27: 디스크 쿼럼에 대한 경고 무시][sap-ha-guide-figure-3018]

***그림 27:** 디스크 쿼럼에 대한 경고 무시*

쿼럼 및 디스크에 대한 경고는 무시해도 됩니다. [SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치][sap-ha-guide-8.12.3]에 설명된 것처럼 나중에 쿼럼을 설정하고 디스크를 공유합니다.

#### <a name="a-namee49a452950c94dcfbde715a0c21d21caa-configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> 클러스터 파일 공유 감시 구성
##### <a name="a-name06260b30d6974c4db1c9d22c0bd64855a-create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> 파일 공유 만들기
쿼럼 디스크 대신 파일 공유 감시를 선택합니다. SIOS DataKeeper는 이 옵션을 지원합니다.

이 문서의 예제에서 파일 공유 감시는 Azure에서 실행되는 Active Directory/DNS 서버에 있습니다. 파일 공유 감시를 **domcontr-0**이라고 합니다. Azure에 대해 VPN(가상 사설망) 연결을 구성했으므로(사이트 간 VPN 또는 Azure Express 경로를 통해) Active Directory/DNS 서비스는 온-프레미스이며 파일 공유 감시를 실행하는 데 적합하지 않습니다.

> [!NOTE]
> Active Directory/DNS 서비스는 온-프레미스로만 실행되므로 온-프레미스로 실행되는 Active Directory/DNS Windows 운영 체제에서 파일 공유 감시를 구성하지 마세요. Azure 및 Active Directory/DNS 온-프레미스로 실행되는 클러스터 노드 간 네트워크 대기 시간이 너무 커서 연결 문제를 일으킬 수 있습니다. 클러스터 노드와 가깝게 실행되는 Azure Virtual Machines에서 파일 공유 감시를 구성해야 합니다.  
> 
> 

쿼럼 드라이브에는 1,024MB 이상의 여유 공간이 필요합니다. 2,048MB의 여유 공간이 권장됩니다.

첫 번째 단계는 클러스터 이름 개체를 추가하는 것입니다.

![그림 28: 클러스터 이름 개체의 공유에 대한 권한 할당][sap-ha-guide-figure-3019]

***그림 28:** 클러스터 이름 개체의 공유에 대한 권한 할당*

클러스터 이름 개체(예제의 **pr1-ascs-vir$**)에 대한 공유에서 데이터를 변경할 수 있는 권한이 포함되는지 확인합니다. 목록에 클러스터 이름 개체를 추가하려면 **추가**를 선택합니다. 그림 29에 표시되는 것 외에 컴퓨터 개체를 확인하도록 필터를 변경합니다.

![그림 29: 컴퓨터 개체를 포함하도록 개체 유형 변경][sap-ha-guide-figure-3020]

***그림 29:** 컴퓨터 개체를 포함하도록 개체 유형 변경*

![그림 30: 컴퓨터 개체에 대한 확인란 선택][sap-ha-guide-figure-3021]

***그림 30:** 컴퓨터 개체에 대한 확인란 선택*

그런 후 그림 29와 같이 클러스터 이름 개체를 입력합니다. 레코드가 이미 만들어졌을 것이므로 그림 28에서처럼 사용 권한을 변경할 수 있습니다.

다음으로, 공유의 **보안** 탭을 선택한 후 클러스터 이름 개체에 대한 좀 더 자세한 사용 권한을 설정합니다.

![그림 31: 파일 공유 쿼럼의 클러스터 이름 개체에 대한 보안 특성 설정][sap-ha-guide-figure-3022]

***그림 31:** 파일 공유 쿼럼의 클러스터 이름 개체에 대한 보안 특성 설정*

##### <a name="a-name4c08c38778a046b19d27b497b08cac3da-set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> 장애 조치 클러스터 관리자에서 파일 공유 감시 쿼럼 설정
장애 조치 클러스터 관리자에서 클러스터 구성을 파일 공유 감시로 변경합니다.

![그림 32: 클러스터 쿼럼 설정 구성 마법사 시작][sap-ha-guide-figure-3023]

***그림 32:** 클러스터 쿼럼 설정 구성 마법사 시작*

![그림 33: 선택할 수 있는 쿼럼 구성][sap-ha-guide-figure-3024]

***그림 33:** 선택할 수 있는 쿼럼 구성*

**쿼럼 감시 선택**을 선택합니다.

![그림 34: 파일 공유 감시 선택][sap-ha-guide-figure-3025]

***그림 34:** 파일 공유 감시 선택*

**파일 공유 감시 구성**을 선택합니다.

![그림 35: 감시 공유에 대한 파일 공유 위치 정의][sap-ha-guide-figure-3026]

***그림 35:** 감시 공유에 대한 파일 공유 위치 정의*

파일 공유에 대한 UNC 경로를 입력합니다(예제의 \\domcontr-0\FSW).

**다음**을 선택하여 변경할 수 있는 목록을 표시합니다. 원하는 변경 내용을 선택하고 **다음**을 선택합니다.

![그림 36: 클러스터를 다시 구성했는지 확인][sap-ha-guide-figure-3027]

***그림 36:** 클러스터를 다시 구성했는지 확인*

이 마지막 단계에서는 그림 36와 같이 클러스터 구성을 다시 구성해야 합니다.  

### <a name="a-name5c8e5482841e45e1a89da05c0907c868a-install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치
이제 Azure에서 Windows Server 장애 조치 클러스터링 구성이 완료되었습니다. 그렇지만 SAP ASCS/SCS 인스턴스를 설치하려면 공유 디스크 리소스가 필요합니다. Azure에서는 필요한 공유 디스크 리소스를 만들 수 없습니다. SIOS DataKeeper Cluster Edition은 공유 디스크 리소스를 만드는 데 사용할 수 있는 타사 솔루션입니다.

#### <a name="a-name1c2788c336484e829e0de058e475e2a3a-add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> .NET Framework 3.5 추가
Microsoft.NET Framework 3.5는 Windows Server 2012 R2에서 자동으로 활성화되거나 설치되지 않습니다. 하지만 SIOS DataKeeper를 사용하려면 DataKeeper를 설치하는 모든 노드에 .NET Framework가 있어야 합니다. 따라서 클러스터의 모든 가상 컴퓨터 게스트 운영 체제에 .NET Framework 3.5를 설치해야 합니다.

.NET Framework 3.5는 두 가지 방법으로 추가합니다. 한 가지 방법은 그림 37과 같이 Windows에서 역할 및 기능 추가 마법사를 사용하는 것입니다.

![그림 37: 역할 및 기능 추가 마법사를 사용하여 .NET Framework 3.5 설치][sap-ha-guide-figure-3028]

***그림 37:** 역할 및 기능 추가 마법사를 사용하여 .NET Framework 3.5 설치*

![그림 38: 역할 및 기능 추가 마법사를 사용하여 .NET Framework 3.5를 설치할 때의 설치 진행률 표시기][sap-ha-guide-figure-3029]

***그림 38:** 역할 및 기능 추가 마법사를 통해 .NET Framework 3.5를 설치할 때의 설치 진행률 표시기*

.NET framework 3.5 기능을 활성화하는 두 번째 옵션은 명령줄 도구 dism.exe를 사용하는 것입니다. 이 유형의 설치에서는 Windows 설치 미디어의 SxS 디렉터리에 액세스할 수 있어야 합니다. 관리자 권한의 명령 프롬프트에서 이 명령을 실행합니다.

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="a-namedd41d5a28083415b9878839652812102a-install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> SIOS DataKeeper 설치
클러스터의 각 노드에 SIOS DataKeeper Cluster Edition을 설치합니다. SIOS DataKeeper를 사용하여 가상 공유 Storage를 만들려면 동기화된 미러를 만든 후 클러스터 공유 Storage를 시뮬레이션합니다.

SIOS 소프트웨어를 설치하기 전에 도메인 사용자 **DataKeeperSvc**를 만듭니다.

> [!NOTE]
> **DataKeeperSvc** 사용자를 두 클러스터 노드의 **로컬 관리자** 그룹에 추가합니다.
> 
> 

두 클러스터 노드에서 SIOS 소프트웨어를 설치합니다.

![SIOS 설치 관리자][sap-ha-guide-figure-3030]

![그림 39: SIOS DataKeeper 설치의 첫 번째 화면][sap-ha-guide-figure-3031]

***그림 39:** SIOS DataKeeper 설치의 첫 번째 화면*

![그림 40: DataKeeper에서 서비스 사용 불가능 상태 알림][sap-ha-guide-figure-3032]

***그림 40:** DataKeeper에서 서비스 사용 불가능 상태 알림*

그림 40에 표시된 대화 상자에서 **예**를 선택합니다.

![그림 41: SIOS DataKeeper에 대한 사용자 선택][sap-ha-guide-figure-3033]

***그림 41:** SIOS DataKeeper에 대한 사용자 선택*

그림 41에 표시된 화면에서는 **도메인 또는 서버 계정**을 선택하는 것이 좋습니다.

![그림 42: SIOS DataKeeper 설치를 위한 도메인 사용자 이름 및 암호 입력][sap-ha-guide-figure-3034]

***그림 42:** SIOS DataKeeper 설치를 위한 도메인 사용자 이름 및 암호 입력*

SIOS DataKeeper에 대해 만든 도메인 계정 사용자 이름 및 암호를 입력합니다.

![그림 43: SIOS DataKeeper 라이선스 입력][sap-ha-guide-figure-3035]

***그림 43:** SIOS DataKeeper 라이선스 입력*

그림 43에서처럼 SIOS DataKeeper 인스턴스에 대한 라이선스 키를 설치합니다. 설치 후에 가상 컴퓨터를 다시 시작하라는 메시지가 표시됩니다.

#### <a name="a-named9c1fc8e87104dffbec21f535db7b006a-set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> SIOS DataKeeper 설정
두 노드에 SIOS DataKeeper를 설치한 후 구성을 시작해야 합니다. 각 가상 컴퓨터에 연결된 추가 VHD 간에 동기식으로 데이터를 복제하기 위해 이러한 구성이 필요합니다. 두 노드를 구성하는 단계는 다음과 같습니다.

![그림 44: SIOS DataKeeper 관리 및 구성 도구][sap-ha-guide-figure-3036]

***그림 44:** SIOS DataKeeper 관리 및 구성 도구*

DataKeeper 관리 및 구성 도구를 시작한 다음 **서버 연결**을 선택합니다. (그림 44에서 이 옵션은 빨간색 원으로 표시됩니다.)

![그림 45: 관리 및 구성 도구가 연결할 첫 번째 노드의 이름 및 TCP/IP 주소 삽입 후 두 번째 단계에서 두 번째 노드에 대해 같은 작업 수행][sap-ha-guide-figure-3037]

***그림 45:** 관리 및 구성 도구가 연결할 첫 번째 노드의 이름 및 TCP/IP 주소 삽입 후 두 번째 단계에서 두 번째 노드에 대해 같은 작업 수행*

다음 단계는 두 노드 간에 복제 작업을 만드는 것입니다.

![그림 46: 복제 작업 만들기][sap-ha-guide-figure-3038]

***그림 46:** 복제 작업 만들기*

마법사에서 복제 작업을 만드는 과정을 안내합니다.

![그림 47: 복제 작업의 이름 정의][sap-ha-guide-figure-3039]

***그림 47:** 복제 작업의 이름 정의*

![그림 48: 현재 원본 노드에 해당하는 노드에 대한 기본 데이터 정의][sap-ha-guide-figure-3040]

***그림 48:** 현재 원본 노드에 해당하는 노드에 대한 기본 데이터 정의*

첫 번째 단계에서 원본 노드의 이름, TCP/IP 주소 및 디스크 볼륨을 정의해야 합니다. 두 번째 단계는 대상 노드를 정의하는 것입니다. 앞서 설명한 것처럼 대상 노드의 이름, TCP/IP 주소 및 디스크 볼륨을 정의해야 합니다.

![그림 49: 현재 대상 노드에 해당하는 노드에 대한 기본 데이터 정의][sap-ha-guide-figure-3041]

***그림 49:** 현재 대상 노드에 해당하는 노드에 대한 기본 데이터 정의*

다음으로 압축 알고리즘을 정의합니다. 예제에서는 복제 스트림을 압축하는 것이 좋습니다. 특히 재동기화 상황에서는 복제 스트림을 압축하면 재동기화 시간을 크게 단축할 수 있습니다. 압축에는 가상 컴퓨터의 CPU 및 RAM 리소스가 사용됩니다. 압축 속도가 증가하면 사용되는 CPU 리소스 양도 증가합니다. 나중에 이 설정을 조정할 수 있습니다.

확인해야 하는 또 다른 설정은 복제가 비동기식 또는 동기식 중에서 어떤 방식으로 발생하는가 입니다. *SAP ASCS/SCS 구성을 보호할 때 동기 복제를 사용해야 합니다*.  

![그림 50: 복제 세부 정보 정의][sap-ha-guide-figure-3042]

***그림 50:** 복제 세부 정보 정의*

마지막 단계는 복제 작업에 의해 복제되는 볼륨을 Windows Server 장애 조치 클러스터링 클러스터 구성에 공유 디스크로 나타낼지 여부를 정의하는 것입니다. SAP ASCS/SCS 구성의 경우 Windows 클러스터가 복제된 볼륨을 클러스터 볼륨으로 사용할 수 있는 공유 디스크로 인식하도록 **예**를 선택합니다.

![그림 51: 복제된 볼륨을 클러스터 볼륨으로 설정하려면 **예** 선택][sap-ha-guide-figure-3043]

***그림 51:** 복제된 볼륨을 클러스터 볼륨으로 설정하려면 **예** 선택*

볼륨을 만든 후 DataKeeper 관리 및 구성 도구에서 복제 작업 활성화되어 있음을 보여 줍니다.

![그림 52: SAP ASCS/SCS 공유 디스크에 대한 DataKeeper 동기 미러링이 활성화됨][sap-ha-guide-figure-3044]

***그림 52:** SAP ASCS/SCS 공유 디스크에 대한 DataKeeper 동기 미러링이 활성화됨*

이제 장애 조치 클러스터 관리자에는 그림 53과 같이 디스크가 DataKeeper 디스크로 표시됩니다.

![그림 53: 장애 조치 클러스터 관리자가 DataKeeper에서 복제한 디스크를 표시함][sap-ha-guide-figure-3045]

***그림 53:** 장애 조치 클러스터 관리자가 DataKeeper에서 복제한 디스크를 표시함*

## <a name="a-namea06f0b498a7a42bf8b0dc12026c5746ba-install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> SAP NetWeaver 시스템 설치
설정은 사용하는 DBMS 시스템에 따라 다르므로 DBMS 설정에 대해서는 설명하지 않습니다. 그러나 다른 DBMS 공급업체가 Azure에 대해 지원하는 기능으로 DBMS의 고가용성 문제가 해결된다고 가정합니다. 예로 SQL Server 및 Oracle용 Oracle Data Guard에 대한 AlwaysOn 또는 데이터베이스 미러링을 들 수 있습니다. 이 문서에서 사용하는 시나리오에서는 DBMS에 대해 추가 보호를 적용하지 않았습니다.

Azure에서 여러 다른 DBMS 서비스가 이러한 종류의 클러스터형 SAP ASCS/SCS 구성과 상호 작용할 경우 특별한 고려 사항은 없습니다.

> [!NOTE]
> SAP NetWeaver ABAP 시스템, Java 시스템 및 ABAP+Java 시스템의 설치 절차는 거의 동일합니다. 가장 중요한 차이점은 SAP ABAP 시스템에 ASCS 인스턴스가 하나 있다는 것입니다. SAP Java 시스템에는 하나의 SCS 인스턴스가 있습니다. SAP ABAP+Java 시스템에서는 동일한 Microsoft 장애 조치 클러스터 그룹에 하나의 ASCS 인스턴스와 하나의 SCS 인스턴스가 실행되고 있습니다. 각 SAP NetWeaver 설치 스택에 대한 설치 차이점은 명시적으로 언급됩니다. 다른 모든 부분은 동일하다고 가정할 수 있습니다.  
> 
> 

### <a name="a-name31c6bd4f51df40579fdf3fcbc619c170a-install-sap-with-a-highavailability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> 고가용성 ASCS/SCS 인스턴스에 SAP 설치
> [!IMPORTANT]
> DataKeeper 미러된 볼륨에 페이지 파일을 배치하지 마세요. DataKeeper는 미러된 볼륨을 지원하지 않습니다. Azure Virtual Machines의 임시 드라이브 D에 페이지 파일을 둘 수 있습니다. 이것이 기본 설정입니다. Windows 페이지 파일을 Azure Virtual Machines의 D 드라이브로 이동하지 않은 경우 이동합니다.
> 
> 

#### <a name="a-namea97ad604909444fea364f89cb39bf097a-create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> 클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기
먼저 Windows DNS 관리자에서 ASCS/SCS 인스턴스의 가상 호스트 이름에 대한 DNS 항목을 만듭니다. 그런 다음 가상 호스트 이름에 할당된 IP 주소를 정의합니다.

> [!NOTE]
> ASCS/SCS 인스턴스의 가상 호스트 이름에 할당하는 IP 주소는 Azure Load Balancer에 할당한 IP 주소와 동일해야 합니다(<*SID*>-lb-ascs).  
> 
> 

가상 SAP ASCS/SCS 호스트 이름의 IP 주소(pr1-ascs-sap)는 Azure Load Balancer의 IP 주소(pr1-lb-ascs)와 같습니다.

Azure에서 하나의 SAP 장애 조치 클러스터 역할만 하나의 Windows Server 장애 조치 클러스터에서 실행할 수 있습니다. 예를 들어 ABAP 시스템용 ASCS 인스턴스가 하나 있고 Java 시스템용 SCS 인스턴스가 하나 있습니다. ABAP+Java의 경우에는 하나의 ASCS 인스턴스와 하나의 SCS 인스턴스가 있습니다.

> [!NOTE]
> SAP 설치 가이드에 설명된 다중 SID 클러스터링([SAP 설치 가이드][sap-installation-guides] 참조)은 현재 Azure에서 작동하지 않습니다.
> 
> 

![그림 54: SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의][sap-ha-guide-figure-3046]

***그림 54:** SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의*

이 항목은 그림 55와 같이 도메인 아래의 DNS 관리자에 있습니다.

![그림 55: SAP ASCS/SCS 클러스터 구성에 대한 새 가상 이름 및 TCP/IP 주소][sap-ha-guide-figure-3047]

***그림 55:** SAP ASCS/SCS 클러스터 구성에 대한 새 가상 이름 및 TCP/IP 주소*

#### <a name="a-nameeb5af918b42f4803bb50eff41f84b0b0a-install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> SAP 첫 번째 클러스터 노드 설치
SAP 첫 번째 클러스터를 설치하려면 클러스터 노드 A에서 첫 번째 클러스터 노드 옵션을 실행합니다(예: **pr1-ascs-0** 호스트).

Azure 내부 부하 분산 장치에 대한 기본 포트를 유지하려는 경우 다음을 선택합니다.

* **ABAP 시스템**: **ASCS** 인스턴스 번호 **00**
* **Java 시스템**: **SCS** 인스턴스 번호 **01**
* **ABAP+JAVA 시스템**: **ASCS** 인스턴스 번호 **00** 및 **SCS** 인스턴스 번호 **01**

먼저 ABAP ASCS 인스턴스에 대해 00, Java SCS 인스턴스에 대해 01 이외의 인스턴스 번호를 사용하려는 경우 [Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경][sap-ha-guide-8.9]에 설명된 대로 Azure 내부 부하 분산 장치 기본 부하 분산 규칙을 먼저 변경해야 합니다.

그런 다음 일반적인 SAP 설치 설명서에서 설명되지 않은 몇 가지 단계를 수행합니다.

> [!NOTE]
> SAP 설치 설명서에는 첫 번째 ASCS/SCS 클러스터 노드를 설치하는 방법을 설명합니다.
> 
> 

#### <a name="a-namee4caaab2e90f4f2cbc842cd2e12a9556a-modify-the-sap-profile-of-the-ascsscs-ipowershellnstance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS i```powershellnstance의 SAP 프로필 수정
새 프로필 매개 변수를 추가해야 합니다. 이 프로필 매개 변수는 연결이 너무 오랫동안 유휴 상태일 때 SAP 작업 프로세스와 큐에 넣기 서버 사이의 연결이 닫히지 않도록 합니다. 이 문서의 [SAP ASCS/SCS 인스턴스의 클러스터 노드 둘 다에 대한 레지스트리 항목 추가][sap-ha-guide-8.11]에 문제 시나리오가 나와 있습니다. 이 섹션에서는 몇 가지 기본 TCP/IP 연결 매개 변수에 대한 두 가지 변경 내용도 설명되어 있습니다. 두 번째 단계에서는 연결이 Azure 부하 부산 장치의 유휴 임계값에 도달하지 않게 **keep_alive** 신호를 보내도록 큐에 추가 서버를 설정해야 합니다.

SAP ASCS/SCS 인스턴스에 프로필에 이 프로필 매개 변수를 추가합니다.

```
enque/encni/set_so_keepalive = true
```
이 예제에서 경로는 다음과 같습니다.

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

예: SAP SCS 인스턴스 프로필 및 해당 경로

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

#### <a name="a-name10822f4f32e74871b63a9b86c76ce761a-add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> 프로브 포트 추가
내부 부하 분산 장치의 프로브 기능을 사용하여 전체 클러스터 구성이 부하 분산 장치에서 작동하도록 합니다. 일반적으로 Azure 내부 부하 분산 장치는 참여하는 가상 컴퓨터 간에 동일하게 들어오는 작업 부하를 분산합니다. 그러나 하나의 인스턴스만 활성 상태가 되므로 일부 클러스터 구성에서는 작동하지 않습니다. 다른 인스턴스는 수동 상태이므로 워크로드를 받아들일 수 없습니다. 검색 기능은 Azure 내부 부하 분산 장치가 활성 인스턴스에만 작업을 할당할 때 도움이 됩니다. 검색 기능을 사용하면 내부 부하 분산 장치는 활성 상태인 인스턴스를 감지한 후 해당 인스턴스만 워크로드 대상으로 지정할 수 있습니다.

먼저 다음 PowerShell 명령을 사용하여 현재 **ProbePort** 설정을 확인합니다. 이 명령은 클러스터 구성의 가상 컴퓨터 중 하나에서 실행합니다.

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![그림 56: 클러스터 구성의 프로브 포트는 기본적으로 0임][sap-ha-guide-figure-3048]

***그림 56:** 클러스터 구성의 프로브 포트는 기본적으로 0임*

그런 다음 프로브 포트를 정의합니다. 프로브 포트 기본값은 0입니다. 예제에서는 프로브 포트 **62300**을 사용합니다.

포트 번호는 SAP Azure Resource Manager 템플릿에서 정의됩니다. PowerShell에서 포트 번호를 할당할 수 있습니다.

먼저 SAP 가상 호스트 이름 클러스터 리소스 **SAP WAC IP** 가져옵니다.

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  }
```

그런 다음 프로브 포트를 **62300**으로 설정

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

변경 내용을 활성화하려면 **SAP PR1** 클러스터 역할을 중지했다가 시작합니다.

**SAP PR1** 클러스터 역할을 온라인으로 전환한 후에 **ProbePort**가 새 값으로 설정되어 있는지 확인합니다.

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![그림 57: 새 값을 설정한 후 클러스터 포트 검색][sap-ha-guide-figure-3049]

***그림 57:** 새 값을 설정한 후 클러스터 포트 검색*

**ProbePort**가 **62300**으로 설정됩니다. 이제 **ascsha-dbas** 등의 다른 호스트에서 **\\\ascsha-clsap\sapmnt** 파일 공유에 액세스할 수 있습니다.

### <a name="a-name85d78414b21d409792b634d8bcb724b7a-install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> 데이터베이스 인스턴스 설치
데이터베이스 인스턴스를 설치하려면 SAP 설치 설명서에 설명된 프로세스를 따릅니다.

### <a name="a-name8a276e16f5074071b829cdc0a4d36748a-install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 두 번째 클러스터 노드 설치
두 번째 클러스터 노드를 설치하려면 SAP 설치 가이드의 단계를 따릅니다.

### <a name="a-name094bc89531d4447191cc1513b64e406aa-change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> SAP ERS Windows 서비스 인스턴스의 시작 유형 변경
두 클러스터 노드에서 SAP ERS(큐에 넣기 복제 서버) Windows 서비스의 시작 유형을 **자동(지연된 시작)**으로 변경합니다.

![그림 58: SAP ERS의 서비스 유형을 지연된 자동으로 변경][sap-ha-guide-figure-3050]

***그림 58:** SAP ERS의 서비스 유형을 지연된 자동으로 변경*

### <a name="a-name2477e58fc5a74a5d9ae37b91022cafb5a-install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP 기본 응용 프로그램 서버 설치
PAS(기본 응용 프로그램 서버)를 호스트하도록 지정한 가상 컴퓨터에 PAS 인스턴스 <*SID*>-di-0를 설치합니다. Azure 또는 DataKeeper 사양과는 관련이 없습니다.

### <a name="a-name0ba4a6c1cc374bcfa8dc025de4263772a-install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP 추가 응용 프로그램 서버 설치
SAP 응용 프로그램 서버를 호스트하도록 지정한 모든 가상 컴퓨터에 SAP AAS(추가 응용 프로그램 서버)를 설치합니다. 예로 <*SID*>-di-1 ~ <*SID*>-di-<n>을 들 수 있습니다.

## <a name="a-name18aa2b9d92d24c0e8ddd5acaabda99e9a-test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 복제 테스트
장애 조치 클러스터 관리자 및 SIOS DataKeeper UI를 사용하여 SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 디스크 복제를 쉽게 테스트하고 모니터링할 수 있습니다.

### <a name="a-name65fdef0f9f9441f9b314ea45bbfea445a-sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS 인스턴스가 클러스터 노드 A에서 실행되고 있습니다.
**SAP WAC** 클러스터 그룹이 클러스터 노드 A(예: **ascsha-clna**)에서 실행되고 있습니다. **SAP WAC** 클러스터 그룹에 속하고 ASCS/SCS 인스턴스에 사용되는 공유 디스크 S를 클러스터 노드 A에 할당합니다.

![그림 59: 장애 조치 클러스터 관리자: SAP <*SID*> 클러스터 그룹이 클러스터 노드 A에서 실행되고 있음][sap-ha-guide-figure-5000]

***그림 59:** 장애 조치 클러스터 관리자: SAP <*SID*> 클러스터 그룹이 클러스터 노드 A에서 실행되고 있음*

SIOS DataKeeper UI를 사용하여 공유 디스크 데이터가 클러스터 노드 A의 원본 볼륨 S에서 클러스터 노드 B의 대상 볼륨 드라이브 S에 동기식으로 복제되는 것을 볼 수 있습니다. 예로 **ascsha-clna [10.0.0.41]****ascsha-clnb [10.0.0.42]**를 들 수 있습니다.

![그림 60: SIOS DataKeeper에서 클러스터 노드 A로부터 클러스터 노드 B로 로컬 볼륨 복제][sap-ha-guide-figure-5001]

***그림 60:** SIOS DataKeeper에서 클러스터 노드 A로부터 클러스터 노드 B로 로컬 볼륨 복제*

### <a name="a-name5e959fa98fcd49e5a12c37f6ba07b916a-failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> 노드 A에서 노드 B로 장애 조치
이러한 옵션 중 하나를 사용하여 클러스터 노드 A에서 클러스터 노드 B로 SAP <*SID*> 클러스터 그룹의 장애 조치를 시작할 수 있습니다.

* 장애 조치(failover) 클러스터 관리자 사용  
* 장애 조치 클러스터 PowerShell 사용
  ```powershell
  Move-ClusterGroup -Name "SAP WAC"
  ```
* Windows 게스트 운영 체제 내에서 클러스터 노드 A 다시 시작(노드 A에서 노드 B로의 SAP <*SID*> 클러스터 그룹의 자동 장애 조치 시작)  
* Azure Portal에서 클러스터 노드 A 다시 시작(노드 A에서 노드 B로의 SAP <*SID*> 클러스터 그룹의 자동 장애 조치 시작)  
* Azure PowerShell을 사용하여 클러스터 노드 A 다시 시작(노드 A에서 노드 B로의 SAP <*SID*> 클러스터 그룹의 자동 장애 조치 시작)
  
  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

장애 조치 후에 SAP <*SID*> 클러스터 그룹이 클러스터 노드 B(예: **ascsha-clna**)에서 실행되고 있습니다.

![그림 61: 장애 조치 클러스터 관리자에서 SAP <*SID*> 클러스터 그룹이 클러스터 노드 B에서 실행되고 있음][sap-ha-guide-figure-5002]

***그림 61**: 장애 조치 클러스터 관리자에서 SAP <*SID*> 클러스터 그룹이 클러스터 노드 B에서 실행되고 있음*

이제 공유 디스크는 클러스터 노드 B에 탑재됩니다. SIOS DataKeeper는 클러스터 노드 B의 원본 볼륨 드라이브 S:에서 클러스터 노드 A의 대상 볼륨 드라이브로 데이터를 복제합니다(예: **ascsha-clnb [10.0.0.42]**에서 **ascsha-clna [10.0.0.41]**로).

![그림 62: SIOS DataKeeper에서 클러스터 노드 B로부터 클러스터 노드 A로 로컬 볼륨 복제][sap-ha-guide-figure-5003]

***그림 62:** SIOS DataKeeper에서 클러스터 노드 B로부터 클러스터 노드 A로 로컬 볼륨 복제*

<!---HONumber=Oct16_HO2-->


