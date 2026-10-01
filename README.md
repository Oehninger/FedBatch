# Optimal Methanol Feeding: LP vs NM

## Description

This code solves and compares two optimal-control formulations for methanol feeding during recombinant ROL production in a fed-batch
*Komagataella phaffii* process. The process balances, growth kinetics, methanol-consumption kinetics, operating constraints, and optimization
criterion are shared by both cases; only the specific protein-production kinetics (q_P) are changed.

The implementation uses GEKKO and a simultaneous dynamic optimization approach based on orthogonal collocation. The source code defines the LP
and NM alternatives, solves both optimization problems, summarizes process-performance indicators, and generates a four-panel comparison
figure. 

## Kinetic formulations

**LP model --- Barrigón et al. (2015).** Protein production is
represented by a growth-associated formulation with a smooth transition
around the critical methanol concentration
(S\_{`\mathrm{crit}`{=tex}}=1.9 `\mathrm{g\,L^{-1}}`{=tex}).

**NM model --- Ponte et al. (2018).** Protein production is represented
by a non-monotonic methanol-dependent expression,

\[ q_P(S)=`\frac{q_{\max,P}S}{K_{S,P}+S+S^2/K_{I,P}}`{=tex}. \]

The remaining growth and substrate-consumption kinetics are common to
both formulations.

## Optimization problem

The manipulated variable is the methanol feed rate (F(t)), while the
final induction time (t_f) is also optimized. The objective is to
maximize the average net total ROL production rate,

\[ J=`\frac{P(t_f)V(t_f)-P_0V_0}{t_f}`{=tex}. \]

The model includes biomass concentration, residual methanol, ROL
activity, and culture volume as dynamic states. The implementation
constrains biomass, methanol concentration, culture volume, feed rate,
and final time. fileciteturn17file0L176-L226

## Reported performance indicators

For each kinetic formulation, the code reports:

-   optimal induction time (t_f);
-   objective value (J\^\*) \[U h(\^{-1})\];
-   net ROL production (`\Delta`{=tex}(PV)) \[U\];
-   final ROL activity (P_F) \[U mL(\^{-1})\];
-   specific productivity (Q\_{P/X}) \[U g(\^{-1}) h(\^{-1})\];
-   final biomass, volume, and residual methanol;
-   mean methanol concentration, specific growth rate, and specific
    protein-production rate.

The specific productivity is calculated as

\[ Q\_{P/X} = `\frac{P(t_f)V(t_f)-P_0V_0}`{=tex} {t_f,X(t_f)V(t_f)}. \]

The code computes these indicators from the optimized trajectories and
stores them in a summary table for direct LP--NM comparison.
fileciteturn17file0L352-L405

## Requirements

-   Python 3
-   NumPy
-   Pandas
-   Matplotlib
-   GEKKO

For Google Colab, the notebook includes installation commands for the
required packages. 

## Execution

Run the notebook/script from top to bottom. The two cases are solved
with:

``` python
resumen_LP, tabla_LP = optimizar_modelo(
    modelo='LP',
    nt=41,
    dmax=None,
    disp=False
)

resumen_NM, tabla_NM = optimizar_modelo(
    modelo='NM',
    nt=41,
    dmax=None,
    disp=False
)
```

The resulting summaries are combined in the `comparacion` DataFrame.


## Output figure

The code generates `LP_NM_comparison` in PDF, EPS, and PNG formats. The
four panels compare:

1.  LP and NM protein-production kinetics;
2.  optimal residual-methanol trajectories;
3.  optimal methanol-feed profiles;
4.  predicted total ROL activity.

The PNG version is exported at 600 dpi. 

## Scope

The numerical comparison is intended to assess how the assumed
protein-production kinetics influence the predicted optimal
methanol-feeding strategy and process performance. A higher objective
value for one formulation should not, by itself, be interpreted as
evidence that the corresponding kinetic model is biologically more
accurate.
