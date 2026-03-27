# ================================================================
#  YouTube Shorts Analytics Dashboard
#  Save as app.py | Run: streamlit run app.py
#  Requirements: pip install streamlit pandas plotly openpyxl
# ================================================================

import streamlit as st
import pandas as pd
import numpy as np
import plotly.express as px
import plotly.graph_objects as go

# ── Page config ──────────────────────────────────────────────────
st.set_page_config(
    page_title="YouTube Shorts Dashboard",
    page_icon="▶️",
    layout="wide",
    initial_sidebar_state="expanded"
)

# ── Brand colours ────────────────────────────────────────────────
RED    = "#FF0000"
DARK   = "#0F0F0F"
GREY   = "#282828"
LIGHT  = "#F9F9F9"
WHITE  = "#FFFFFF"
GOLD   = "#FFC107"
TEAL   = "#00B4D8"

# ── CSS ─────────────────────────────────────────────────────────
st.markdown(f"""
<style>
  .stApp {{ background-color: {LIGHT}; }}

  [data-testid="stSidebar"] {{
      background-color: {DARK} !important;
  }}
  [data-testid="stSidebar"] * {{ color: white !important; }}

  [data-testid="stMetric"] {{
      background-color: {WHITE};
      border-left: 4px solid {RED};
      border-radius: 10px;
      padding: 16px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  }}
  [data-testid="stMetricValue"] {{
      color: {DARK} !important;
      font-size: 1.8rem !important;
      font-weight: 800 !important;
  }}
  [data-testid="stMetricLabel"] {{
      color: #555 !important;
      font-size: 0.82rem !important;
      font-weight: 600 !important;
  }}

  h1, h2, h3 {{ color: {DARK}; }}
  h2 {{ border-bottom: 3px solid {RED}; padding-bottom: 6px; }}

  .stTabs [data-baseweb="tab-list"] {{
      gap: 4px;
      background-color: {WHITE};
      padding: 6px;
      border-radius: 10px;
      box-shadow: 0 1px 4px rgba(0,0,0,0.08);
  }}
  .stTabs [data-baseweb="tab"] {{
      background-color: {LIGHT};
      border-radius: 8px;
      color: {DARK};
      font-weight: 600;
      padding: 8px 22px;
  }}
  .stTabs [aria-selected="true"] {{
      background-color: {RED} !important;
      color: white !important;
  }}

  .insight-box {{
      background: {WHITE};
      border-left: 4px solid {GOLD};
      border-radius: 8px;
      padding: 14px 18px;
      margin: 12px 0;
      box-shadow: 0 2px 6px rgba(0,0,0,0.06);
      color: {DARK};
      font-size: 0.92rem;
  }}
  .insight-box b {{ color: {RED}; }}

  .highlight-card {{
      background: linear-gradient(135deg, {DARK}, {GREY});
      border-radius: 12px;
      padding: 20px 24px;
      color: white;
      margin-bottom: 20px;
  }}
</style>
""", unsafe_allow_html=True)


# ── Load data ────────────────────────────────────────────────────
@st.cache_data
def load_data():
    df = pd.read_excel(
        "YouTube_Dashboard-_DA.xlsx",
        sheet_name="YouTube_Shorts_Engagement_and_G"
    )
    df = df.dropna(subset=["Video_ID"])
    df["Engagement_Rate_%"] = pd.to_numeric(df["Engagement_Rate_%"], errors="coerce")
    df["Views_Per_Day"]     = pd.to_numeric(df["Views_Per_Day"], errors="coerce")
    df["Views_M"]           = (df["Views"] / 1_000_000).round(2)
    df["Likes_K"]           = (df["Likes"] / 1_000).round(1)
    return df

df = load_data()


