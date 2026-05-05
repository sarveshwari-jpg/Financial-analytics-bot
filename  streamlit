import streamlit as st
import pandas as pd
import numpy as np
import plotly.express as px
import plotly.graph_objects as go
from io import StringIO

st.set_page_config(page_title="Financial Analytics BOT", layout="wide")
st.title("📊 Financial Analytics BOT")
st.markdown("**Professor's Toolkit** – Cost Sheet · CVP · Budgeting/Forecasting · Variance Analysis")

# ---------- Navigation ----------
page = st.sidebar.radio("Navigate", ["Cost Sheet", "CVP Analysis", "Budgeting & Forecasting", "Variance Analysis"])

# ========== COST SHEET ==========
if page == "Cost Sheet":
    st.header("Cost Sheet Builder")
    st.markdown("Enter cost data to generate a complete cost sheet with charts and interpretation.")

    col1, col2 = st.columns(2)
    with col1:
        units_produced = st.number_input("Units Produced (optional, for per‑unit costs)", min_value=1, value=1000, step=100)
    with col2:
        selling_price = st.number_input("Selling Price per Unit (₹)", min_value=0.0, value=500.0, step=10.0)

    st.subheader("Direct Costs")
    direct_material = st.number_input("Direct Materials (₹)", min_value=0.0, value=200000.0, step=1000.0)
    direct_labor = st.number_input("Direct Labour (₹)", min_value=0.0, value=100000.0, step=1000.0)
    direct_expenses = st.number_input("Direct Expenses (₹)", min_value=0.0, value=20000.0, step=1000.0)

    st.subheader("Factory Overheads")
    factory_overheads = st.number_input("Factory Overheads (₹)", min_value=0.0, value=80000.0, step=1000.0)

    st.subheader("Office & Administration Overheads")
    admin_overheads = st.number_input("Administration Overheads (₹)", min_value=0.0, value=40000.0, step=1000.0)

    st.subheader("Selling & Distribution Overheads")
    selling_overheads = st.number_input("Selling & Distribution Overheads (₹)", min_value=0.0, value=30000.0, step=1000.0)

    if st.button("Generate Cost Sheet", type="primary"):
        # Calculations
        prime_cost = direct_material + direct_labor + direct_expenses
        works_cost = prime_cost + factory_overheads
        cost_of_production = works_cost + admin_overheads
        total_cost = cost_of_production + selling_overheads
        sales_value = selling_price * units_produced
        profit = sales_value - total_cost
        profit_per_unit = profit / units_produced if units_produced else 0

        # Cost per unit helper
        def per_unit(x):
            return x / units_produced if units_produced else 0

        # Display cost sheet table
        data = {
            "Particulars": [
                "Direct Materials", "Direct Labour", "Direct Expenses",
                "**Prime Cost**",
                "Factory Overheads",
                "**Works Cost (Factory Cost)**",
                "Administration Overheads",
                "**Cost of Production**",
                "Selling & Distribution Overheads",
                "**Total Cost**",
                "**Sales**",
                "**Profit**"
            ],
            "Total (₹)": [
                direct_material, direct_labor, direct_expenses,
                prime_cost,
                factory_overheads,
                works_cost,
                admin_overheads,
                cost_of_production,
                selling_overheads,
                total_cost,
                sales_value,
                profit
            ],
            "Per Unit (₹)": [
                per_unit(direct_material), per_unit(direct_labor), per_unit(direct_expenses),
                per_unit(prime_cost),
                per_unit(factory_overheads),
                per_unit(works_cost),
                per_unit(admin_overheads),
                per_unit(cost_of_production),
                per_unit(selling_overheads),
                per_unit(total_cost),
                selling_price,
                profit_per_unit
            ]
        }
        df = pd.DataFrame(data)
        # Format only the numeric columns, leave "Particulars" as text
        st.dataframe(
            df.style.format("{:,.2f}", subset=["Total (₹)", "Per Unit (₹)"]),
            use_container_width=True
        )

        # Waterfall chart: cost buildup to sales
        fig_waterfall = go.Figure(go.Waterfall(
            name="Cost Build-up",
            orientation="v",
            measure=["relative", "relative", "relative", "relative", "relative", "relative", "total"],
            x=["Direct Materials", "Direct Labour", "Direct Expenses",
               "Factory O/H", "Admin O/H", "Selling O/H", "Sales"],
            y=[direct_material, direct_labor, direct_expenses,
               factory_overheads, admin_overheads, selling_overheads, sales_value],
            text=[f"₹{x:,.0f}" for x in [direct_material, direct_labor, direct_expenses,
                                         factory_overheads, admin_overheads, selling_overheads, sales_value]]
        ))
        fig_waterfall.update_layout(title="Cost Build‑up to Sales", yaxis_title="₹")
        st.plotly_chart(fig_waterfall, use_container_width=True)

        # Pie chart for cost components
        pie_data = {
            "Component": ["Direct Materials", "Direct Labour", "Direct Expenses",
                          "Factory Overheads", "Admin Overheads", "Selling Overheads"],
            "Amount": [direct_material, direct_labor, direct_expenses,
                       factory_overheads, admin_overheads, selling_overheads]
        }
        fig_pie = px.pie(pd.DataFrame(pie_data), values="Amount", names="Component",
                         title="Cost Structure Breakdown")
        st.plotly_chart(fig_pie, use_container_width=True)

        # Interpretation
        prime_pct = (prime_cost / total_cost) * 100
        st.markdown("### 📘 Interpretation")
        st.markdown(f"""
        - **Prime Cost** accounts for **{prime_pct:.1f}%** of total cost.  
        - **Total Cost** is **₹{total_cost:,.0f}**; selling all units yields **₹{sales_value:,.0f}** in revenue.  
        - **Profit** of **₹{profit:,.0f}** gives a profit margin of **{(profit/sales_value)*100:.1f}%** on sales.  
        - Per‑unit cost: **₹{per_unit(total_cost):,.2f}**, leaving a per‑unit profit of **₹{profit_per_unit:,.2f}**.  
        - The waterfall chart shows how each cost layer eats into the final selling price.
        """)
