# DewHarvest

A calculator for the water air conditioners throw away. It estimates how much there is, predicts its chemistry, and works out the dosing that would make it stable enough to store and use.

Built for: STEM Innovation Challenge 2026, Engineering & Scientific Computing  
Author: Abdulaziz (solo), IB Diploma student, Dhahran, Saudi Arabia

## The problem

Dhahran is hot and humid, and air conditioning runs most of the year. Every unit drips condensate into a drain. The same city gets its drinking water from desalination, which costs roughly 3 to 4 kWh per cubic metre.

Condensate starts out close to distilled water, so it looks like a free resource. But water with no minerals and no buffer is aggressive. It takes up CO₂ from the air, turns mildly acidic, and dissolves the metal it touches, including copper coil tubing. Catching the drip takes a bucket. Making the water safe for pipes and tanks takes chemistry, and that is the part this tool works out.

## What it computes

1. Quantity. A mass balance on water vapour across the cooling coil gives litres per hour for any outdoor temperature, humidity, fresh-air flow and coil temperature. One button runs the same balance over all 8,760 hours of 2025 weather for Dhahran, Riyadh, Jeddah, Doha or Dubai and charts litres per month.
2. Raw condensate chemistry. The page solves Henry's law for CO₂, both dissociation steps of carbonic acid, water autoionisation and a charge balance for pH. It also predicts conductivity from ionic conductivities.
3. Treatment design. This is the remineralisation step desalination plants use: inject CO₂, then pass the water through a calcite (CaCO₃) contactor. The tool solves for the CO₂ dose so that the calcite dissolved meets a hardness target, and reports the final pH, alkalinity and calcite saturation index. A chart shows pH rising as the calcite dissolves.
4. Disinfection check. The share of free chlorine present as HOCl at the raw and treated pH. Raising pH protects metal but weakens chlorine, and this shows by how much.
5. Sample comparison. Enter a measured pH, conductivity and (optionally) alkalinity for a real condensate sample. The page compares them with the pure-water prediction and says what the gap implies.

## Example results (defaults)

| Input | Result |
|---|---|
| 38 °C, 60% humidity, 1,000 m³/h fresh air, coil at 12 °C | about 18 L/h of condensate |
| Pure condensate in 800 ppm room air at 20 °C | pH about 5.4, conductivity about 1.4 µS/cm |
| Target hardness 60 mg/L as CaCO₃ | about 26 mg/L CO₂ and 60 mg/L calcite, final pH about 8.1, saturation index 0 |

## Test against real samples

I collected condensate from an air conditioner twice on Nov 25, 2026 and tested each collection the same day.

| | Model (pure water, 800 ppm CO₂, 20 °C) | Sample A (first collection) | Sample B (repeat collection) |
|---|---|---|---|
| pH | about 5.4 | between 7 and 8 | about 5.8 |
| Conductivity | about 1.4 µS/cm | 42 µS/cm | 4.2 µS/cm |

Instruments: pH indicator paper and a handheld TDS & EC pen meter. Photos of sample A's strips and meter reading are in `sample-test/`. Sample B has no photos in this repository.

The two collections disagree with each other. Sample A had about 30 times the predicted conductivity and a near-neutral pH, so the water had picked up dissolved material before it reached my glass. Sample B came out much closer to the pure-water prediction: about 3 times the conductivity and a pH within half a unit. I have not tested what caused the difference.

Neither sample is explained. I did not measure alkalinity. If the ions in each were mostly calcium and bicarbonate, sample A (42 µS/cm) corresponds to about 0.4 meq/L of alkalinity, and the model gives a pH near 7.5, which fits the strip. Sample B (4.2 µS/cm) corresponds to about 0.04 meq/L, and the model gives a pH near 6.5, against 5.8 on the paper. A gap of about 0.7 pH units is within what pH paper can resolve in weakly buffered water, but it means B is not an exact match either.

What I take from the tests:

- The pure-water model in section 2 is a lower bound on minerals and pH. Real condensate picked up dissolved material in sample A and a little in sample B.
- Both samples were low in minerals. Even if every ion were calcium bicarbonate, hardness would be at most about 20 mg/L as CaCO₃ (A) and about 2 mg/L (B), against the 60 mg/L target, so remineralisation is still needed.
- Two collections on the same day differed by a factor of ten in conductivity, so one measurement cannot stand for a unit.
- These are two samples from one day. Indicator paper is coarse in weakly buffered water, and pen meters of this kind are known to be less reliable at very low conductivity, so B's 4.2 µS/cm is the less certain of the two conductivity readings.

## Method and sources

- Saturation vapour pressure: Magnus equation, coefficients from Alduchov and Eskridge (1996).
- Humidity ratio W = 0.622·e/(P − e). Air is assumed to leave the coil saturated.
- Henry's constant, K₁, K₂ and calcite K_sp as functions of temperature: Plummer and Busenberg (1982).
- HOCl dissociation constant: Morris (1966).
- Hourly weather: Open-Meteo historical archive API (temperature and dew point at 2 m).
- Numerical method: bisection on the charge balance, nested for the calcite equilibrium and the CO₂ dose.

## Limits

- Activity coefficients are taken as 1. That is reasonable for water this dilute (below about 2 mmol/L ionic strength).
- Copper, zinc, ammonia, dust and microbes are not modelled. My samples show real condensate picks up dissolved material, though how much varied between collections.
- Only moisture brought in with fresh air is counted. Moisture from people and infiltration is not.
- Real contactors stop slightly short of equilibrium.
- The model has been compared with two home-tested samples (above). It matched one poorly and one fairly closely, and I have not explained why.
- This is a design-comparison tool. It does not certify water as safe to drink.

## How to run

Download `index.html` and open it in any browser. There is nothing to install and no build step. Section 5 opens with my sample readings already filled in. The full-year button needs an internet connection. Everything else works offline.

## What was built during the hackathon, and what came before

Everything in this repository was built during the competition (Nov 23 to 25, 2026). I started the idea, the model and the page on Nov 24.

Earlier work, disclosed: I have a background in thermodynamics from an independent research project on CO₂/R-1270 refrigerant blends (preprint: doi 10.31224/8749, under review at the International Journal of Refrigeration). No code or results from that project are used here. It is where I learned the psychrometrics that section 1 relies on.

## Use of AI tools

I used Claude (Anthropic) as a coding assistant. I chose the problem and the chemistry-first direction and decided what the tool should and should not claim. Claude wrote the code and ran numerical checks on the functions under my direction. I have read the code and can explain each function.

## Next steps

- Test more samples, with alkalinity by titration and copper by test strip, to find out what the dissolved material is and why two collections differed.
- Add an activity-coefficient correction (Davies equation).
- Add a simple first-order model for how far a real contactor gets toward equilibrium.

## License

MIT
