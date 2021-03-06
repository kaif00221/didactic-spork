---
title: 对组织启用 OAuth App 访问限制
intro: 'Organization owners can enable {% data variables.product.prodname_oauth_app %} access restrictions to prevent untrusted apps from accessing the organization''s resources while allowing organization members to use {% data variables.product.prodname_oauth_apps %} for their personal accounts.'
redirect_from:
  - /articles/enabling-third-party-application-restrictions-for-your-organization/
  - /articles/enabling-oauth-app-access-restrictions-for-your-organization
  - /github/setting-up-and-managing-organizations-and-teams/enabling-oauth-app-access-restrictions-for-your-organization
versions:
  fpt: '*'
topics:
  - Organizations
  - Teams
shortTitle: 启用 OAuth 应用程序
---

{% data reusables.organizations.oauth_app_restrictions_default %}

{% warning %}

**警告**：
- Enabling {% data variables.product.prodname_oauth_app %} access restrictions will revoke organization access for all previously authorized {% data variables.product.prodname_oauth_apps %} and SSH keys. 更多信息请参阅“[关于 {% data variables.product.prodname_oauth_app %} 访问限制](/articles/about-oauth-app-access-restrictions)”。
- 在设置 {% data variables.product.prodname_oauth_app %} 访问限制后，确保重新授权任何需要持续访问组织私有数据的 {% data variables.product.prodname_oauth_app %}。 所有组织成员将需要创建新的 SSH 密钥，并且组织将需要根据需要创建新的部署密钥。
- 启用 {% data variables.product.prodname_oauth_app %} 访问限制后，应用程序可以使用 OAuth 令牌访问有关 {% data variables.product.prodname_marketplace %} 事务的信息。

{% endwarning %}

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.organizations.oauth_app_access %}
5. 在“Third-party application access policy”（第三方应用程序访问策略）下，单击 **Setup application access restrictions（设置应用程序访问限制）**。 ![设置限制按钮](/assets/images/help/settings/settings-third-party-set-up-restrictions.png)
6. 审查有关第三方访问限制的信息后，单击 **Restrict third-party application access（限制第三方应用程序访问）**。 ![限制确认按钮](/assets/images/help/settings/settings-third-party-restrict-confirm.png)
