# RemovePassword Object(删除密码对象)

* `type` String  - `password`。
* `origin` String(可选) - 当提供时,与原始相关的认证信息将被删除,否则整个缓存会被清除。
* `scheme` String(可选) - 认证方案。可选 `basic`, `digest`, `ntlm`, `negotiate`。如果通过 `origin`删除,必须提供。
* `realm` String(可选) - 认证的领域。如果通过 `origin`删除,必须提供。
* `username` String(可选) - 认证的凭证。如果通过 `origin`删除,必须提供。
* `password` String(可选) - 认证的凭证。如果通过 `origin`删除,必须提供。