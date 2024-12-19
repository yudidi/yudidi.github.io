---
layout: post 
title: uniswap计算实际储备量
categories: [web3]
tags: [dex]
---

# 根据L,sqrt_price_x96,min-max-tick 计算出当前的实际储备量。同时计算当前价格。
slot0返回的
* sqrt_price_x96精确
* tick:这个不精确，只有跨越tick才回记录一下

> 对应的池子 https://app.uniswap.org/positions/v3/ethereum_sepolia/30563

# 参考资料
1. https://blog.uniswap.org/uniswap-v3-math-primer
2. [Liquidity Math in Uniswap v3 by Atis Elsts](https://atiselsts.github.io/pdfs/uniswap-v3-liquidity-math.pdf)

* 2.1 [pdf翻译](https://fanyi.baidu.com/mtpe-individual/editor/read/vGdvRzNvqJ)

* 2.2 [pdf通义](https://lxblog.com/qianwen/share?shareId=38475a0a-bdc5-4fc4-a973-38905bb0c9dc)

# 计算结果和显示流动性一致

## 群讨论
![_config.yml]({{ site.baseurl }}/images/content/20241219储备量和流动性.png)

```python
import math


def tick_to_price(tick):
    """将tick值转换为价格"""
    return 1.0001 ** tick


def sqrt_price_x96_to_price(sqrt_price_x96):
    """将sqrtPriceX96转换为价格P"""
    # Convert Q64.96 fixed-point number to float and square it to get the price
    sqrt_price = sqrt_price_x96 / (2 ** 96)
    price = sqrt_price ** 2

    # 单独打印价格
    # Adjust for token decimals
    adjusted_decimal_price = price * (10 ** decimals_x) / (10 ** decimals_y)
    print(f'price内部使用价格:{price:.20f}')
    print(f'adjusted_decimal_price使用精度调整后的价格:{adjusted_decimal_price:.20f}')
    reverted_price = 1/adjusted_decimal_price
    print(f"reverted_price(对应UI上的价格10000)(reverted_price=1/adjusted_decimal_price): {reverted_price:.20f} ")  # xxx token per ETH.
    return price


def calculate_real_reserves(L, sqrt_price_x96, lower_tick, upper_tick):
    # 将tick转换为价格
    P = sqrt_price_x96_to_price(sqrt_price_x96)
    pa = tick_to_price(lower_tick)
    pb = tick_to_price(upper_tick)

    # 根据当前价格和流动性计算实际储备量
    if P <= pa:
        # 当前价格低于下界；所有流动性都提供x资产
        x_real = L * (math.sqrt(pb) - math.sqrt(P)) / (math.sqrt(P) * math.sqrt(pb))
        y_real = 0
    elif P >= pb:
        # 当前价格高于上界；所有流动性都提供y资产
        x_real = 0
        y_real = L * (math.sqrt(P) - math.sqrt(pa))
    else:
        # 当前价格位于范围内；流动性同时提供x和y资产
        x_real = L * (math.sqrt(pb) - math.sqrt(P)) / (math.sqrt(P) * math.sqrt(pb))
        y_real = L * (math.sqrt(P) - math.sqrt(pa))

    return x_real, y_real


def calculate_real_reserves_with_decimals(L, sqrt_price_x96, lower_tick, upper_tick, decimals_x=18, decimals_y=18):
    # 将tick转换为价格
    P = sqrt_price_x96_to_price(sqrt_price_x96)
    pa = tick_to_price(lower_tick)
    pb = tick_to_price(upper_tick)

    # 根据当前价格和流动性计算实际储备量
    if P <= pa:
        # 当前价格低于下界；所有流动性都提供x资产
        x_real = L * (math.sqrt(pb) - math.sqrt(P)) / (math.sqrt(P) * math.sqrt(pb))
        y_real = 0
    elif P >= pb:
        # 当前价格高于上界；所有流动性都提供y资产
        x_real = 0
        y_real = L * (math.sqrt(P) - math.sqrt(pa))
    else:
        # 当前价格位于范围内；流动性同时提供x和y资产
        x_real = L * (math.sqrt(pb) - math.sqrt(P)) / (math.sqrt(P) * math.sqrt(pb))
        y_real = L * (math.sqrt(P) - math.sqrt(pa))

    # 调整显示以匹配代币的小数位数
    x_real_adjusted = x_real / (10 ** decimals_x)
    y_real_adjusted = y_real / (10 ** decimals_y)

    return x_real_adjusted, y_real_adjusted


# 示例参数
L = 171559584868497  # 流动性
sqrt_price_x96 = 25054131037751093162929900172930  # sqrtPriceX96
lower_tick = 111060  # 最小tick值
upper_tick = 122040  # 最大tick值
decimals_x = 9  # 假设Token X有9个小数位
decimals_y = 18  # 假设Token Y有18个小数位

# x:mkndx
# y:eth

# 计算实际储备量
x_real, y_real = calculate_real_reserves(L, sqrt_price_x96, lower_tick, upper_tick)

print(f"x_real整数无精度 = {x_real:.8f}")
print(f"y_real整数无精度 = {y_real:.8f}")
print('<<<==========>>>>')
# 计算实际储备量并调整显示
x_real_dem, y_real_dem = calculate_real_reserves_with_decimals(L, sqrt_price_x96, lower_tick, upper_tick, decimals_x, decimals_y)

print(f"x_real_dem (adjusted) = {x_real_dem:.8f}")
print(f"y_real_dem (adjusted) = {y_real_dem:.8f}")
```

    price内部使用价格:99999.88984058334608562291
    adjusted_decimal_price使用精度调整后的价格:0.00009999988984058334
    reverted_price(对应UI上的价格10000)(reverted_price=1/adjusted_decimal_price): 10000.01101595380168873817 
    x_real整数无精度 = 158385514873.74179077
    y_real整数无精度 = 9999999999967028.00000000
    <<<==========>>>>
    price内部使用价格:99999.88984058334608562291
    adjusted_decimal_price使用精度调整后的价格:0.00009999988984058334
    reverted_price(对应UI上的价格10000)(reverted_price=1/adjusted_decimal_price): 10000.01101595380168873817 
    x_real_dem (adjusted) = 158.38551487
    y_real_dem (adjusted) = 0.01000000


# 计算上下界对应的价格


```python
import math

# Min and Max ticks from the document
min_tick = 111060
max_tick = 122040

# Calculate the prices corresponding to the ticks
price_min = 1.0001 ** min_tick
price_max = 1.0001 ** max_tick

"""
p = y / x; y:eth(18) x:mkdnx(9), 表示y=px,表示x的价格,1个x兑换多少y. // 1个eth兑换多少usdc.
p =
p(调) = y/10^y的精度 / x/10^x的精度 = p * 10^(x精度-y精度) =  p * 10^(9-18)
1/p(调) =
"""

# Adjust for the decimals of USDC and ETH
adjusted_price_min = price_min * (10 ** (9-18))
adjusted_price_max = price_max * (10 ** (9-18))

# Print the adjusted prices in human-readable form
print(f"The minimum price is {adjusted_price_min:.8f} ETH per mkdnx.")
print(f"The maximum price is {adjusted_price_max:.8f} ETH per mkdnx.")
print(f"1/adjusted_price_min: {1/adjusted_price_min:.20f} mkdnx per ETH.")  # 使用 f-string 格式化，保留 20 位小数
print(f"1/adjusted_price_max: {1/adjusted_price_max:.20f} mkdnx per ETH.")  # 使用 f-string 格式化，保留 20 位小数
```

    The minimum price is 0.00006653 ETH per mkdnx.
    The maximum price is 0.00019946 ETH per mkdnx.
    1/adjusted_price_min: 15030.26478859876260685269 mkdnx per ETH.
    1/adjusted_price_max: 5013.43203397946945187869 mkdnx per ETH.

