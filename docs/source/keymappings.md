# Couchbase Key Mappings

## Overview

To reduce the size of entries in Couchbase, a shortcutting tool substitutes commonly used words in schema keys. The new, shorter keys are then stored in Couchbase.

## Static rules:
- `objectClass` -> `oc`

## Dynamic rules:
We use `_` as a key mapping marker since it's not used in the current schema:

### Drop prefix  

| Original | Example|
| --- | --- |
| `gluu` | `gluuAttributeType` -> `attr_t` |  
| `oxAuth` | `oxAuthAppType` -> `app_t`, `oxAuthLogoutSessionRequired` -> `logoutSessionRequired` |  
| `ox` | `oxIconUrl` -> `iconUrl` | 
| `oxTrust` | `oxTrustConfCacheRefresh` -> `ConfCacheRefresh` |

### Shortcuts with markers  

| Original | Shortened | Example |
| --- | --- | --- |
| `Group` | `_g` | `gluuGroupVisibility` -> `_gVisibility` |  
| `Client` | `_c` | `oxAuthTrustedClient` -> `trusted_c` |  
| `Type` | `_t` | `oxAuthSubjectType` -> `subject_t` | 
| `User` | `_u` | `oxAuthUserId` -> `_uId`, `oxAuthUserDN` -> `_uDN` |  
| `Default` | `_d` | `oxAuthDefaultAcrValues` -> `_dAcrValues` |  

### Shortcuts without markers

| Original | Shortened | Example |
| --- | --- | --- |
| `Configuration` | `Conf` | `oxSmtpConfiguration` -> `smtpConf` |  
| `Application` | `App` | `oxTrustConfApplication` -> `confApp` |  
| `Request` | `Req` | |  
| `Response` | `Resp` | `oxAuthUserInfoEncryptedResponseAlg` -> `_uInfoEncrRespAlg` |  
| `Attribute` | `Attr` | |
| `Authentication` | `Authn` | `oxAuthAuthenticationTime` -> `authnTime`, `oxIDPAuthentication` -> `iDPAuthn` |  
| `Authorization` | `Authz` | `oxAuthSkipAuthorization` -> `skipAuthz` |  
| `Encrypted`, `Encryption` | `Enc` | `oxAuthUserInfoEncryptedResponseAlg` -> `_uInfoEncRespAlg` |  
| `Signing` | `Sig` | `oxAuthTokenEndpointAuthSigningAlg` -> `tokEndpointAuthSigAlg` |   
| `Expiration` | `Exp` | `oxLinkExpirationDate` -> `linkExpDate` |  
| `Object` | `Obj` | `oxAuthRequestObjectEncryptionAlg` -> `reqObjEncrAlg` |  
| `Token` | `Tok` | `oxAuthTokenType` -> `tok_t`, `oxAuthTokenEndpointAuthSigningAlg` -> `tokEndpointAuthSigAlg` |
