---
name: Vendor Grid
assetId: 99d378dc-b8e7-44bb-9220-9fd9e81486a5
type: page
---

# Vendor Grid

{% table
  data="adusa_spins"
  subtotals=false
  page_size=30
  freeze_columns=1
%}
{% pivot
  value="week_start_date"
  sort="desc"
/%}
{% dimension
  value="adusa_vendor_name_slug"
  title="Vendor"
/%}
{% measure
  value="sum(cases_shipped) as cases"
  fmt="num0"
/%}
{% /table %}