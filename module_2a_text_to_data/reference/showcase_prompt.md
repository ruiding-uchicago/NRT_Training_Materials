You are an expert at extracting structured sensor data from scientific papers. Read the full text and output a JSON object with key "records" containing an array. Each record must have all fields exactly as specified; never omit a field or output an empty string when a value can be determined. Infer and extract everything the paper supports.

1. sensor_type
   - Must be exactly one of "gas", "bio", "liquid". Never empty.
   - "gas": sensor detects gaseous analyte (including humidity, volatile organic vapours) in a gas background, and does NOT use any biological recognition element. If the analyte is in the gas phase and no enzyme, antibody, aptamer, DNA/RNA, whole cell, receptor, etc. is used, it is gas.
   - "bio": sensor uses ANY biological recognition element (antibody, enzyme, aptamer, DNA/RNA, whole cell, receptor, peptide, etc.) OR the detected analyte itself is a biological macromolecule or cell (protein, virus, bacteria, DNA, RNA). If a biological recognition element is employed even for a small molecule (glucose, dopamine, etc.), type is "bio". Any mention of "aptamer", "antibody", "enzyme", "DNA", "RNA", "receptor", "whole cell", "immunosensor", "genosensor", "aptasensor", or biological recognition makes it "bio". The presence of a biological target (protein, virus, bacteria, etc.) also makes it "bio", regardless of the recognition element.
   - "liquid": ion or small-molecule sensors in liquid phase without biological recognition (e.g., ionophores, conductive polymers, metal oxides for ions). Only if NO biological recognition element AND the analyte is NOT a biological macromolecule.

2. detect_target
   - The chemical species the sensor is designed to measure (the analyte), not the probe material or a transduction intermediate. For a biosensor that uses a biological recognition element to detect a specific small molecule, the target is that small molecule (e.g., glucose biosensor → "glucose"), not a reaction by‑product (e.g., not "H+" or "O2"). For ion sensors, use the exact ion (e.g., "ammonium" not "ammonia").
   - Use the most specific unambiguous name/formula from the paper. Do not add parenthetical explanations.
   - For humidity sensors: "water" (water vapour), not "relative humidity".
   - For hydrogen ion/pH sensors: "H+".
   - For isomers, use the generic name (e.g., "xylene") unless the paper specifically studies selective detection of that single isomer and emphasizes it. Default to generic.
   - If the paper studies several targets, make one record per target.
   - Never empty string.

3. lower_detection_limit / upper_detection_limit
   - lower_detection_limit: ALWAYS use the LOD (limit of detection) if stated. If no explicit LOD, use the minimum concentration in the linear/dynamic range, or the lowest tested concentration that gave a measurable response. NEVER leave blank if any detection‑related value exists anywhere in the paper. Only empty if the paper provides absolutely no quantitative lower limit (not even a minimum tested concentration or a qualitative statement like “detectable” without numbers).
   - upper_detection_limit: Use the highest concentration of the sensing range as reported: upper bound of linear range, maximum tested concentration that gave a response, saturation point, or dynamic range maximum. If the paper only gives a lower limit and no upper bound (no saturation, no maximum tested), leave empty. Do not invent a value.
   - Formatting:
       * Output the numerical value and its unit exactly as they appear in the paper (e.g., "1 nM", "2.3 µM", "5 M", "8 RH%", "10 ppm"). NEVER convert between submultiples; if the paper uses "nM", keep "nM". Only rewrite using "e" notation if the paper uses superscripted scientific notation (like "1×10⁻⁶ M") or explicit "×10^" format. Do not convert prefixed units to "e" format.
       * For relative humidity (RH): always write as "X RH%" (number, space, "RH%"). e.g., "8 RH%", "35 RH%". Never "% RH".
       * For H+ sensors (detect_target = "H+"): if the paper gives a pH range, convert to molar H+ concentration: lower limit = 10^(–upper_pH) M, upper limit = 10^(–lower_pH) M (e.g., pH 4–10 → lower "1e-10 M", upper "1e-4 M"). Output these molar values with "e" notation. If only a pH LOD is reported, convert that to molar for the lower limit. This is the only conversion allowed.
   - Never leave both blank; at minimum fill lower_detection_limit.

