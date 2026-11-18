# Beam Designer

## Purpose
A beam design tool for engineers and students. The user sets up a beam,
places loads on it, and picks a steel or lumber section. The app shows the
shear, moment, and deflection diagrams and checks whether the beam is
strong and stiff enough, updating instantly as anything changes.

## Users
A civil engineering student checking a homework beam, or an engineer
sizing a floor beam or deck joist.

## Inputs
- Beam dimensions:
  - Span: 4 to 40 ft, default 20 ft, set with a slider and a number box that stay in sync.
    The slider increments in 0.5 ft. The number box accepts manual values
    to 0.001 ft for more precise analysis.
  - Numeric precision: all numeric inputs accept no more than three decimal
    places. A value with more than three decimal places shows an inline
    validation error and remains visible for correction; it is never rounded
    or silently changed.
  - When the span changes, preserve every existing manually entered point-
    load position and every uniform-load start/end boundary. If a preserved
    position falls outside the new 0-to-span range, show an inline validation
    error and make calculated results unavailable. Do not clamp or rewrite
    manually entered values.
  - A newly added full-span uniform load has an end position that tracks the
    current beam span automatically until the user manually edits that end
    position. After the end position is manually edited, preserve the entered
    value when the span changes and validate it normally.
  - No separate beam-width or beam-depth input is needed; cross-section dimensions and properties come from the selected steel or lumber section.
- Support type: Simply supported (default) or Cantilever (fixed at the left end), selected with two radio buttons or a dropdown.
- Loads, listed in an editable panel:
  - Point loads:
    - Magnitude: 0 to 100 kips, default 0 kip for a newly added load.
      The number box uses 0.001 kip steps and accepts no more than three
      decimal places.
    - Direction: Down or Up, default Down.
    - Position: 0 to the span, in ft, default at midspan or at the cantilever free end.
      The number box uses 0.001 ft steps and accepts no more than three decimal
      places. Dragged positions snap to the nearest 0.001 ft and remain
      continuous relative to the 0.5-ft span-slider increments.
    - Control: number box for magnitude and position, with a direction dropdown or radio control.
    - Point loads can also be dragged along the beam in the drawing. Dragging a point load outside the beam clamps its position to the nearest beam boundary.
    - Manually entering a point-load position outside 0 ft to the span must show a clear validation error and leave the entered invalid value visible rather than silently changing it.
  - Uniform loads:
    - Magnitude: 0 to 10 kips/ft, default 1 kip/ft for a newly added load.
      The number box uses 0.001 kip/ft steps and accepts no more than three
      decimal places.
    - Direction: Down or Up, default Down.
    - Start and end positions: 0 to the span, in ft.
      Each number box uses 0.001 ft steps and accepts no more than three
      decimal places. Start must be strictly less than end and both must remain
      within 0 ft through the span.
    - Default coverage: start 0 ft and end at the span, producing a full-span load.
    - Control: number boxes for magnitude, start, and end.
    - Manually entering a uniform-load start or end outside 0 ft to the span must show a clear validation error and leave the entered invalid value visible rather than silently changing it.
  - "Add point load" and "Add uniform load" buttons.
- Limit the editable load list to 20 point loads and 10 uniform loads.
  Disable or hide further add actions at the limit and show a clear message:
  "Maximum of 20 point loads reached." or "Maximum of 10 uniform loads reached."
- Each load can be edited or removed. A beam may have no loads.
- Numeric validation:
  - Blank, non-numeric, negative where prohibited, and out-of-range values
    show an inline error rather than silently changing or clearing the entry.
  - Preserve invalid entries so the user can correct them.
  - Apply this to span, point-load magnitude and position, uniform-load
    magnitude, and uniform-load start/end positions.
  - Suppress or clearly mark shear, moment, deflection, and check results
    unavailable whenever a required input is invalid.
