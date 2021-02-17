*** ** * ** ***

resumo: Saiba mais sobre os métodos de autenticação disponíveis para usuários finais no OutSystems.
Tags: Support\-Mobile\_Apps; webapps de suporte
-----------------------------------------------------------------------------------------------------------------------------------------------------

Autenticação de Usuários
========================

Esta seção se aplica apenas à autenticação de **usuários finais — os** usuários de seus aplicativos OutSystems.   
A autenticação de **usuários de TI** , como desenvolvedores e administradores de TI, é [configurada no Lifetime](../../../../managing-the-applications-lifecycle/secure-the-applications/use-an-external-authentication-provider.md).

Quando você começa a desenvolver um novo módulo, ele tem a lógica interna para autenticação do usuário final. O OutSystems vem com seis métodos de autenticação distintos: Interno, Active Directory, LDAP \(Lightweight Directory Access Protocol\), SAML 2\.0 \(Security Assertion Markup Language\), Azure AD \(Azure Active Directory\) e Okta.

Interno
: O método de autenticação padrão. As informações do usuário final são armazenadas no banco de dados OutSystems. As credenciais não são armazenadas, mas uma função de hash criptográfica é calculada usando as credenciais e somente seu resultado é armazenado. Quando o usuário final tenta fazer login, a função hash é calculada novamente e seu resultado é comparado com o que está no banco de dados.

Active Directory
: usa seu Active Directory para autenticar os usuários finais. Disponível apenas em instalações locais.   
Verifique como [configurar a autenticação do Active Directory](configure-active-directory.md).

LDAP
: Autentica o usuário final em relação ao servidor LDAP, usando o LDAP configurado para o Active Directory ou o LDAP padrão.   
Verifique como [configurar a autenticação LDAP](configure-ldap.md).

SAML 2\.0
: usa autenticação baseada em SAML para autenticar os usuários finais com Single Sign\-On \(SSO\) fornecido por empresas comerciais do Provedor de Identidade, como OneLogin e PingFederated.   
Verifique como [configurar a autenticação SAML 2\.0](configure-saml.md).

AD do Azure
: usa a autenticação baseada em SAML para autenticar os usuários finais com SSO \(Single Sign\-On\) fornecido pelo Provedor de Identidade do Azure AD.   
Verifique como [configurar a autenticação do Azure AD](configure-azuread.md).

Okta
: usa autenticação baseada em SAML para autenticar os usuários finais com SSO \(Single Sign\-On\) fornecido pela Okta.   
Verifique como [configurar a autenticação Okta](configure-okta.md).

**Notas:** 

* Se você usar o Active Directory, LDAP, SAML 2\.0, Azure AD ou Okta para autenticar usuários, não precisará criar os usuários manualmente. Em vez disso, eles são criados automaticamente no banco de dados OutSystems no primeiro login sem armazenar dados de senha.

* Ao usar a autenticação do Active Directory ou LDAP, o módulo Usuários tenta autenticar o usuário localmente primeiro, se existir no banco de dados OutSystems e se tiver uma senha definida. Esta primeira tentativa de autenticação não ocorre ao usar os métodos de autenticação SAML 2\.0, Azure AD ou Okta.

Configurar a autenticação de usuários finais
--------------------------------------------

Para configurar como a plataforma autentica os usuários finais, faça o seguinte:

1. No [aplicativo Usuários](../accessing-users.md), clique em "Configurar Autenticação" na barra lateral.

2. Selecione um dos métodos de **autenticação na lista suspensa Autenticação** : `Somente Interno`, `Active Directory`, `LDAP`, `Azure AD`, `SAML 2.0`ou `Okta`.

   ![](images/ldap-2.png)
3. Se você escolheu **Active Directory** , **LDAP** , **SAML 2\.0** , **Azure AD** ou **Okta** na etapa anterior, será necessário preencher outros campos de configuração específicos do método de autenticação selecionado na etapa 2\. Verifique os outros tópicos nesta seção para obter mais informações sobre como configurar esses métodos de autenticação.

Fluxo de autenticação
---------------------

Quando o usuário final usa o aplicativo pela primeira vez e a tela acessada permite que apenas os usuários finais autenticados o vejam, a plataforma gera uma exceção de segurança. A OutSystems faz o seguinte:

1. Se a plataforma estiver configurada para usar a autenticação SAML 2\.0, Azure AD ou Okta, o usuário final será redirecionado para uma página da Web onde ele deverá inserir suas credenciais corporativas \(nome de usuário e senha\). Após a autenticação bem\-sucedida, o usuário final é redirecionado de volta ao aplicativo OutSystems.

2. Se a plataforma estiver [configurada para usar a Autenticação Integrada do Windows](integrated-authentication.md) e o usuário final estiver no mesmo domínio que o servidor de plataforma, o usuário final será autenticado usando a Autenticação Integrada do Windows.

   Uma vez que o usuário final faz uma solicitação, o servidor responde com um status HTTP 401, sinalizando ao navegador do usuário final que a autenticação é necessária. Se o navegador já tiver as credenciais do usuário final armazenadas, ele envia automaticamente as credenciais para o servidor Web. Caso contrário, o navegador exibe um formulário para o usuário final inserir as credenciais e enviá\-las para o servidor. Isso significa que mesmo se você tiver uma página de login personalizada, o usuário final não a verá.
3. Se a plataforma não estiver configurada para usar a Autenticação Integrada do Windows, o usuário final será redirecionado para uma tela de login. Quando o usuário final envia as credenciais:

   1. As credenciais são validadas em relação ao banco de dados OutSystems.

   2. A plataforma verifica se a autenticação está configurada para usar a autenticação do Active Directory ou LDAP e faz um dos seguintes procedimentos:

      A\) Se a plataforma estiver configurada para autenticar usando o Active Directory, as credenciais serão validadas no servidor de domínio configurado.

      B\) Se a plataforma for configurada para autenticar usando LDAP, as credenciais serão validadas contra o servidor LDAP configurado.

4. Se, após este processo, o usuário final não puder ser autenticado, uma" mensagem de login "inválido será exibida ao usuário final.

Sincronização de dados do usuário
---------------------------------

Ao autenticar usuários finais usando o Active Directory, LDAP, SAML 2\.0, Azure AD ou Okta, os dados do usuário no banco de dados OutSystems são atualizados, em uma ou mais ocasiões, com os dados mais recentes do sistema de autenticação externa. Os atributos atualizados para cada usuário são os seguintes:

* Nome
* E\-mail
* Celular

Essa sincronização ocorre nos seguintes momentos, dependendo da autenticação configurada:

Depois de um login bem\-sucedido
: Os dados do **usuário conectado** são atualizados. Ocorre ao usar a autenticação do Active Directory, LDAP, SAML 2\.0, Azure AD ou Okta.

Em um temporizador diário
: Os dados de **todos os usuários existentes de fontes de autenticação externas** no banco de dados OutSystems são atualizados. Esses usuários são aqueles cujo nome de usuário contém um caractere `\` \(barra\).   
O temporizador é nomeado `SynchronizeDomainUsers` e é configurado no módulo Users. Essa sincronização baseada em temporização ocorre somente ao usar o Active Directory.   
Observe que esse processo não cria novos usuários no banco de dados OutSystems.

