# Your ML Pipeline Already Knows Why It Made That Decision. Are You Capturing It?

Picture the last time someone asked you to explain a model's decision after the fact. Maybe it was a credit score that got flagged, or a churn prediction a stakeholder didn't trust. You went back to the notebook, re-ran SHAP, cross-referenced which version of the model was in production that week, and pieced together an explanation that took an afternoon to produce and would be stale the moment the model retrained.

That's the gap dakma-sdk is built to close.

## The problem isn't explainability. It's when you do it.

SHAP, Integrated Gradients, LIME — the tooling for explaining a model's predictions is mature and well-validated. What's missing in most teams isn't a better attribution method, it's a habit: explanations get generated on demand, in a notebook, disconnected from the run that actually produced the decision in production. By the time you need the explanation — for a regulator, an auditor, or an angry customer — reconstructing it means guessing which data, which model version, and which parameters were actually involved.

With the EU AI Act and provisions like GDPR Article 22 pushing toward documented, per-decision transparency, "I can explain it if you give me a day" isn't really an answer anymore.

## The idea: instrument the pipeline you already have

dakma-sdk doesn't ask you to build a separate reporting pipeline. It attaches to the functions your pipeline already runs — feature engineering, training, inference — through simple decorators, so the audit trail is a side effect of running your code rather than a project on its own.

```python
import dakma
from xgboost import XGBClassifier

dm_c = dakma.init(
    project="credit-scoring-package",
    regulation="internal-policy",
    risk_level="high",
)

@dm_c.track_data
def prepare_features(df):
    df["debt_ratio"] = df["total_debt"] / df["annual_income"]
    df["credit_util"] = df["balance"] / df["credit_limit"]
    return df

@dm_c.track_training
def train(X_train, y_train):
    model = XGBClassifier(n_estimators=200, max_depth=5)
    model.fit(X_train, y_train)
    return model

@dm_c.explain(counterfactual=True)
def score_applicant(model, applicant_row):
    return model.predict_proba(applicant_row)

result = score_applicant(model, applicant_row)
print(result.explain.plain_language)
print(result.explain.audit_trail_id)
```

Four decorators, and you've got schema tracking, training metadata, a SHAP-based explanation, a counterfactual, and an audit trail ID — all tied to this specific call, not a generic model description written six months ago.

## Two backends, one interface

For tabular and tree-based models, dakma-sdk reaches for SHAP. For differentiable models — a PyTorch network, say — it switches to Captum's Integrated Gradients via `@dm_c.explain_integrated_gradients`. Same decorator pattern, same reporting pipeline underneath, so a team running both classical and deep models isn't maintaining two separate governance processes.

## It's not just explanations — it's the whole record

Beyond per-decision attributions, you can register evaluation results (`register_evaluation()`: hold-out metrics, confusion matrix) and governance documentation (`register_governance()`: intended use, limitations, human oversight) once, and they get snapshotted onto every exported report. `write_audit_report()` and `write_report()` then produce Markdown or HTML you can hand to a reviewer or print to PDF, without a separate documentation exercise.

One thing worth saying plainly, because the project itself says it plainly: this is documentation tooling, not a compliance stamp. It doesn't certify your model, and it won't do your fairness audit for you.

## Where it stands today

dakma-sdk just shipped as v0.1.0, alpha, on PyPI:

```bash
pip install dakma-sdk                    # core
pip install "dakma-sdk[ml]"              # + pandas, scikit-learn, XGBoost, SHAP
pip install "dakma-sdk[ml,dl]"           # + PyTorch, Captum for Integrated Gradients
```

It's honest about its current edges — feature-lineage tracking works off simple `df["col"] = ...` patterns rather than full dataflow analysis, decision labels default to an approve/decline framing, and the drift monitoring is a starting point rather than a full bias audit. That's expected for an alpha release, and the architecture is built to grow from there.

## Why this matters beyond one library

The broader point is less about this specific tool and more about a shift in how explainability should work: not as a report you generate when someone asks, but as a record your pipeline keeps as it runs. If your model already knows why it made a decision, the question worth asking is whether you're actually capturing that — or reconstructing it later, under pressure, from memory.

*dakma-sdk is open source under Apache 2.0. Find it on [PyPI](https://pypi.org/project/dakma-sdk/).*
