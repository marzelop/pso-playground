# PSO Playground

An interactive, tunable demonstration of **Particle Swarm Optimization (PSO)** — watch a swarm of particles hunt the minimum of a 2‑D function in real time, and *feel* how the algorithm's parameters change its behaviour.

**▶ Live demo: https://panas-bhattarai.github.io/pso-playground/**

No install, no server, no sign‑up — the whole optimizer runs client‑side in your browser. Open the link and play.

It is a single self‑contained `index.html` (vanilla JavaScript + HTML Canvas). Clone it, double‑click it, and it runs offline just the same.

---

## What you can do

- **Pick a landscape** — Sphere, Rastrigin, Ackley, Himmelblau, or Rosenbrock — shown as a top‑down color‑graded heatmap (dark valleys → bright peaks).
- **Tune the swarm live** — swarm size (10–1000), inertia $w$, cognitive/social pulls $c_1, c_2$, velocity clamp $V_{\max}$, and animation speed. Sliders take effect *mid‑run*.
- **See it get stuck, then free it** — two presets, **Trap it** and **Free it**, reproduce the classic lesson: a small, low‑inertia swarm freezes in a local pit; a larger, higher‑inertia swarm escapes and finds the true minimum.
- **Click‑to‑set‑target mode** — click anywhere and the selected function is *shifted* so its global minimum lands under your cursor; the swarm then chases that point (through all the local traps, if you're on Rastrigin).
- **Read the convergence plot** — global‑best value vs. iteration on a log axis, live. A flat line is undeniable proof the swarm is stuck; a step down is the escape.
- **Reproducible runs** — a random seed makes any "stuck" configuration repeatable for teaching.

---

## The algorithm

Each particle $i$ has a position $\mathbf{x}_i$ and velocity $\mathbf{v}_i$ in the search space. It remembers its own best‑ever position $\mathbf{p}_i$ (the *personal best*), and the swarm shares the best position found by anyone, $\mathbf{g}$ (the *global best*). At every iteration each particle updates:

$$
\mathbf{v}_i^{\,t+1} = \underbrace{w\,\mathbf{v}_i^{\,t}}_{\text{inertia}} \;+\; \underbrace{c_1 r_1 \,(\mathbf{p}_i - \mathbf{x}_i^{\,t})}_{\text{cognitive: pull to own best}} \;+\; \underbrace{c_2 r_2 \,(\mathbf{g} - \mathbf{x}_i^{\,t})}_{\text{social: pull to global best}}
$$

$$
\mathbf{x}_i^{\,t+1} = \mathbf{x}_i^{\,t} + \mathbf{v}_i^{\,t+1}
$$

where $r_1, r_2 \sim \mathcal{U}(0,1)$ are fresh random numbers each step, drawn independently per component. The velocity is clamped to $[-V_{\max}, V_{\max}]$ and positions are reflected back at the domain walls.

**Reading the knobs physically:**

| Parameter | Symbol | Effect |
|---|---|---|
| Inertia | $w$ | Momentum carried between steps. High $w$ → wide exploration, overshoots pits. Low $w$ → quick settling, easily trapped. |
| Cognitive | $c_1$ | How strongly a particle returns toward its own best find. |
| Social | $c_2$ | How strongly the swarm rushes toward the global best. High $c_2$ + low $w$ → premature collapse onto the first decent point found. |
| Velocity clamp | $V_{\max}$ | Caps step size; too small and particles can't climb out of a basin. |
| Swarm size | $N$ | More particles → more of the landscape sampled at once → better odds of finding the global basin. |

The **stuck → escape** story is just $w$, $V_{\max}$, and $N$ fighting the local minima: raise any of them and a trapped swarm gains the reach to jump the ridge separating it from the true optimum.

---

## The test functions

All are classic optimization benchmarks. Each is evaluated on a bounded box (the search bounds); the true global minimum value is $0$ for every one of them.

**Sphere** — a single convex bowl. The sanity check: any reasonable setting converges.

$$ f(x,y) = x^2 + y^2, \qquad \min \text{ at } (0,0) $$

**Rastrigin** — a bowl tiled with a regular grid of local minima (an "egg carton"). The star of the trap/escape demo.

$$ f(x,y) = 20 + \left[x^2 - 10\cos(2\pi x)\right] + \left[y^2 - 10\cos(2\pi y)\right], \qquad \min \text{ at } (0,0) $$

**Ackley** — a nearly flat outer plateau with one deep, narrow hole in the centre. Deceptive: the swarm wanders until someone stumbles into the hole.

$$ f(x,y) = -20\,\exp\!\left(-0.2\sqrt{\tfrac{1}{2}(x^2+y^2)}\right) - \exp\!\left(\tfrac{1}{2}\left[\cos 2\pi x + \cos 2\pi y\right]\right) + 20 + e $$

**Himmelblau** — **four** equally deep global minima. Watch the swarm split, then collapse onto one of them.

$$ f(x,y) = (x^2 + y - 11)^2 + (x + y^2 - 7)^2 $$

**Rosenbrock** — a single minimum at the bottom of a long, curved, narrow valley (the "banana"). Not about local traps — about slow progress along a valley floor.

$$ f(x,y) = (1 - x)^2 + 100\,(y - x^2)^2, \qquad \min \text{ at } (1,1) $$

### Click‑to‑set‑target

Benchmark functions have *fixed* minima, so instead of moving a minimum, the app **shifts the whole landscape**. Given a base function $f$ with global minimum at $\mathbf{m}_0$ and a clicked target $\mathbf{t}$, the active objective becomes

$$ g(\mathbf{x}) = f(\mathbf{x} - \mathbf{d}), \qquad \mathbf{d} = \mathbf{t} - \mathbf{m}_0 $$

so $g$'s global minimum sits exactly at $\mathbf{t}$ while every bump and local pit of $f$ travels along with it. Chasing a click on Rastrigin therefore still means fighting through the egg carton.

---

## Running / hosting it yourself

**Locally:** clone and open the file — that's it.

```bash
git clone https://github.com/panas-bhattarai/pso-playground.git
cd pso-playground
# then just open index.html in any browser (double-click it)
```

**On the web (GitHub Pages):** because it's one static file, Settings → Pages → deploy from the `main` branch serves it at `https://<user>.github.io/pso-playground/`. No build step, no backend.

---

## Why this exists

PSO is a *metaheuristic* — a derivative‑free optimizer for non‑convex problems where gradient methods stall. It (and its relatives GA and DE) shows up across engineering, including power systems: economic dispatch, optimal capacitor placement, reactive‑power / VAr optimization, and non‑convex optimal power flow. This playground is an intuition‑builder for that whole family of solvers: *why* a search stalls above the true optimum, and *what* you change to get it moving again.

---

## License

[MIT](LICENSE) © 2026 Panas Bhattarai