# ========== CVP ANALYSIS ==========
elif page == "CVP Analysis":
    st.header("Cost‑Volume‑Profit (CVP) Analysis")
    st.markdown("Compute break‑even, target profit, margin of safety, and CVP graph.")

    col1, col2, col3 = st.columns(3)
    with col1:
        sp = st.number_input("Selling Price per Unit (₹)", min_value=0.0, value=100.0)
    with col2:
        vc = st.number_input("Variable Cost per Unit (₹)", min_value=0.0, value=60.0)
    with col3:
        fc = st.number_input("Total Fixed Costs (₹)", min_value=0.0, value=200000.0)

    col4, col5 = st.columns(2)
    with col4:
        current_units = st.number_input("Current Sales (units, optional)", min_value=0, value=5000, step=100)
    with col5:
        target_profit = st.number_input("Target Profit (₹, optional)", min_value=0.0, value=100000.0)

    if sp <= vc:
        st.warning("Selling price must be greater than variable cost for a meaningful analysis.")
    else:
        cm_per_unit = sp - vc
        cm_ratio = cm_per_unit / sp
        be_units = fc / cm_per_unit
        be_sales = be_units * sp
        mos_units = current_units - be_units if current_units > 0 else None
        mos_sales = mos_units * sp if mos_units else None
        required_units_target = (fc + target_profit) / cm_per_unit

        st.subheader("Key Metrics")
        metrics_cols = st.columns(5)
        metrics_cols[0].metric("Contribution per Unit", f"₹{cm_per_unit:,.2f}")
        metrics_cols[1].metric("CM Ratio", f"{cm_ratio:.1%}")
        metrics_cols[2].metric("Break‑Even (units)", f"{be_units:,.0f}")
        metrics_cols[3].metric("Break‑Even Sales (₹)", f"₹{be_sales:,.0f}")
        if mos_units:
            metrics_cols[4].metric("Margin of Safety (units)", f"{mos_units:,.0f}")

        if target_profit > 0:
            st.markdown(f"🎯 To achieve a target profit of **₹{target_profit:,.0f}**, sell **{required_units_target:,.0f}** units (₹{required_units_target*sp:,.0f} in sales).")

        # CVP Chart
        units_range = np.linspace(0, max(be_units*2, current_units*1.2, 1000), 100)
        sales_line = units_range * sp
        total_cost_line = fc + units_range * vc
        fixed_line = np.full_like(units_range, fc)

        fig_cvp = go.Figure()
        fig_cvp.add_trace(go.Scatter(x=units_range, y=sales_line, mode='lines', name='Total Revenue'))
        fig_cvp.add_trace(go.Scatter(x=units_range, y=total_cost_line, mode='lines', name='Total Cost'))
        fig_cvp.add_trace(go.Scatter(x=units_range, y=fixed_line, mode='lines', name='Fixed Costs',
                                     line=dict(dash='dash')))
        fig_cvp.add_vline(x=be_units, line_dash="dot", annotation_text=f"B.E: {be_units:.0f} units")
        fig_cvp.update_layout(title="CVP / Break‑Even Chart", xaxis_title="Units", yaxis_title="₹")
        st.plotly_chart(fig_cvp, use_container_width=True)

        # Interpretation
        st.markdown("### 📘 Interpretation")
        st.markdown(f"""
        - Every unit sold contributes **₹{cm_per_unit:,.2f}** towards fixed costs and profit.  
        - You must sell **{be_units:,.0f}** units (₹{be_sales:,.0f}) to break even.  
        - At current sales of {current_units:,.0f} units, your margin of safety is **{mos_units:,.0f}** units, meaning sales can drop by that amount before you incur a loss.  
        - To reach a profit of ₹{target_profit:,.0f}, you need to sell **{required_units_target:,.0f}** units.
        """)