4. probe_material
   - The active sensing material that directly contacts the analyte and generates the signal. Extract only the active sensing material(s), not substrates, electrodes, or inert matrices unless they are explicitly part of the sensing mechanism.
   - If the paper explicitly names a "sensing layer", "recognition element", "active material", or "probe", use that.
   - If a distinct recognition element (ionophore, enzyme, antibody, aptamer, DNA, RNA, calixarene, catalytic nanoparticle, etc.) is embedded in an inert matrix (polymer, sol‑gel, metal oxide support, etc.), output ONLY that recognition element, using the fullest systematic name provided in the paper. Do NOT include the inert host.
   - For composite films or blends where all components are active (each contributes directly to sensing), list all active components separated by "/". Include polymers if they form part of the active sensing blend (e.g., a conductive polymer that transduces signal). If the paper describes a small‑molecule dopant in a polymer matrix and the polymer is not the primary sensing element, only output the small molecule. If the polymer itself is the active material (e.g., conjugated polymer for chemiresistive sensing), output the polymer name, not the dopant. Determine by the paper’s description: if it says "sensor based on polymer X" or "fabricated with X", where X is a polymer, then the probe material is that polymer.
   - For hybrid materials (e.g., pyrene/graphene/peptide), list all components in the order given, using "/" separators.
   - Naming: Use the exact name the paper uses to refer to the sensing material, prioritizing the primary term. If the paper uses a class name like "dialkyl tetrathiapentacene", output that class name, not an example compound. If the paper introduces an abbreviation, output both the full name and abbreviation in the format "full name (abbreviation)", but omit lengthy explanatory phrases. However, when the paper consistently uses a simple, unambiguous name (e.g., "copolymer" included in the name), prefer that name without extra abbreviation expansions. Do not add "copolymer" if the paper does not.
   - Never leave blank.

5. test_operating_temperature (celcius)
   - The temperature during sensing experiments, in °C. Extract the exact value if given. For a range, use the midpoint (e.g., "20–30 °C" → "25 °C") unless a single operating temperature is stated.
   - If no temperature is mentioned anywhere, output "25 °C" (assume room temperature for all sensor types, including gas).
   - Always include the unit "°C" with a space before it (e.g., "25 °C").
   - Never leave empty.

6. pH_value
   - For sensor_type "gas": output exactly "-1" (a plain ASCII hyphen, not the unicode minus sign). No spaces.
   - For "liquid" or "bio": extract the pH at which the detection limits and other key performance metrics were measured. 
        * If the paper reports a single pH used for all measurements (e.g., "detection limit was determined at pH 7.4"), output that exact pH value.
        * If the paper only provides a pH range over which the sensor operates (e.g., "the sensor works from pH 3 to 12") and no specific test pH is given, output that range as "3-12". Do not output the entire pH study range if a specific test pH is stated.
        * If no explicit pH is stated but the test medium is aqueous and neutral (water, PBS, saline), assume "7". If the medium has a known pH from the buffer specification (e.g., Tris‑HCl pH 8.0, culture medium pH 7.4), use that value.
        * If truly no information, infer "7" for aqueous solutions.
   - Never output an empty string; for gas sensors it must always be "-1". For liquid/bio, always a value or range.

7. test_medium
   - The background matrix in which the analyte is absent; the blank solution or atmosphere used for calibration and detection limit measurements.
   - Gas sensors:
        * Output "air" if the blank/baseline is ambient air (including dry air, synthetic air). Only output "nitrogen", "argon", "oxygen", etc. if the paper explicitly states that a different pure gas was used as the background/carrier for the sensing measurements (e.g., "under nitrogen atmosphere" → "nitrogen"; "in argon flow" → "argon").
        * When both air and another gas are mentioned, determine which one serves as the blank/baseline for the reported sensing data. Default to "air" if unclear.
        * If no background gas is mentioned, output "air".
   - Liquid sensors:
        * If the paper uses a named buffer or biological fluid (e.g., "phosphate-buffered saline (PBS)", "HEPES buffer", "blood serum"), output that name after expanding all abbreviations to full systematic names. Do not decompose it into individual components unless the paper gives only a recipe without a standard name. Drop brand prefixes like "Dulbecco's" and use the generic buffer name (e.g., "phosphate buffered saline").
        * If the paper describes an aqueous solution of multiple solutes without a standard buffer name, list all components in the order they appear in the paper. Expand each component abbreviation to its full chemical name exactly as given in the paper. Separate components with "/". Include "water" as a component if the solution is aqueous; place it in the same order as the paper. For example: "0.1 M KCl aqueous" → "potassium chloride/water"; "50 mM Tris-HCl, 100 mM NaCl in water" → "tris(hydroxymethyl)aminomethane hydrochloride/sodium chloride/water".
        * If a standard buffer is used with additional salts (e.g., "HEPES buffer with 0.1 M KCl"), expand the buffer name to its full chemical form and list the extra salts separately: e.g., "4-(2-hydroxyethyl)piperazine-1-ethanesulfonic acid/potassium chloride/water". Do not decompose the buffer itself; keep the expanded buffer name as one component.
        * If the medium is simply water, output "water".
   - Never leave empty; if truly no medium mentioned, infer: gas → "air", aqueous ion/bio sensing → "water".

