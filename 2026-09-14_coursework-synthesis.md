# Coursework Journal — Week of 2026-09-14 (weekly hub)

Thin index for the week. **Course-specific depth now lives in the course folders**; this hub keeps
only the header table, the cross-course synthesis (which belongs to neither course alone), the tag
summary, and the promoted interview lines.

| Course | Tag | This week | Depth |
|---|---|---|---|
| **CIS 9760 — Big Data Technologies** | 🛡️ **Portfolio-Defense** | AWS (budget, S3, EC2) + Linux terminal + Docker on a cloud server | [cis9760/Module2-3_AWS-terminal](./cloud-data-systems/aws/Module2-3_AWS-terminal.md) |
| **STA 9750 — Software Tools for Reproducible Research** | 📚 **General Coursework** | R (vectors, functions, control flow) + Markdown/Quarto + git | [sta9750/Lecture2-3_R-Quarto](./r-analytics/Lecture2-3_R-Quarto.md) |

---

## Cross-course synthesis — reproducibility via clean environments

The through-line, and the most portfolio-relevant idea of the week (it belongs to neither course
alone, so it lives here in the hub):

| | STA 9750 (Quarto) | CIS 9760 (Docker) |
|---|---|---|
| Mechanism | Render runs in a **brand-new R session** every time | Container runs in an **isolated, from-scratch environment** |
| What it guarantees | The `.qmd` is a complete, reproducible record | The image runs the same anywhere, no hidden host state |
| Failure it prevents | "Works in my Console, breaks on Render" | "Works on my machine, breaks in production" |
| My portfolio link | — | Directly the prototype→production / deployment gap (§12.2) |

**Both say: the artifact must run from a clean state, not from accumulated session history.** That's
the same maturity leap my apps need — and now I can speak to it from two angles. *(This idea recurs
in the STA MP#00 journal's render-trap and the CIS Module 4 Docker stale-image trap — two courses,
two toolchains, one principle.)*

---

## Tag summary (the honesty gate)

- **🛡️ CIS 9760** → Portfolio-Defense. AWS provisioning, S3, IAM, EC2, Linux CLI, and Docker
  containerization patch the deployment/scale/ops gap. Earns interview lines (below).
- **📚 STA 9750** → General Coursework. R/Quarto/Markdown/git — logged for completeness, not pitched
  as core, except the 🔁 transferable lessons in the course doc. Upgrades to 🛡️ only if a target JD
  names R or reproducible-reporting.

## Interview lines earned (🛡️ only)

> I've provisioned and managed AWS resources hands-on — set cost budgets, created S3 buckets with
> scoped IAM permissions, launched EC2 instances, and run Docker containers on them over SSH with
> port mapping and volume mounts. So when I talk about my apps being portfolio prototypes, I can
> also speak concretely to how I'd containerize and deploy them.

> Quarto's render-in-a-fresh-session and Docker's clean container are the same reproducibility
> principle — run from scratch, not from hidden state. That's the discipline that separates a
> prototype that "works on my machine" from something production-ready.