# ── Sidebar ──────────────────────────────────────────────────────
with st.sidebar:
    st.markdown("## ▶️ YouTube Shorts")
    st.markdown("*Analytics Dashboard*")
    st.markdown("---")

    st.markdown("### 🔍 Filters")

    # Channel filter
    top_channels = df["Channel_Name"].value_counts().head(30).index.tolist()
    channel_filter = st.multiselect(
        "Channel (Top 30)",
        options=top_channels,
        default=top_channels
    )

    # Views range
    min_v, max_v = int(df["Views"].min()), int(df["Views"].max())
    views_range = st.slider(
        "Views Range",
        min_value=min_v, max_value=max_v,
        value=(min_v, max_v),
        format="%d"
    )

    # Engagement range
    min_e = float(df["Engagement_Rate_%"].min())
    max_e = float(df["Engagement_Rate_%"].max())
    eng_range = st.slider(
        "Engagement Rate (%)",
        min_value=round(min_e, 2),
        max_value=round(max_e, 2),
        value=(round(min_e, 2), round(max_e, 2))
    )

    # Age filter
    age_range = st.slider(
        "Video Age (Days)",
        min_value=int(df["Age_In_Days"].min()),
        max_value=int(df["Age_In_Days"].max()),
        value=(int(df["Age_In_Days"].min()), int(df["Age_In_Days"].max()))
    )

    st.markdown("---")
    st.markdown(f"**Total Videos:** {len(df):,}")

# ── Apply filters ────────────────────────────────────────────────
dff = df[
    df["Channel_Name"].isin(channel_filter) &
    df["Views"].between(views_range[0], views_range[1]) &
    df["Engagement_Rate_%"].between(eng_range[0], eng_range[1]) &
    df["Age_In_Days"].between(age_range[0], age_range[1])
]


# ── Header ───────────────────────────────────────────────────────
st.markdown(f"""
<div class='highlight-card'>
  <h1 style='color:white; margin:0; font-size:2rem;'>▶️ YouTube Shorts Analytics</h1>
  <p style='color:#aaa; margin:6px 0 0 0;'>
    Engagement & Growth Dashboard &nbsp;·&nbsp; {len(dff):,} videos shown
  </p>
</div>
""", unsafe_allow_html=True)


# ── KPI Row ──────────────────────────────────────────────────────
k1, k2, k3, k4, k5, k6 = st.columns(6)

k1.metric("📹 Videos",          f"{len(dff):,}")
k2.metric("👁️ Avg Views",       f"{dff['Views'].mean()/1e6:.2f}M")
k3.metric("👍 Avg Likes",       f"{dff['Likes'].mean()/1e3:.1f}K")
k4.metric("💬 Avg Comments",    f"{dff['Comments'].mean():,.0f}")
k5.metric("⚡ Avg Engagement",  f"{dff['Engagement_Rate_%'].mean():.2f}%")
k6.metric("📈 Avg Views/Day",   f"{dff['Views_Per_Day'].mean():,.0f}")

st.markdown("<br>", unsafe_allow_html=True)

# ── Key highlights (matches the image) ───────────────────────────
st.markdown(f"""
<div style='background:{WHITE}; border-radius:10px; padding:16px 24px;
            box-shadow:0 2px 8px rgba(0,0,0,0.07); margin-bottom:20px;'>
  <p style='font-weight:700; color:{DARK}; margin:0 0 10px 0; font-size:1rem;'>
    📌 From the Dataset
  </p>
  <div style='display:flex; gap:32px; flex-wrap:wrap;'>
    <span>👁️ <b style="color:{RED}">Highest Avg Views</b> — Sierra &amp; Rhia FAM</span>
    <span>👍 <b style="color:{RED}">Highest Avg Likes</b> — Sierra &amp; Rhia FAM</span>
    <span>💬 <b style="color:{RED}">Highest Avg Comments</b> — Artcoaster</span>
    <span>⚡ <b style="color:{RED}">Max Engagement Rate</b> — Poonia Boutique</span>
  </div>
</div>
""", unsafe_allow_html=True)


# ── Tabs ─────────────────────────────────────────────────────────
tab1, tab2, tab3, tab4 = st.tabs([
    "📊 Overview",
    "📺 Channel Analysis",
    "⚡ Engagement Deep Dive",
    "🔍 Video Explorer"
])