- Material and section:
  - Material: Steel W-shapes or Douglas Fir-Larch No. 2 lumber, selected with a dropdown or segmented control.
  - Section: a dropdown containing only the sections in the selected material, default Steel W12x26.
    When material changes, preserve the selected section only if that exact
    section name exists in the new material. Otherwise, automatically select
    the new material's default section and recalculate immediately. The
    lumber default section is 2x8.
  - The fixed section tables and their units are:

  Steel W-shapes (E = 29,000 ksi, allowable bending stress Fb = 30 ksi):

  | Section | Ix (in^4) | Sx (in^3) | Weight (lb/ft) |
  |---------|-----------|-----------|----------------|
  | W8x10   | 30.8      | 7.81      | 10 |
  | W10x12  | 53.8      | 10.9      | 12 |
  | W12x26  | 204       | 33.4      | 26 |
  | W14x22  | 199       | 29.0      | 22 |
  | W16x26  | 301       | 38.4      | 26 |
  | W18x35  | 510       | 57.6      | 35 |
  | W21x44  | 843       | 81.6      | 44 |
  | W24x55  | 1350      | 114       | 55 |

  Douglas Fir-Larch No. 2 lumber (E = 1,600 ksi, Fb = 0.900 ksi):

  | Section | Ix (in^4) | Sx (in^3) |
  |---------|-----------|-----------|
  | 2x8     | 47.63     | 13.14     |
  | 2x10    | 98.93     | 21.39     |
  | 2x12    | 177.98    | 31.64     |

- Deflection limit: L/240 or L/360, selected with two radio buttons or a dropdown, default L/360.
- Self-weight is ignored.

## Calculations
- Sign convention:
  - Downward point and uniform loads are positive; upward loads are negative.
  - Positive shear is upward on the left face of a beam cut.
  - Positive moment is sagging moment.
  - Positive deflection is downward.
  - Vertical reactions Ay, By, and Av are positive upward and negative downward.
- Reactions from statics:
  - Simply supported: let Ay and By be the left and right vertical reactions.
    Let P and w be signed, with downward positive. First, total downward
    load magnitude is sum(P) + sum(w * Lw). Vertical equilibrium is
    Ay + By = sum(P) + sum(w * Lw). Moment equilibrium about the left
    support is By * L = sum(a * P) + sum(xbar * w * Lw). Solve By first,
    then Ay. For upward loads, P or w is negative and reduces both the
    reaction sum and moment sum.
  - Cantilever (fixed at the left end): let Av be the vertical reaction and
    Mreaction be the external fixed-end reaction moment. Vertical equilibrium
    is Av = sum(P) + sum(w * Lw), so Av is the signed vertical reaction.
    A negative Av represents a downward vertical support reaction, not a
    downward applied load. Moment equilibrium about the fixed end gives
    Mreaction = sum(a * P) + sum(xbar * w * Lw). Define the external
    reaction moment positive counterclockwise and negative clockwise. Thus,
    a downward point load at the free end produces a counterclockwise external
    reaction moment of +P*a, or +50 kip-ft for a 5-kip load at 10 ft.
    The internal bending moment follows the sagging-positive
    convention, so the same fixed-end case has negative internal bending
    moment. No horizontal reaction is included because all loads are vertical.
    Display Av and Mreaction with their physical directions.
- Point loads and uniform loads:
  - A point load contributes its signed magnitude P at its exact position.
  - A partial uniform load is represented by signed intensity w over
    interval [a, b]. At position x, its contribution is zero for x < a,
    w * (x - a) for a <= x <= b, and w * (b - a) for x > b.
  - If a uniform-load end position is less than or equal to its start
    position, stop the shear, moment, deflection, and check calculations
    and show a clear error. Do not silently swap or cut off the interval.
- Shear and moment:
  - Calculate V(x) and M(x) by statics or by integrating the signed load
    resultants, using the reaction values from equilibrium.
  - Use the differential relationships dV/dx = -q(x) and dM/dx = V(x),
    with the existing sign convention: downward distributed load q(x) is
    positive and upward distributed load q(x) is negative.
  - Evaluate V and M at 1 ft spacing or finer, plus exactly at every point-
    load position and every uniform-load start and end position.
  - Evaluate shear immediately to the left and right of every point load,
    including one-sided values at beam boundaries where applicable.
  - Find maximum and minimum shear using these one-sided values and the
    extrema within each segment.
  - Find exact moment extrema by evaluating every load boundary and every
    location where V(x) = 0 within each segment; do not rely only on 1-ft
    sampling.
  - Include upward loads in the signed equations. The maximum and minimum
    displayed shear and moment are taken from both the sampled segments and
    the exact load boundaries.
