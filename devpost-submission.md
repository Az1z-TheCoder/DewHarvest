# DewHarvest: Devpost submission text (paste each block into its field)

## Project title
DewHarvest

## Tagline (short)
A calculator for air-conditioner condensate: how much there is, why it corrodes metal, and what dosing fixes it.

## Category
Engineering & Scientific Computing

## Inspiration / the problem
I live in Dhahran, where summer air is hot and humid and air conditioners run most of the year. Every unit drips water into a drain. The same city gets its drinking water from desalination, which takes roughly 3 to 4 kWh for every cubic metre. I wanted to know how much water is going down those drains and why nobody uses it.

The reason nobody uses it is chemistry. Condensate starts out close to distilled water. With no minerals and no buffer it absorbs CO₂, turns mildly acidic, and dissolves the metal it touches, including copper coil tubing. Collecting it is easy. Treating it is where the engineering is, so that is what I built the tool around.

## What it does
DewHarvest is a single web page that:
- Calculates litres of condensate per hour from outdoor temperature, humidity, fresh-air flow and coil temperature, and runs the same balance over a full year of hourly weather for five Gulf cities.
- Predicts the pH and conductivity of raw condensate from carbonate equilibrium.
- Designs the treatment: the CO₂ dose and the amount of calcite that must dissolve to reach a chosen hardness, with the final pH, alkalinity and saturation index.
- Checks how the final pH affects chlorine disinfection.
- Compares the prediction with a measured sample and explains the gap.

It is meant for building managers, schools and mosques in humid climates deciding whether condensate recovery is worth it, and for students learning water chemistry.

## How I built it
- Water quantity: a mass balance on water vapour across the cooling coil, using the Magnus equation for saturation pressure and the humidity ratio of the air entering and leaving.
- Chemistry: Henry's law, the two carbonic acid dissociation steps, water autoionisation and a charge balance, solved for pH by bisection. Temperature-dependent constants come from Plummer and Busenberg (1982).
- Treatment: a closed-system calcite dissolution model solved to saturation, wrapped in a second solver that finds the CO₂ dose for the hardness target.
- Weather: hourly temperature and dew point from the Open-Meteo archive.
- Stack: plain HTML, CSS and JavaScript in one file, with charts drawn as SVG. No libraries and no build step.

## Technologies used
HTML, CSS, JavaScript, SVG, Open-Meteo API, Claude (AI coding assistant)

## Challenges
- The treatment design needs three solvers nested inside each other (pH inside calcite equilibrium inside CO₂ dose), and each has to stay stable across the whole input range.
- My own sample disagreed with my model. Pure-water chemistry predicts pH 5.4 and almost no conductivity. The condensate I collected read 42 µS/cm and a pH between 7 and 8. I kept the pure-water model, now describe it as a lower bound, and reported the mismatch in the README.

## What I learned
The step desalination plants use to stabilise their product water also applies to a drip tray. I also learned that condensate from a real unit is not the pure water the textbook assumes, and that I only found that out by testing it.

## Results
- At 38 °C and 60% humidity, 1,000 m³/h of fresh air yields about 18 L/h of condensate.
- The model puts pure, untreated condensate near pH 5.4 with about 1.4 µS/cm conductivity.
- Reaching 60 mg/L hardness takes about 26 mg/L of CO₂ and 60 mg/L of calcite, and ends near pH 8.1 at saturation.
- Real sample: condensate from an air conditioner, collected Nov 25, 2026, measured 42 µS/cm on a handheld TDS & EC meter and between pH 7 and 8 on wide-range indicator paper. That is about 30 times the predicted conductivity, so the water had picked up dissolved material I have not identified. If it were mostly calcium bicarbonate, the conductivity would mean about 0.4 meq/L of alkalinity and a pH near 7.5, which fits the strip. I did not measure alkalinity, so that explanation is not confirmed. It is one sample, and the pH paper is rough.

## What's next
More real samples with alkalinity by titration and copper by test strip, an activity-coefficient correction, and a kinetic model of the calcite contactor.

## Disclosure of earlier work and AI use
Everything in the repository was built during the competition period, starting Nov 24, 2026. My earlier independent research on CO₂/R-1270 refrigerant blends (preprint doi 10.31224/8749) gave me the thermodynamics background. No code or results from it are reused. I used Claude as a coding assistant. I set the problem, the approach and the claims. Claude wrote the code and ran numerical checks under my direction. I have read the code and can explain it.

## Team
Abdulaziz (solo): problem definition, modelling choices, review, testing, documentation.

## Links to paste
- Demo: your GitHub Pages link
- Repository: your public GitHub repo link