# ════════════════════════════════════════════════════════════════
# TAB 1 — OVERVIEW
# ════════════════════════════════════════════════════════════════
with tab1:
    st.markdown("## 📊 Performance Overview")

    col1, col2 = st.columns(2)

    with col1:
        # Views distribution (log scale)
        fig_views = px.histogram(
            dff, x="Views", nbins=50,
            title="Views Distribution",
            labels={"Views": "Views", "count": "Number of Videos"},
            color_discrete_sequence=[RED]
        )
        fig_views.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            title_font_color=DARK, font_color=DARK,
            title_font_size=15, bargap=0.05
        )
        fig_views.update_xaxes(type="log", title="Views (log scale)")
        st.plotly_chart(fig_views, use_container_width=True)

    with col2:
        # Engagement rate distribution
        fig_eng = px.histogram(
            dff, x="Engagement_Rate_%", nbins=50,
            title="Engagement Rate Distribution",
            labels={"Engagement_Rate_%": "Engagement Rate (%)"},
            color_discrete_sequence=[GOLD]
        )
        fig_eng.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            title_font_color=DARK, font_color=DARK,
            title_font_size=15, bargap=0.05
        )
        st.plotly_chart(fig_eng, use_container_width=True)

    col3, col4 = st.columns(2)

    with col3:
        # Views vs Likes scatter
        fig_vl = px.scatter(
            dff, x="Views", y="Likes",
            title="Views vs Likes",
            color="Engagement_Rate_%",
            color_continuous_scale=[[0, TEAL], [0.5, GOLD], [1, RED]],
            hover_data=["Channel_Name", "Title"],
            labels={"Views": "Views", "Likes": "Likes",
                    "Engagement_Rate_%": "Engagement %"},
            opacity=0.65
        )
        fig_vl.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            title_font_color=DARK, font_color=DARK, title_font_size=15
        )
        fig_vl.update_xaxes(type="log")
        fig_vl.update_yaxes(type="log")
        st.plotly_chart(fig_vl, use_container_width=True)

    with col4:
        # Age vs Views per day
        fig_age = px.scatter(
            dff, x="Age_In_Days", y="Views_Per_Day",
            title="Video Age vs Views Per Day",
            color="Engagement_Rate_%",
            color_continuous_scale=[[0, TEAL], [1, RED]],
            hover_data=["Channel_Name", "Title"],
            labels={"Age_In_Days": "Age (Days)",
                    "Views_Per_Day": "Views/Day",
                    "Engagement_Rate_%": "Engagement %"},
            opacity=0.65
        )
        fig_age.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            title_font_color=DARK, font_color=DARK, title_font_size=15
        )
        fig_age.update_yaxes(type="log")
        st.plotly_chart(fig_age, use_container_width=True)

    # Top 10 videos by views
    st.markdown("### 🏆 Top 10 Videos by Views")
    top10 = dff.nlargest(10, "Views")[
        ["Title", "Channel_Name", "Views", "Likes", "Comments",
         "Engagement_Rate_%", "Views_Per_Day"]
    ].reset_index(drop=True)
    top10.index += 1
    top10["Views"]  = top10["Views"].apply(lambda x: f"{x/1e6:.2f}M")
    top10["Likes"]  = top10["Likes"].apply(lambda x: f"{x/1e3:.1f}K")
    top10["Views_Per_Day"] = top10["Views_Per_Day"].apply(lambda x: f"{x:,.0f}")
    top10["Engagement_Rate_%"] = top10["Engagement_Rate_%"].apply(lambda x: f"{x:.2f}%")
    st.dataframe(top10, use_container_width=True)

    st.markdown(f"""
    <div class='insight-box'>
      💡 <b>Key Insight:</b> Views follow a classic power-law distribution — a small
      number of videos capture the vast majority of views. Most videos cluster under 10M
      views, while a handful of viral outliers exceed 1B. High engagement rate does not
      always correlate with high views, suggesting niche content can be highly engaging
      even without mass reach.
    </div>""", unsafe_allow_html=True)


