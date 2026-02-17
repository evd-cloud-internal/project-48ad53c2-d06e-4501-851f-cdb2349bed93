---
name: PnL_Var
assetId: 31923563-0795-4ced-950d-a92cb5550b25
type: page
---

# VAR vs DoD

```sql pnl_data
WITH 
    date_spine AS (
        SELECT arrayJoin(
            arrayMap(x -> toDate('2025-04-01') + x, 
            range(toUInt32(dateDiff('day', toDate('2025-04-01'), toDate('2026-02-14')))))
        ) as date
    ),
    raw AS (
        SELECT 
            toDate(asofdate) as date,
            "Power Dod YTD" as Ytd_pnL,
            DoD as daily_pnl,
            "VAR+1" as VAR
        FROM var_vs_dodv2
        WHERE "Power Dod YTD" != 0
    ),
    joined AS (
        SELECT 
            ds.date,
            r.Ytd_pnL,
            r.daily_pnl,
            r.VAR
        FROM date_spine ds
        LEFT JOIN raw r ON ds.date = r.date
    )
SELECT 
    date,
    last_value(Ytd_pnL) IGNORE NULLS OVER (ORDER BY date) as Ytd_pnL,
    daily_pnl,
    last_value(VAR) IGNORE NULLS OVER (ORDER BY date) as VAR
FROM joined
ORDER BY date
```

{% combo_chart
    data="pnl_data"
    x="date"
    x_sort="data"
    title="YTD Power PnL"
    subtitle="YTD PnL, VAR & DoD PnL"
    y_fmt="num0"
    x_axis_options={max_interval="month" gridlines=false}
%}
    {% line y="Ytd_pnL" /%}
    {% line y="VAR" options={color="#00CC66"} /%}
    {% bar y="daily_pnl" options={color="#e63946" opacity=0.8} /%}
    {% reference_point 
        x="2025-06-24" 
        y=3500000 
        label="Largest DoD Gain" 
        color="black"
        label_options={
            variant="callout"
            position="top"
            color="black"
        }
        symbol_options={
            color="black"
        }
    /%}
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
    {% reference_point 
        x="2026-01-02" 
        y=-1427444 
        label="DoD Loss" 
        color="#e63946"
        label_options={
            variant="callout"
            position="left"
        }
    /%}
{% /combo_chart %}

{% callout type="info" title="Largest DoD Gain: Jun 24, 2025" %}
The largest single-day gain was **$1.7M** — bearish weather ample & ample near-term supply of LNG into Europe allowed desk to take advantage of customer flows & TTF seasonal timespreads.
{% /callout %}

{% callout type="error" title="Worst DoD Loss: Oct 6, 2025" %}
The largest single-day loss was **-$2.7M** — a significant drop in wind forecast resulted in front weeks increasing by c. +£20/MWh in the UK & +€25/MWh.

VAR remains high as desk close out and re-enter into net energy long positions resulting in further losses.

Approx. **-$7M** across the two weeks.
{% /callout %}

{% callout type="error" title="DoD Loss: Jan 2, 2026" %}
Additional weather-driven losses recorded as the prompt strengthened on colder temperatures and low wind output, with the risk of a potential Dunkelflaute (a period of weak wind and solar generation).

As the desk approaches stop limits, VAR is declining as positions are reduced and risk-taking becomes more constrained.
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