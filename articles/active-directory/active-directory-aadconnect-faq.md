<properties
	pageTitle="Azure AD Connect: FAQ | Microsoft Azure"
	description="이 페이지에는 Azure AD Connect에 대해 자주 묻는 질문과 대답이 있습니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="billmath"/>

# Azure AD Connect FAQ

## 일반 설치
**Q: Azure AD 전역 관리자가 2FA를 사용하도록 설정한 경우 설치가 작동하나요?** 2016년 2월 빌드에서 지원됩니다.

**Q: Azure AD Connect를 무인 설치하는 방법이 있나요?** 설치 마법사를 사용하여 Azure AD Connect 설치만 지원됩니다. 무인 및 자동 설치는 지원되지 않습니다.

**Q: 하나의 도메인에 연결할 수 없는 포리스트가 있습니다. Azure AD Connect를 설치하려면 어떻게 하나요?** 2016년 2월 빌드에서 지원됩니다.

**Q: AD DS 상태 에이전트는 서버 코어에서 작동하나요?** 예. 에이전트를 설치한 후 다음 PowerShell commandlet을 사용하여 등록 프로세스를 완료할 수 있습니다.

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## 네트워크
**Q: 방화벽, 네트워크 장치 또는 네트워크에서 열려 있는 상태로 유지할 수 있는 최대 시간 연결을 제한하는 다른 요소가 있습니다. Azure AD Connect를 사용하는 경우 클라이언트 쪽 기간 내 클라이언트 쪽 시간 제한 임계값은 얼마나 길어야 합니까?** 모든 네트워킹 소프트웨어, 물리적 장치 또는 열린 상태로 둘 수 있는 최대 시간 연결을 제한하는 다른 것은 Azure AD Connect 클라이언트를 설치한 서버와 Azure Active Directory 간의 연결에 대해 적어도 5분(300초)의 임계값을 사용해야 합니다. 이는 이전에 릴리스된 모든 Microsoft ID 동기화 도구에 적용됩니다.

**Q: SLD(단일 레이블 도메인)가 지원되나요?** 아니요 Azure AD Connect는 SLD를 사용하는 온-프레미스 포리스트/도메인을 지원하지 않습니다.

**Q: "마침표"를 포함하는 NetBios 이름이 지원되나요?** 아니요, Azure AD Connect는 NetBios 이름에 마침표 "."를 포함하는 온-프레미스 포리스트/도메인을 지원하지 않습니다.

## 페더레이션
**Q: 내 Office 365 인증서를 갱신하라는 메일을 받는 경우 어떻게 해야 하나요?** 인증서를 갱신하는 방법은 [인증서 갱신](active-directory-aadconnect-o365-certs.md) 항목에 설명된 참고 자료를 사용하세요.

**Q: O365 신뢰 당사자에 대해 "신뢰 당사자 자동 업데이트"를 설정했습니다. 토큰 서명 인증서가 자동으로 롤오버될 때 어떤 조치를 취해야 하나요?** [인증서 갱신](active-directory-aadconnect-o365-certs.md) 문서에 설명된 참고 자료를 사용하세요.

## Environment
**Q: Azure AD Connect를 설치한 후에 서버 이름을 변경하는 것이 지원되나요?** 아니요. 서버 이름을 변경하면 동기화 엔진이 SQL 데이터베이스에 연결할 수 없게 되고 서비스를 시작할 수 없게 됩니다.

## ID 데이터
**Q: Azure AD의 UPN(userPrincipalName) 특성이 온-프레미스 UPN가 일치하지 않습니다. 왜 그런가요?** 아래 문서를 참조하세요.

- [Office 365, Azure, 또는 Intune의 사용자 이름이 온-프레미스 UPN 또는 대체 로그인 ID와 일치하지 않습니다(영문).](https://support.microsoft.com/ko-KR/kb/2523192)
- [다른 페더레이션된 도메인을 사용하기 위해 사용자 계정의 UPN을 변경한 후에 Azure Active Directory 동기화 도구에서 변경 사항이 동기화되지 않습니다(영문).](https://support.microsoft.com/ko-KR/kb/2669550)

[Azure AD Connect 동기화 서비스 기능](active-directory-aadconnectsyncservice-features.md)에 설명된 대로 동기화 엔진이 userPrincipalName을 업그레이드할 수 있도록 Azure AD를 구성할 수도 있습니다.

## 사용자 지정 구성
**Q: Azure AD Connect에 대한 PowerShell cmdlet 설명서는 어디에 있나요?** 이 사이트에 설명되어 있는 cmdlet을 제외하고, Azure AD Connect에 나오는 다른 PowerShell cmdlet은 고객 사용이 지원되지 않습니다.

**Q: *Synchronization Service Manager*의 "서버 내보내기/서버 가져오기"를 사용하여 서버 간에 구성을 이동할 수 있나요?** 아니요. 이 옵션은 모든 구성 설정을 가져오는 것이 아니므로 사용하지 말아야 합니다. 그 대신 마법사를 사용하여 두 번째 서버에 기본 구성을 만들고 동기화 규칙 편집기를 사용하여 PowerShell 스크립트를 생성하여 서버 간에 사용자 지정 규칙을 이동할 수 있습니다. [활성 서버에서 준비 서버로 사용자 지정 구성 이동](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server)을 참조하세요.

## 문제 해결
**Q: Azure AD Connect에 대한 도움을 받으려면 어떻게 합니까?**

[Microsoft KB(기술 자료) 검색](https://www.microsoft.com/ko-KR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Microsoft KB(기술 자료)에서 Azure AD Connect 지원에 대한 일반적인 고장 수리 문제에 대한 기술 솔루션을 검색하세요.

[Microsoft Azure Active Directory 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=WindowsAzureAD)

- 커뮤니티에서 기술 질문 및 대답을 검색하고 찾아보거나 [여기](https://social.msdn.microsoft.com/Forums/azure/ko-KR/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)를 클릭하여 직접 질문을 할 수 있습니다.

[Azure AD Connect 고객 지원](https://manage.windowsazure.com/?getsupport=true)

- Azure 포털을 통해 지원을 받으려면 이 링크를 사용합니다.

<!---HONumber=AcomDC_0810_2016-->