# ════════════════════════════════════════════════════════════════
# TAB 2 — CHANNEL ANALYSIS
# ════════════════════════════════════════════════════════════════
with tab2:
    st.markdown("## 📺 Channel Performance Analysis")

    # Aggregate by channel
    ch = dff.groupby("Channel_Name").agg(
        Videos=("Video_ID", "count"),
        Avg_Views=("Views", "mean"),
        Avg_Likes=("Likes", "mean"),
        Avg_Comments=("Comments", "mean"),
        Max_Engagement=("Engagement_Rate_%", "max"),
        Avg_Engagement=("Engagement_Rate_%", "mean"),
        Avg_Views_Per_Day=("Views_Per_Day", "mean"),
    ).reset_index()
    ch = ch[ch["Videos"] >= 2].sort_values("Avg_Views", ascending=False)

    col1, col2 = st.columns(2)

    with col1:
        top_n = st.slider("Show Top N Channels", 5, 30, 15)
        top_ch = ch.head(top_n)

        fig_ch_views = px.bar(
            top_ch.sort_values("Avg_Views"),
            x="Avg_Views", y="Channel_Name",
            orientation="h",
            title=f"Top {top_n} Channels by Average Views",
            labels={"Avg_Views": "Avg Views", "Channel_Name": ""},
            color="Avg_Views",
            color_continuous_scale=[[0, GOLD], [1, RED]],
        )
        fig_ch_views.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            coloraxis_showscale=False,
            title_font_color=DARK, font_color=DARK, title_font_size=15
        )
        st.plotly_chart(fig_ch_views, use_container_width=True)

    with col2:
        top_eng = ch.sort_values("Max_Engagement", ascending=False).head(top_n)

        fig_ch_eng = px.bar(
            top_eng.sort_values("Max_Engagement"),
            x="Max_Engagement", y="Channel_Name",
            orientation="h",
            title=f"Top {top_n} Channels by Max Engagement Rate",
            labels={"Max_Engagement": "Max Engagement (%)", "Channel_Name": ""},
            color="Max_Engagement",
            color_continuous_scale=[[0, TEAL], [1, RED]],
        )
        fig_ch_eng.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            coloraxis_showscale=False,
            title_font_color=DARK, font_color=DARK, title_font_size=15
        )
        st.plotly_chart(fig_ch_eng, use_container_width=True)

    col3, col4 = st.columns(2)

    with col3:
        # Avg Likes
        top_likes = ch.sort_values("Avg_Likes", ascending=False).head(top_n)
        fig_likes = px.bar(
            top_likes.sort_values("Avg_Likes"),
            x="Avg_Likes", y="Channel_Name",
            orientation="h",
            title=f"Top {top_n} Channels by Average Likes",
            labels={"Avg_Likes": "Avg Likes", "Channel_Name": ""},
            color="Avg_Likes",
            color_continuous_scale=[[0, GOLD], [1, RED]],
        )
        fig_likes.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            coloraxis_showscale=False,
            title_font_color=DARK, font_color=DARK, title_font_size=15
        )
        st.plotly_chart(fig_likes, use_container_width=True)

    with col4:
        # Avg Comments
        top_comments = ch.sort_values("Avg_Comments", ascending=False).head(top_n)
        fig_comments = px.bar(
            top_comments.sort_values("Avg_Comments"),
            x="Avg_Comments", y="Channel_Name",
            orientation="h",
            title=f"Top {top_n} Channels by Average Comments",
            labels={"Avg_Comments": "Avg Comments", "Channel_Name": ""},
            color="Avg_Comments",
            color_continuous_scale=[[0, TEAL], [1, GOLD]],
        )
        fig_comments.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            coloraxis_showscale=False,
            title_font_color=DARK, font_color=DARK, title_font_size=15
        )
        st.plotly_chart(fig_comments, use_container_width=True)

    # Bubble chart: Views vs Engagement, size = video count
    st.markdown("### 🫧 Channel Bubble Chart — Views vs Engagement")
    bubble = ch.head(40)
    fig_bubble = px.scatter(
        bubble,
        x="Avg_Views", y="Avg_Engagement",
        size="Videos", color="Avg_Views_Per_Day",
        hover_name="Channel_Name",
        color_continuous_scale=[[0, TEAL], [0.5, GOLD], [1, RED]],
        title="Avg Views vs Avg Engagement Rate (size = video count)",
        labels={"Avg_Views": "Avg Views", "Avg_Engagement": "Avg Engagement (%)",
                "Avg_Views_Per_Day": "Avg Views/Day"},
        size_max=50
    )
    fig_bubble.update_layout(
        plot_bgcolor=WHITE, paper_bgcolor=WHITE,
        title_font_color=DARK, font_color=DARK, title_font_size=15
    )
    fig_bubble.update_xaxes(type="log")
    st.plotly_chart(fig_bubble, use_container_width=True)

    st.markdown(f"""
    <div class='insight-box'>
      💡 <b>Key Insight:</b> <b>Sierra & Rhia FAM</b> dominates both average views and
      average likes — a channel optimised for mass reach. Meanwhile <b>Poonia Boutique</b>
      leads engagement rate, indicating a highly loyal and reactive niche audience.
      <b>Artcoaster</b> drives the most comments per video — a strong signal of community
      building. These are three distinct growth archetypes worth studying separately.
    </div>""", unsafe_allow_html=True)


