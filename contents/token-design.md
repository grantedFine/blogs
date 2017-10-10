# token 的设计

token 的生成和验证都是比较频繁的操作，设计时需要尽可能降低消耗的性能。所以应该尽力减少对称加密和非对称加密方法。

总体原则上，第一种选择是使用缓存，速度快，只消耗少量 IO，缺点是不适合处理大量的 token，也不适合处理长时间的 token；第二种选择是保持在数据库中，缺点是没有自动过期机制；第三种选择是 hash，缺点是需要消耗 CPU 资源；第四种选择是对称加密，缺点是除了要消耗 CPU 资源，还依赖密钥的安全性。

对于手动过期的 token，常见于 OAuth2 中，发给第三方的 access_token，用户可以手动取消授权，使 access_token 失效。这里可以采用随机的 guid，并保存在数据库中。

对于短时过期的 token，常见于临时的授权，可以采用随机的 guid，保存在缓存中，并设置好较短的过期时间。如果要求是一次性的 token，用后删除该缓存。

对于长期自动登录的 token，常见于网站的自动登陆和 APP 的自动登陆，可以在 token 的组成中加入过期时的时间戳。

具体可以是：

```
token = hash(用户 id＋密码＋时间戳)+ 用户 id + 时间戳

```

验证时，可以先分割出用户 id 和时间戳，根据时间戳判断是否过期，如果没有过期，再查询出密码，再 hash 计算一次，判断结果是否一致，为了性能，可以在缓存中保存 token 较短时间（10-20 分钟），即如果缓存中存在，则验证通过，否则按照上述的逻辑计算一次，验证的结果保存的缓存中。

也可以采用更标准的形式，例如 json-web-token，nodejs 中可以使用 https://github.com/auth0/node-jsonwebtoken 库来简化生成和验证过程。

对于滑动过期的 token，类似 session 机制，常见于网站的非自动登陆，可以采用随机的 guid，保存在缓存中，并设置好较短的过期时间，并且在每次收到请求后重新设置过期时间。

对于支持多种情况的 token，可以用单独的字段来区分，也可以把这个用来区分的字段包含在 token 中。例如，`1,${token1}` 和 `2,${token2}`。