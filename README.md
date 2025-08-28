# mirror-quantum-circuit-vs-circuit-fidelity
Measures fidelity vs. circuit depth using mirror circuits. For each depth, build a random forward circuit (sx/x/h/rx/ry + cx), add a barrier, append its inverse, and measure P(0…0). Convert to fidelity via a depolarizing model. Runs on noisy Aer or IBM hardware (SamplerV2), records raw/ISA depth, writes CSV; params are easily tweakable.