# ════════════════════════════════════════════════════════════════
# TAB 3 — ENGAGEMENT DEEP DIVE
# ════════════════════════════════════════════════════════════════
with tab3:
    st.markdown("## ⚡ Engagement Deep Dive")

    col1, col2 = st.columns(2)

    with col1:
        # Engagement rate by age buckets
        dff2 = dff.copy()
        dff2["Age_Bucket"] = pd.cut(
            dff2["Age_In_Days"],
            bins=[0, 90, 180, 365, 730, 99999],
            labels=["0-3 mo", "3-6 mo", "6-12 mo", "1-2 yr", "2+ yr"]
        )
        age_eng = dff2.groupby("Age_Bucket", observed=True)["Engagement_Rate_%"].mean().reset_index()
        fig_age_eng = px.bar(
            age_eng, x="Age_Bucket", y="Engagement_Rate_%",
            title="Avg Engagement Rate by Video Age",
            labels={"Age_Bucket": "Video Age", "Engagement_Rate_%": "Avg Engagement (%)"},
            color="Engagement_Rate_%",
            color_continuous_scale=[[0, GOLD], [1, RED]],
        )
        fig_age_eng.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            coloraxis_showscale=False,
            title_font_color=DARK, font_color=DARK, title_font_size=15
        )
        st.plotly_chart(fig_age_eng, use_container_width=True)

    with col2:
        # Description length vs engagement
        dff3 = dff[dff["Description_Length"] > 0].copy()
        dff3["Desc_Bucket"] = pd.cut(
            dff3["Description_Length"],
            bins=[0, 100, 500, 1000, 2000, 99999],
            labels=["<100", "100-500", "500-1K", "1K-2K", "2K+"]
        )
        desc_eng = dff3.groupby("Desc_Bucket", observed=True)["Engagement_Rate_%"].mean().reset_index()
        fig_desc = px.bar(
            desc_eng, x="Desc_Bucket", y="Engagement_Rate_%",
            title="Avg Engagement by Description Length",
            labels={"Desc_Bucket": "Description Length (chars)", "Engagement_Rate_%": "Avg Engagement (%)"},
            color="Engagement_Rate_%",
            color_continuous_scale=[[0, TEAL], [1, RED]],
        )
        fig_desc.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            coloraxis_showscale=False,
            title_font_color=DARK, font_color=DARK, title_font_size=15
        )
        st.plotly_chart(fig_desc, use_container_width=True)

    col3, col4 = st.columns(2)

    with col3:
        # Views per day distribution by engagement quartile
        dff["Eng_Quartile"] = pd.qcut(
            dff["Engagement_Rate_%"], q=4,
            labels=["Q1 (Low)", "Q2", "Q3", "Q4 (High)"]
        )
        fig_box = px.box(
            dff, x="Eng_Quartile", y="Views_Per_Day",
            title="Views Per Day by Engagement Quartile",
            labels={"Eng_Quartile": "Engagement Quartile", "Views_Per_Day": "Views/Day"},
            color="Eng_Quartile",
            color_discrete_sequence=[TEAL, GOLD, "#FF6B35", RED]
        )
        fig_box.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            title_font_color=DARK, font_color=DARK,
            title_font_size=15, showlegend=False
        )
        fig_box.update_yaxes(type="log")
        st.plotly_chart(fig_box, use_container_width=True)

    with col4:
        # Likes vs Comments coloured by engagement
        fig_lc = px.scatter(
            dff, x="Likes", y="Comments",
            color="Engagement_Rate_%",
            color_continuous_scale=[[0, TEAL], [0.5, GOLD], [1, RED]],
            title="Likes vs Comments (coloured by Engagement)",
            labels={"Likes": "Likes", "Comments": "Comments",
                    "Engagement_Rate_%": "Engagement %"},
            hover_data=["Channel_Name", "Title"],
            opacity=0.7
        )
        fig_lc.update_layout(
            plot_bgcolor=WHITE, paper_bgcolor=WHITE,
            title_font_color=DARK, font_color=DARK, title_font_size=15
        )
        fig_lc.update_xaxes(type="log")
        fig_lc.update_yaxes(type="log")
        st.plotly_chart(fig_lc, use_container_width=True)

    # Correlation heatmap
    st.markdown("### 🔗 Metric Correlation Heatmap")
    corr_cols = ["Views", "Likes", "Comments", "Age_In_Days",
                 "Engagement_Rate_%", "Views_Per_Day", "Description_Length"]
    corr = dff[corr_cols].corr().round(2)
    fig_corr = px.imshow(
        corr,
        color_continuous_scale=[[0, TEAL], [0.5, WHITE], [1, RED]],
        title="Correlation Between Key Metrics",
        text_auto=True, aspect="auto",
        zmin=-1, zmax=1
    )
    fig_corr.update_layout(
        paper_bgcolor=WHITE, title_font_color=DARK,
        font_color=DARK, title_font_size=15
    )
    st.plotly_chart(fig_corr, use_container_width=True)

    st.markdown(f"""
    <div class='insight-box'>
      💡 <b>Key Insight:</b> Newer videos (0–3 months) show significantly higher engagement
      rates — YouTube's algorithm boosts fresh content. Videos with <b>shorter descriptions
      (under 100 chars)</b> outperform longer ones in engagement, suggesting audiences
      prefer concise hooks. <b>Likes and Comments are highly correlated</b>, but engagement
      rate is weakly correlated with raw views — proving that viral reach and genuine
      community engagement are two very different things.
    </div>""", unsafe_allow_html=True)


