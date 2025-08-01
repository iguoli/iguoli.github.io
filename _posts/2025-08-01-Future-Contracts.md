---
title: Futures Contracts
date: 2025-08-01
modify_date: 2025-08-01
tags: Financial
key: Futures-Contracts-2025-08-01
---

## Futures Contracts

**Futures contracts** are standardized agreements to buy or sell an underlying asset at a predetermined price at a specified time in the future. The transaction flow involves multiple parties, including traders, brokers, exchanges, and clearinghouses.

<!--more-->

### Futures Contract Transaction Flow

```mermaid
sequenceDiagram
    title Futures Contract Transaction Flow  
    participant 多头 as 多头 (Long Trader)
    participant 空头 as 空头 (Short Trader)
    participant 经纪商A as 经纪商A (Broker A)
    participant 经纪商B as 经纪商B (Broker B)
    participant 交易所 as 交易所 (Exchange)
    participant 清算所 as 清算所 (Clearing House)

    Note over 多头,空头: 投资者基于对标的资产价格的预期决定交易方向  
    多头 ->> 经纪商A: 提交买入期货合约订单（开仓，多头）
    空头 ->> 经纪商B: 提交卖出期货合约订单（开仓，空头）

    经纪商A ->> 交易所: 传递多头订单
    经纪商B ->> 交易所: 传递空头订单
    交易所 -->> 经纪商A: 订单匹配成功（形成标准化期货合约）
    交易所 -->> 经纪商B: 订单匹配成功（形成标准化期货合约）

    交易所 ->> 清算所: 将合约提交至清算所（清算所成为双方对手方）
    清算所 -->> 交易所: 确认合约生效

    经纪商A ->> 多头: 要求缴纳初始保证金
    多头 -->> 经纪商A: 缴纳初始保证金
    经纪商B ->> 空头: 要求缴纳初始保证金
    空头 -->> 经纪商B: 缴纳初始保证金
    经纪商A ->> 清算所: 划转多头保证金
    经纪商B ->> 清算所: 划转空头保证金

    loop 每日结算（直至到期/平仓）
        交易所 ->> 清算所: 发布当日结算价
        清算所 ->> 经纪商A: 计算多头当日盈亏 (MtM)
        清算所 ->> 经纪商B: 计算空头当日盈亏 (MtM)
        
        alt 多头盈利/空头亏损
            经纪商B ->> 清算所: 划转空头亏损金额
            清算所 ->> 经纪商A: 划转多头盈利金额
            经纪商A ->> 多头: 保证金账户增加盈利
            经纪商B ->> 空头: 保证金账户减少亏损
        else 空头盈利/多头亏损
            经纪商A ->> 清算所: 划转多头亏损金额
            清算所 ->> 经纪商B: 划转空头盈利金额
            经纪商B ->> 空头: 保证金账户增加盈利
            经纪商A ->> 多头: 保证金账户减少亏损
        end
        
        alt 某方保证金低于维持保证金
            经纪商A ->> 多头: 发出追保通知
            多头 -->> 经纪商A: 追加保证金
        else
            经纪商B ->> 空头: 发出追保通知
            空头 -->> 经纪商B: 追加保证金
        end
    end

    Note over 多头,空头: 合约到期前或到期时，选择平仓或交割  
    alt 选择平仓
        多头 ->> 经纪商A: 卖出相同期货合约（平仓）
        空头 ->> 经纪商B: 买入相同期货合约（平仓）
        经纪商A ->> 交易所: 平仓订单
        经纪商B ->> 交易所: 平仓订单
        交易所 -->> 清算所: 确认平仓，合约终止
        清算所 ->> 经纪商A: 返还多头剩余保证金+最终盈亏
        清算所 ->> 经纪商B: 返还空头剩余保证金+最终盈亏
        经纪商A ->> 多头: 划转资金
        经纪商B ->> 空头: 划转资金
    else 选择交割（少数情况）
        空头 ->> 经纪商B: 准备标的资产（如原油、小麦）
        经纪商B ->> 交易所: 交付标的资产
        多头 ->> 经纪商A: 准备合约价款
        经纪商A ->> 交易所: 支付合约价款
        交易所 ->> 多头: 交付标的资产
        交易所 ->> 空头: 支付合约价款
        清算所 -->> 交易所: 确认交割完成，合约终止
    end
```