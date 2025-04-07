# 回测策略必要配置
本文档整理总结了回测策略算法时，必要的配置项

官方配置手册

[https://www.freqtrade.io/en/stable/configuration/](https://www.freqtrade.io/en/stable/configuration/)

简单策略样例

[https://github.com/freqtrade/freqtrade-strategies](https://github.com/freqtrade/freqtrade-strategies)

## 🌟 交易基础设置
+ `timeframe 时间框架`：指定策略所使用的时间框架。
+ `max_open_trades`：持有最大未平仓交易数。取值可以是正整数，用来限制同时进行的交易数量；也可以设为`-1`，表示不受限制，但实际数量仍可能受**交易对列表长度的约束**。
+ `stake_currency`：明确交易的基础货币，如常见的`"USDT"` 。
+ `stake_amount`：每笔交易投入加密货币数量（正数或"unlimited"）。例如，设置为`0.005`，则每次交易固定投入 0.005 个单位的`stake_currency`；若设为`"unlimited"`，会根据可用余额和允许的交易数量动态分配交易金额 。
+ `order_types`：订单类型映射，明确是限价单 or 市价单

```python
# Optional order type mapping
order_types = {
    'entry': 'limit',
    'exit': 'limit',
    'stoploss': 'market',
    'stoploss_on_exchange': False
}
```

## 🌟 交易策略核心设置
+ `【退出/止盈信号】minimal_roi 最小投资回报率`：该配置项以 JSON 对象的形式，定义了不同持仓时间对应的**最小收益率**。达到相应的收益率和持仓时间条件后，会自动退出交易。例如：`{"40": 0.0, "30": 0.01, "20": 0.02, "0": 0.04}`表示持仓 40 分钟后，若利润>= 0 则退出；持仓 30 分钟后，若利润>=达到 1% 则退出，以此类推。如果未在配置文件或策略中设置，默认使用`{"0": 10}`，即盈利达到 1000% 才会触发退出，这在实际应用中几乎不会发生，等同于禁用了最小收益率限制。
+ 【退出/止损信号】`stoploss**`止损：设置止损比例。当资产价值下跌到设定的止损比例时，自动卖出资产以限制损失。例如，设置为`-0.1`，表示当资产价格相较于买入价下跌 10% 时，触发止损操作 。

## 交易模式设置
+ `dry_run`：这一配置项用来确定机器人的运行模式，`true`代表干运行模式，机器人不会进行真实的资金交易，仅模拟交易过程，适合策略测试；`false`则表示生产模式，机器人会进行真实交易，操作真实资金。

## 交易所相关设置
+ `exchange.name`：明确要使用的交易所名称，确保与实际交易平台一致，如`"binance"` 、`"okx"`等，使平台能够与相应的交易所进行交互。
+ `exchange.pair_whitelist`：列出允许机器人进行交易和在回测时考虑的交易对列表。可以使用正则表达式，如`".*/BTC"`，表示所有与比特币交易的交易对。若使用`VolumePairList`，该设置将不生效 。

## 策略选择设置
+ `strategy`：指定要使用的策略类名称，用于指导机器人的交易决策。推荐通过`--strategy NAME`在命令行中设置，也可在配置文件中配置。例如，若策略类名为`MyCustomStrategy`，则在此处填写该名称 。

