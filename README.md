# Atomic SCF Notebook: user manual

A browser-based notebook for solving the electronic structure of a single atom or ion, for any element from hydrogen (Z = 1) to oganesson (Z = 118), with several different methods, and comparing the results side by side. It is built for learning and exploring: see how Hartree, Hartree–Fock and the density functionals differ, what a basis set buys you, how self-interaction error shows up, and how atomic properties vary across the periodic table.

Everything is computed in your browser. There is no server: nothing you enter leaves your machine.

> **How far to trust it.** The numerical Hartree–Fock and LDA energies are accurate enough to be useful as references (a few thousandths of a percent against independent calculations, see [Accuracy](#8-accuracy-what-to-trust)). The model is still *spherical, scalar-relativistic and single-configuration*, so treat the heaviest elements and open-shell details as illustrations of real trends, not research-grade atomic physics. The Notes tab in the app and the [Known limitations](#10-known-limitations) section below say exactly where the edges are.

**Contents**

1. [Quick start](#1-quick-start)
2. [Layout and navigation](#2-layout-and-navigation)
3. [The Setup tab (§1) in detail](#3-the-setup-tab-1-in-detail)
4. [Choosing a method](#4-choosing-a-method)
5. [Choosing a basis](#5-choosing-a-basis)
6. [Reading the results (§2 to §9)](#6-reading-the-results-2-to-9)
7. [Periodic Trends](#7-periodic-trends)
8. [Accuracy: what to trust](#8-accuracy-what-to-trust)
9. [Run times](#9-run-times)
10. [Known limitations](#10-known-limitations)
11. [Troubleshooting and FAQ](#11-troubleshooting-and-faq)
12. [Glossary](#12-glossary)
13. [About this version](#13-about-this-version)

---

## 1. Quick start

1. **Open the page.** It opens on oxygen with three starting configurations: Hartree, Hartree–Fock and DFT (PW92 correlation), all in the double-zeta+ basis, all neutral.
2. **Pick an element** in the *Element* box in §1 (Setup).
3. **Click *Run comparison*.** Every configuration in the list is solved for that element. Each one bundles a *method*, a *basis*, a *correlation functional*, a *relativistic* on/off choice and a *charge*.
4. **Scroll or use the tabs** to see the same calculations as orbital energy levels (§2), total energies (§3), radial wavefunctions (§4), the local potential V(r) (§5), ionisation energies (§6), self-interaction (§7), 3D orbital shapes (§8) and basis-set convergence (§9).
5. **Add your own configurations.** Choose a method, basis and so on, give it a label if you like, and click *Add configuration*. Any number can sit side by side: for example HF vs PW92 vs PBE vs rSCAN, or the same method with relativity off and on, or a neutral atom next to its cation.
6. **Try *Periodic Trends*** to scan a configuration across all 118 elements.

A good first experiment: choose neon, add a KLI configuration and a PBE configuration, run, then open §5 (Potential) and tick all three to see how the exact-exchange potential differs from the density-functional ones in the tail.

---

## 2. Layout and navigation

The page has two modes. The first is the **element page**, a row of tabs for one element at a time. The second is **Periodic Trends**, which scans configurations across the whole table.

| Tab | What it shows |
|---|---|
| **§1 Setup** | Element, and the list of configurations to compare. Run button, time estimates, convergence warnings. |
| **§2 Orbital energy levels** | Each configuration's orbital energies as a ladder, with zoom controls and a *Jump to* orbital selector. |
| **§3 Total energy** | Total energy per configuration, with a difference column against a baseline you choose. |
| **§4 Radial wavefunctions** | R(r) or the radial probability density r²R² for any orbital, linear or log radius. |
| **§5 Local potential V(r)** | The potential each electron moves in, split into nuclear, Hartree, exchange and correlation parts, for any chosen set of configurations. |
| **§6 Ionisation energy** | The Koopmans estimate −ε(HOMO) for every configuration. |
| **§7 Self-interaction** | The spurious self-repulsion of the outermost orbital and how much each method cancels. |
| **§8 Orbital shape in 3D** | A rotatable point cloud of a chosen orbital, sampled from its actual probability density. |
| **§9 Basis set convergence** | Total energy under increasingly good basis sets, towards the numerical (basis-free) answer. |
| **Notes** | Methodology and caveats: a long, specific write-up of scope, charge handling, every method, accuracy, relativity and corrected past bugs. Read it when a result surprises you. |

**Units.** Energies can be shown in hartree (Ha), rydberg (Ry) or electronvolts (eV), and lengths in bohr or ångström (Å). The unit buttons apply across the plots and tables.

---

## 3. The Setup tab (§1) in detail

### Element and charge
- **Element** selects Z (1 to 118).
- **Charge** is set per configuration. A nonzero charge changes the electron count to Z − charge while the nuclear charge stays Z, so a neutral atom and an ion of the same element can be compared in one run.
  - **Cations** lose electrons in the order real ionisation follows (highest *n* first, then higher *l* before lower *l*). So Fe²⁺ comes out [Ar]3d⁶ (not 4s²3d⁴), and heavy p-block cations show the inert-pair effect (Sn²⁺ is [Kr]4d¹⁰5s²).
  - **Anions** are built as isoelectronic with the neutral atom of atomic number Z − charge (O²⁻ is built like neon). That is right for ordinary main-group anions and a looser approximation otherwise.

### Configuration fields

| Field | Choices |
|---|---|
| **Method** | Hartree, Hartree–Fock, DFT, KLI (local exact exchange), OEP (exact local exchange). See [Choosing a method](#4-choosing-a-method). |
| **Basis** | Numerical grid, minimal, minimal+, double-zeta+ STO, even-tempered. See [Choosing a basis](#5-choosing-a-basis). |
| **Correlation** | Shown when the method is DFT: None (exchange only), Wigner (1938), VWN (1980, RPA fit), PW92 (1992), PBE (1996, GGA), rSCAN (2019, meta-GGA). The default is PW92. |
| **Relativistic corrections** | A checkbox: adds a scalar-relativistic treatment. Available for Hartree, DFT, Hartree–Fock and rSCAN with any basis. Not available for KLI or OEP (the box is locked off for them). |
| **Charge** | Integer; positive for cations, negative for anions. |
| **Label** | Optional name; otherwise one is generated. |

Each configuration has its own colour (used consistently in every plot) and a radio button that makes it the **baseline** for the difference column in §3. Removing the current baseline falls back to the first configuration in the list.

### Running
- **Run comparison** recomputes *every* configuration against the current element. There is no caching on the element page; changing the element or clicking *Run* recomputes.
- Beside the button (and per configuration) the page shows an **estimated time**. See [Run times](#9-run-times).
- A numerical run reports whether its density actually settled. A configuration that did not converge gets a **warning above the energy table**; do not trust its numbers.

### Constraints the page enforces
Some combinations are disabled, with an explanation in the warnings area:
- **KLI and OEP** exist on the numerical grid only, are non-relativistic and have no correlation. Choosing them switches the basis to the numerical grid and locks the relativistic box off.
- **OEP** is offered for neutral atoms and cations from H to Kr (Z ≤ 36), and for anions from H to Ar (Z ≤ 18). Outside that range *Add* is disabled and the warning says why.
- The basis-set comparison in §9 does not offer KLI or OEP.

---

## 4. Choosing a method

| Method | What it is | Exchange | Self-interaction | Notes |
|---|---|---|---|---|
| **Hartree** | Each electron moves in the average field of all the others, including itself. No exchange, no correlation. | none | **present** (full) | Useful as a baseline that shows what exchange buys you. |
| **Hartree–Fock** | Exact exchange as a non-local operator acting on each orbital. No correlation. | exact | cancelled exactly | The reference for exchange-only physics. Gives every orbital its own potential. |
| **DFT** | A local (or semi-local) functional of the density. Choose the correlation with the *Correlation* box. | approximate (Dirac LDA exchange; PBE and rSCAN have their own) | **partly** present | LDA/PW92 is the simple default. PBE adds a gradient correction; rSCAN is a meta-GGA (it also depends on the kinetic-energy density). |
| **KLI** | A local potential approximating exact exchange (the Krieger–Li–Iafrate approximation to the OEP). | exact exchange energy, local potential | cancelled for one and two electrons | Numerical grid, non-relativistic, no correlation. Energy sits slightly above Hartree–Fock. Exact −1/r tail. |
| **OEP** | The exact optimised effective potential: the *best local potential* for exact exchange (the one that minimises the energy). | exact exchange energy, local potential | cancelled for one and two electrons | Same limits as KLI, plus the range limits above. Energy lies between Hartree–Fock and KLI. |

**Which should I use?**
- To see *what exchange does*: compare Hartree with Hartree–Fock.
- To see *what correlation and the functional form do*: compare DFT configurations with different correlation choices (and PW92 vs PBE vs rSCAN).
- To see *self-interaction error in a local potential*: compare LDA or PBE with KLI/OEP in §5, §6 and §7.
- For the most accurate non-relativistic total energy in this app: numerical Hartree–Fock (exchange only), or a DFT with a correlation functional if you want correlation energy included.

### Notes on specific methods
- **PW92 vs VWN.** PW92 is a fit to quantum Monte Carlo data and is the default. The VWN option here is the older RPA fit, which correlates 24 to 34% more strongly. PW92 is also the uniform-gas part of the app's PBE, so comparing LDA/PW92 with PBE isolates the gradient correction.
- **DFT energies.** For a density functional the energy of the Kohn–Sham determinant and the functional's own total energy are different quantities; Periodic Trends offers both (*Total energy* and *Total energy (the method's own functional)*), and the Notes explain the difference.
- **Hartree–Fock for open shells.** Partly filled shells are handled with an exact treatment of each spin-shell, so iron, scandium and similar atoms converge normally.
- **KLI and OEP energies are upper bounds.** The OEP is run with two optimisers and the *lower* energy is reported; either is the energy of a real set of orbitals, so it is an upper bound on the true OEP minimum, and the lower of the two is the better bound. For iron, scandium and weakly bound anions the reported value may still lie about 0.1 mHa above the true minimum. See [Accuracy](#8-accuracy-what-to-trust).

---

## 5. Choosing a basis

| Basis | What it is |
|---|---|
| **Numerical grid** | Solves the radial equation directly on a grid, self-consistently. No basis-set error at all. The reference everything else is compared with. |
| **Minimal** | One Slater-type orbital (STO) per occupied shell, exponents from Slater's rules. Fast and rough. |
| **Minimal+** | The minimal basis plus one spare diffuse function per angular-momentum channel. |
| **Double-zeta+ STO** | A second, more diffuse STO per shell, plus one spare diffuse function per channel. A good everyday compromise. |
| **Even-tempered** | A geometric series of exponents. Adjustable size; automatically given a few extra functions when an element needs more shells than the chosen size. |

- For **Hartree and DFT**, the numerical grid is a simple point-wise potential with a direct tridiagonal solve. For **Hartree–Fock** the exchange operator is non-local, so "numerical" means diagonalising the Fock operator over a radial basis built from the self-consistent potential's own bound states, enriched with extra functions. It is still an accurate, essentially basis-limit answer.
- **Basis exponent optimisation** (§9) can variationally optimise the double-zeta+ exponent spread for a given atom. It is slow, and the page warns you before it runs.
- Bigger is not always better in practice for the heaviest elements; see the Notes entry on basis-set convergence.

---

## 6. Reading the results (§2 to §9)

### §2 Orbital energy levels
Each configuration's orbital energies drawn as a ladder, aligned so you can compare methods. Use the **zoom** buttons (+, −, *Reset zoom*) and the **Jump to** selector to find a particular orbital. Differences between ladders are where exchange, correlation and relativity show up: for example the 3d and 4s ordering in a transition metal.

### §3 Total energy
Total energy for each configuration, with a **Δ column** against the baseline (the radio button in §1), followed by a comparison of what each correlation functional would predict for this atom. That comparison evaluates each functional on one converged density; it does not re-run the self-consistent calculation for each (re-running shows the further, usually small, effect on the orbitals). A configuration that did not converge carries a warning here.

### §4 Radial wavefunctions
Pick an **orbital** and a **quantity** (the radial probability density r²R(r)² or the radial function R(r)) and a **linear or log** radius axis. Nodes, peak positions and tails show how a method changes an orbital's size and shape.

### §5 Local potential V(r) and its components
The potential V(r) = −Z/r + V_H + V_x + V_c that each electron moves in, drawn together with its parts.
- **Configurations.** Tick any one, several or all of the computed configurations (*All* and *None* buttons help). With **one** configuration the colour says which *component* it is. With **several**, the colour says which *configuration* it is (the same colours as elsewhere) and the **line style** says which component: total solid, Hartree dashed, exchange short-dashed, correlation dash-dot. The nuclear −Z/r is the same for all and is drawn once, in grey. Spin-down curves are drawn lighter.
- **Quantity.** *V(r)* or *r·V(r)*. The second shows the long-range tail: for exact exchange (KLI, OEP) the total tends to −(q+1) for an ion of charge q, while LDA and PBE tend to −q, because their exchange potential decays exponentially. That difference is the self-interaction error appearing in the potential. A dotted line marks the exact-exchange value.
- **X-axis and Y-range.** Log radius is the default (the structure is near the nucleus). The default Y-range frames the valence region and lets the divergent −Z/r core run off the plot; choose *full range* to see it.
- **Component tick-boxes** hide or show each part.
- **Which methods have a potential.** Hartree (nuclear + Hartree only), DFT (including PBE), KLI and OEP. **Hartree–Fock and rSCAN have none** (Hartree–Fock exchange is a non-local operator; rSCAN's exchange-correlation term depends on the kinetic-energy density and changes the kinetic operator). Such configurations are named in a note instead of being silently dropped.
- Curves that coincide hide each other (for example the LDA and PBE totals beyond about a bohr).

### §6 Ionisation energy (Koopmans' theorem)
Shows −ε(HOMO) for each configuration. This is strictly a Hartree–Fock result (removing an electron leaves the other orbitals unrelaxed). It is shown for every method only so you can see how self-interaction error pushes the HOMO energy in the wrong direction. For a better ionisation energy, use the ΔSCF values in Periodic Trends.

### §7 Self-interaction, orbital by orbital
For the outermost orbital, shows its Hartree self-Coulomb energy J_self (the spurious repulsion of an electron with its own charge cloud) alongside how much of it each method's exchange cancels. For Hartree–Fock the cancellation is exact. For DFT it is partial. The size of the shortfall is the self-interaction error.

### §8 Orbital shape in 3D
A rotatable 3D point cloud of one orbital R_nl(r) combined with a real spherical harmonic, sampled in proportion to |ψ|². Colour marks the sign of ψ, so phase changes at nodes are visible. Choose the method, the orbital (n, l), the magnetic quantum number m, the shape, and a density threshold. This view loads a graphics library from the web; if that is unavailable the tab says "3D view unavailable" and the rest of the app still works.

### §9 Basis set convergence
For the current element and charge, total energy under minimal, double-zeta+ (fixed exponent spread), double-zeta+ with the spread optimised for this atom, and even-tempered bases, climbing toward the near-exact numerical value. Click **Compute convergence** (the sweep can take a while, especially the optimised basis). KLI and OEP are not offered here.

---

## 7. Periodic Trends

Build one or more configurations (method, basis, correlation, relativistic, charge: the same five choices as §1), then **scan each across all 118 elements**. You can then chart any property across the table, and compare configurations against a baseline.

**Building and running**
- **Add configuration** adds it to the cached list. Each configuration is scanned independently.
- Tick **"Also compute the +1 and −1 ions at the same time"** if you want the ΔSCF ionisation energy, electron affinity, electronegativity and hardness (these need the neighbouring ions).
- **Stop** halts a scan. A partly finished configuration offers **Resume** (or *Compute*/*Recompute* when none or all of it is done).
- A live "about … remaining" estimate runs while scanning. Full-scan times vary enormously by method; the estimate is shown before you start.
- **Clear all cached data** deletes everything stored.

**Properties you can chart** (the *Property* box)

| Group | Properties |
|---|---|
| Energies | Total energy, total energy (the method's own functional), total energy per electron, correlation energy per electron |
| Ionisation | Ionisation energy (Koopmans), ionisation energy (ΔSCF), electron affinity (ΔSCF) |
| Chemical | Mulliken electronegativity (IE + EA)/2, chemical hardness (IE − EA)/2 |
| Sizes | Atomic radius (the 0.001 e/bohr³ density contour is the default measure), density radii, orbital radii (r_peak, ⟨r⟩, √⟨r²⟩) |

**Viewing.** The chart is a periodic-table heat map. *View* chooses absolute values, the difference from a baseline configuration, or the percentage difference from it. *Transform* can show the change from the previous element. *Colour by* picks which of the selected configurations' series the heat map's colour channel shows.

**OEP in Periodic Trends.** OEP covers H to Kr (Z = 1 to 36) for neutral atoms and cations, and H to Ar for anions; other elements are skipped, and the page says so.

**Caching.** Results are stored in your browser (local storage) so a scan survives a reload. A configuration whose density did not converge has its ionisation energy withheld. Stored data from an older version of the app is discarded with a message, because newer versions store more per element.

---

## 8. Accuracy: what to trust

### Reference values (numerical grid, neutral atoms, non-relativistic, exchange only)

Total energies in hartree. "KLI − HF" and "OEP − HF" are how far above Hartree–Fock the local-exchange methods sit, in millihartree (1 mHa = 0.001 Ha). The OEP value is the lower of the two optimisers' energies (an upper bound on the true OEP minimum).

| Atom | Hartree–Fock (Ha) | KLI − HF (mHa) | OEP − HF (mHa) | OEP (Ha) |
|---|---|---|---|---|
| He | −2.861680 | 0.00 | 0.00 | −2.861680 |
| Be | −14.573023 | 0.74 | 0.59 | −14.572433 |
| Ne | −128.547043 | 2.21 | 1.63 | −128.545412 |
| Ar | −526.817505 | 7.03 | 5.29 | −526.812217 |
| Fe | −1262.449988 | 15.35 | 12.29 | −1262.437700 |
| Zn | −1777.848096 | 17.41 | 13.76 | −1777.834335 |
| Kr | −2752.054943 | 15.20 | 12.03 | −2752.042917 |

For one- and two-electron atoms HF, KLI and OEP are identical. For every heavier neutral atom from lithium to krypton (all 36 atoms H to Kr were tested) the order is **E(HF) < E(OEP) < E(KLI)**: Hartree–Fock is the unrestricted minimum, the OEP is the best *local* potential, and KLI is one particular local potential.

### Rules of thumb
- **Numerical Hartree–Fock and LDA** are accurate references: a few thousandths of a percent against independently optimised basis-set calculations, even for the heaviest atoms tested (checked directly up to cerium, Z = 58, where the iteration converges).
- **Basis sets.** With the minimal basis, Hartree–Fock total energies are typically within 0.1 to 0.6% of published HF-limit values for light elements, and worse for heavy atoms. Larger bases converge toward the numerical value.
- **Plain numerical Hartree** is the exception: its gap against the basis-set method grows with atomic number (a few tenths of a percent by the lanthanides). The Notes explain why; it is not noise.
- **rSCAN and PBE** are as implemented here, in a spherical, single-configuration setting. Do not compare their absolute energies with all-electron molecular codes without allowing for the model differences.
- **Relativistic corrections** reproduce the grid's shift to within about 1% for neon with a double-zeta basis and recover somewhat less for heavy atoms (the Notes give the figures). It is scalar-relativistic only: no spin-orbit coupling in the energies, and no four-component description.

### KLI and OEP: what the numbers mean
- Each KLI or OEP energy is the energy of the determinant of a *real set of orbitals* in a local potential, so it can never be below the true OEP minimum. That is why it is described as an **upper bound**.
- The OEP is solved by two optimisers and the lower energy is reported. Measured over the 36 neutral atoms H to Kr, the second optimiser (a quasi-Newton method) gave the lower energy for 32, by 0.055 mHa on average and up to 0.195 mHa (krypton). The older optimiser was lower for B, O, Ti and Cr (by 0.01 to 0.09 mHa), which is why both are run.
- The older optimiser alone left the energy too high by about 0.02 mHa for neon, 0.05 for argon, 0.11 for zinc and 0.2 for krypton. The current result removes most of that, but for **iron, scandium and weakly bound anions such as O⁻** the energy may still lie about 0.1 mHa above the true minimum, because the convergence test only examines the region where the density is not tiny.

### Convergence warnings
If the self-consistent density does not settle, the page says so above the energy table, and Periodic Trends withholds that element's ionisation energy. Never use the numbers from a configuration that carries such a warning.

---

## 9. Run times

- The page shows an **estimated time** before a run (per configuration and in total), and a live estimate while a Periodic Trends scan runs. The estimate comes from an empirical table of timings at seven anchor elements (He, Ne, Ar, Kr, Xe, Au, Og), interpolated in between, and it **calibrates itself to your machine** as you run jobs (the speed factor is remembered in your browser).
- Rough orders of magnitude on a typical machine:
  - Hartree, LDA and basis-set runs: seconds.
  - PBE costs about 1.4 to 1.6 times the local functionals; rSCAN about 2 to 3.4 times.
  - Relativistic corrections add almost nothing (about 5%).
  - Numerical Hartree–Fock is the heavy one for large elements.
  - **OEP runs both optimisers**, so it is the slowest of the local-exchange methods: on the reference machine about 2 seconds for helium, 10 s for neon, 32 s for argon and about two minutes for krypton.
  - A **full Periodic Trends scan** with a heavy method (numerical Hartree–Fock, rSCAN) over all 118 elements takes far longer than a single-element run. The estimate shown before you start tells you how long, and *Stop* and *Resume* are there for that reason.
- Keep the browser tab in the foreground for long scans; some browsers slow down background tabs.

---

## 10. Known limitations

These are inherent to the model, not bugs:

- **Spherical atoms.** Orbitals are spherically symmetric radial functions; partly filled shells are treated within that framework.
- **Single configuration.** Each element uses one configuration (Aufbau/Madelung order with the standard exceptions). Some elements, especially among the lanthanides and actinides, have competing configurations close in energy, and the "ground state" assignment (for example lawrencium) is itself debated. The page uses one choice and does not search.
- **Fixed point nucleus.** Only the electronic problem is solved.
- **Scalar relativity only.** The relativistic checkbox is a scalar-relativistic treatment, not a Dirac one. It captures the main contraction/expansion effects but not spin-orbit physics. For the very heaviest elements (oganesson is predicted to behave unusually) treat results as illustrations of trends.
- **KLI and OEP scope.** Numerical grid, non-relativistic, exchange-only; OEP only for neutral atoms and cations to Kr, and anions to Ar. Some anions are unbound in exact exchange (He⁻, and most anions beyond argon) and the page will not report them as converged.
- **No correlation in Hartree, Hartree–Fock, KLI or OEP.** Correlation energy comes only from the DFT functionals. "Hartree–Fock" here is not a correlated method.
- **OEP accuracy.** See above: upper bound, possibly ~0.1 mHa high for iron, scandium and weakly bound anions.
- **Koopmans' theorem.** The §6 values are the Koopmans estimate and are strictly meaningful only for Hartree–Fock.

---

## 11. Troubleshooting and FAQ

**The Add button is disabled for OEP.** OEP covers H to Kr (neutral atoms and cations) and H to Ar (anions). The warning under the form says which limit you hit.

**My configuration shows a convergence warning.** The numerical iteration did not settle. Try a different method or basis; the Notes explain a known case (iron's Hartree–Fock) that was fixed. Never rely on a flagged result.

**KLI/OEP is selected and the basis changed to "numerical."** That is expected: these methods only exist on the numerical grid.

**§5 says "No local potential" for a configuration.** Hartree–Fock and rSCAN have no single multiplicative potential V(r). The other configurations are still drawn.

**The 3D view says it is unavailable.** The 3D orbital view loads a graphics library from the web. If it cannot load (offline, or blocked), the rest of the app still works.

**The time estimate is far off.** The first estimate uses a short startup benchmark; it improves as the page times real jobs on your machine and remembers the correction. Estimates for rare combinations are rougher than for common ones.

**A Periodic Trends scan is taking very long.** Press *Stop*. A partial scan keeps what it has and offers *Resume*. Heavy methods (numerical Hartree–Fock, OEP, rSCAN) over all 118 elements are long by nature.

**Why does the energy of the same atom differ between two methods?** They are different models. Compare like with like: the numerical-grid Hartree–Fock energy is the exchange-only reference; DFT energies include correlation and approximate exchange; KLI/OEP sit just above Hartree–Fock by construction.

**Where did my stored Periodic Trends data go after an update?** If a new version stores more information per element, older cached results are discarded with a message on the page, so you never mix incompatible data. Recompute them.

---

## 12. Glossary

- **Hartree (Ha).** The atomic unit of energy, about 27.211 eV. 1 mHa = 0.001 Ha.
- **Bohr.** The atomic unit of length, about 0.529 Å.
- **Orbital / HOMO.** A one-electron wavefunction; the highest occupied molecular (here atomic) orbital is the outermost occupied one.
- **SCF (self-consistent field).** Iterating until the orbitals and the potential they generate agree.
- **Self-interaction.** The spurious repulsion of an electron with its own charge cloud, which the Hartree method keeps and the exchange term should cancel.
- **LDA / GGA / meta-GGA.** Density functionals that use the density only / also its gradient (PBE) / also the kinetic-energy density (rSCAN).
- **KLI, OEP.** Local-potential approximations to exact exchange: OEP is the best possible local potential, KLI a simpler approximation to it.
- **Upper bound.** A value guaranteed to be at or above the true answer. Here, a KLI or OEP energy is the energy of some real determinant, so the true OEP minimum cannot be above it.
- **STO / basis set.** Slater-type orbital; a finite set of functions used to expand the orbitals. The numerical grid needs none.
- **ΔSCF.** Computing an ionisation energy or electron affinity as the difference between separate self-consistent calculations of the neutral atom and its ion.
- **ZORA.** The scalar-relativistic approach used for the relativistic checkbox (position-dependent effective mass).

---

## 13. About this version

- **Current build:** hash `34337dc07ff9672b`. It includes the §5 Potential V(r) tab, the renumbered sections (§6 to §9), PW92 as the default LDA correlation, a faster numerical eigen-solver (shared by every grid method), and the two-optimiser OEP.
- **Testing.** The project has a suite of 99 automated tests, including a "golden master" that records full-precision results from the validated release across the method matrix, and an OEP test that checks the ordering E(HF) < E(OEP) < E(KLI), published values and the scope limits. A change is released only when every test passes on the exact build.
- **Source.** The app is a single self-contained HTML page built from an engine, an application script and a page template. Developer notes, the test suite and the build script live with the source tree (see the project's developer README).
- **Feedback.** If a result looks wrong, note the element, charge, method, basis and correlation, and whether the page showed a convergence warning; that is enough to reproduce it.