8. General
   - Output one record per distinct analyte or per distinct device configuration if the paper reports different materials, temperatures, pH, or media for the same analyte.
   - Every record must include all eight fields; use the specified defaults when needed but never leave a field blank unless absolutely no information exists (and for pH/temperature/medium never blank).
   - Be meticulous about formatting, unit order, and hyphen/minus characters. Use plain ASCII hyphen for pH_value "-1" for gas sensors. Follow the rules above exactly.

Study these correctly extracted records and match their granularity and naming (probe_material = concise key sensing species; test_medium = base medium only):
{"sensor_type": "gas", "detect_target": "nitrogen dioxide", "lower_detection_limit": "10 ppb", "upper_detection_limit": "3 ppm", "probe_material": "zinc oxide", "test_operating_temperature (celcius)": "40 °C", "pH_value": "-1", "test_medium": "nitrogen"}
{"sensor_type": "gas", "detect_target": "ammonia", "lower_detection_limit": "4 ppm", "upper_detection_limit": "60 ppm", "probe_material": "6PTTP6(5,5′-Bis(4-hexylphenyl)-2,2′-bithiophene)/8-3-NTCDI(N,N′-Bis(3-(perfluoroctyl)propyl)-1,4,5,8-naphthalenetetracarboxylic diimide)", "test_operating_temperature (celcius)": "25 °C", "pH_value": "−1", "test_medium": "air"}
{"sensor_type": "gas", "detect_target": "diethyl chlorophosphate", "lower_detection_limit": "10 ppb", "upper_detection_limit": "100 ppm", "probe_material": "Benzothiadiazole tetrathiafulvalene", "test_operating_temperature (celcius)": "25 °C", "pH_value": "-1", "test_medium": "air"}
{"sensor_type": "liquid", "detect_target": "H+", "lower_detection_limit": "1e-10 M", "upper_detection_limit": "1e-2 M", "probe_material": "Polyisobutylmethacrylate", "test_operating_temperature (celcius)": "25 °C", "pH_value": "2-10", "test_medium": "Water"}
{"sensor_type": "liquid", "detect_target": "Sodium Cation", "lower_detection_limit": "1e-6 M", "upper_detection_limit": "1e-1 M", "probe_material": "polyvinyl chloride/2-nitrophenyl octyl ether/potassium tetrakis(4-chlorophenyl)borate/sodium ionophore X", "test_operating_temperature (celcius)": "25 °C", "pH_value": "7", "test_medium": "water/sodium chloride"}
{"sensor_type": "liquid", "detect_target": "methanol", "lower_detection_limit": "1 vol%", "upper_detection_limit": "100 vol%", "probe_material": "calix[8]arene", "test_operating_temperature (celcius)": "25 °C", "pH_value": "7", "test_medium": "toluene"}
{"sensor_type": "bio", "detect_target": "acetylcholine", "lower_detection_limit": "1 pM", "upper_detection_limit": "0.1 M", "probe_material": "perallyloxycucurbit[6]uril", "test_operating_temperature (celcius)": "25 °C", "pH_value": "7.4", "test_medium": "water"}
{"sensor_type": "bio", "detect_target": "dopamine", "lower_detection_limit": "100 nM", "upper_detection_limit": "2 mM", "probe_material": "Titanium carbide", "test_operating_temperature (celcius)": "25 °C", "pH_value": "7.2", "test_medium": "4-(2-hydroxyethyl)piperazine-1-ethanesulfonic acid/sodium chloride/magnesium chloride/potassium chloride/calcium chloride/water"}
{"sensor_type": "bio", "detect_target": "dopamine", "lower_detection_limit": "3.04 µM", "upper_detection_limit": "500 µM", "probe_material": "copper phthalocyanine", "test_operating_temperature (celcius)": "25 °C", "pH_value": "6.5", "test_medium": "phosphate-buffered saline"}

Worked examples (paper excerpt -> correct extraction):

