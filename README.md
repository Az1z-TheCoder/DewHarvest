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

## Test against a real sample

I collected condensate from an air conditioner on Nov 25, 2026 and tested it the same day.

| | Model (pure water, 800 ppm CO₂, 20 °C) | My sample |
|---|---|---|
| pH | about 5.4 | between 7 and 8 |
| Conductivity | about 1.4 µS/cm | 42 µS/cm |

Instruments: wide-range pH indicator paper (1 to 14, one colour step per pH unit) and a handheld TDS & EC pen meter. Photos of the strips and the meter reading are in `sample-test/`.

The sample did not match the pure-water prediction. Its conductivity was about 30 times higher and its pH was near neutral, not acidic. So the water had already picked up dissolved material somewhere between the coil and my glass. I do not know what that material is. Dust on the coil, the metal of the coil and drain pan, and the drain line are all candidates.

One explanation fits both numbers. If the ions were mostly calcium and bicarbonate, 42 µS/cm corresponds to roughly 0.4 meq/L of alkalinity, and water with that alkalinity in equilibrium with 800 ppm CO₂ sits near pH 7.5. That falls inside the range the strip showed. I did not measure alkalinity, so this is a consistent story and not a confirmed one.

What I take from the test:

- The pure-water model in section 2 is a lower bound on minerals and pH. Condensate as it comes out of a real unit is not pure.
- The sample was still low in minerals. Even if every ion were calcium bicarbonate, hardness would be about 20 mg/L as CaCO₃, a third of the 60 mg/L target, so remineralisation is still needed.
- This is one sample, one unit and one day. Indicator paper is also known to be unreliable in weakly buffered water, so the pH reading is rough.

## Method and sources

- Saturation vapour pressure: Magnus equation, coefficients from Alduchov and Eskridge (1996).
- Humidity ratio W = 0.622·e/(P − e). Air is assumed to leave the coil saturated.
- Henry's constant, K₁, K₂ and calcite K_sp as functions of temperature: Plummer and Busenberg (1982).
- HOCl dissociation constant: Morris (1966).
- Hourly weather: Open-Meteo historical archive API (temperature and dew point at 2 m).
- Numerical method: bisection on the charge balance, nested for the calcite equilibrium and the CO₂ dose.

## Limits

- Activity coefficients are taken as 1. That is reasonable for water this dilute (below about 2 mmol/L ionic strength).
- Copper, zinc, ammonia, dust and microbes are not modelled. My sample shows real condensate does pick something up.
- Only moisture brought in with fresh air is counted. Moisture from people and infiltration is not.
- Real contactors stop slightly short of equilibrium.
- The model has been compared with one home-tested sample (above), which it did not match.
- This is a design-comparison tool. It does not certify water as safe to drink.

## How to run

Download `index.html` and open it in any browser. There is nothing to install and no build step. Section 5 opens with my sample readings already filled in. The full-year button needs an internet connection. Everything else works offline.

## What was built during the hackathon, and what came before

Everything in this repository was built during the competition (Nov 23 to 25, 2026). I started the idea, the model and the page on Nov 24.

Earlier work, disclosed: I have a background in thermodynamics from an independent research project on CO₂/R-1270 refrigerant blends (preprint: doi 10.31224/8749, under review at the International Journal of Refrigeration). No code or results from that project are used here. It is where I learned the psychrometrics that section 1 relies on.

## Use of AI tools

I used Claude (Anthropic) as a coding assistant. I chose the problem and the chemistry-first direction and decided what the tool should and should not claim. Claude wrote the code and ran numerical checks on the functions under my direction. I have read the code and can explain each function.

## Next steps

- Test more samples, with alkalinity by titration and copper by test strip, to find out what the dissolved material is.
- Add an activity-coefficient correction (Davies equation).
- Add a simple first-order model for how far a real contactor gets toward equilibrium.

## License

MIT
