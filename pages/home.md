---
name: Home
assetId: c69939e6-c601-4c11-9b9e-45f8ad027b9b
type: page
---

# {% logo domain="adusa.com" size="lg" /%} ADUSA Spins

{% dropdown
    id="vendor_dropdown"
    data="adusa_spins_vendors"
    value_column="adusa_vendor_name_slug"
    label_column="adusa_vendor_name"
    order="adusa_vendor_name asc"
    select_first=true
    multiple=false
    search=true
    title="Vendor"
/%}

{% range_calendar
    id="calendar_filter"
    title="Time Period"
    default_range="year to date"
/%}

{% button_group
    id="division_dropdown"
    data="adusa_spins"
    title="ADUSA Brand"
    value_column="division"
    filters=["vendor_dropdown"]
/%}

{% dropdown
    id="facility_dropdown"
    data="adusa_spins"
    value_column="facility_description"
    placeholder="Select Facility"
    title="Facility"
    filters=["vendor_dropdown", "division_dropdown"]
    search=false
/%}

{% dropdown
    id="product_dropdown"
    data="adusa_spins"
    value_column="item_description_full"
    title="Product"
    search=true
    multiple=true
    filters=["vendor_dropdown", "division_dropdown", "facility_dropdown"]
    placeholder="Select Product"
/%}

{% callout type="info" title="Time Period"%}
{% value data="adusa_spins" value="min(week_start_date)" filters=["vendor_dropdown"] date_range={date="week_start_date" range="{{calendar_filter}}"} fmt="longdate" /%} to {% value data="adusa_spins" value="max(week_start_date) + 6" filters=["vendor_dropdown"] date_range={date="week_start_date" range="{{calendar_filter}}"} fmt="longdate" /%} ({% value data="adusa_spins" value="count(distinct week_start_date)" filters=["vendor_dropdown"] fmt="num0" date_range={date="week_start_date" range="{{calendar_filter}}"} /%} Weeks). {{vendor_dropdown.label}} has {% value data="adusa_spins" value="count(distinct week_start_date)" filters=["vendor_dropdown"] fmt="num0" /%} weeks of data available.
{% /callout %}

{% big_value
    data="adusa_spins"
    value="sum(cases_shipped) as 'Cases'"
    fmt="num"
    comparison={
        compare_vs="target"
        target="sum(cases_shipped_year_ago)"
        display_type="pct"
        pct_fmt="pct1"
        abs_fmt="num"
        text="vs. year ago"
    }
    sparkline={
        type="area"
        x="week_start_date"
    }
    filters=["vendor_dropdown","division_dropdown","facility_dropdown"]
    date_range={
        range="{{calendar_filter}}"
        date="week_start_date"
    }
/%}

{% big_value
    data="adusa_spins"
    value="sum(packs_shipped) as 'Units'"
    fmt="num"
    comparison={
        compare_vs="target"
        target="sum(packs_shipped_year_ago)"
        display_type="pct"
        pct_fmt="pct1"
        abs_fmt="num"
        text="vs. year ago"
    }
    sparkline={
        type="area"
        x="week_start_date"
    }
    filters=["vendor_dropdown","division_dropdown","facility_dropdown"]
    date_range={
        range="{{calendar_filter}}"
        date="week_start_date"
    }
/%}

{% row %}
{% pie_chart
    data="adusa_spins"
    title="Cases by Brand"
    subtitle="{{vendor_dropdown.label}}"
    category="division"
    value="sum(cases_shipped) as 'Cases'"
    filters=["vendor_dropdown"]
    inner_radius="0"
    date_range={
        range="{{calendar_filter}}"
        date="week_start_date"
    }
    width=40
    height=300
/%}
{% combo_chart
    data="adusa_spins"
    title="Cases by Week"
    subtitle="{{vendor_dropdown.label}} {{division_dropdown.label}}"
    x="week_start_date"
    filters=["vendor_dropdown","division_dropdown","facility_dropdown"]
    date_range={
        range="{{calendar_filter}}"
        date="week_start_date"
    }
%}

{% line
    y="sum(cases_shipped) as 'Cases'"
    fmt="num0"
    options={
        width=3
    }
/%}

{% line
    y="sum(cases_shipped_year_ago) as 'Cases YA'"
    fmt="num"
    options={
        width=3
    }
/%}

{% /combo_chart %}
{% /row %}

{% table
    data="adusa_spins"
    title="Brand Detail"
    subtitle="{{vendor_dropdown.label}}"
    filters=["vendor_dropdown"]
    order="sum(cases_shipped) desc"
    date_range={
        range="{{calendar_filter}}"
        date="week_start_date"
    }
    total_position="top"
%}
{% dimension
    value="division"
    title="ADUSA Brand"
    logo="adusa_brand_logo"
    logo_options={
        size="lg"
    }
/%}
{% measure
    value="sum(cases_shipped) as cases"
    fmt="num0"
    sort="desc"
    column_group="Cases"
/%}
{% measure
    value="sum(cases_shipped) - sum(cases_shipped_year_ago) as cases_yoy"
    title="vs YA"
    fmt="num0"
    viz="bar"
    column_group="Cases"
/%}
{% measure
    value="(sum(cases_shipped) - sum(cases_shipped_year_ago)) / nullIf(sum(cases_shipped_year_ago), 0) as cases_yoy_pct"
    title="vs YA %"
    fmt="pct1"
    viz="delta"
    column_group="Cases"
/%}
{% measure
    value="sum(packs_shipped) as units"
    fmt="num0"
    sort="desc"
    column_group="Units"
/%}
{% measure
    value="sum(packs_shipped) - sum(packs_shipped_year_ago) as units_yoy"
    title="vs YA"
    fmt="num0"
    viz="bar"
    column_group="Units"
/%}
{% measure
    value="(sum(packs_shipped) - sum(packs_shipped_year_ago)) / nullIf(sum(packs_shipped_year_ago), 0) as units_yoy_pct"
    title="vs YA %"
    fmt="pct1"
    viz="delta"
    column_group="Units"
/%}
{% /table %}

