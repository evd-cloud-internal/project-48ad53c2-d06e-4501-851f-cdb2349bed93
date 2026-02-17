---
name: PnL_Var
assetId: 31923563-0795-4ced-950d-a92cb5550b25
type: page
---

# VAR vs DoD

```sql pnl_data
SELECT 
    addDays(toDate32('1899-12-30'), toInt32(asofdate)) as date,
    "Power Dod YTD" as cumulative_pnl,
    DoD as dod_pnl,
    "VAR+1" as var_plus_1
FROM var_vs_dod
WHERE DoD != 0 OR "Power Dod YTD" != 0 OR "VAR+1" != 0
ORDER BY asofdate
```

{% combo_chart
    data="pnl_data"
    x="date"
    title="VAR vs DoD PnL"
    subtitle="Cumulative PnL & VAR+1 (left axis) vs Daily DoD (right axis)"
    y_fmt="num0"
    y2_fmt="num0"
    y2_axis_options={title="DoD PnL"}
    y_axis_options={title="Cumulative PnL / VAR+1"}
%}
    {% line y="cumulative_pnl" /%}
    {% line y="var_plus_1" /%}
    {% bar y="dod_pnl" axis="y2" options={opacity=0.4} /%}
{% /combo_chart %}