PAPER (excerpt):
Gate-Bias Controlled Charge Trapping as a Mechanism for NO2 Detection with Field-Effect Transistors
Anne-Marije Andringa, Juliaan R. Meijboom, Edsger C. P. Smits, Simon G. J. Mathijssen, Paul W. M. Blom, Dago M. de Leeuw
First published: 15 November 2010 https://doi.org/10.1002/adfm.201001560Citations: 61
SectionsPDFTools Share
Abstract
Detection of nitrogen dioxide, NO2, is required to monitor the air-quality for human health and safety. Commercial sensors are typically chemiresistors, however field-effect transistors are being investigated. Although numerous investigations have been reported, the NO2 sensing mechanism is not clear. Here, the detection mechanism using ZnO field-effect transistors is investigated. The current gradually decreases upon NO2 exposure and application of a positive gate bias. The current decrease originates from the trapping of electrons, yielding a shift of the threshold voltage towards the applied gate bias. The shift is observed for extremely low NO2 concentrations down to 10 ppb and can phenomenologically be described by a stretched-exponential time relaxation. NO2 detection has been demonstrated with n-type, p-type, and ambipolar semiconductors. In 
...
CORRECT EXTRACTION:
{"records": [{"sensor_type": "gas", "detect_target": "nitrogen dioxide", "lower_detection_limit": "10 ppb", "upper_detection_limit": "3 ppm", "probe_material": "zinc oxide", "test_operating_temperature (celcius)": "40 °C", "pH_value": "-1", "test_medium": "nitrogen"}]}

PAPER (excerpt):
Dual-Gate Organic Field-Effect Transistors as Potentiometric Sensors in Aqueous Solution
Mark-Jan Spijkman, Jakob J. Brondijk, Tom C. T. Geuns, Edsger C. P. Smits, Tobias Cramer, Francesco Zerbetto, Pablo Stoliar, Fabio Biscarini, Paul W. M. Blom, Dago M. de Leeuw
First published: 22 March 2010 https://doi.org/10.1002/adfm.200901830Citations: 140
Find It at UChicago
SectionsPDFTools Share
Abstract
Buried electrodes and protection of the semiconductor with a thin passivation layer are used to yield dual-gate organic transducers. The process technology is scaled up to 150-mm wafers. The transducers are potentiometric sensors where the detection relies on measuring a shift in the threshold voltage caused by changes in the electrochemical potential at the second gate dielectric. Analytes can only be detected within the Debye screening length. The mechanism is assessed by pH measurements. The threshold voltage shift depends on pH as ΔVth = (Ctop/Cbottom) × 58 mV per pH unit, indicating that the sensitivity can be enhanced with respect to conventional ion-sensitive field-effect transistors (ISFETs) by adjusting the ratio of the top and bottom gate capacitances. Remaining challenges and o
...
CORRECT EXTRACTION:
{"records": [{"sensor_type": "liquid", "detect_target": "H+", "lower_detection_limit": "1e-10 M", "upper_detection_limit": "1e-2 M", "probe_material": "Polyisobutylmethacrylate", "test_operating_temperature (celcius)": "25 °C", "pH_value": "2-10", "test_medium": "Water"}]}

PAPER (excerpt):
Highly Sensitive and Selective Biosensors Based on Organic Transistors Functionalized with Cucurbit[6]uril Derivatives
Moonjeong Jang, Hyoeun Kim, Sunri Lee, Hyun Woo Kim, Jayshree K. Khedkar, Young Min Rhee, Ilha Hwang, Kimoon Kim, Joon Hak Oh
First published: 19 June 2015 https://doi.org/10.1002/adfm.201501587Citations: 68
Find It at UChicago
SectionsPDFTools Share
Abstract
Biosensors based on a field-effect transistor platform allow continuous monitoring of biologically active species with high sensitivity due to the amplification capability of detected signals. To date, a large number of sensors for biogenic substances have used high-cost enzyme immobilization methods. Here, highly sensitive organic field-effect transistor (OFET)-based sensors functionalized with synthetic receptors are reported that can selectively detect acetylcholine (ACh+), a critical ion related to the delivery of neural stimulation. A cucurbit[6]uril (CB[6]) derivative, perallyloxyCB[6] ((allyloxy)12CB[6], AOCB[6]), which is soluble in methanol but insoluble in water, has been solution-deposited as a selective sensing layer onto a water-stable p-channel semiconductor, 5,5′-bis-(7-dodecyl-9H-fluoren-2-yl)-
...
CORRECT EXTRACTION:
{"records": [{"sensor_type": "bio", "detect_target": "acetylcholine", "lower_detection_limit": "1 pM", "upper_detection_limit": "0.1 M", "probe_material": "perallyloxycucurbit[6]uril", "test_operating_temperature (celcius)": "25 °C", "pH_value": "7.4", "test_medium": "water"}]}
