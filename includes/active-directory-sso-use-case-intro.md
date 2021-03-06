점차 쉽게 사용할 수 있도록 클라우드 기술 및 도구가 발전하고 있으므로 조직에서는 생산성을 위해 [SaaS(Software as a Service)](https://azure.microsoft.com/overview/what-is-saas/) 응용 프로그램을 더 많이 사용하고 있습니다. SaaS 앱 수가 증가함에 따라 관리자가 계정 및 액세스 권한을 관리하고 사용자가 다양한 암호를 기억하는 것이 어려워지고 있습니다. 이러한 응용 프로그램을 개별적으로 관리하면 추가 작업이 필요하고 보안 수준이 저하됩니다.


- 많은 암호를 관리해야 하는 직원은 암호를 기록해 놓거나 많은 계정에서 동일한 암호를 사용하는 등, 보안 수준이 낮은 방법을 사용하여 암호를 기억하는 경향이 있습니다.

- 직원이 입사하거나 퇴사하는 경우 해당 직원의 모든 계정을 개별적으로 프로비전하거나 프로비전 해제해야 합니다.

- 또한 직원은 IT를 거치지 않고 작업에 SaaS 앱을 사용하기 시작할 수 있습니다. 즉, 직원이 IT 관리자의 승인과 모니터링 없이 시스템에서 자신의 계정을 만듭니다.

이러한 모든 문제에 대한 해결책은 SSO(Single Sign-On)입니다. SSO는 여러 앱을 관리하고 사용자에게 일관된 로그온 환경을 제공하는 가장 간단한 방법입니다. Azure Active Directory(Azure AD)는 강력한 SSO 솔루션을 제공하며 신속하게 새 앱을 설치하고 사용자 프로비저닝을 시작할 수 있도록 하는 자습서와 함께 미리 통합된 많은 응용 프로그램을 보유하고 있습니다.


## Azure Active Directory가 앱과 통합되는 방식  

Azure AD를 사용하여 앱 및 프로비전된 계정을 통합할 수 있습니다. 이 작업은 두 가지 방법 중 하나를 통해 수행할 수 있습니다.

- 앱이 응용 프로그램 갤러리에서 미리 통합된 경우 해당 포털을 통해 앱을 설치하고 SSO를 허용하도록 설정을 구성할 수 있습니다. 모든 갤러리 앱의 경우 응용 프로그램 갤러리와 Azure 포털에서 제시하는 간단한 단계별 지침을 따라 Single Sign-On을 사용하도록 설정하여 시작할 수 있습니다.

- 앱이 갤러리에 없는 경우에도 Azure AD에서 대부분의 앱을 사용자 지정 앱으로 설치할 수 있습니다. 이렇게 하려면 구성하는 데 기술적인 전문성이 조금 더 필요합니다. SAML 2.0을 페더레이션된 앱으로 지원하는 모든 응용 프로그램 또는 HTML 기반 로그인 페이지가 암호 SSO 앱으로 있는 모든 응용 프로그램을 추가할 수 있습니다.

사용자가 IT에서 관리하지 않는 SaaS 앱에 대한 자신의 계정을 만든 경우 [Cloud App Discovery](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) 도구가 해결책을 제공합니다. 이 도구는 웹 트래픽을 모니터링하여 조직 전체에서 사용되고 있는 앱과 이러한 각 앱을 사용하는 사용자 수를 식별합니다. IT는 이 정보를 사용하여 사용자가 선호하는 앱을 파악하고 SSO를 위해 Azure AD에 통합할 앱을 결정할 수 있습니다.

Azure AD에 앱을 통합할 때 사용자의 설정된 응용 프로그램 ID를 개별 Azure AD ID에 매핑할 수 있습니다.

<!---HONumber=AcomDC_0727_2016-->