---
layout: page
title: User Guide to eduroam JP Federated ID Service
---
## Overview

This service will issue accounts necessary for connecting terminals to eduroam WiFi network which is the global academic wireless LAN roaming infrastructure. In order to issue accounts, users have to be authenticated with the IdP operated by the affiliated institution under the GakuNin federation. The issued account will be temporary with a limited validity period. Since this account itself does not contain information that identifies individuals, there is no concern of privacy leakage upon using eduroam. Also, since the password is temporary, even if the password is leaked by accidentally connecting to an unauthorized access point, the damage can be minimized.

## How to Use

### Login

If authentication with the institution IdP for GakuNin is successful, you can see the menu including four functions as follows:

* New ID / Password account issue (新規ID/Passwordアカウント発行)
* Verification and revocation of issued ID / Password account (発行済みID/Passwordアカウントの確認および失効)
* New certificate [EAP-TLS] account issue (新規証明書[EAP-TLS]アカウント発行)
* Verified and revoked issued certificate [EAP-TLS] account (発行済み証明書[EAP-TLS]アカウントの確認および失効)

Accounts issued by these functions are only for yourself. It is prohibited to tell others.

Students can issue accounts only if the institution administrator allows.

Furthermore, the following function will also be displayed for the faculty and staff if the administrator of the institution allows.

* Visitor account issue function (ビジター用アカウント発行機能)

Please use this function to provide a temporary account to a visitor. Note: do not provide the same account to multiple visitors.

Valid period of any accounts cannot be changed after issue. Please re-issue if you want to change.

Maximum number of accounts to be issued is limited (defined by each institute). Expired accounts and revoked accounts are not counted.

### Server Certificate to be verified

You should verify server certificate when you connect to eduroam network using an account issued by this service.

Information about the service certificate is described in the page: <https://federated-id.eduroam.jp/cert.html>

### New ID / Password account issue

First, specify the start date and expiration date of the account you want to use. After that, by pressing the application (申請) button, a new ID / Password account is issued and the account name (ID) and the corresponding password (Password) are displayed. Make a note of this ID / Password and use it to connect to the eduroam access point. When issuing multiple accounts, you can record the purpose of each account (such as the type of terminal you use) in the account memo field. Note that password and valid period cannot be changed.

### Verification and revocation of issued ID / Password account

You can verify ID / Password accounts issued so far. Since the password is also displayed, if you forget the ID or Password of the issued accounts, you can confirm it again here.

In addition, you can revoke the specified ID / Password account to make it unusable. For ID / Password accounts that are no longer needed or concerned about leakage of passwords, please revoke promptly. Note that an account past the valid period will be automatically invalidated, and you do not have to revoke such "old" accounts.

### New certificate [EAP-TLS] account issue

You can be authenticated with a certificate [EAP-TLS] account (client certificate) instead of an ID / Password account. Although it depends on functionality of the terminal, you can be authenticated without entering password every time, making it easier to connect to eduroam.

To issue a new certificate [EAP-TLS] account, first specify the start date and expiration date of the account you want to use. After that, by pressing the application button, a new certificate [EAP-TLS] account will be issued and the URL for certificate download and the password to install the certificate will be displayed. Install the certificate using the password on the terminal to use it to connect to the eduroam access point. (In the eduroam connection setting on the terminal, specify the installed certificate. In some terminals you need to specify the ID of the account at the same time) When you issue multiple accounts, you can record the purpose of each account (such as the type of terminal you use) in the account memo field. Note that password and valid period cannot be changed.

### Verified and revoked issued certificate [EAP-TLS] account

You can verify the certificate [EAP-TLS] account issued so far. Since the URL for certificate download and password for installation are also shown, if you lost the certificate of the certificate or forgot the password, you can get it again here.

Also, it is possible to revoke a specified certificate [EAP-TLS] account to make it unusable.Please revoke the certificate as soon as possible which became unnecessary or concerned about the leakage of the password. Note that an account past the valid period will be automatically invalidated, and you do not have to revoke such "old" accounts.

### Issuing visitor's accounts

A faculty and staff member can issue accounts for visitors if the administrator of the institution allows. You can choose the type of accounts: ID / Password account and certificate [EAP-TLS] account. There are also two types of  valid period: one week and one month. The each maximum number of valid issued accounts are specified by the administrator. Please note that numbers of issued accounts are counted including ones before valid period.

The procedure for issuing accounts is the same as for the case of its own use. In the account memo field, you can record information about the visitor who the account is provided (the account memo field can be changed later).

Use of visitor's accounts may be restricted outside of the issuing institution (except service areas by NII - Hitotsubashi Hall, etc.).

In the issued account list page, it is possible to output a consent form (PDF) for the issued account. which can be printed, distributed to visitors and signed. In addition, it is also possible to acquire the account list with CSV form, so it is easy to issue consent forms on other systems.

### Confirmation on Continuation of Use (Students Only)

Institution administrator may ask for confirmation on continuity of use with issued accounts, such as on a yearly basis. If you have a valid eduroam account, a message asking for confirmation on continuation of use will be shown, so if you wish to continue to use, please confirm. If you did not confirm, the issued eduroam accounts will be forcibly revoked after the continuation confirmation period is over. If you forgot to confirm and the accounts have been revoked, please issue new accounts again.

## Notes

With this service, we provide a mechanism that can not identify an individual by an issued pseudonym account. Since we only refer to pseudonymous information called [eduPersonTargetedID](https://translate.googleusercontent.com/translate_c?depth=1&hl=ja&ie=UTF8&prev=_t&rurl=translate.google.com.hk&sl=ja&sp=nmt4&tl=en&u=https://upki-portal.nii.ac.jp/docs/fed/technical/attribute/eduPersonTargetedID&usg=ALkJrhgiRH4nQzMgY0bM05eO3NiO0pAvHA) sent from the affiliated institution's IdP when issuing accounts, we can not identify specific individuals even on this service site. However, we keep records for the account issuance for a certain period of time, so if you encountered unauthorized access, etc. using this account, we will collaborate with the affiliated institution to identify the specific individual.

When using this service, please comply with the usage rules.

## How to contact us

Please do not contact to us directly. If you have any problems using the service, please contact IT staff of your organization first. We only accept inquireies from the IT staff.