# ════════════════════════════════════════════════════════════════
# TAB 4 — VIDEO EXPLORER
# ════════════════════════════════════════════════════════════════
with tab4:
    st.markdown("## 🔍 Video Explorer")
    st.markdown("Search and explore individual videos from the dataset.")

    col1, col2 = st.columns([2, 1])
    with col1:
        search = st.text_input("🔍 Search by title or channel name", "")
    with col2:
        sort_by = st.selectbox(
            "Sort by",
            ["Views", "Likes", "Comments", "Engagement_Rate_%", "Views_Per_Day"]
        )

    explore = dff.copy()
    if search:
        mask = (
            explore["Title"].str.contains(search, case=False, na=False) |
            explore["Channel_Name"].str.contains(search, case=False, na=False)
        )
        explore = explore[mask]

    explore = explore.sort_values(sort_by, ascending=False)

    display_cols = ["Title", "Channel_Name", "Views", "Likes", "Comments",
                    "Engagement_Rate_%", "Views_Per_Day", "Age_In_Days", "Video_URL"]
    explore_disp = explore[display_cols].reset_index(drop=True)
    explore_disp.index += 1
    explore_disp["Views"] = explore_disp["Views"].apply(lambda x: f"{x:,.0f}")
    explore_disp["Likes"] = explore_disp["Likes"].apply(lambda x: f"{x:,.0f}")
    explore_disp["Views_Per_Day"] = explore_disp["Views_Per_Day"].apply(lambda x: f"{x:,.0f}")
    explore_disp["Engagement_Rate_%"] = explore_disp["Engagement_Rate_%"].apply(lambda x: f"{x:.2f}%")

    st.markdown(f"**{len(explore):,} videos found**")
    st.dataframe(explore_disp, use_container_width=True, height=500)

    # Download filtered data
    csv = explore[display_cols].to_csv(index=False).encode("utf-8")
    st.download_button(
        label="⬇️ Download Filtered Data as CSV",
        data=csv,
        file_name="youtube_shorts_filtered.csv",
        mime="text/csv"
    )


# ── Footer ────────────────────────────────────────────────────────
st.markdown("<br>", unsafe_allow_html=True)
st.markdown(f"""
<div style='background:{DARK}; padding:16px 28px; border-radius:10px;
            text-align:center; color:#666; font-size:0.82rem;'>
  ▶️ YouTube Shorts Analytics Dashboard &nbsp;|&nbsp; Built with Streamlit & Plotly
</div>
""", unsafe_allow_html=True)
