After reading the scientific publication full text provided above, please extract the following information into a JSON object with the format shown below. Leave fields empty string ("") if the information is not available, except for `pH_value` which must be `-1` for gas sensors.

```json
{
  "records": [
    {
      "sensor_type": "...",
      "detect_target": "...",
      "lower_detection_limit": "...",
      "upper_detection_limit": "...",
      "probe_material": "...",
      "test_operating_temperature (celcius)": "...",
      "pH_value": "...",
      "test_medium": "..."
    }
  ]
}
```

**Field extraction rules (apply strictly):**

- **sensor_type**  
  Choose exactly one label: `gas`, `liquid`, or `bio`.  
  - `gas`: sensor operates in a gaseous medium (air, nitrogen, etc.).  
  - `liquid`: sensor operates in a liquid medium (water, organic solvents, etc.) without biological recognition elements.  
  - `bio`: sensor uses biological components for recognition (enzymes, antibodies, DNA, cells, aptamers, etc.).

- **detect_target**  
  The chemical species the sensor detects. Use standard chemical notation (e.g., `H+`, `Na+`, `Hg2+`, `NO2`, `dopamine`, `glucose`). For pH sensors, the target is `H+` (not `pH`). For humidity sensors, use `H2O`. Do not put the measurement unit as the target. If multiple targets appear, create separate records.

- **lower_detection_limit**  
  The minimum concentration of the target that can be detected (limit of detection, LOD). Format: number + unit, e.g., `1e-10 M`, `30 ppm`, `1 nM`. Use the unit as given in the paper. Do not confuse with pH, humidity, or other operational parameters. If no LOD is reported, leave empty.

- **upper_detection_limit**  
  The maximum concentration of the target in the sensor's linear or dynamic range. Format: number + unit, e.g., `100 ppm`, `1e-2 M`. If not reported, leave empty. Do not substitute pH or humidity values.

- **probe_material**  
  The primary chemical substance that interacts with the target. Provide the full chemical name or common chemical name of the active sensing component.  
  - Avoid acronyms, trade names, abbreviations, and support materials.  
  - For composites, extract the key functional substance: e.g., for "Pd nanoparticles on SnO2 nanowire" use `palladium`; for "cross-linked P3HT-azide co-polymer / calix[8]arene" use `calix[8]arene`; for "F4TCNQ-MoS2" use `2,3,5,6-tetrafluoro-7,7,8,8-tetracyanoquinodimethane`.

- **test_operating_temperature (celcius)**  
  Temperature during sensor testing, in degrees Celsius. Include number and ` °C`, e.g., `25 °C`. If the paper states "room temperature" or "ambient temperature", record `25 °C`. If no temperature information is provided, leave empty.

- **pH_value**  
  - If `sensor_type` is `gas`, set to `-1`.  
  - For `liquid` or `bio` sensors, record the pH of the test medium as a number, e.g., `7`, `7.4`. If the sensor is a pH sensor (`detect_target = H+`), provide the detection pH range as `min-max`, e.g., `3-5`. If pH is not specified for a liquid/bio sensor, leave empty.

- **test_medium**  
  The base medium in which the sensor was tested. State the essential medium name only, without target analyte or interfering substances.  
  - For gases: `air`, `nitrogen`, etc. (not `air with NO2`).  
  - For liquids: `water`, `buffer`, `ethanol`, etc. (not `aqueous buffer solutions with ...`).  
  Simplify to the core medium. If multiple media, select the one where the main detection performance is reported.

**Multiple records:** Create one record per distinct target or sensor configuration. Omit irrelevant information.

Return only the JSON object, no additional text.