# Beam Designer

Beam Designer is a single-page educational tool for analyzing a straight beam under vertical loads. Adjust the beam, supports, loads, material, and section to view reactions, shear, bending moment, deflection, and basic bending and deflection checks.

## Features

- Simply supported and left-fixed cantilever beams, with spans from 4 to 40 ft.
- Editable point loads and partial or full-span uniform loads, directed upward or downward.
- Steel W-shapes and Douglas Fir-Larch No. 2 lumber section selections.
- Synchronized beam and diagram cursor, including left- and right-hand shear values at point loads.
- Bending and deflection checks, with a passing-section recommendation when one is available.
- Input validation, load limits, and responsive layouts for desktop and phone screens.

The application opens with a 20 ft simply supported W12x26 beam carrying a 1 kip/ft full-span uniform load.

## Engineering calculations

Reactions are calculated from static equilibrium. Shear and moment account for signed point loads and uniform loads; their extrema include load boundaries and applicable one-sided shear values. Deflection is calculated by integrating the elastic-curve relationship `y'' = -M/EI` using the selected section properties and the specified support boundary conditions. The app then calculates maximum bending stress and compares stress and deflection with the selected allowable values. Steel recommendations use the lightest passing listed section; lumber recommendations use the first passing section in the table.

## Units and sign conventions

- Loads and reactions: kips; uniform-load intensity: kips/ft.
- Span and load positions: ft; shear: kips; moment: kip-ft.
- Bending stress: ksi; deflection: inches.
- Downward loads are positive and upward loads are negative.
- Positive shear acts upward on the left face of a cut; positive moment is sagging; positive deflection is downward.
- Vertical reactions are positive upward. For a cantilever, the displayed external fixed-end reaction moment is positive counterclockwise.

## Run locally

1. Download or open the project folder.
2. Open `index.html` directly in a modern web browser.
3. Change the controls in the input panel. Valid changes update the diagrams and results; invalid inputs are shown inline and make calculated results unavailable.

The application is self-contained in `index.html` and does not require a build step or external packages.

## Assumptions and limitations

The model uses a prismatic beam with constant `EI`, vertical loading, and the listed material and section properties. It does not include self-weight, shear stress, lateral-torsional buckling, load combinations, or continuous beams. Saving designs and analyzing overhanging beams are also outside the current scope.

This project is an educational engineering tool, not a substitute for professional engineering judgment. Independently verify calculations and design decisions before using results for professional work.