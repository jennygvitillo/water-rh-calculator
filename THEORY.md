# The physics and math behind this tool

This document explains, in plain language, what the calculator is actually doing — so you don't need to dig into the original IAPWS paper to understand or trust the numbers.

## 1. What is saturation vapor pressure?

At any given temperature, there's a maximum amount of water vapor that can coexist in equilibrium with liquid water (or ice). That maximum partial pressure is the **saturation vapor pressure**, p_sat(T). Go above it and vapor condenses; stay below it and the liquid keeps evaporating.

p_sat rises steeply with temperature — roughly doubling every 10 °C in the middle of the range — which is why RH% swings so dramatically across a temperature ramp even when the absolute amount of water vapor stays fixed.

## 2. The IAPWS formula, term by term

The tool uses the IAPWS (Wagner & Pruss, 1993) equation for the water/vapor saturation curve:

```
ln(p_sat/p_c) = (T_c/T) x [a1*tau + a2*tau^1.5 + a3*tau^3 + a4*tau^3.5 + a5*tau^4 + a6*tau^7.5]
tau = 1 - T/T_c
```

Here's what each piece means physically:

- **T_c (647.096 K) and p_c (22.064 MPa)** are water's *critical point* — the temperature and pressure above which liquid and vapor stop being distinct phases. The whole saturation curve lives between the triple point (0.01 °C) and this critical point, so anchoring the equation to T_c and p_c lets one formula cover the entire liquid-vapor coexistence line.
- **tau** is a "distance from the critical point," rescaled to run from 0 (at T_c) upward as temperature drops. It's the actual variable the polynomial is built on.
- **The polynomial in tau, with its unusual fractional exponents (1.5, 3.5, 7.5)** isn't derived from first principles — it's an empirical fit to a very large, high-quality set of experimental vapor pressure measurements. Fractional exponents let a short polynomial (just 6 terms) capture the true curvature of the data far better than integer powers would.
- **Working in ln(p/p_c)** rather than p directly keeps the fit numerically well-behaved: p_sat itself spans several orders of magnitude between 0 °C and 374 °C, and fitting a log-ratio avoids the numerical instability that would come from fitting such a wide range directly.

This formulation reproduces experimental data to within about 0.02% and is the reference used by metrology labs — noticeably better than simpler correlations (like Buck's), though for most practical humidity work the difference is smaller than the instrument's own uncertainty.

**Validity range: 0.01-373.9 °C (273.16-647.096 K)** — from the triple point to the critical point. Outside this range the equation has no physical meaning (below the triple point, liquid water can't exist in equilibrium with vapor at all — ice takes over).

## 3. Below 0 °C: why the calculator switches formulas

Below the triple point, the relevant equilibrium is **ice-vapor**, not liquid-vapor, and it follows a different (though related) physics. For dew/frost point inputs below 0 °C, the tool switches to the **Buck (1996) ice saturation formula**:

```
p_sat,ice(T) = 6.1115 x exp[(23.036 - T/333.7) x (T/(279.82+T))]    (T in degC, mbar)
```

This is a well-established empirical correlation valid down to -80 °C, and is the standard choice for frost point calculations in meteorology and humidity metrology.

## 4. Isobaric conditions and RH%

"Isobaric" here means holding the **partial pressure of water vapor (p_H2O)** constant while temperature changes — i.e., the absolute amount of water vapor in the gas phase stays fixed, even though relative humidity does not.

Relative humidity is, by definition:

```
RH(T) [%] = ( p_H2O / p_sat(T) ) x 100
```

Since p_H2O is held constant but p_sat(T) changes steeply with T, RH% must decrease as temperature rises (and increase as it falls) to keep the same absolute water content. This is exactly what the batch mode computes: fix p_H2O once, then recalculate RH% at every temperature step from p_sat(T).

## 5. The four ways to specify p_H2O

The tool accepts the same physical quantity (water vapor partial pressure) in four different, commonly used forms:

- **mbar / kPa** — direct pressure units.
- **RH% at 25 °C** — a very common way humidity is reported in instrument specs and datasheets. The tool converts it once, using p_sat(25 °C) as a reference, into an absolute p_H2O, then treats it like any other input.
- **Dew point / frost point (°C)** — the temperature at which the given amount of water vapor would itself become saturated. This is a direct physical measurement in many instruments (chilled-mirror hygrometers, for example), so being able to enter it directly avoids a manual conversion step.

All four are just different labels for the same underlying quantity, p_H2O — the tool's job is simply to convert whichever one you have into the others.

## References

1. Wagner, W.; Pruss, A. International Equations for the Saturation Properties of Ordinary Water Substance. Revised According to the International Temperature Scale of 1990. *J. Phys. Chem. Ref. Data* **1993**, *22*, 783-787.
2. Buck, A. L. New Equations for Computing Vapor Pressure and Enhancement Factor. *J. Appl. Meteorol.* **1981**, *20*, 1527-1532.
3. Buck Research Instruments. *Buck Research CR-1A Hygrometer Instruction Manual, Appendix 1*; 1996. (Ice saturation formula.)
