---
name: PnL_Var
assetId: 31923563-0795-4ced-950d-a92cb5550b25
type: page
---

# VAR vs DoD

```sql pnl_data
SELECT 
    toString(asofdate) as date,
    "Power Dod YTD" as Ytd_pnL,
    DoD as daily_pnl,
    "VAR+1" as VAR
FROM var_vs_dodv2
WHERE "Power Dod YTD" != 0
ORDER BY asofdate
```

{% combo_chart
    data="pnl_data"
    x="date"
    x_sort="data"
    title="VAR vs DoD PnL"
    subtitle="YTD PnL, VAR & DoD PnL"
    y_fmt="num0"
%}
    {% line y="Ytd_pnL" /%}
    {% line y="VAR" /%}
    {% bar y="daily_pnl" options={color="#e63946" opacity=0.8} /%}
    {% reference_point 
        x="2025-10-06" 
        y=-2741880 
        label="Worst DoD Loss" 
        color="#e63946"
        label_options={
            variant="callout"
            position="left"
        }
    /%}
{% /combo_chart %}

{% callout type="error" title="Worst DoD Loss: Oct 6, 2025" %}
The largest single-day loss was **-$2,741,880** on October 6, 2025 — significantly exceeding the VAR estimate for that day.
{% /callout %}

{% combo_chart
    data="pnl_data"
    x="date"
    x_sort="data"
    title="YTD PnL & VAR"
    y_fmt="num0"
%}
    {% line y="Ytd_pnL" /%}
    {% line y="VAR" /%}
{% /combo_chart %}

{% combo_chart
    data="pnl_data"
    x="date"
    x_sort="data"
    title="DoD PnL & VAR"
    y_fmt="num0"
%}
    {% bar y="daily_pnl" options={opacity=0.8} /%}
    {% line y="VAR" /%}
{% /combo_chart %}