{% table
    data="adusa_spins"
    title="Cases by Week Start Date by Product (Year to Date)"
    freeze_columns=1
    subtitle="{{vendor_dropdown.label}} {{division_dropdown.label}}"
    filters=["vendor_dropdown","division_dropdown"]
    order="sum(cases_shipped) desc"
    date_range={
        range="year to date"
        date="week_start_date"
    }
    total_position="top"
%}
{% pivot
    value="week_start_date"
    date_grain="quarter of year"
/%}
{% pivot
    value="week_start_date"
    date_grain="week"
    fmt="shortdate"
/%}
{% dimension
    value="item_description_full"
    title="Product"
/%}
{% measure
    value="sum(cases_shipped) as cases"
    fmt="num0"
    sort="desc"
/%}
{% measure
    value="(sum(cases_shipped) - sum(cases_shipped_year_ago)) / nullIf(sum(cases_shipped_year_ago), 0) as cases_yoy_pct"
    title="vs YA %"
    fmt="pct1"
    viz="delta"
/%}
{% /table %}

{% table
    data="adusa_spins"
    title="Facility Detail"
    subtitle="{{vendor_dropdown.label}} {{division_dropdown.label}}"
    filters=["vendor_dropdown","division_dropdown"]
    order="sum(cases_shipped) desc"
    date_range={
        range="{{calendar_filter}}"
        date="week_start_date"
    }
    total_position="top"
%}
{% dimension
    value="facility_description"
    title="Facility"
/%}
{% measure
    value="sum(cases_shipped) as cases"
    fmt="num0"
    sort="desc"
    column_group="Cases"
/%}
{% measure
    value="sum(cases_shipped) - sum(cases_shipped_year_ago) as cases_yoy"
    title="vs YA"
    fmt="num0"
    viz="bar"
    column_group="Cases"
/%}
{% measure
    value="(sum(cases_shipped) - sum(cases_shipped_year_ago)) / nullIf(sum(cases_shipped_year_ago), 0) as cases_yoy_pct"
    title="vs YA %"
    fmt="pct1"
    viz="delta"
    column_group="Cases"
/%}
{% measure
    value="sum(packs_shipped) as units"
    fmt="num0"
    sort="desc"
    column_group="Units"
/%}
{% measure
    value="sum(packs_shipped) - sum(packs_shipped_year_ago) as units_yoy"
    title="vs YA"
    fmt="num0"
    viz="bar"
    column_group="Units"
/%}
{% measure
    value="(sum(packs_shipped) - sum(packs_shipped_year_ago)) / nullIf(sum(packs_shipped_year_ago), 0) as units_yoy_pct"
    title="vs YA %"
    fmt="pct1"
    viz="delta"
    column_group="Units"
/%}
{% /table %}

{% table
    data="adusa_spins"
    title="Item Detail"
    freeze_columns=1
    subtitle="{{vendor_dropdown.label}} {{division_dropdown.label}}"
    filters=["vendor_dropdown","division_dropdown","facility_dropdown"]
    order="sum(cases_shipped) desc"
    date_range={
        range="{{calendar_filter}}"
        date="week_start_date"
    }
    show_subtotal_rows=false
    total_position="top"
    repeat_values=true
%}
{% dimension
    value="item_description"
    title="Description"
/%}
{% dimension
    value="upc"
    title="UPC"
/%}
{% dimension
    value="adusa_item_number"
    title="ADUSA Num"
/%}
{% measure
    value="sum(cases_shipped) as cases"
    fmt="num0"
    sort="desc"
    column_group="Cases"
/%}
{% measure
    value="sum(cases_shipped) - sum(cases_shipped_year_ago) as cases_yoy"
    title="vs YA"
    fmt="num0"
    viz="bar"
    column_group="Cases"
/%}
{% measure
    value="(sum(cases_shipped) - sum(cases_shipped_year_ago)) / nullIf(sum(cases_shipped_year_ago), 0) as cases_yoy_pct"
    title="vs YA %"
    fmt="pct1"
    viz="delta"
    column_group="Cases"
/%}
{% measure
    value="sum(packs_shipped) as units"
    fmt="num0"
    sort="desc"
    column_group="Units"
/%}
{% measure
    value="sum(packs_shipped) - sum(packs_shipped_year_ago) as units_yoy"
    title="vs YA"
    fmt="num0"
    viz="bar"
    column_group="Units"
/%}
{% measure
    value="(sum(packs_shipped) - sum(packs_shipped_year_ago)) / nullIf(sum(packs_shipped_year_ago), 0) as units_yoy_pct"
    title="vs YA %"
    fmt="pct1"
    viz="delta"
    column_group="Units"
/%}
{% /table %}

{% download
    data="adusa_spins"
    label="Download Raw Data"
    variant="secondary"
    filters=["vendor_dropdown","facility_dropdown"]
    where="week_start_date {{calendar_filter.between}}"
/%}