# 개요
## [시작](get-started.md)
## [인증서](sap-certifications.md)
# Azure(큰 인스턴스)에서 SAP HANA 사용
## 개요
### [SAP HANA on Azure(대규모 인스턴스)란?](hana-overview-architecture.md)
### [약관을 알고 있습니다.](hana-know-terms.md)
### [인증](hana-certification.md)
### [HLI에 사용 가능한 SKU](hana-available-skus.md)
### [크기 조정](hana-sizing.md)
### [온보딩 요구 사항](hana-onboarding-requirements.md)
### [SAP HANA 데이터 계층화 및 확장 노드](hana-data-tiering-extension-nodes.md)
### [작업 모델 및 책임](hana-operations-model.md)
## 아키텍처
### [일반 아키텍처](hana-architecture.md)
### [네트워크 아키텍처](hana-network-architecture.md)
### [Storage 아키텍처](hana-storage-architecture.md)
### [HLI 지원 시나리오](hana-supported-scenario.md)
## 인프라 및 연결
### [HLI 배포](hana-overview-infrastructure-connectivity.md)
### [Azure VM을 HANA 큰 인스턴스에 연결](hana-connect-azure-vm-large-instances.md)
### [VNet을 HANA 큰 인스턴스 ExpressRoute에 연결](hana-connect-vnet-express-route.md)
### [추가 네트워크 요구 사항](hana-additional-network-requirements.md)
## SAP HANA 설치
### [구성 유효성 검사](hana-installation.md)
### [샘플 HANA 설치](hana-example-installation.md)
## 고가용성 및 재해 복구
### [옵션 및 고려 사항](hana-overview-high-availability-disaster-recovery.md)
### [Backup 및 복원](hana-backup-restore.md)
### [원칙 및 준비](hana-concept-preparation.md)
### [재해 복구 장애 조치(failover) 프로시저](hana-failover-procedure.md)
## 문제 해결 및 모니터링
### [모니터링 HLI](troubleshooting-monitoring.md)
### [HANA 쪽에서 모니터링 및 문제 해결](hana-monitor-troubleshoot.md)
## 방법
### [STONITH로 HA 설치](ha-setup-with-stonith.md)
### [Type II SKU용 OS 백업](os-backup-type-ii-skus.md)
### [HANA 대형 인스턴스를 위한 OS 업그레이드](os-upgrade-hana-large-instance.md)
### [SUSE Linux용 SMT 서버 설정](hana-setup-smt.md)
# Azure Virtual Machines의 SAP HANA
## [단일 인스턴스 SAP HANA 설치](hana-get-started.md)
## [S/4 HANA 또는 BW/4 HANA SAP CAL 배포 가이드](cal-s4h.md)
## [Azure에서 SAP HANA 인프라 구성 및 작업](hana-vm-operations.md)
## Azure Virtual Machines의 SAP HANA 가용성
### [Azure의 SAP HANA 가용성 개요](sap-hana-availability-overview.md)
### [단일 Azure 지역 내 Azure의 SAP HANA 가용성](sap-hana-availability-one-region.md)
### [Azure 지역 간 Azure의 SAP HANA 가용성](sap-hana-availability-across-regions.md)
### [SLES에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability.md)
### [RHEL에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability-rhel.md)
### [SLES에서 SAP HANA 스케일 아웃 및 Pacemaker 문제 해결](hana-vm-troubleshoot-scale-out-ha-on-sles.md)
## [SAP HANA 백업 개요](sap-hana-backup-guide.md)
## [SAP HANA 파일 수준 백업](sap-hana-backup-file-level.md)
## [SAP HANA 저장소 스냅숏 백업](sap-hana-backup-storage-snapshots.md)
# Azure Virtual Machines의 SAP NetWeaver 및 Business One
## [Azure Virtual Machines의 SAP Business One](business-one-azure.md)
## [Windows/SQL Server SAP CAL 배포 가이드에 SAP IDES](cal-ides-erp6-erp7-sp3-sql.md)
## [Azure Linux VM의 SAP NetWeaver](suse-quickstart.md)
## [Azure에서 SAP NetWeaver 계획 및 구현](planning-guide.md)
## [SAP NetWeaver 배포 가이드](deployment-guide.md)
## [Azure용 SAP LaMa 커넥터](lama-installation.md)
## SAP 워크로드에 대한 DBMS 배포 가이드
### [SAP 워크로드에 대한 일반적인 Azure Virtual Machines DBMS 배포](dbms_guide_general.md)
### [SAP 워크로드에 대한 SQL Server Azure Virtual Machines DBMS 배포](dbms_guide_sqlserver.md)
### [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](dbms_guide_oracle.md)
### [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](dbms_guide_ibm.md)
### [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](dbms_guide_sapase.md)
### [Azure에서 SAP MaxDB, liveCache 및 콘텐츠 서버 배포](dbms_guide_maxdb.md)
### Azure Virtual Machines의 SAP HANA 가용성
### [Azure의 SAP HANA 가용성 개요](sap-hana-availability-overview.md)
### [단일 Azure 지역 내 Azure의 SAP HANA 가용성](sap-hana-availability-one-region.md)
### [Azure 지역 간 Azure의 SAP HANA 가용성](sap-hana-availability-across-regions.md)
## Windows 및 Linux의 HA(고가용성)
### [개요](sap-high-availability-guide-start.md)
### 고가용성 아키텍처
#### [HA 아키텍처 및 시나리오](sap-high-availability-architecture-scenarios.md)
#### [고가용성 아키텍처 및 시나리오](sap-higher-availability-architecture-scenarios.md)
#### [(A)SCS 인스턴스에 공유 디스크를 사용하는 Windows의 HA](sap-high-availability-guide-wsfc-shared-disk.md)
#### [(A)SCS 인스턴스에 SOFS 파일 공유를 사용하는 Windows의 HA](sap-high-availability-guide-wsfc-file-share.md)
#### [(A)SCS 인스턴스의 SUSE Linux에 대한 HA](high-availability-guide-suse.md)
#### [(A)SCS 인스턴스용 Red Hat Enterprise Linux의 HA](high-availability-guide-rhel.md)
### Azure 인프라 준비
#### [(A)SCS 인스턴스에 공유 디스크를 사용하는 Windows](sap-high-availability-infrastructure-wsfc-shared-disk.md)
#### [(A)SCS 인스턴스에 SOFS 파일 공유를 사용하는 Windows](sap-high-availability-infrastructure-wsfc-file-share.md)
#### [SLES의 Azure VM에서 NFS에 대한 고가용성](high-availability-guide-suse-nfs.md)
#### [SAP NetWeaver에 대한 Red Hat Enterprise Linux에 있는 Azure VM의 GlusterFS](high-availability-guide-rhel-glusterfs.md)
#### [SLES의 Pacemaker](high-availability-guide-suse-pacemaker.md)
#### [RHEL의 Pacemaker](high-availability-guide-rhel-pacemaker.md)
### SAP 설치
#### [(A)SCS 인스턴스에 공유 디스크를 사용하는 Windows](sap-high-availability-installation-wsfc-shared-disk.md)
#### [(A)SCS 인스턴스에 SOFS 파일 공유를 사용하는 Windows](sap-high-availability-installation-wsfc-file-share.md)
#### [(A)SCS 인스턴스에 NFS를 사용하는 SUSE Linux](high-availability-guide-suse.md)
#### [Red Hat Enterprise Linux에서 SAP NetWeaver에 대한 고가용성](high-availability-guide-rhel.md)
### SAP 다중 SID
#### [(A)SCS 인스턴스에 공유 디스크를 사용하는 Windows](sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
#### [(A)SCS 인스턴스에 SOFS 파일 공유를 사용하는 Windows](sap-ascs-ha-multi-sid-wsfc-file-share.md)
##  [SAP 재해 복구를 위한 Azure Site Recovery](../../../site-recovery/site-recovery-workload.md#protect-sap)
# AAD SAP ID 통합 및 Single-Sign-On
## [SAP 클라우드와 통합](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD와 SAP 클라우드 플랫폼 ID 인증 통합](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [SAP 클라우드 플랫폼에 Single-Sign-On 설정](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD와 SAP NetWeaver 통합](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD와 SAP Business ByDesign 통합](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD와 SAP HANA DBMS 통합](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
##[Azure AD로 SAP Fiori Launchpad SAML Single Sign-On](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad)
# SAP에 Azure 서비스 통합
## [Power BI Desktop에서 SAP HANA 사용](https://docs.microsoft.com/power-bi/desktop-sap-hana)
## [DirectQuery 및 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
## [Power BI Desktop에서 SAP BW 커넥터 사용](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector)
## [Azure Data Factory는 SAP HANA 및 Business Warehouse 데이터 통합을 제공합니다.](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)
# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/)