- Deflection:
  - Use the selected section properties and constant EI:
    E = 29,000 ksi and I = Ix for steel; E = 1,600 ksi and I = Ix for lumber.
  - Because positive bending moment is sagging and positive deflection is
    downward, use the elastic-curve equation y''(x) = -M(x)/EI.
    Integrate this equation twice using consistent length and force units.
  - Simply supported boundary conditions: y(0) = 0 and y(L) = 0.
  - Cantilever boundary conditions at the fixed left end: y(0) = 0 and
    y'(0) = 0.
  - Convert forces and lengths to inches for the deflection calculation:
    E is in kips/in^2, M is converted from kip-ft to kip-in by multiplying
    by 12, I is in in^4, and L is in inches.
  - Sample y(x) at 1 ft spacing or finer and evaluate it at every point-load
    position and uniform-load start/end position. Find the absolute maximum
    deflection across the entire beam, including between sample points, by
    finding the roots of the deflected slope y'(x) in every polynomial
    segment between load boundaries and including both beam ends.
- Bending stress:
  - Convert the maximum absolute bending moment from kip-ft to kip-in:
    M (kip-in) = |M max| (kip-ft) x 12.
  - Bending stress is fb = M (kip-in) / Sx (in^3), yielding ksi.
  - The bending check passes if fb <= Fb, where Fb is 30 ksi for steel or
    0.900 ksi for lumber.
- Deflection check:
  - Convert the beam span L to inches: L (in) = span (ft) x 12.
  - The deflection check passes if |y max| (in) <= L/240 or L/360,
    according to the selected limit.
- Lightest passing section:
  - For steel, test the sections in the selected material table and select
    the lowest-weight passing section that passes both bending and
    deflection checks. If two passing sections have the same weight, select
    the one with the larger Ix.
  - For lumber, test the sections in table order and select the first
    section that passes both bending and deflection checks. This is the
    smallest passing listed lumber section.
- Units: report reactions in kips, x, span, and load positions in ft, shear
  in kips, moment in kip-ft, bending stress in ksi, and deflection in inches.

## Outputs
- Beam drawing:
  - Show the beam along a horizontal x-axis labeled in feet, from 0 ft to
    the selected span.
  - Draw the support symbols for the selected support type. A simply
    supported beam shows a pin and roller; a cantilever shows a fixed support
    at the left end.
  - Point loads are single arrows with their signed magnitude and units
    labeled. Downward arrows are positive; upward arrows are negative.
    Uniform loads are rows of small arrows labeled with the signed intensity
    in kips/ft and their start and end positions.
  - Display reactions with magnitude in kips and direction. Show the
    cantilever vertical reaction and fixed-end reaction moment with their
    physical directions.
- Diagrams:
  - Stack the shear, moment, and deflected-shape diagrams directly under
    the beam drawing and align all of them to the same 0-to-span ft x scale.
  - Each diagram has a labeled y-axis in kips for shear, kip-ft for moment,
    or inches for deflected shape, and a horizontal zero line.
  - Shear is plotted under the positive-shear convention: positive values
    are above the zero line and negative values are below it. Moment is
    plotted with sagging-positive values above the zero line and negative
    values below it. Deflection is plotted positive downward: downward
    deflection extends below the zero line and upward deflection, if present,
    extends above it.
  - Fill each diagram and label the exact maximum and minimum shear and
    moment values and locations. For deflection, label the absolute maximum
    deflection and its location. Every label states the value and x position
    in its output units.
- Hover behavior:
  - Moving the mouse over the beam or any diagram shows a vertical cursor
    line at the corresponding beam position across the beam drawing and all
    three diagrams.
  - On touch devices, tapping or clicking a beam or diagram position selects
    that position and shows the same hover values. A selected position remains
    visible until the user selects another position or clears the selection.
  - Display V in kips, M in kip-ft, and y in inches at the same x position.
  - At a point-load position, display both the left-hand limiting shear
    value, labeled V−, and the right-hand limiting shear value, labeled V+.
    Display the continuous moment value M and continuous deflection value y
    at that same location.
  - Evaluate hover values at the exact beam position and include the load-
    boundary locations and segment extrema; do not interpolate through a
    point-load shear discontinuity.
