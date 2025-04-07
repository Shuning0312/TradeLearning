本文档整理了Freqtrade官方配置手册，官方手册如下👇

[https://www.freqtrade.io/en/stable/configuration/](https://www.freqtrade.io/en/stable/configuration/)

【对配置项有更新的详细理解，欢迎在文档中随时补充👏】

## 一、配置文件基础
### （一）配置文件概述
Freqtrade通过配置文件进行参数设置，默认从当前工作目录下的`config.json`文件加载配置。也可使用`-c/--config`命令行选项指定其他配置文件。

若默认配置文件不存在，推荐使用`freqtrade new-config --config user_data/config.json`生成基础配置文件，文件采用JSON格式，支持`//`和`/* */`注释及参数列表的尾随逗号。

### （二）环境变量
+ **设置方式**：环境变量需以`FREQTRADE__`为前缀，`__`作为层级分隔符，格式为`FREQTRADE__{section}__{key}`. 如`export FREQTRADE__STAKE_AMOUNT=200`

+ **优先级**：环境变量优先级高于配置文件和策略中的对应值，但**命令行参数优先级最高**；


### （三）多配置文件
1. **合并方式**：可在`add_config_files`中指定多个配置文件，这些文件将与初始配置文件合并，路径相对于初始配置文件。也可在命令行使用多个`--config`参数指定配置文件。
2. **秘密配置**：可使用第二个配置文件存放敏感信息（如API密钥），避免在共享主要配置文件时泄露机密。在合并配置时，若同一设置存在于多个文件，最后指定的配置文件中的设置生效（除父配置已定义的键）。

### （四）配置验证与自动补全
使用支持JSON模式的编辑器，在配置文件顶部添加`"$schema": "``https://schema.freqtrade.io/schema.json``"`，可实现自动补全和验证。

开发版本模式为`https://schema.freqtrade.io/schema_dev.json`，建议使用稳定版本。

## 二、配置参数详解
### （一）交易基础参数
1. `max_open_trades`：必填，允许的最大交易数量，每个交易对仅允许一个开仓交易，取值为正整数或`-1`（表示无限制，受交易对列表长度约束），可被策略覆盖。
2. `stake_currency`：必填，交易使用的加密货币。
3. `stake_amount**`：必填，每次交易使用的加密货币数量，可设为正浮点数或`"unlimited"`（表示使用全部可用余额）。

### （二）账户与资金参数
1. `tradable_balance_ratio`：默认`0.99`，允许交易的账户余额比例。
2. `available_capital`：多机器人在同一账户交易时，指定每个机器人的起始资金。
3. `amend_last_stake_amount`：默认`false`，是否在必要时减少最后一次交易的 stake 金额。
4. `last_stake_amount_min_ratio`：默认`0.5`，修正最后一次 stake 金额时的最小比例。

### （三）交易时间与频率参数
1. `timeframe`：交易时间框架，如`1m`、`5m`等，通常在策略中指定，**可被策略覆盖**。
2. `unfilledtimeout.entry`：必填，入场订单等待成交的最长时间（分钟或秒）。
3. `unfilledtimeout.exit`：必填，出场订单等待成交的最长时间（分钟或秒）。
4. `unfilledtimeout.unit`：默认`"minutes"`，`unfilledtimeout`设置的时间单位。
5. `unfilledtimeout.exit_timeout_count`：默认`0`，出场订单允许的超时次数。

### （四）盈利与止损参数
1. `minimal_roi`：必填，以字典形式设置不同持仓时间对应的最小收益率，**可被策略覆盖**。
2. `stoploss`：必填，止损比例，**可被策略覆盖**。
3. `trailing_stop`：是否启用追踪止损，**可被策略覆盖**。
4. `trailing_stop_positive`：盈利达到一定程度后调整止损的比例，**可被策略覆盖**。
5. `trailing_stop_positive_offset`：默认`0.0`，应用`trailing_stop_positive`的偏移量，**可被策略覆盖**。
6. `trailing_only_offset_is_reached`：默认`false`，仅在达到偏移量时应用追踪止损，**可被策略覆盖**。

### （五）交易手续费与成本参数
1. `fee`：回测或模拟交易时的手续费，通常使用交易所默认手续费。
2. `futures_funding_rate`：默认`None`，历史资金费率不可用时使用的用户指定资金费率。

### （六）交易模式与杠杆参数
1. `trading_mode`：默认`"spot"`，交易模式，包括现货、杠杆、合约交易。
2. `margin_mode`：杠杆交易时，抵押品的模式（共享或隔离）。
3. `liquidation_buffer`：默认`0.05`，清算缓冲区比例，防止仓位达到清算价格。

### （七）订单价格相关参数
1. `entry_pricing.price_side`：默认`"same"`，入场时参考订单簿的哪一侧价格。
2. `entry_pricing.price_last_balance`：必填，用于插值计算入场价格。
3. `entry_pricing.use_order_book`：默认`true`，是否使用订单簿数据入场。
4. `entry_pricing.order_book_top`：默认`1`，使用订单簿中指定位置的价格入场。
5. `entry_pricing.check_depth_of_market.enabled`：默认`false`，是否检查市场深度。
6. `entry_pricing.check_depth_of_market.bids_to_ask_delta`：默认`0`，市场深度检查中买单和卖单差异比例。
7. `exit_pricing.price_side`：默认`"same"`，出场时参考订单簿的哪一侧价格。
8. `exit_pricing.price_last_balance`：用于插值计算出场价格。
9. `exit_pricing.use_order_book`：默认`true`，是否使用订单簿数据出场。
10. `exit_pricing.order_book_top`：默认`1`，使用订单簿中指定位置的价格出场。
11. `custom_price_max_distance_ratio`：默认`0.02`，当前价格与自定义入场或出场价格的最大距离比例。

### （八）交易信号与订单类型参数
1. `use_exit_signal`：默认`true`，是否使用策略产生的退出信号。
2. `exit_profit_only`：默认`false`，是否仅在达到盈利偏移量后才退出。
3. `exit_profit_offset`：默认`0.0`，盈利偏移量，仅在`exit_profit_only`为`true`时有效。
4. `ignore_roi_if_entry_signal`：默认`false`，如果入场信号仍有效，是否忽略最小收益率退出条件。
5. `ignore_buying_expired_candle_after`：指定买入信号过期的时间（秒）。
6. `order_types`：配置不同交易行为（入场、出场、止损等）的订单类型，需包含`entry`、`exit`、`stoploss`和`stoploss_on_exchange`四个值。
7. `order_time_in_force`：配置入场和出场订单的执行策略，如`GTC`、`FOK`、`IOC`等，目前仅部分交易所支持。

### （九）交易所相关参数
1. `exchange.name`：必填，交易所名称。
2. `exchange.key`：生产模式下必填，交易所API密钥。
3. `exchange.secret`：生产模式下必填，交易所API密钥。
4. `exchange.password`：部分交易所生产模式下必填，API密码。
5. `exchange.uid`：部分交易所生产模式下必填，API用户ID。
6. `exchange.pair_whitelist`：交易对白名单。
7. `exchange.pair_blacklist`：交易对黑名单。
8. `exchange.ccxt_config`：传递给ccxt实例的额外配置参数。
9. `exchange.ccxt_sync_config`：传递给同步ccxt实例的额外配置参数。
10. `exchange.ccxt_async_config`：传递给异步ccxt实例的额外配置参数。
11. `exchange.enable_ws`：默认`true`，是否启用交易所Websocket。
12. `exchange.markets_refresh_interval`：默认`60`分钟，市场数据刷新间隔。
13. `exchange.skip_open_order_update`：默认`false`，启动时是否跳过未结订单更新。
14. `exchange.unknown_fee_rate`：默认`None`，计算交易手续费时的备用费率。
15. `exchange.log_responses`：默认`false`，是否记录交易所响应日志。
16. `exchange.only_from_ccxt`：默认`false`，是否仅从ccxt下载数据。
17. `experimental.block_bad_exchanges`：默认`true`，是否阻止已知不兼容的交易所。

### （十）插件与通知参数
1. `edge.*`：参考边缘配置文档。
2. `pairlists`：默认`StaticPairList`，定义使用的交易对列表。
3. `telegram.enabled`：是否启用Telegram通知。
4. `telegram.token`：Telegram机器人令牌。
5. `telegram.chat_id`：Telegram用户ID。
6. `telegram.balance_dust_level`：Telegram余额显示的最小金额。
7. `telegram.reload`：默认`true`，是否允许Telegram消息中的“重新加载”按钮。
8. `telegram.notification_settings.*`：详细通知设置。
9. `telegram.allow_custom_messages`：是否允许策略通过`dataprovider.send_msg()`函数发送Telegram消息。
10. `webhook.enabled`：是否启用Webhook通知。
11. `webhook.url`：Webhook地址。
12. `webhook.entry`：入场时发送的Webhook负载。
13. `webhook.entry_cancel`：入场订单取消时发送的Webhook负载。
14. `webhook.entry_fill`：入场订单成交时发送的Webhook负载。
15. `webhook.exit`：出场时发送的Webhook负载。
16. `webhook.exit_cancel`：出场订单取消时发送的Webhook负载。
17. `webhook.exit_fill`：出场订单成交时发送的Webhook负载。
18. `webhook.status`：状态调用时发送的Webhook负载。
19. `webhook.allow_custom_messages`：是否允许策略通过`dataprovider.send_msg()`函数发送Webhook消息。

### （十一）API与其他参数
1. `api_server.enabled`：是否启用API服务器。
2. `api_server.listen_ip_address`：API服务器绑定的IP地址。
3. `api_server.listen_port`：API服务器绑定的端口。
4. `api_server.verbosity`：默认`info`，日志详细程度。
5. `api_server.username`：API服务器用户名。
6. `api_server.password`：API服务器密码。
7. `api_server.ws_token`：消息WebSocket的API令牌。
8. `bot_name`：默认`freqtrade`，机器人名称。
9. `external_message_consumer`：启用生产者/消费者模式的配置。
10. `initial_state`：默认`stopped`，初始应用状态。
11. `force_entry_enable`：是否启用强制入场命令，默认禁用。
12. `disable_dataframe_checks`：默认`False`，是否禁用策略返回的OHLCV数据帧正确性检查。
13. `internals.process_throttle_secs`：默认`5`秒，机器人循环的最小时间间隔。
14. `internals.heartbeat_interval`：默认`60`秒，心跳消息打印间隔。
15. `internals.sd_notify`：是否启用`sd_notify`协议通知系统d服务管理器。
16. `strategy`：必填，使用的策略类。
17. `strategy_path`：策略查找路径。
18. `recursive_strategy_search`：是否递归搜索策略目录。
19. `user_data_dir`：默认`./user_data/`，用户数据目录。
20. `db_url`：数据库URL，干运行时默认`sqlite:///tradesv3.dryrun.sqlite`，生产环境默认`sqlite:///tradesv3.sqlite`。
21. `logfile`：日志文件名，采用滚动策略进行日志文件轮换。
22. `add_config_files`：额外配置文件列表。
23. `dataformat_ohlcv`：默认`feather`，OHLCV数据存储格式。
24. `dataformat_trades`：默认`feather`，交易数据存储格式。
25. `reduce_df_footprint`：默认`False`，是否减少数据帧内存/磁盘使用。
26. `log_config`：默认`FtRichHandler`，日志配置字典。

## 三、特殊配置场景
### （一）每笔交易金额配置
1. **最小交易限额**：取决于交易所和交易对，Freqtrade会考虑止损和预留比例调整最小交易金额，计算出的最小交易限额不会超过实际限额的50%。
2. **干运行钱包**：干运行模式下，使用模拟钱包，起始余额由`dry_run_wallet`定义，可设为浮点数或字典。
3. **可交易余额**：通过`tradable_balance_ratio`配置可交易的账户余额比例，该设置不适用于多机器人在同一账户交易的场景。
4. **分配可用资本**：使用`available_capital`为多机器人在同一账户交易时分配起始资金，与`tradable_balance_ratio`不兼容。
5. **调整最后一笔交易金额**：`amend_last_stake_amount`设为`True`时，机器人可减少最后一笔交易金额以填满交易槽，最小最后交易金额可通过`last_stake_amount_min_ratio`配置。
6. **静态交易金额**：`stake_amount`静态配置每笔交易的加密货币数量，需结合`max_open_trades`考虑，且需遵循可用余额配置。
7. **动态交易金额**：将`stake_amount`设为`"unlimited"`并配合`tradable_balance_ratio=0.99`使用，交易金额根据可用余额和允许的交易数量动态计算。

### （二）订单价格配置
1. **入场价格**
    1. **参考订单簿一侧**：`entry_pricing.price_side`决定入场时参考订单簿的哪一侧价格，推荐使用`"same"`或`"other"`。
    2. **使用订单簿数据**：`entry_pricing.use_order_book=True`时，从订单簿获取指定位置的价格入场。
    3. **不使用订单簿数据**：根据`entry_pricing.price_last_balance`插值计算入场价格。
    4. **检查市场深度**：`entry_pricing.check_depth_of_market.enabled=True`时，根据市场深度过滤入场信号。
2. **出场价格**
    1. **参考订单簿一侧**：`exit_pricing.price_side`决定出场时参考订单簿的哪一侧价格，推荐使用`"same"`或`"other"`。
    2. **使用订单簿数据**：`exit_pricing.use_order_book=True`时，从订单簿获取指定位置的价格出场。
    3. **不使用订单簿数据**：根据`exit_pricing.price_last_balance`插值计算出场价格。
3. **市价订单定价**：使用市价订单时，需正确配置`order_types`、`entry_pricing.price_side`和`exit_pricing.price_side`。

### （三）其他特殊配置
1. **理解最小****收益率****（**`minimal_roi`）：以持仓时间为键，最小收益率为值的字典配置，也可使用`"<N>": -1`强制机器人在N分钟后退出交易。若在配置文件和策略中均未设置，默认使用`{"0": 10}`（即 1000%），此时除非盈利达 1000%，否则最小收益率功能禁用。
2. **理解强制入场（**`force_entry_enable`）：启用后可通过Telegram和REST API使用强制入场命令，使用时需谨慎。如`/forcelong`、`/forceshort`。例如发送`/forceenter ETH/BTC`可使机器人买入该交易对并持有，直至出现常规退出信号（如 ROI、止损、`/forceexit`）。但此功能对某些策略可能有风险，使用时需谨慎。
3. **忽略过期蜡烛**：设置`ignore_buying_expired_candle_after`可忽略超过指定时间的买入信号。
4. **理解订单类型（**`order_types`）：用于配置不同交易行为（入场、出场、止损、紧急退出、强制退出、强制入场）对应的订单类型（如`market`、`limit`等），还可设置止损是否在交易所执行以及止损在交易所的更新间隔（秒）。配置时需包含`entry`、`exit`、`stoploss`和`stoploss_on_exchange`四个值，否则机器人无法启动。示例配置如下：

```json
// 策略中的配置示例
order_types = {
    "entry": "limit",
    "exit": "limit",
    "emergency_exit": "market",
    "force_entry": "market",
    "force_exit": "market",
    "stoploss_on_exchange": false,
    "stoploss_on_exchange_interval": 60,
    "stoploss": "market",
    "stoploss_on_exchange_limit_ratio": 0.99
}
// 配置文件中的配置示例
"order_types": {
    "entry": "limit",
    "exit": "limit",
    "emergency_exit": "market",
    "force_entry": "market",
    "force_exit": "market",
    "stoploss": "market",
    "stoploss_on_exchange": false,
    "stoploss_on_exchange_interval": 60
}
```

并非所有交易所都支持`market`订单，若交易所不支持，机器人启动时会提示`"Exchange <yourexchange> does not support market orders."`并拒绝启动。使用市价订单时，需仔细阅读 “市价订单定价” 部分内容。

1. **止损在交易所**：`order_types.stoploss_on_exchange`启用后，止损订单在买入订单成交后立即放置在交易所，若创建失败会触发紧急退出。
2. **理解订单生效时间（**`rder_time_in_force`）：定义订单在交易所的执行策略，常见的有以下几种：
+ GTC（Good Till Canceled）：大多数情况下的默认策略，订单会一直保留在交易所，直至用户取消，可部分或全部成交，部分成交后剩余部分仍保留在交易所。
+ FOK（Fill Or Kill）：若订单不能立即全部成交，则被交易所取消。
+ IOC（Immediate Or Canceled）：与 FOK 类似，但可部分成交，剩余部分自动被交易所取消。
+ PO（Post only）：仅作为挂单方下单，即订单必须在订单簿中以未成交状态存在至少一段时间，否则取消。`order_time_in_force`参数包含一个字典，用于设置入场和出场订单的生效时间策略，可在配置文件或策略中设置，配置文件中的值会覆盖策略中的值。
1. **法定货币****转换**：Freqtrade 使用 Coingecko API 将加密货币价值转换为法定货币，用于 Telegram 报告。可在配置文件中通过`fiat_display_currency`设置法定货币，有效取值包括常见法定货币（如`"AUD"`、`"BRL"`、`"USD"`等）和部分加密货币（如`"BTC"`、`"ETH"`等）。
2. **交易所 Websocket**：Freqtrade 可通过 ccxt.pro 使用 Websocket 获取数据，旨在确保数据始终可用。若 Websocket 连接失败或被禁用，机器人将回退到 REST API 调用。若怀疑问题由 Websocket 引起，可通过设置`<font style="background-color:rgb(187,191,196);">exchange.enable_ws</font>`（默认`<font style="background-color:rgb(187,191,196);">true</font>`）禁用 Websocket。

## 四、运行模式配置
### （一）干运行模式
推荐在开始时使用干运行模式，以观察机器人行为和策略性能。在干运行模式下，机器人不会使用真实资金交易，仅进行实时模拟。配置步骤如下：

1. 编辑`config.json`配置文件，将`dry_run`设为`true`，并指定用于持久化数据库的`db_url`，如：

```json
"dry_run": true,
"db_url": "sqlite:///tradesv3.dryrun.sqlite"
```

1. 移除或修改交易所 API 密钥和密钥为无效值，例如：

```json
"exchange": {
    "name": "binance",
    "key": "",
    "secret": ""
}
```

干运行模式下，模拟钱包的起始资本由`dry_run_wallet`（默认 1000）决定。需注意以下几点：

+ API 密钥可提供也可不提供，干运行模式仅执行交易所的只读操作（不改变账户状态的操作）。
+ 钱包余额（`/balance`）基于`dry_run_wallet`进行模拟。
+ 订单为模拟订单，不会发送到交易所。
+ 市价订单根据订单簿成交量在下单时成交，最大滑点为 5%。
+ 限价订单在价格达到设定水平时成交，或根据`unfilledtimeout`设置超时。若限价订单价格跨越超过 1%，将转换为市价订单并按市价订单规则立即成交。
+ 结合`stoploss_on_exchange`时，假设止损价格已成交。
+ 机器人重启后，未成交订单（非交易，交易存储在数据库中）保持打开状态，假设其在离线时未成交。

### （二）生产模式
生产模式下，机器人将使用真实资金交易，错误的策略可能导致资金损失，因此切换到生产模式时需谨慎。切换步骤如下：

1. 编辑`config.json`文件，将`dry_run`设为`false`，并根据需要调整数据库 URL：

```json
"dry_run": false
```

1. 插入真实的交易所 API 密钥，如：

```plain
"exchange": {
    "name": "binance",
    "key": "af8ddd35195e9dc500b9a6f799f6f5c93d89193b",
    "secret": "08a9dc6db3d7b53e1acebd9275677f4b0a04f1a5"
    // "password": "", // 部分交易所需要，可选
}
```

切换到生产模式时，建议使用新的数据库，避免干运行交易数据影响真实交易和统计信息。同时，应阅读文档中交易所相关部分，了解特定交易所的潜在配置细节。

## 五、安全与代理配置
### （一）保持机密信息安全
为保护机密信息，建议使用第二个配置文件存放 API 密钥等敏感信息。例如创建`config-private.json`文件，将密钥相关配置放在其中，启动机器人时使用`freqtrade trade --config user_data/config.json --config user_data/config-private.json <...>`加载密钥。切勿与他人分享私人配置文件或交易所密钥。

### （二）使用代理
1. 通用代理设置：若要为 Freqtrade 使用代理，可通过导出`HTTP_PROXY`和`HTTPS_PROXY`环境变量来设置，这将应用于除交易所请求外的所有请求（如 Telegram、Coingecko 等），设置示例如下：

```plain
export HTTP_PROXY="http://addr:port"export HTTPS_PROXY="http://addr:port"
```

1. 交易所代理设置：若要为交易所连接使用代理，需在`ccxt_config`中定义代理，示例如下：

```plain
"exchange": {"ccxt_config": {"httpsProxy": "http://addr:port","wsProxy": "http://addr:port"}}
```

有关可用代理类型的更多信息，可查阅 ccxt 代理文档。