# ========== BUDGETING & FORECASTING ==========
elif page == "Budgeting & Forecasting":
    st.header("Budgeting & Forecasting")
    st.markdown("Enter historical sales data (or upload CSV) and forecast future periods.")

    data_input_method = st.radio("Input method", ["Manual Entry", "Upload CSV"])
    hist_values = None
    if data_input_method == "Manual Entry":
        raw = st.text_area("Enter comma‑separated sales values (e.g., 120,135,142,...)", "120,135,142,155,160,168")
        try:
            hist_values = [float(x.strip()) for x in raw.split(",") if x.strip()]
        except:
            st.error("Please enter valid numbers separated by commas.")
    else:
        uploaded_file = st.file_uploader("Upload CSV (single column, no header)", type=["csv"])
        if uploaded_file:
            df_in = pd.read_csv(uploaded_file, header=None)
            if df_in.shape[1] == 1:
                hist_values = df_in.iloc[:, 0].dropna().astype(float).tolist()
            else:
                st.error("CSV must have only one column of numbers.")

    if hist_values and len(hist_values) >= 3:
        method = st.selectbox("Forecasting method", ["Moving Average", "Exponential Smoothing", "Linear Trend"])
        periods = st.slider("Number of future periods to forecast", 1, 12, 3)
        window = 3
        alpha = 0.3
        if method == "Moving Average":
            window = st.number_input("Moving average window", min_value=2, max_value=len(hist_values), value=3)
        elif method == "Exponential Smoothing":
            alpha = st.slider("Smoothing factor (α)", 0.1, 0.9, 0.3)

        if st.button("Generate Forecast", type="primary"):
            hist_series = pd.Series(hist_values)
            forecasts = []
            if method == "Moving Average":
                for i in range(periods):
                    next_val = hist_series.rolling(window).mean().iloc[-1]
                    forecasts.append(next_val)
                    hist_series = pd.concat([hist_series, pd.Series([next_val])], ignore_index=True)
            elif method == "Exponential Smoothing":
                smoothed = hist_series.iloc[-1]
                for i in range(periods):
                    smoothed = alpha * hist_series.iloc[-1] + (1 - alpha) * smoothed
                    forecasts.append(smoothed)
                    hist_series = pd.concat([hist_series, pd.Series([smoothed])], ignore_index=True)
            elif method == "Linear Trend":
                x = np.arange(len(hist_values)).reshape(-1, 1)
                y = np.array(hist_values)
                from sklearn.linear_model import LinearRegression
                model = LinearRegression().fit(x, y)
                future_x = np.arange(len(hist_values), len(hist_values) + periods).reshape(-1, 1)
                forecasts = model.predict(future_x).tolist()
                hist_series = pd.concat([hist_series, pd.Series(forecasts)], ignore_index=True)

            # Plot
            fig_forecast = go.Figure()
            fig_forecast.add_trace(go.Scatter(
                y=hist_values, mode='lines+markers', name='Historical'))
            fig_forecast.add_trace(go.Scatter(
                x=list(range(len(hist_values)-1, len(hist_values)+periods)),
                y=[hist_values[-1]] + forecasts, mode='lines+markers',
                name='Forecast', line=dict(dash='dash')))
            fig_forecast.update_layout(title="Sales Forecast", xaxis_title="Period", yaxis_title="Sales")
            st.plotly_chart(fig_forecast, use_container_width=True)

            # Interpretation
            st.markdown("### 📘 Interpretation")
            avg_hist = np.mean(hist_values)
            avg_forecast = np.mean(forecasts)
            trend = "upward" if avg_forecast > avg_hist else "downward" if avg_forecast < avg_hist else "flat"
            st.markdown(f"""
            - Historical average sales: **{avg_hist:,.1f}**; forecast average: **{avg_forecast:,.1f}**.  
            - The forecast indicates an **{trend}** trend.  
            - Using **{method}** with {'window='+str(window) if method=='Moving Average' else 'α='+str(alpha) if method=='Exponential Smoothing' else 'linear regression'}, the next {periods} periods are predicted.  
            - Use these estimates for revenue budgeting and capacity planning.
            """)
    elif hist_values is not None:
        st.info("Please provide at least 3 data points.")