- Check cards:
  - Bending card: show actual fb = |Mmax| x 12 / Sx in ksi, where Mmax is
    in kip-ft and Sx is in in^3, and allowable Fb in ksi. PASS if fb <= Fb.
  - Deflection card: show actual |ymax| in inches, allowable L/240 or L/360
    in inches, and PASS if |ymax| <= the selected allowable deflection.
  - Each card shows the ratio actual/allowable as a percentage. Use green
    below 70%, amber from 70% up to but not including 100%, and red at or
    above 100%. A 100% ratio passes because the engineering comparison is
    actual <= allowable.
  - A ratio bar represents 100% at the full pass boundary and remains
    readable for ratios above 100%; show the actual percentage separately,
    even when the bar is capped or scaled.
- Section recommendation:
  - Show a line recommending the selected-material section that passes both
    checks according to the material-specific rule: the lowest-weight
    passing steel section (using larger Ix for equal weight), or the first,
    smallest passing listed lumber section in table order.
  - A "Use this section" button changes the selected section dropdown to the
    recommended section and recalculates all results.
  - If no available section passes both checks, show a clear message stating
    that no available section passes.
- Invalid-input behavior:
  - If an input is invalid or incomplete, show the relevant in-place
    validation error and do not calculate or display misleading results.
  - Hide or disable affected diagrams and check cards, or clearly mark them
    as stale and unavailable, until the input is corrected.
- Output precision:
  - Keep full precision internally for every calculation and comparison.
  - Display loads, reactions, shear, moment, bending stress, and deflection
    to two decimal places by default. Use more appropriate engineering
    precision only when a two-decimal display would become misleading.
- Everything updates immediately whenever a valid input changes.

## Look and feel
- Opens with an example already loaded (20 ft simply supported W12x26
  with a 1 kip/ft uniform load over the full span), so it is useful the
  moment it opens.
- Overall appearance:
  - Use a clean, professional commercial-engineering aesthetic: a light
    neutral background, white cards with subtle shadows and thin borders,
    one restrained accent color, and a modern system font.
  - Use a clear hierarchy: 24 px page headings, 18 px panel headings,
    14-16 px body text, and 12-14 px labels and units. Use consistent
    16 px card padding and 16-24 px gaps. Avoid decorative clutter.
- Desktop layout:
  - For widths at or above 1100 px, use a 320 px fixed left input panel and
    a flexible right results panel. Keep 24 px between them and 24 px of
    outer page padding.
  - Group controls inside the input panel by beam dimensions/support,
    loads, section, and deflection limit. Keep the beam drawing, diagrams,
    and check cards in the right panel.
  - Below 1100 px, stack the input panel above the results; do not require
    horizontal page scrolling.
- Phone layout:
  - For widths below 700 px, use a single-column layout with no fixed side
    panels or horizontal page scrolling.
  - Stack sections in this order: beam dimensions and support, loads,
    material and section, deflection limit, beam drawing, diagrams, and check
    cards.
  - Keep the beam and all three diagrams aligned to the same horizontal
    coordinate scale. If the full drawing is wider than the available width,
    present it in a bounded horizontally scrollable drawing area rather than
    shrinking labels below readable size.
- Status colors:
  - Use green text, border, and icon for PASS.
  - Use amber text, border, and icon for an engineering ratio from 70% up
    to but not including 100%.
  - Use red text, border, and icon for FAIL or an engineering ratio at or
    above 100%.
  - Use a red input border and an inline error icon/text for invalid input;
    never rely on color alone.
  - Keep the restrained accent color separate from pass/fail colors and use
    it for primary controls and selected input states.
- Readability and dense diagrams:
  - Use a minimum of 8 px of horizontal separation between load labels, and
    hide only redundant load labels before hiding beam values or axes.
  - When point loads or uniform-load ranges overlap, list their editable
    controls separately by load row, and use compact arrows plus leader lines
    or collision-avoiding label placement in the drawing. Keep remove/edit
    controls reachable for every load without requiring the drawing labels
    to be selected.
  - At many loads or short spans, shorten arrowheads and use smaller but
    readable labels rather than overlapping labels. Keep the full span,
    supports, reaction values, and diagram axes visible.
  - Use a stable 0 line and avoid changing the drawing scale abruptly between
    small value changes. Autoscale independently in each diagram only enough
    to keep values readable.
  - If labels overlap, draw leader lines or place extrema labels outside the
    filled area while keeping their exact x positions and units.
