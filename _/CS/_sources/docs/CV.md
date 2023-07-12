---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.14.6
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# Curriculum Vitae

+++

In the charts below, two representations of my life's development are shown. 

The first one displays how my productivity, according to my own subjective estimation, developed throughout my academic and professional life. 

In the second chart more concrete events are shown. Some of them are clickable and will take you to the corresponding section of my CV.

+++

(chart)=

## Productivity & Main Life Events Charts

```{code-cell} ipython3
:tags: [remove-cell]

import pandas as pd
import altair as alt
```

```{code-cell} ipython3
:tags: [remove-cell]

def get_data():
    productivity = pd.read_csv("../data.txt", sep="\t", encoding="latin-1")
    events = pd.read_csv("../events.txt", sep="\t", encoding="latin-1")
    events.loc[events["id"] != "noLink", "id"] = "#" + events.loc[events["id"] != "noLink", "id"]
    data = (pd.concat(
        [
            productivity.melt('year').assign(type="line", id=float("nan")), 
            events.assign(type="point")
        ])
            .reset_index(drop=True)
           )
    return data
```

```{code-cell} ipython3
:tags: [remove-cell]

def get_chart(data):
    domain = ['education', 'work', 'extra-curricular activities & hobbies', 'family', 'productivity']
    range_ = ['#1b9e77', '#8e6c87', '#a76854', '#d69e09', '#666666']

    selector = alt.selection_point(fields=['variable'], on='mouseover', nearest=False)

    base = alt.Chart(data).properties(
        width=500,
        height=250,
    #    title="Productivity and main events"
    ).add_params(selector)

    points = base.mark_point(filled=True, size=200).encode(
        x=alt.X('year:Q', axis=alt.Axis(title=None, format="g")).scale(domain=[2007,2023]),
        y=alt.Y('variable:O', axis=alt.Axis(title=None)),
        color=alt.condition(selector, alt.Color('variable:O', scale=alt.Scale(domain=domain, range=range_)).legend(None), alt.value('lightgray')),
        tooltip="value:N",
        href='id:N'
    ).transform_filter(
        {"and": [alt.FieldEqualPredicate(field='type', equal='point'), {"not": alt.FieldEqualPredicate(field='id', equal='noLink')}]}
    )
    
    points_clickable = base.mark_point(filled=True, size=200).encode(
        x=alt.X('year:Q', axis=alt.Axis(title=None, format="g")).scale(domain=[2007,2023]),
        y=alt.Y('variable:O', axis=alt.Axis(title=None)),
        color=alt.condition(selector, alt.Color('variable:O', scale=alt.Scale(domain=domain, range=range_)).legend(None), alt.value('lightgray')),
        tooltip="value:N"
    ).transform_filter(
        alt.FieldEqualPredicate(field='id', equal='noLink')
    )
    
    timeseries_chart = base.mark_line().encode(
        x=alt.X('year:Q', axis=alt.Axis(title=None, format="g")).scale(domain=[2007,2023]),
        y=alt.Y('value:Q', axis=alt.Axis(title=None, labelOpacity=0)).scale(domain=(0, 40)),
        color=alt.Color('variable:O', scale=alt.Scale(domain=domain, range=range_)).legend(title="category", orient="right")
    ).transform_filter(
        {"and": [alt.FieldEqualPredicate(field='type', equal='line'), selector]}
    )

    return timeseries_chart & (points + points_clickable)
```

```{code-cell} ipython3
:tags: [remove-cell]

data = get_data()
data.tail()
```

```{code-cell} ipython3
:tags: [remove-input]

get_chart(data)
```

---

+++

## Education

+++

(cas)=
### CAS UniBern Applied Data Science

+++

Title final project: Network-wide estimation of train passenger counts using machine learning

([Official page](https://www.unibe.ch/continuing_education_programs/cas_in_applied_data_science/index_eng.html)) [⤣](chart)

+++

(msc)=
### MSc ETH Spatial Development & Infrastructure Systems

+++

MSc Thesis: Convergence towards equilibrium in an agent-based transport simulation. [⤣](chart)

+++

(bsc)=
### B.Eng Transportation & Logistics

+++

Graduation thesis: Development of a MATSim transport model for the city of Joinville (Brazil). [⤣](chart)

+++

---

+++

## Work Experience

+++

(sbb2)=
### Transport Modelling at SBB

+++

Model development and application: 

- Agent-based (reinforcement learning)
- Machine learning (supervised, tree-based)
- Discrete choice modelling (i.e. logistic regression applied to choice behavior)

Workflow automation:

- Developing custom workflows (Windows, AWS)
- Setting up DevOps pipelines (git, Jenkins, Tekton)
- Configuring MLOps pipelines (Argo, MLFlow)

Analytics/Other:

- Custom dashboarding and reporting with Jupyter
- (Statistical) Anonymization of sensitive data
- Geographical analysis and visualization
[⤣](chart)

+++

(sbb1)=
### Internship & MSc Thesis at SBB

+++

Statistical analysis of an agent-based transport model, particularly in respect to stochastic convergence and results variability (hypothesis testing).

Note: writing my MSc Thesis was part of my internship at SBB. [⤣](chart)

+++

(hiwi)=
### Student Research Assistant

+++

Data analysis and preparation for the development of multi-agent transport models. [⤣](chart)

+++

(ippuj)=
### Internship in Geographical Statistics

+++

Geographical data preparation for spatial and transport planning. [⤣](chart)

+++

---

+++

## Skills

```{code-cell} ipython3
:tags: [remove-cell]

skills = pd.read_csv("../skills.txt", sep="\t", encoding="latin-1")

domain = ['skill', 'programming', 'language', 'processes']
range_ = ['#1b9e77', '#8e6c87', '#a76854', '#d69e09', '#666666']

charts = [alt.Chart(skills.query(f"category=='{cat}'"), title=cat).properties(
        width=200).mark_bar().encode(
    x=alt.X('level:Q', axis=alt.Axis(title=None, labelOpacity=0)).scale(domain=[0, 5]),
    color=alt.Color('category:N', scale=alt.Scale(domain=domain, range=range_)).legend(None),
    y=alt.Y('skill:N', axis=alt.Axis(title=None)),
) for cat in skills.category.unique()]
```

```{code-cell} ipython3
:tags: [remove-input]

(charts[0] & charts[1]) | (charts[2] & charts[3])
```