# ========== VARIANCE ANALYSIS ==========
elif page == "Variance Analysis":
    st.header("Variance Analysis")
    st.markdown("Compare standard vs actual costs to calculate and interpret variances.")

    cost_element = st.selectbox("Cost Element", ["Direct Materials", "Direct Labour"])
    element_label = "Price/Rate" if cost_element == "Direct Materials" else "Rate per Hour"
    quantity_label = "Quantity" if cost_element == "Direct Materials" else "Hours"

    col1, col2 = st.columns(2)
    with col1:
        st.subheader("Standard")
        std_price = st.number_input(f"Standard {element_label} (₹)", min_value=0.0, value=10.0)
        std_qty = st.number_input(f"Standard {quantity_label}", min_value=0.0, value=1000.0)
    with col2:
        st.subheader("Actual")
        act_price = st.number_input(f"Actual {element_label} (₹)", min_value=0.0, value=11.0)
        act_qty = st.number_input(f"Actual {quantity_label}", min_value=0.0, value=950.0)

    if st.button("Calculate Variances", type="primary"):
        price_var = (std_price - act_price) * act_qty
        usage_var = (std_qty - act_qty) * std_price
        total_var = (std_qty * std_price) - (act_qty * act_price)

        var_df = pd.DataFrame({
            "Variance": ["Price / Rate Variance", "Usage / Efficiency Variance", "Total Cost Variance"],
            "Amount (₹)": [price_var, usage_var, total_var],
            "Nature": ["Favourable" if price_var > 0 else "Adverse" if price_var < 0 else "None",
                       "Favourable" if usage_var > 0 else "Adverse" if usage_var < 0 else "None",
                       "Favourable" if total_var > 0 else "Adverse" if total_var < 0 else "None"]
        })
        st.table(var_df.style.format({"Amount (₹)": "{:,.2f}"}))

        fig_var = go.Figure(go.Waterfall(
            name="Variance Breakdown",
            orientation="v",
            measure=["relative", "relative", "total"],
            x=["Standard Cost", "Price Var", "Usage Var", "Actual Cost"],
            y=[std_qty*std_price, price_var, usage_var, act_qty*act_price],
            text=[f"₹{x:,.2f}" for x in [std_qty*std_price, price_var, usage_var, act_qty*act_price]]
        ))
        fig_var.update_layout(title="Variance Breakdown", yaxis_title="₹")
        st.plotly_chart(fig_var, use_container_width=True)

        st.markdown("### 📘 Interpretation")
        price_interp = "less" if price_var > 0 else "more"
        usage_interp = "efficient" if usage_var > 0 else "inefficient"
        st.markdown(f"""
        - **Price Variance**: Because the actual {element_label.lower()} was **{price_interp}** than standard, the price variance is **{'favourable' if price_var > 0 else 'adverse'}** (**₹{price_var:,.2f}**).  
        - **Usage Variance**: The usage of {quantity_label.lower()} was **{usage_interp}**, leading to a **{'favourable' if usage_var > 0 else 'adverse'}** usage variance (**₹{usage_var:,.2f}**).  
        - **Total Variance**: Overall, total cost is **₹{abs(total_var):,.2f}** **{'lower' if total_var > 0 else 'higher'}** than standard.  
        - Managers should investigate {'higher actual prices' if price_var < 0 else ''} {'and' if price_var < 0 and usage_var < 0 else ''} {'excessive usage' if usage_var < 0 else ''} to control costs.
        """)