- Controls and accessibility:
  - Give every input a short visible label and units. Labels, units, validation
    text, and values must remain readable at the phone breakpoint.
  - Use at least 44 px height for buttons and primary controls, with visible
    keyboard focus outlines on every interactive element.
  - Use native or semantically equivalent form controls, with programmatic
    labels and validation messages announced to assistive technology.
  - Provide hover formulas and diagram values as tap/click or persistent
    tooltip alternatives on touch devices.
  - Do not rely on color alone: PASS/FAIL cards include text and icons,
    invalid fields include text and icons, and selected controls include
    a visible checked or selected state.

## Validation
1. Simply supported, 20 ft, W12x26, uniform 1 kip/ft over the full span:
   reactions 10 k and 10 k; max shear 10 k; max moment 50.0 kip-ft at
   10 ft; fb = 17.96 ksi; max deflection 0.609 in at 10 ft; L/360 =
   0.667 in; both checks pass.
2. Simply supported, 20 ft, W12x26, point load 10 k at 5 ft: reactions
   7.5 k (left) and 2.5 k (right); max moment 37.5 kip-ft at 5 ft;
   fb = 13.47 ksi; max deflection 0.340 in at 8.82 ft.
3. Cantilever, 10 ft, W12x26, point load 5 k at the free end: reaction
   5 k and fixed-end moment 50 kip-ft; max deflection 0.487 in at the free
   end (PL^3/3EI); L/360 = 0.333 in, so deflection fails.
4. Simply supported 12 ft 2x10 joist, uniform 0.05 kip/ft: max moment
   0.90 kip-ft; fb = 0.505 ksi; max deflection 0.147 in; both pass.

## Out of scope
- Self-weight, shear stress, lateral-torsional buckling, load
  combinations, and continuous beams.
- Saving designs.
- Hand checks:
  - Case 1, simply supported beam with full-span uniform load:
    R_A = R_B = wL/2 and M_max = wL^2/8.
    y_max = 5wL^4/(384EI).
  - Case 3, cantilever with a downward point load P at the free end:
    R_A = P.
    External support reaction moment = +PL counterclockwise.
    Internal fixed-end bending moment = -PL.
    y_max = PL^3/(3EI).
  - Use consistent units in all hand checks. Convert feet to inches and
    kip/ft to kip/in for deflection calculations.
  - For case 2, calculate the reactions and maximum moment.

Ideas for features: An overhanging beam. Self-weight, added automatically from the section's weight. A side-by-side comparison of two sections. A printable calculation sheet showing the inputs, diagrams, and checks.

## Implementation stages

### 1. Visual mockup
- Create a single `index.html` with embedded HTML, CSS, and JavaScript.
- Build the desktop two-column layout and phone stacking.
- Add placeholder beam drawing, shear/moment/deflection diagrams, and check cards.
- **Verification:** Open in desktop and phone-sized browsers; confirm layout, responsiveness, one-file operation, and initial example presentation.

### 2. Inputs and validation
- Add support, span, load, material, section, and deflection-limit controls.
- Implement ranges, three-decimal precision, span-change preservation, uniform-load tracking, intervals, and load limits.
- Keep invalid entries visible and mark results unavailable.
- **Verification:** Test blank, negative, non-numeric, out-of-range, reversed interval, span-change, material-change, overload, and unloaded cases.

### 3. Reactions and V/M behavior
- Add a separate engineering-calculation section within the embedded JavaScript.
- Implement reactions, point and uniform loads, signed shear/moment, exact extrema, and the one-sided point-load values.
- Render the beam, supports, loads, reactions, shear diagram, and moment diagram.
- **Verification:** Check all four validation cases plus upward loads and mixed partial loads. This stage must confirm the specified reaction, moment, sign, and unit behavior before deflection is added.

### 4. Deflection and design checks
- Implement `y'' = -M/EI`, both boundary-condition sets, exact absolute maximum deflection, and bending stress.
- Add PASS/FAIL cards, ratio colors, and steel/lumber section recommendations.
- **Verification:** Confirm validation deflections and pass/fail outcomes, the 100% pass boundary, cantilever signs, partial-load deflection, and “Use this section” recalculation.

### 5. Interaction and final polish
- Complete mouse/touch/click hover behavior, persistent selection, `V-`/`V+`, dense labels, accessibility, and invalid-state presentation.
- Apply final responsive styling and two-decimal output formatting.
- **Verification:** Test desktop and phone layouts, keyboard/touch interaction, maximum loads, overlapping labels, invalid inputs, all validation examples, and section-switch behavior.